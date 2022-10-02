```cpp
// system/core/init/first_stage_main.cpp
// 这是系统的入口文件，由 build/make/target/product/generic_ramdisk.mk 引用 init_first_stage
// 最终会编译为 init, 存放在ramdisk目录下，会在加载后成为kernel启动的第一个进程
// 参见： https://source.android.com/devices/bootloader/partitions/generic-boot

#include "first_stage_init.h"

int main(int argc, char** argv) {
    return android::init::FirstStageMain(argc, argv);
}
```

## First stage init
第一阶段初始化用于初始化必须的文件系统及内核模块，涉及的文件均在ramdisk中，大体流程如下：
- 配置基础的文件系统及节点
  - setenv("PATH", ...)
  - mount("tmpfs", "/dev", ...)
  - mount("proc", "/proc", ...)
  - mount("sysfs", "/sys", ...)
  - mount("selinuxfs", "/sys/fs/selinux", ...)
  - mknod("/dev/kmsg", ...)
  - mount("tmpfs", "/mnt", ...)
  - mount("tmpfs", "/debug_ramdisk", ...)
  - mount("tmpfs", "/second_stage_resources", ...)
  - stdin/stdout/stderr -> /dev/null
  - Logger -> /dev/kmsg
- 加载`vendor_ramdisk/lib/modules/*.ko`(根据modules.load)
- cp ramdisk/system/etc/ramdisk/build.prop -> /second_stage_resources 用于提供boot image属性
- if androidboot.force_normal_boot=1 (正常启动)
  - switch root /first_stage_ramdisk, 其下有 fstable.xxx 用于后续第一阶段mount
  - mount 所有 first_stage_mount设备，并在`/dev/block/by-name`下生成设备链接
    > 这里在挂载/system时会switch root 到system分区，而/system分区实际上是 $OUT/root/system 打包的，也就是它是有根目录的各个目录的，system.img的文件在/system/里面。 
- 将 `stdout/stderr` 关联到 `/dev/kmsg`
- execv /system/bin/init selinux_setup, 这里的`init`为`init_second_stage`
```cpp
int FirstStageMain(int argc, char** argv) {
    // 出现异常后直接重启到bootloader，实际上如果FirstStageMain return也会导致kernel检查到init进程退出
    // 进而系统重启，但是无法控制启动到哪里
    if (REBOOT_BOOTLOADER_ON_PANIC) {
        InstallRebootSignalHandlers();
    }
    ...
    // Clear the umask.
    umask(0);

    CHECKCALL(clearenv());
    // 参见： bionic/libc/include/paths.h
    //      /product/bin
    //      /apex/com.android.runtime/bin
    //      /apex/com.android.art/bin
    //      /system_ext/bin
    //      /system/bin
    //      /system/xbin
    //      /odm/bin
    //      /vendor/bin
    //      /vendor/xbin
    CHECKCALL(setenv("PATH", _PATH_DEFPATH, 1));
    // 系统运行必须的分区和一些设备节点、文件
    CHECKCALL(mount("tmpfs", "/dev", "tmpfs", MS_NOSUID, "mode=0755"));
    CHECKCALL(mkdir("/dev/pts", 0755));
    CHECKCALL(mkdir("/dev/socket", 0755));
    CHECKCALL(mkdir("/dev/dm-user", 0755));
    CHECKCALL(mount("devpts", "/dev/pts", "devpts", 0, NULL));
#define MAKE_STR(x) __STRING(x)
    CHECKCALL(mount("proc", "/proc", "proc", 0, "hidepid=2,gid=" MAKE_STR(AID_READPROC)));
#undef MAKE_STR
    // Don't expose the raw commandline to unprivileged processes.
    CHECKCALL(chmod("/proc/cmdline", 0440));
    // 部分设备是升级上来的，不支持bootconfig, 因此没有使用 CHECKCALL
    // Don't expose the raw bootconfig to unprivileged processes.
    chmod("/proc/bootconfig", 0440);
    gid_t groups[] = {AID_READPROC};
    CHECKCALL(setgroups(arraysize(groups), groups));
    CHECKCALL(mount("sysfs", "/sys", "sysfs", 0, NULL));
    CHECKCALL(mount("selinuxfs", "/sys/fs/selinux", "selinuxfs", 0, NULL));
    // 这个节点创建之后，就有dmesg了，可以通过串口查看log了，参见下面的说明
    CHECKCALL(mknod("/dev/kmsg", S_IFCHR | 0600, makedev(1, 11)));
    if constexpr (WORLD_WRITABLE_KMSG) {
        CHECKCALL(mknod("/dev/kmsg_debug", S_IFCHR | 0622, makedev(1, 11)));
    }
    // TODO: libc 中有用到，具体哪里引用使用后续了解
    CHECKCALL(mknod("/dev/random", S_IFCHR | 0666, makedev(1, 8)));
    CHECKCALL(mknod("/dev/urandom", S_IFCHR | 0666, makedev(1, 9)));

    // This is needed for log wrapper, which gets called before ueventd runs.
    CHECKCALL(mknod("/dev/ptmx", S_IFCHR | 0666, makedev(5, 2)));
    CHECKCALL(mknod("/dev/null", S_IFCHR | 0666, makedev(1, 3)));
    // These below mounts are done in first stage init so that first stage mount can mount
    // subdirectories of /mnt/{vendor,product}/.  Other mounts, not required by first stage mount,
    // should be done in rc files.
    // Mount staging areas for devices managed by vold
    // See storage config details at http://source.android.com/devices/storage/
    CHECKCALL(mount("tmpfs", "/mnt", "tmpfs", MS_NOEXEC | MS_NOSUID | MS_NODEV,
                    "mode=0755,uid=0,gid=1000"));
    // /mnt/vendor is used to mount vendor-specific partitions that can not be
    // part of the vendor partition, e.g. because they are mounted read-write.
    CHECKCALL(mkdir("/mnt/vendor", 0755));
    // /mnt/product is used to mount product-specific partitions that can not be
    // part of the product partition, e.g. because they are mounted read-write.
    CHECKCALL(mkdir("/mnt/product", 0755));

    // 包含adb_debug.prop 和 调试版的selinux文件
    // 使用 make bootimage_debug 来构建
    // See: build/make/core/main.mk: 1561
    CHECKCALL(mount("tmpfs", "/debug_ramdisk", "tmpfs", MS_NOEXEC | MS_NOSUID | MS_NODEV,
                    "mode=0755,uid=0,gid=0"));

    // /second_stage_resources is used to preserve files from first to second
    // stage init
    CHECKCALL(mount("tmpfs", kSecondStageRes, "tmpfs", MS_NOEXEC | MS_NOSUID | MS_NODEV,
                    "mode=0755,uid=0,gid=0"));
    // 将kernel提供的stdin/stdout/stderr(在无ramdisk,有串口时关联到了/dev/console)关闭，
    // 关联到 /dev/null
    SetStdioToDevNull(argv);
    // 输出log到/dev/kmsg, 类似： LOG(INFO) << "test";
    // TODO: 目前只能输出10行log，kmsg只打开一次，后续会一直写入，可能和sync有关，待查
    InitKernelLogging(argv);
    LOG(INFO) << "init first stage started!";
    ...
    // 加载vendor_ramdisk中/lib/modules/下的ko文件
    boot_clock::time_point module_start_time = boot_clock::now();
    int module_count = 0;
    if (!LoadKernelModules(IsRecoveryMode() && !ForceNormalBoot(cmdline, bootconfig), want_console,
                           module_count)) {
        if (want_console != FirstStageConsoleParam::DISABLED) {
            LOG(ERROR) << "Failed to load kernel modules, starting console";
        } else {
            LOG(FATAL) << "Failed to load kernel modules";
        }
    }
    if (module_count > 0) {
        auto module_elapse_time = std::chrono::duration_cast<std::chrono::milliseconds>(
                boot_clock::now() - module_start_time);
        setenv(kEnvInitModuleDurationMs, std::to_string(module_elapse_time.count()).c_str(), 1);
        LOG(INFO) << "Loaded " << module_count << " kernel modules took "
                  << module_elapse_time.count() << " ms";
    }
    ...
    // cp /system/etc/ramdisk/build.prop /second_stage_resources/system/etc/ramdisk/build.prop
    if (access(kBootImageRamdiskProp, F_OK) == 0) {
        std::string dest = GetRamdiskPropForSecondStage();
        std::string dir = android::base::Dirname(dest);
        std::error_code ec;
        if (!fs::create_directories(dir, ec) && !!ec) {
            LOG(FATAL) << "Can't mkdir " << dir << ": " << ec.message();
        }
        if (!fs::copy_file(kBootImageRamdiskProp, dest, ec)) {
            LOG(FATAL) << "Can't copy " << kBootImageRamdiskProp << " to " << dest << ": "
                       << ec.message();
        }
        LOG(INFO) << "Copied ramdisk prop to " << dest;
    }
    ...
    // 检查 bootconfig/cmdline 中是否有androidboot.force_normal_boot=1
    if (ForceNormalBoot(cmdline, bootconfig)) {
        mkdir("/first_stage_ramdisk", 0755);
        // SwitchRoot() must be called with a mount point as the target, so we bind mount the
        // target directory to itself here.
        if (mount("/first_stage_ramdisk", "/first_stage_ramdisk", nullptr, MS_BIND, nullptr) != 0) {
            LOG(FATAL) << "Could not bind mount /first_stage_ramdisk to itself";
        }
        // 会将现有的挂载设备移动到新的root下，排除以下的分区：
        // - 挂载点为"/"
        // - 新root的挂载点
        // - 已经在其他分区下的挂载点，例如： /sys/fs/selinux
        SwitchRoot("/first_stage_ramdisk");
    }
    // Recovery 忽略挂载
    // 正常启动挂载所有在fstab中标识为first_stage_mount的节点，会在/dev/block/by-name/中生成启动设备各个分区的链接
    if (!DoFirstStageMount(!created_devices)) {
        LOG(FATAL) << "Failed to mount required partitions early ...";
    }

    // 释放ramdisk
    // TODO: 这里为了调试放入了toybox，sh等，log中会打印not freeing ramdisk，不清楚后续是否有影响
    struct stat new_root_info;
    if (stat("/", &new_root_info) != 0) {
        PLOG(ERROR) << "Could not stat(\"/\"), not freeing ramdisk";
        old_root_dir.reset();
    }
    if (old_root_dir && old_root_info.st_dev != new_root_info.st_dev) {
        FreeRamdisk(old_root_dir.get(), old_root_info.st_dev);
    }
    // 将 stdout/stderr 关联到 /dev/kmsg
    auto fd = open("/dev/kmsg", O_WRONLY | O_CLOEXEC);
    dup2(fd, STDOUT_FILENO);
    dup2(fd, STDERR_FILENO);
    close(fd);
    // 在当前经常替换执行init
    const char* path = "/system/bin/init";
    const char* args[] = {path, "selinux_setup", nullptr};
    execv(path, const_cast<char**>(args));
```
## Second stage init
第二阶段初始化主要用于配置selinux，根据rc文件启动系统
```cpp
// system/core/init/main.cpp

int main(int argc, char** argv) {
#if __has_feature(address_sanitizer)
    __asan_set_error_report_callback(AsanReportCallback);
#endif
    // Boost prio which will be restored later
    setpriority(PRIO_PROCESS, 0, -20);
    if (!strcmp(basename(argv[0]), "ueventd")) {
        return ueventd_main(argc, argv);
    }

    if (argc > 1) {
        if (!strcmp(argv[1], "subcontext")) {
            android::base::InitLogging(argv, &android::base::KernelLogger);
            const BuiltinFunctionMap& function_map = GetBuiltinFunctionMap();

            return SubcontextMain(argc, argv, &function_map);
        }
        // 第二阶段启动的第一步，配置selinux
        if (!strcmp(argv[1], "selinux_setup")) {
            return SetupSelinux(argv);
        }
        // 加载rc文件，启动系统
        if (!strcmp(argv[1], "second_stage")) {
            return SecondStageMain(argc, argv);
        }
    }

    return FirstStageMain(argc, argv);
}

```

---------------
- makedev
  ```c
  // maj, min must match kernel define:
  // https://www.kernel.org/doc/Documentation/admin-guide/devices.txt
  dev_t makedev(unsigned int maj, unsigned int min);
  
  // Eg: for /dev/kmsg
  makedev(1, 11)
  ```

- mknod
  ```c
  // creates a filesystem node (file, device
  // special file, or named pipe) named pathname, with attributes
  // specified by mode and dev.
  // 'dev' can be make by makedev()
  int mknod(const char *pathname, mode_t mode, dev_t dev);

  // Eg: create kmsg device file only current user(root) can rw.
  mknod("/dev/kmsg", S_IFCHR | 0600, makedev(1, 11))
  ```