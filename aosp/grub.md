- [GRUB Home](https://www.gnu.org/software/grub/)
- [GRUB Documentation](https://www.gnu.org/software/grub/grub-documentation.html)
- [GNU GRUB Manual 2.06](https://www.gnu.org/software/grub/manual/grub/grub.html#Overview)

设备名称规则：
- 均用`(xx)`表示, 设备名称从0开始，分区名称从1开始。
- `(fd0)`: 第一个光盘设备
- `(hd0,msdos2)`: 第一个硬盘设备上的第二个分区，`msdos`指明分区表类型，`2`为第二个分区，分区从1开始计数
- 命令行操作时，可以用tab来自动完成，例如：`set root=(`，后面按`Tab`就可以自动列出目前识别的硬盘设备
- `boot/grub/grub.cfg`: 是默认的grub配置目录，包含了启动项的配置
- `search`: 通过文件名称(`-f, --file`)、文件系统label(`-l, --label`)、文件系统UUID(`-u, --fs-uuid`)来查找磁盘
  > 查找第一个包含kernel文件的磁盘, 并把磁盘位置`例如(hd0)`设置给android变量
  > search --set android -f /kernel

一个启动linux系统的例子：
```grub
linux /kernel root=/dev/ram0
initrd /initrd.img
boot
```
- `/kernel`为内核的文件路径，默认没有磁盘路径时，会使用root变量的路径，也就是这里等同于`($root)/kernel`
- 后面的参数都是传递给内核的参数，这里的`root=xxx`不会修改grub中的`root`变量的值
- `initrd`是指一个临时文件系统，它在启动阶段被Linux内核调用，[参考这里](https://zh.wikipedia.org/wiki/Initrd)
- `boot`命令用于启动系统，前面的几个都是用于设置启动的必要参数
