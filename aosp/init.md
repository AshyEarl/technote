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