

- 启用调试用sh

  要使用shell，必须满足下面的条件
  - 有shell二进制工具，Android中为`/system/bin/sh`
  - `libc`: 用于包装syscall，供sh调用
  - `linker64`: 用于链接其他必须的动态库
  - `toybox`: Android上的小工具集合，类似`busybox`, 用于命令行操作
  Android12中当把Recovery资源打包进`vendor_boot`时
  ```MakeFile
  BOARD_USES_RECOVERY_AS_BOOT :=
  BOARD_USES_GENERIC_KERNEL_IMAGE := true
  BOARD_MOVE_RECOVERY_RESOURCES_TO_VENDOR_BOOT := true
  BOARD_EXCLUDE_KERNEL_FROM_RECOVERY_IMAGE :=

  PRODUCT_PACKAGES += \
    sh.recovery \
    linker.recovery \
    toybox.recovery \
  ```
- rust shell: https://poor.dev/blog/terminal-anatomy/
- 磁盘设备： /sys/block
- cgroup: https://www.cnblogs.com/hellokitty2/p/13775811.html
- fstab: https://source.android.com/devices/architecture/kernel/mounting-partitions-early
- init启动流程
  - first stage init 主要负责: 
    1. 挂载`/dev`,`/sys`,`/proc`,`/sys/fs/selinux`等，创建`/dev/socket`,`/mnt/vendor`等目录，创建设备节点`/dev/ptmx`,`/dev/null`,`/dev/kmsg`等，
    2. 加载vendor ramdisk指定的内核动态库ko
    3. 如果正常开机，switch root到`first_stage_ramdisk`用于切换使用的根目录
    4. 挂载fatab定义的文件系统，包括`/system`等
    5. 跳转到`/system/bin/init`, args: selinux_setup
  - selinux_setup: 
    1. 加载selinux policy
    2. 跳转到`/system/bin/init second_stage`
  - second_stage:
    1. 挂载`/apex`,`/linkerconfig`
    2. 设置部分`/dev`文件selinux
    3. 启动property service
    4. 挂载vendor overlay文件系统
    5. 加载rc文件，开始开机执行rc
       1. SetupCgroupsAction
       2. early-init
       3. init
       4. late-init/charge
       5. queue_property_triggers_action
  ```bash
  # action
  on <trigger> [&& <trigger>]*
    <command>
    <command>
    <command>
  # Eg:
  on boot
    ifup lo

  # service
  service <name> <pathname> [ <argument> ]*
    <option>
    <option>
    ...
  # Eg:
  service recovery /system/bin/recovery
    socket recovery stream 422 system system
    seclabel u:r:recovery:s0

  # import
  import <path>
  # Eg:
  import /init.recovery.${ro.hardware}.rc

  # properties
  # init.svc.<name>: State of a named service ("stopped", "stopping", "running", "restarting")

  # ctl.[<target>_]<command>: 控制命令
  setprop ctl.start logd
  ```

- 目前的文件系统

| 分区          | 大小 | 说明                                    |
| ------------- | ---- | --------------------------------------- |
| bootloader    | 5MB  | UEFI启动分区，BootLoader                |
| misc          | 2MB  | BootLoader、recovery、system通讯        |
| metadata      | 16MB | data分区加密信息临时存放                |
| boot_a        | 30MB | Kernel, 通用ramdisk（init）             |
| boot_b        | 30MB |                                         |
| vendor_boot_a | 50MB | vendor ko, recovery ramdisk             |
| vendor_boot_b | 50MB |                                         |
| super         | 3GB  | super(system,vendor,product,system_ext) |
| userdata      | 3GB  | data分区                                |

