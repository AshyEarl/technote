[Dynamic Partitions](https://source.android.com/devices/tech/ota/dynamic_partitions/implement)
- 可以使用下面的方法来查看super分区的最小io size
  ```bash
  # ls -l /dev/block/by-name/super
  lrwxrwxrwx 1 root root 16 2022-06-10 14:31 /dev/block/by-name/super -> /dev/block/vda12
  # cat /sys/block/vda/queue/minimum_io_size
  512
  ```
- 确认super分区已对齐
  ```bash
  # cat /sys/block/sda/sda17/alignment_offset
  0
  ```
- 根据[这里](https://source.android.com/devices/tech/ota/dynamic_partitions/implement#system-as-root-changes)的说明，bootloader引导时必须添加启动选择到bootconfig中
  ```bash
  androidboot.force_normal_boot=1
  // bootloader开机必须传递boot_devices给init
  androidboot.boot_devices = xxx
  ```
- bootconfig开机后可以在下面的文件找到
  ```bash
  # cat /proc/bootconfig
  ```
- 直接编译 boot/ramdisk
  ```bash
  make ramdisk
  // 会在out目录生成 ramdisk.img 和 ramdisk 目录
  ```

- 生成efi.img
  ```bash
  dd if=/dev/zero of=efi.img bs=128k count=1
  mkfs.fat efi.img
  mkdir efi
  sudo mount -o loop efi.img efi
  cd efi
  sudo mkdir -p efi/boot
  sudo cp ~/opensource/aosp/boot_uefi_loader/x86_64/loader/efi_loader.efi efi/boot/bootx64.efi
  cd ..
  sudo unmount efi
  ```