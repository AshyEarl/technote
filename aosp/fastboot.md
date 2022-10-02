Android 10以前`Fastboot`是在`bootloader`中实现的，后来将它移动到了用户空间中，也就是在Recovery启动后的一个`fastbootd`进程中实现
## Fastbootd的启动流程
```cpp
// bootable/recovery/recovery_main.cpp

// int main(int argc, char** argv)
// 1.1 启动参数中指定启动到fastboot
if (option == "fastboot" && android::base::GetBoolProperty("ro.boot.dynamic_partitions", false)) {
          fastboot = true;
}
// 1.2 UI界面中选择进入fastboot
case Device::REBOOT_FASTBOOT:
    ui->Print("Rebooting to recovery/fastboot...\n");
    Reboot("fastboot");
    break;
case Device::ENTER_FASTBOOT:
    if (android::fs_mgr::LogicalPartitionsMapped()) {
        ui->Print("Partitions may be mounted - rebooting to enter fastboot.");
        Reboot("fastboot");
    } else {
        LOG(INFO) << "Entering fastboot";
        fastboot = true;
    }
    break;

// 2. 启动fastbootd
// 2.1 调试代码，设置tcp模式（最终应该在recovery_ui中实现，这里仅仅测试用）
if (fastboot) {
    device->PreFastboot();
    // 设置fastboot连接模式，具体实现参见 system/core/fastboot/device/fastboot_device.cpp: 70
    // FastbootDevice::FastbootDevice()
    // 
    // if (android::base::GetProperty("fastbootd.protocol", "usb") == "tcp") {
    //     transport_ = std::make_unique<ClientTcpTransport>();
    // } else {
    //     transport_ = std::make_unique<ClientUsbTransport>();
    // }
    android::base::SetProperty("fastbootd.protocol", "tcp");
}
// 2.1 设置属性： sys.usb.config
std::string usb_config = fastboot ? "fastboot" : 
    IsRoDebuggable() || IsDeviceUnlocked() ? "adb" : "none";
if (!SetUsbConfig(usb_config)) {
    LOG(ERROR) << "Failed to set USB config to " << usb_config;
}
// Sets the usb config to 'state'.
static bool SetUsbConfig(const std::string& state) {
  android::base::SetProperty("sys.usb.config", state);
  return android::base::WaitForProperty("sys.usb.state", state);
}
// 2.2 bootable/recovery/etc/init.rc -> /system/etc/init/hw/init.rc
// 由init启动fastbootd服务
on property:sys.usb.config=fastboot
    start fastbootd
on property:sys.usb.config=fastboot && property:sys.usb.configfs=0
    write /sys/class/android_usb/android0/idProduct 4EE0
    write /sys/class/android_usb/android0/functions fastboot
    write /sys/class/android_usb/android0/enable 1
    setprop sys.usb.state ${sys.usb.config}
service fastbootd /system/bin/fastbootd
    disabled
    group system
    seclabel u:r:fastbootd:s0
```
## 常用的命令
- fastboot连接, 下面的命令均用`fastboot`代替
  ```bash
  # Bootloader 中使用udp连接，读写峰值3mb/s(有线连接)
  fastboot -s udp:192.168.0.100 xxxx
  # fastbootd 中使用tcp连接，读写峰值10mb/s(有线连接，瓶颈在路由器，未测试千兆路由器)
  # 默认使用usb连接，因为测试x86设备无gaget(无法使用usb device模式)，因此使用tcp方式
  fastboot -s tcp:192.168.0.100 xxxx
  ```
- 获取版本
  ```bash
  $ fastboot getvar version
  version: 0.4
  Finished. Total time: 0.007s
  $ fastboot getvar all
  (bootloader) cpu-abi:x86_64
  (bootloader) snapshot-update-status:none
  (bootloader) super-partition-name:super
  ...
  ```
  ```cpp
  // system/core/fastboot/device/commands.cpp
  bool GetVarHandler(FastbootDevice* device, const std::vector<std::string>& args) {
      const std::unordered_map<std::string, VariableHandlers> kVariableMap = {
          {FB_VAR_VERSION, {GetVersion, nullptr}},
          ...
      };
      // Special case: return all variables that we can.
      if (args[1] == "all") {
        for (const auto& [name, handlers] : kVariableMap) {
            GetAllVars(device, name, handlers);
        }
        return device->WriteOkay("");
      }
  }
  // system/core/fastboot/device/variables.cpp
  constexpr char kFastbootProtocolVersion[] = "0.4";

  bool GetVersion(FastbootDevice* /* device */, const std::vector<std::string>& /* args */,
                    std::string* message) {
    *message = kFastbootProtocolVersion;
    return true;
  }
  ```
- 刷写boot分区
  ```bash
  $ fastboot flash --slot a boot boot.img
  Sending 'boot_a' (18660 KB)                        OKAY [  1.658s]
  Writing 'boot_a'                                   OKAY [  0.505s]
  Finished. Total time: 2.212s
  ```
  ```cpp
  // 以下为fastbootd收到的命令

  // 获取是否有该物理或逻辑分区（在super分区中）
  getvar:has-slot:boot -> yes
  // 获取当前启动号，a或者b
  getvar:current-slot -> a
  // 由于部分分区（例如super分区刷写）很大，无法一次性放入内存后再写入磁盘，因此使用了分次写入的方式
  getvar:max-download-size -> 0x10000000
  // 是否为逻辑分区（在super分区中）
  getvar:is-logical:boot_a -> no
  // 获取分区大小
  getvar:partition-size:boot_a -> 0x1F00000
  // 开始下载到内存
  download:01239000
  // 将下载到内存的数据写入到磁盘
  flash:boot_a
  ```