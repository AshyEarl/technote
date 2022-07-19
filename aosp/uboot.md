- `ACPI`: Advanced Configuration and Power Interface
  > aims to establish
industry-standard interfaces enabling OS-directed configuration, power
management, and thermal management of mobile, desktop, and server platforms.

- `UEFI Boot for Mere Mortals`: [视频在这里](https://www.youtube.com/watch?v=6jt1nNWAwEk)
- `edk2`: [一个uefi的开源实现](https://github.com/tianocore/edk2)
- `UEFI Secure Boot in U-Boot`: [视频在这里](https://www.youtube.com/watch?v=VnsF3uRZzNk)
- `Marrying U-Boot, uEFI and grub2`: [视频在这里](https://www.youtube.com/watch?v=qJAkJ3nmWgM)
- `Tutorial: Introduction to the Embedded Boot Loader U-boot`: [视频在这里](https://www.youtube.com/watch?v=INWghYZH3hI)
- `UEFI boot: how does that actually work, then?`: [文章在这里](https://www.happyassassin.net/posts/2014/01/25/uefi-boot-how-does-that-actually-work-then/)
- Ubuntu中可以使用`efibootmgr`这个工具查看boot entry:
  ```bash
  $ sudo efibootmgr -v
  BootCurrent: 0002
  Timeout: 1 seconds
  BootOrder: 0002,0000
  Boot0000* Windows Boot Manager	HD(1,GPT,37c7af9f-8c62-4b89-9d40-4bd5c3a7e18c,0x800,0x32000)/File(\EFI\MICROSOFT\BOOT\BOOTMGFW.EFI)WINDOWS.........x...B.C.D.O.B.J.E.C.T.=.{.9.d.e.a.8.6.2.c.-.5.c.d.d.-.4.e.7.0.-.a.c.c.1.-.f.3.2.b.3.4.4.d.4.7.9.5.}...a................
  Boot0002* ubuntu	HD(1,GPT,37c7af9f-8c62-4b89-9d40-4bd5c3a7e18c,0x800,0x32000)/File(\EFI\UBUNTU\SHIMX64.EFI)
  ```
- `源码地址`: [github](https://github.com/u-boot/u-boot), [gitlab](https://source.denx.de/u-boot/u-boot)
### 开启`NetConsole`
- 开启udp支持
  ```bash
  > Networking support 
      [*] Enable generic udp framework
      [*] NetConsole support
  > Boot options
      [*] Enable preboot
      // 这里参见 doc/usage/netconsole.rst, 192.168.0。101是主机地址，默认使用6666为输入输出端口号
      preboot default value: dhcp; ping 192.168.0.101; setenv nc 'setenv stdout nc;setenv stdin nc'; setenv ncip 192.168.0.101; run nc
  ```
- 电脑端开启`netcat`，这一步可以在uboot启动前运行，就会有更早的log了
  ```bash
  // 这里的uboot-device-ip可以通过在uboot上运行dhcp命令看到，我这里usb键盘
  // 在uboot下不工作，可以在上面的preboot里写入`dhcp`，然后开机就能在屏幕上
  // 看到输出ip
  tools/netconsole <uboot-device-ip> [port]
  ```
### 一些常用的命令
- 环境相关
  ```bash
  // 输出当前key-value环境信息
  => env print
  // 编辑
  => env set name value
  // 编辑
  => env edit name
  edit: xxxx
  // 运行变量中的命令
  run <env-key>
  ```
  一些特殊变量：
  - `stdin`,`stdout`,`stderr`: 输入输出流指向，`coninfo`可以查看支持的设备,该设置立即生效
  - `bootcmd`: 默认的开机命令，指定启动设备、内核路径及initrd位置等
  - `preboot`: 自动开机前执行的脚本，多个以分号`;`分割

- 配置网络
  ```bash
  // 静态
  env set ipaddr 192.168.0.101
  env set netmask 255.255.255.0
  // 测试
  ping 192.168.0.1
  // 动态获取，这里会尝试加载tftp的image报错，不管他
  dhcp
  ```
- 文件系统
  ```bash
  // 可以用 mmc, scsi, usb
  // scsi列出文件, 0为第一个设备，1为第一个分区：
  ls scsi 0:1
  // scsi查看分区
  scsi part
  ```
- 启动系统
  ```bash
  // 具体命令帮助参见各自的help: help xxx
  =======================
  // 加载bzImage kernel到0x1000000内存位置
  ext4load scsi 0:2 01000000 android-2022-05-27/kernel
  // 加载initrd
  ext4load scsi 0:2 07000000 android-2022-05-27/initrd.img
  // 设置启动参数
  env set bootargs BOOT_IMAGE=/android-2022-05-27/kernel root=/dev/ram0 androidboot.hardware=android_x86_64
  // 启动, 后面的大小0x14ecb9是initrd的大小
  zboot 01000000 - 07000000 0x14ecb9
  ```