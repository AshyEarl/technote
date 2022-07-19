- [Linux-USB project](http://www.linux-usb.org)
- [Kernel USB Gadget
Configfs Interface](https://elinux.org/images/e/ef/USB_Gadget_Configfs_API_0.pdf)


----
使用下面的方法来编辑`kernel config`([参见这里](https://www.android-x86.org/documentation/customize_kernel.html)), 最终的config会生成在`./out/target/product/x86_64/obj/kernel/`:
```bash
make -C kernel O=$OUT/obj/kernel ARCH=x86_64 menuconfig
```

根据[这里](https://wowothink.com/a64c6a27/)的文章打开对应的config：
- `CONFIG_CONFIGFS_FS`: 默认已开启
- `CONFIG_USB_LIBCOMPOSITE`:
  ```
  依赖项激活后自动开启，这项标记为m
  Device Drivers/USB support/USB Gadget Support

  里面的两项也标记开启：
  USB Gadget functions configurable through configfs       -> m
  Mass storage   -> *
  ```
- `CONFIG_USB_CHIPIDEA`, `CONFIG_USB_CHIPIDEA_UDC`:
  ```
  这项标记为m
  Device Drivers/USB support/ChipIdea Highspeed Dual Role Controller
  
  里面的标记开启：
  ChipIdea device controller
  ``` 