[安装参考链接](https://android.googlesource.com/device/google/cuttlefish/)

[Google-Source](https://source.android.com/setup/create/cuttlefish)

- 安装过程中需要设置`Go`代理：
```shell
go env -w GOPROXY=https://goproxy.cn,direct
# 取消代理
go env -u GOPROXY
# 查看GO的配置
go env
go env -json
```

- 安装主机端工具
```bash
sudo apt install -y git devscripts config-package-dev debhelper-compat golang
git clone https://github.com/google/android-cuttlefish
cd android-cuttlefish
debuild -i -us -uc -b -d
sudo dpkg -i ../cuttlefish-common_*_*64.deb || sudo apt-get install -f
sudo usermod -aG kvm,cvdnetwork,render $USER
sudo reboot
```

- 启用主机GL硬件加速渲染
```
HOME=$PWD ./bin/launch_cvd --gpu_mode=gfxstream
```

> - `crosvm`：Rust实现的虚拟机Monitor
> - [项目地址](https://android.googlesource.com/platform/external/crosvm/)
> - [主机端工具](https://github.com/google/android-cuttlefish)

```bash
// RemoveZip 用于免下载直接列出zip目录
// 项目地址： https://pypi.org/project/remotezip/
pip install remotezip

remotezip -l http://xxx
```

----
## 有用的git logs
可以在review站点上查看这些修改：`https://android-review.linaro.org/plugins/gitiles/device/google/cuttlefish/+/[change_ref]`
- 72c9b54eaffb8edb40d1f2a3776e5dec5a06c647
```bash

Relocate VSoC to vendor partition.
    
This change starts a process of moving all custom vsoc/cuttlefish changes to
vendor partition. Once it completes, we will no longer have to transfer
large system.img files with every change - and everyone will be able to
test their Android changes with the same vsoc vendor code.

Oh, and a bonus: to build vendor image, execute:

    m vnod

takes about 7 seconds.
```

- 5f73a258d29eb56bce466589bd344ef0d51f42df
```bash
Exact copy of GCE Camera.

Changes in Android.mk file to update build target to camera.vsoc

[Camera HAL IMPL]
```

- 7a8daddb1f8e54e3269978e8a46d5a9b03e7333e
```bash
Gralloc hal implementation
```

- ca7fb19c52b33a9a498c9f58817c649b8204525e
```bash
Adapted the VNC server to run on host side.
```

- b34828cd72a3e7b5d7c7cbac758349fa3836dba4
```bash
Exact copy of GCE Audio
```

- 68ce823dd1f9c269184d44d2687325937e5c537c
```bash
Use libvirt to start cuttlefish.

This change employs libvirt to set up virtual machine monitor for us.
Libvirt is a library for configuring and managing VM instances.
```

- 2f781fbad62d840be62c045ac7b58318e2c41fed
```bash
Move documentation to its proper place
```

- 43e88fe80093e1359b427139b4ad9c01d6923282
```bash
Some useful READMEs

The README in Launcher folder explains how installing, configuring,
deploying and launching cuttlefish image looks right now. It is super
simple.
```

- 49e8063385e7b9b45db28423acd8e4514fcfeaba
```bash
Add adbshell to allow ssh access across adb
```

- e54deb503a711fad92e67caeddf2dcae464a1fc9
```bash
Attach vendor partition to cuttlefish.

This change is part of the effort to move custom (vendor) extensions to
/vendor partition.
This change complements ag/2924351 and requires vendor.img to be
available (a dummy file would currently suffice).
```

- c339ab5674381658ea0f53b3cb39e2fe26c4277f
```
Virtual WIFI, stage 1: netlink interface.

NOTE: Some headers are not included in this CL. I've done this to split
a larger code review into smaller chunks.

Makefiles not included, either.
```

- 9dc14e79ae86595fc91af97feef3a70a8387a585
```bash
Verbatim copy of gralloc, hwcomposer, framebuffer, vnc_server and wpa_supllicatn_lib
```

- e201723de81269e0ddaeba37b6ac26b40c6c51e8
```
Adapted legacy gralloc, hwcomposer hals for cuttlefish
```

- 2571613aaebe142a0c7afb2dd4781aeec08d0186
```bash
Implement kernel log server to properly retain all log and detect
VIRTUAL_DEVICE_BOOT_COMPLETED message.
```

- 6d56889026e6259ac8284a3ad5c7acc2eb92fed6
```
VSoC input service

Adds a service to android that waits for input events on each device's
queue and forwards them to the appropriate virtual device in the
kernel. It also takes care of configuring said virtual devices.
```

- af49e9bd9240048ebbd42bac2254625fec0d2fb8
```bash
Mount system and vendor via the DTB
```

- 91f8142b22126f76db78a6804b47b4462d6fd3e1
```bash
Add the ability to debug the kernel via tcp
```

- 4cc9ce682d860b912e4f5025c243dd150d96cf5e
```
Change the vsoc RIL to use eth1, not eth0.

On the current version of cuttlefish, the backing virtio connections for
mobile and wireless connectivity are identical, but some devices such as
android auto do not have mobile connectivity. Using eth1 instead of eth0
for mobile then allows the auto configuration to always use eth0,
instead of using eth0 when there's only one connection and eth1 when
there are two.
```

- 3262dbb861bce88824eacb04193359199c781371
```bash
Add Android Things build target for Cuttlefish.
```

- b3de8ef0fde35861ab2b770d027fdc6a17f0a637
```
Add Go targets on cuttlefish.

Test: boot device with the following, verify RAM in UI, and also
      verify that ro.config.low_ram gets set to true.
    lunch cf_x86_go_phone-userdebug
    m cf_local_image
    $ANDROID_BUILD_TOP/vendor/google_devices/cuttlefish_common/crun --memory 1024

    lunch cf_x86_go_512_phone-userdebug
    m cf_local_image
    $ANDROID_BUILD_TOP/vendor/google_devices/cuttlefish_common/crun --memory 512

    lunch aosp_cf_x86_64_go_512_phone-userdebug
    lunch aosp_cf_x86_64_go_phone-userdebug
    lunch aosp_cf_x86_go_512_phone-userdebug
    lunch aosp_cf_x86_go_phone-userdebug
```

- 572df462f095c96d86151f43004d6468f26ce455
```
Enable zram on cuttlefish go image

Test: Verified that /proc/swap and vmstat showed that swap was working
```

- 7338d35b81f77cfc3f4980ff34aae10a5750dba1
```
Makes adb shell read device name from config

Rather than reconstructing the device names based on the patterns we'd
expect to see.
```

- 20d896ac24f4be483ff4a89ea7114fc561064d2e
```
Basic audio streamer (pcm)

Streams the audio out of the running cuttlefish instance.
```

- e7949c3276ba6f476b0d889bee1faa0febaa70bd
```
Make it possible to disable SYSTEM_ROOT_IMAGE feature

In newer platform versions, the system-as-root image feature is
deprecated, eliminating the need to pass the Android fstab through
device-tree. As cuttlefish only uses the device-tree for the Android
fstab, we can stop using it altogether.

For maximum backwards compatibility, this change does not actually
remove the build/install of the initrd-root.dtb file, but the file is
not required any more. An fstab.vsoc file is installed in the root of
the initramfs which describes all of the mount points.

This change can be enabled with TARGET_BUILD_SYSTEM_ROOT_IMAGE=false
on the build command line.

This change also makes it possible to boot newer platform versions on
hypervisors that do not support device-tree.
```

- d946b5f9b112522709e02273f156b0c416b05a6a
```
Support enabling vsock connections with a launcher flag.

vsock connections are a guest/host communication channel.

This is gated on a flag as it requires user access to /dev/vhost-vsock
on the host as well as guest kernel support, neither of which are
guaranteed by Cuttlefish at the moment. The flag is enabled when the
capability "vsock" is detected.
```

- fa69c38ec51c8aa29658a08a3a7d3a454ecc0651
```
Support ADB over vsock from the guest and the host.

The implementation is based off of socket_forward_proxy, but the data is
sent through a vsock file descriptor instead of the shared memory
window.

This allows replacing one of the dependencies on the shared memory
window with a dependency on vsock.

Test: adb bugreport on launch_cvd -kernel=...cf-4.14... -vsock_guest_cid=3 -adb_mode=vsock_tunnel
```

- 5b47aac60b25032664f5549cf6feb4571a83a06c
```
Implement basic support for recovery
```

- 1231b6d74a9d8bd77a31f32612b8cbabdf2650e2
```
Add fstab for vendor partition loading in crosvm

A separate file is required because the parser on the
crosvm side iterates through every line and creates a
partition in the device tree in the guest.  This also
allows for expansion in case there's a need for
partitions beyond the vendor partition.
```

- 54f8574c673df62089222d0cff598497e6f4ae29
```
Configure NeuralNetworks HAL default drivers for cuttlefish

This change:
* Adds the NN sample driver binaries to cuttlefish
* Configures the NN sample driver sepolicy for cuttlefish

Note that the NN vintf is configured via vintf_fragments in
platform/frameworks/ml/nn/driver/sample/Android.bp.
```

- 63d10059b61ebb3a309b055271f3cb67ed7b5aba
```
Create a vsock_half_tunnel mode.

This uses native adb/vsock support in the guest, but presents a TCP
connection on the host that can be used across the network. This is a
compromise between the high-performance vsock code and remote access.

Observed behavior with adb push/pull:
$ adb push ~/tmp/100m /data/local/tmp
1 file pushed. 135.9 MB/s (104857600 bytes in 0.736s)
$ adb push ~/tmp/100m /data/local/tmp
1 file pushed. 229.4 MB/s (104857600 bytes in 0.436s)
$ adb pull /data/local/tmp ~/tmp/100m2
1 file pulled. 13.2 MB/s (104857600 bytes in 7.603s)

Push is surprisingly close to the performance of native_vsock, while
pull is still comparable to tunnel and vsock_tunnel.
```

- 7fd180b0531f6a0291b91b765ce7c86567c48096
```
Enable Bluetooth Audio HAL 2.0 for cuttlefish
```

- 252d98bf5b6aad7c6f173b4ea90315757ba14f3f
```
Add a new audio HAL for native VMM audio

Most VMMs emulate audio hardware which is compatible with a Linux ALSA
driver. We can take advantage of this standard interface using tinyalsa
and eliminate the complexity of forwarding audio through a custom
method. This eliminates the dependency on the vsoc driver for audio.

```

- 3a28319be1e1730ffc3c3abaa35fc3622d01a241
```
cf: add missing labels for /dev/* files

Label /dev/input_events and /dev/socket_forward as input_events_device
and socket_forward_device respectively. And add appropriate permission
to domains using these /dev nodes.
```

- 803eb9627bb14b2dfc21fcb27610d65bc551982b
```
Fix recovery and enable by default

Ensure the boot partition is defined in fstab so "make dist" works
again, and also define sepolicy for recovery to enable the installation
of OTA packages with selinux enforcing. The build is now enabled by
default so we can catch future breakages more easily.
```

- 81976d083c7c14fad77cbb013c6f1b7e20eae76e
```
Support dynamic partitions feature by default

Build with dynamic partitions enabled by default, which produces a
"super.img" file instead of the {system,product,vendor}.img files
generated previously. The system image is no longer a "root image"; it
still contains a useful root filesystem, but the true root is now
contained in the ramdisk, which chain loads it after the dynamic
partition has been constructed.

Use TARGET_USE_DYNAMIC_PARTITIONS=false to disable this feature.
```

- 534196b5c66828af055cb6bc303b27050dd9d3eb
```
Generate OTA package for Dynamic partitions.
```

- 96f189eedf95e7a16896ea7c850a89d25e77dab8
```
Switch cf_x86_phone to NATIVE_BRIDGE

This change switches cf_x86_phone to new native_bridge support
from build system. NATIVE_BRIDGE_* properties allow us to build
up to 2 translated architectures and solve the problem with
having native_bridge build support on x86_64 products.
```

- 938867b3a1a470ef68a6c2b8c242ada49dd60af1
```
media: disable Codec 2.0

Cuttlefish does not support ION needed for Codec 2.0
```

- 5ca63108f7ce566d6799881785e30b2c534068bc
```
Add tools to host package for rebuilding super.img

lpmake and lpunpack are used to create super.img and extract logical
partitions from super.img, which are needed if we want to mix images
from different targets (e.g., GSI + CF vendor) to form new super.img.

Bug: 134461288
Test: $ lunch aosp_cf_x86-userdebug
      $ m dist
      $ tar xf cvd-host_package.tar.gz
      $ bin/lpmake
      (proper usage text)
      $ bin/lpunpack
      (proper usage text)
```

- 3d784b554bf7439a5262af6782572bab4cef9e33
```
Add aosp_cf_x86_phone_noapex

Test: acloud create --local-image
Test: adb shell getprop ro.apex.updatable (is false)
Test: adb shell ls -l /system/apex/ (apexes are flattened)
```

- 2d25dc9c5d724ed7bed5e797ab125e38161ed797
```
Adding cec 1.0 service entry in Cuttlefish

Test: manual
```

- 3e008ba5d3e729663d3cea0650d86fa208a88f6d
```
Enable system_ext partition for cuttlefish

It adds system_ext both for dynamic partition and for legacy
partition configurations.

Bug: 134359158
Bug: 138347390
Test: lunch cf_x86_phone-userdebug; make
      check if system_ext is mounted
```

- 0a5d7dec4f90fd26c5a09652b9b08af2f0806ee6
```
Enable CAN bus HAL

Bug: 135918744
Test: adb shell /data/nativetest/VtsHalCanControllerV1_0TargetTest/VtsHalCanControllerV1_0TargetTest --hal_service_instance=android.hardware.automotive.can@1.0::ICanController/socketcan
Test: adb shell /data/nativetest/VtsHalCanBusVirtualV1_0TargetTest/VtsHalCanBusVirtualV1_0TargetTest --hal_service_instance=android.hardware.automotive.can@1.0::ICanController/socketcan
Test: adb shell canhalctrl up test virtual vcan0
      adb shell /data/nativetest/VtsHalCanBusV1_0TargetTest/VtsHalCanBusV1_0TargetTest --hal_service_instance=android.hardware.automotive.can@1.0::ICanBus/test
```

- 9824c8f9353c7294626984bc39ed8c254ca18980
```
Tuner Hal 1.0 Add Tuner Hal 1.0 entry to Cuttlefish

Test: Cuttlefish
```

- 19d713e7602b4fb3597915210d3416cb20e0e2ed
```
Add misc partition

misc partition is used to store bootloader messages and
commands to recovery.

Originally aosp/955522

Bug: 79094284
Test: boot with -composite_disk, check /dev/block/by-name
```

- 089bab2156dd29fe490406a2926d1932cf1b4e85
```
Add script to flash a block device with a disk image

This is intended for use with the arm64 Rockchip RK3399 board.
```

- 16b4aa62f402e23a81b63ea89f27eb95d0f0c388
```
Configure host PC to connect to Rock Pi over ethernet

 - configure host PC to be a DHCP server on 2nd ethernet port
 - set up IP Masquerade to route packets through PC
 - generate a key pair for the rock pi
 - set up ssh config with port forwarding
```

- e8915624902c44370a1897e94fe4006043f0ec57
```
Revise CF BoardConfig inherit from BoardConfigMainlineCommon.mk

Some settings in BoardConfig affect the output content in system.img.
This patch revises CF BoardConfig to align the setting in
BoardConfigMainlineCommon.mk. Which can fix the following system
difference between CF and GSI:

- output apex dexpreopt
- libbluetooth.so
- lmkd (TARGET_LMKD_STATS_LOG)

The patch tries to keep other setiings.

Bug: 140156429
Bug: 137709825
Bug: 137711200
Bug: 137713484
Test: compare the output files between with/without the patch
```

- c3fd6e525cf247e3c60b38f0a8f79bd69907ac20
```
Use the composite disk support in crosvm.

Depends on this:
https://chromium-review.googlesource.com/c/chromiumos/platform/crosvm/+/1667767

Performance characteristics:
Composite disk assembly on disk: 60 seconds
Composite disk assembly on tmpfs: 20 seconds
Composite disk support in crosvm: 0.3 seconds

bpttool creates "holes" between the partition table and the first disk
and between the last disk and the backup partition table. Android seems
to use these so this change creates separate temporary files for them.

Test: launch_cvd -composite_disk=composite.img
```

- f56f6b404e21463b3b68141de46a468ce222e6ba
```
Send logcat ouput over serial ports instead of vsocket

- Delete logcat_receiver and vsock_logcat

Sending logcat over vsocket requires a couple of extra processes with
the only purpose of forwarding the logs around, using the serial port
is possible to make the logcat command write directly to a node file
in the guest, the device for which is connected to a file host side.

Bug: 132911257
Test: locally
```

- a9e82c0bb8da2871ae3a372389f707872da7eda6
```
Add mesa as a GL implementation

BUG: 128842306
BUG: 130058017
```

- 6f83918858fed3d1a4b18684e9b2f69625ebe367
```
Add an error check after flashing device

During flashing the block device, it could fail (user could
manually eject the device, out of disk space, etc.) in which
case the script should exit and inform the user.
```

- e9d94701788bd9d814bd7ac99d78d9ee5bcefe82
```
Add a boot control HAL.

Bug: 138861550
Bug: 130078382
Test: VtsHalBootV1_0TargetTest
      VtsHalBootV1_1TargetTest
```

- a4220b9554fe8f2bece415ec36fcecb8074a9358
```
Make cuttlefish a Virtual A/B device.

With this patch, all dynamic partitions are A/B.

Bug: 140527427
Bug: 143108875
Test: builds, boots
```

- 82c10506c3dfd48fc8839ee3a3941c81a1b077ca
```
Create arm64 stable host image build script

This is intended for use with Rockchip's RK3399.

This goes through a number of build steps including:
 - Building ARM Trusted Firmware
 - Generating the U-Boot TPL/SPL + miniloader
 - Generating the U-Boot image tree blob
 - Generating the boot environment + boot script
 - Building the kernel + root file system
 - Generating the initial ramdisk
 - Wrapping everything up into a minimized image file

BUG: 141692755
Test: local build and run on rock pi 4b
```

- e4eeded3a70aaf89e5ce66d8eb8ec4f42d319324
```
Add capability to write ARM host image to block device

This allows for writing directly to a block device
instead of going through an intermediate step of
creating the image and then writing to the device.

BUG: 141692755
Test: local build and run on rock pi 4b
```

- 5f263951c783a2aecadb29df143a9bb6229c50f1
```
Add trampoline init script to ARM host image

This init script is used by secondary storage
to set the initial state of the environment
partition on the eMMC.  This is useful for two
purposes: setting the boot order so secondary
storage is always booted first, and setting
the ethaddr to the value that is stored on the
secondary storage.

BUG: 141692755
Test: local build and run on rock pi 4b
```

- bcdc07b903a1803c9443ead275772cf4521dedc8
```
Add support for DFU over TFTP to ARM host image

This adds support for firmware upgrades over TFTP.
The device will broadcast BOOTP messages on the
network to find the TFTP server.  If one is running,
it attempts to download the manifest in the expected
format.  The manifest has a SHA field that describes
the contents of the update.  If this is different
than the current version, the device will run through
the rest of the manifest and request those image files
from the TFTP server.  Gzipped files are supported.

BUG: 142283345
Test: local build and run on rock pi 4b
```

- 3cedcc510d3930030204866a4ef669119b29bda3
```
Adds gpu driver setup to host image build scripts

Updates the Cuttlefish host image build scripts to always use a GCE instance
with a GPU/accelerator for building and to always install the latest GRID GPU
driver. The driver fails gracefully when started on a GCE instance without a
GPU/accelerator which allows us to just always have it present.

Also updates default base image to debian-10 from debian-9 to ensure that kvm
group is created. It was previously created when installing some of the qemu
packages but those were removed in
https://github.com/google/android-cuttlefish/commit/8156c58a7bf57efa9e9ab75d48f8ec52264a32ef

Also adds lzop package installation to the GCE installation. It was removed in
https://github.com/google/android-cuttlefish/commit/8156c58a7bf57efa9e9ab75d48f8ec52264a32ef
but it is needed when creating instances via `acloud create`.

Bug: b/128842306
Test: built host image and booted cvd on gpu and non-gpu instances
```

- 3c35fd87b6c779f0ccde6bf059f0ad2ffc034fd5
```
Delete virtual_usb_manager and USB support

This functionality was never enabled for crosvm.

Test: Build and run locally
```

- 3a78defa0b1479f6d200fec2d8c1baf4be0b4ce2
```
Support Resume on Reboot

When an OTA is downloaded, the RecoverySystem can be triggered to store
the user's lock screen knowledge factor in a secure way using the
IRebootEscrow HAL. This will allow the credential encrypted (CE)
storage, keymaster credentials, and possibly others to be unlocked when
the device reboots after an OTA.

Bug: 63928581
Test: make
Test: boot with emulator
Test: atest VtsHalRebootEscrowTargetTest
```

- 225ddf4c4be7ba454dc1666b0278351d2960be9a
```
Adds a WaylandScreenConnector to VNC Server

Creates a ScreenConnector which runs a minimal WaylandServer compositing server
to receive frame updates from the device.

Note: This changes the interface of ScreenConnector to require clients to
provide a callback functor which can be run within the WaylandServer event
loop. Unlike the existing ScreenConnectors, the Wayland compositing server
must explicitly acknowledge and notify the source when the server is done
with a given buffer so the source can reuse that buffer. The reference
Wayland server implementation also does not appear to be thread safe with
in a single Wayland client so ScreenConnector clients can not be responsible
for acknowledging and notifying the source.

Bug: b/128842306
Test: locally built and ran cuttlefish with crosvm<-wayland->vnc
```

- a18ff1ac0b75d7788979847c46fff3dcc37396c7
```
Add webrtc streamer
```

- e049b793c17cfca7139dc53555d40ceafbff1936
```
Integrate webRTC with the rest of the cuttlefish host elements

Call webRTC from the launcher based on launcher flag
Ensure only one streamer is enabled (vnc vs webrtc)
Break webrtc's dependencies on vsoc

Bug: 143713267
Test: launch_cvd -start_webrtc=true -start_vnc_server=false -webrtc_public_ip=127.0.0.1 --webrtc_certs_dir=$(pwd)/host/frontend/gcastv2/webrtc/certs --x_res=720 --y_res=1440 --webrtc_assets_dir=$(pwd)/host/frontend/gcastv2/webrtc
```

- 373640c36da5263a2c5a748c92bdc3063a1d46f0
```
Use ScreenConnector from webrtc

This allows to run webrtc even when using a host side gpu

The screen connector API and the webrtc's run loops are not
immediately compatible, so a thread is added in webrtc to wait for new
frames and post them (synchronously) to the run loop where the frame
is processed.

Bug: 141887532
Test: launch_cvd -start_webrtc [-gpu_mode=drm_virgl]
```

- f0e3d8a6a8dfad53f4ea5ec4181c7a4d629b5473
```
nable multiple instances of webrtc in the same host

- Different http server for each cuttlefish instance (8443..8450)
- UDP ports are chosen from a given range, new ports are tried until
  an unused one is found.

The above changes enable any number of webRTC-enabled cuttlefish to
run concurrently in the same host, with a limit of at most 4 clients
connected overall.

Bug: 141887532
Test: launch_cvd -start_webrtc and try to connect from 5 browser tabs.
```

- cfd08406466e36821a5047321518f7fb2b39b2a4
```
Add keyboard support to webrtc

Bug: 141887532
Test: launch_cvd --start_webrtc
```

- b04746d6ff9247f48e781e5d7e17ef8b339694f4
```
[webrtc] Client doesn't request for audio

The server is only sending silence anyways, so this change doesn't
lose features. It reduces the number of ports required on the server,
effectively increasing the maximum number of clients to 8.

Bug: 147910720
Test: 5 clients connected simultaneosly
```

- 9e3e3568fc9688665e23d9b95f827102c965830f
```
Mark cuttlefish with the resume-on-reboot feature

This marks the cuttlefish device as having the feature for OTA programs
to use.

Test: Use mock OTA console app to apply OTA
```

- fadce78ec3d76f301cc6bb06266df776feafeadf
```
Delete usbforward

This is not currently launched automatically inside the guest.

Test: launch_cvd
```

- d86434b01c1f071166d2dee7ca334118dbf2acb4
```
Update libvirglrenderer for aarch64

Test: tested on rockpi
```

- ba4a524ca6ef100ea26c62fa263195ccffdf38bf
```
add gfxstream guest-side libraries

bug: 146066070

- for vk, egl gles1,2,3
- minigbm is assumed
```

- 630837953eda88738f89941eb7f06fd549e9f054
```
add gfx_stream gpu mode

bug: 146066070

To be used with compatible crosvm + gfxstream libraries on host

Test:

With current tree's crosvm (no 2d or gfxstream backend):
launch_cvd...boot complete using swiftshader (expected)
launch_cvd --gpu_mode=drm_virgl...boot complete using virgl (expected)
launch_cvd --gpu_mode=gfxstream...fails (expected)

With 2d + gfxstream crosvm + gfxstream host libs in LD_LIBRARY_PATH:
launch_cvd...boot complete using swiftshader (expected)
launch_cvd --gpu_mode=drm_virgl...boot complete using virgl (expected)
launch_cvd --gpu_mode=gfxstream...boot complete using gfxstream (expected)
```

- 1d4670f519d2061b6b957e23415482bc179efaa3
```
gfxstream: use drm_hwcomposer

bug: 146066070

all seems to work as long as we implement CMD_FLUSH_RESOURCE on the host
side
```

- 26b48cf645e2b523540c6df6c6eaf59f977b80b6
```
Add multi-display support

This adds an external physical display to CuttleFish, rendering to separate vnc
session is not supported yet, but screencap/screenrecord works well, and
should be good for some testing purpose.

Bug: 139209467
Test: adb shell dumpsys display
Test: confirms that existing single display with vnc/webrtc still works
```

- 9d7dab663695de87a5e41c93336ac2ede6f54279
```
Remove ranchu hwc from device packages

Not needed for gfxstream
```

- f3f62e2bd097ba32f6979d33267677bd15bca647
```
Add HWC2.3 support to CuttleFish

code is modified from HWC2On1Adapter, then add missing HWC 2.3
functions, for example, EDID related ones.

This is a rather large CL, here are some details:

(1) skeleton of code is copied from HWC2On1Adapter.
(2) add glue layer to initiate HWC2 on top of existing 1.1 cutf_cvm composer.
(3) debug changes made in (1) and (2) without changing anything else,
this is a critical step, and time consuming. At first, it simply causes
crashes, and need to fix them, such as fences, capabilities, etc.
(4) once (3) works, then add HWC 2.3 specific functions.
(5) it's then ready to upgrade manifest xml to version 2.3.

```

- 617305f094d13cb84f60c8de5c25f666c2c02c01
```
Add ODM partition to Cuttlefish

This can be used by derived targets downstreams to add
additional behaviors (e.g. specific ueventd rules)

Bug: 150094400
Bug: 150478175
Test: acloud create --local-image --local-instance
```

- 1a6ac8245b66c1b9f8c7a805cae26da91dc9db0b
```
[WebRTC] Send input events over data channel
```

- 9782e79d5d67dea25cd76ad43c0badb8a29ba3fc
```
Establish webRTC media transport over TCP

Add "?use_tcp=true" to the URL options to use it.
```

- 683adf95cd435d63cbfb860e979bab467d7c6253
```
Allow downstream Cuttlefish devices to format userdata as ext4

This is technically already possible as one can set TARGET_USERDATAIMAGE_FILE_SYSTEM_TYPE
to be ext4 instead of the default f2fs, but then fstab only allows for mounting
userdata as f2fs.

Allow setting an ext4-based fstab file if that's what TARGET_USERDATAIMAGE_FILE_SYSTEM_TYPE
points to.
There are other issues around this that will need solving before the setting actually kicks in,
but this is a first step.
```

- 2688132d11e9c89299acb57bcce61b143fc506a9
```
Stop sharing system uid with cuttlefish service

- Remove shared uid from the manifest.
- Remove the permissions that cannot be granted to vendor apks.
- Remove PackageVerifierManager that requires WRITE_SECURE_SETTINGS.
- Convert cuttlefish service to a foreground service as vendor apks must
  not start in background.
- Convert cuttlefish service to a privileged app so that selinux allows
  it to write to /dev/kmsg.

Bug: 153706617
Test: fetch_cvd -default_build cf_dist:cf_x86_phone \
      -system_build gsi_dist:aosp_x86 ; \
      bin/launch_cvd
```

- b93c3c0ad3d356da54f6a1eba4f30fd8282106d4
```
Add debugfs support for cuttlefish.

Mounts debugfs and chmods to 0755 just like a real device.
Bug: 152792812
Test: Build cuttlefish with KASAN+KCOV, test KCOV.
```

- d610b3b9e3445f1faefa1af033e5baa2716c4b05
```
Implement RecoveryUI for cuttlefish

This initial version just allows us to fake a USB connection, so
recovery will not exit after 120s.
```

- d512a6851706c6291b2c0aa57adb4e645fe2c4d9
```
Create an "env" partition to control u-boot.

This partition is created at runtime and contains the initial
environment and startup instructions for u-boot.

Bug: 13379709
Test: launch_cvd -bootloader=/path/to/u-boot.rom -use_bootloader -composite_disk=composite.img
```

- d1340bf6bcd2a51cd72bc1007e9a41c029018383
```
Simulate bootloader androidboot.vbmeta parameters

This allows us to enable AVB 2.0 correctly without disabling the feature
in the generated metadata. For now, use avbtool to calculate the digest,
and directly access the AvbVBMetaImageHeader to calculate the total
vbmeta size needed by the framework.
```

- e17d0435e49594c89463df7ac6dd5085aaa7f005
```
[WebRTC] Add the adb endpoints to the client library

It allows to exchange data directly between the client (browser) and
the adb daemon running in the guest. It's only used to show logcat
ouput for now as an example.

The current implementation temporarily uses the signaling server to
forward the data, but that will migrate to using a direct data channel
between client and device when possible.
```

- a8c6f7ba73f29fb83e85d1f18c86005a99efe981
```
[WebRTC] Provide the option to connect via TCP in the UI

Instead of relying on URL parameters
```

- 7b69463c7f6232671751b9787b22b9283cbe61e4
```
Install vulkan loader in host image

... so that the vulkan loader can be used for gpu detection
and auto accelerated rendering enablement.

Bug: b/150944551
Test: ./create_base_image.sh
Test: ./upload_to_gce_and_run.py
Test: gcloud compute ssh
Test: launch_cvd
Test: launch_cvd --gpu_mode=drm_virgl
```

- 66787ebc369ac401fe7fa316eb075df2c92e6032
```
[WebRTC] Limit logcat output in the UI to 100 lines

Logcat output can grow pretty fast and render the page unusable. Since
this feature is only meant for testing and demonstrating ADB
connectivity, it's better to only show the last 100 lines and keep the
UI responsive.
```

- 3bba62857b85ed4c4f4ac05fe00414fdad110a52
```
Add sdcard support to cuttlefish

This adds support for formatting with vfat, and uses it to create an
sdcard.img file with embedded MBR and FAT32 partition data. The
partition defaults to 2GB but this can be configured.

This image persists across runs but it is not overlayed because it is
assumed that the initial contents of the file are not useful.
```

- b66b7883812cafe68d45beceec170ac141476c7a
```
Enable Android to mount and format sdcard1

Enable support for external SD Cards unconditionally.
```

- f91996982436ef6ae61b2a15cd693ff822b708e1
```
Bring back Ethernet feature

This is needed to run EthernetTetheringTest, even though cuttlefish does
not have Ethernet devices, we must add it back.
```

- af8ad6218e554f00db8e109775a25815656d340c
```
Add vendor_boot to the composite disk

Give the vendor_boot partition a fixed/maximum size like boot and
recovery and add it to the composite disk. Fix up the a/b slotting for
boot and move misc to the top of the partition table. Move all of the
partitions needed by the bootloader to the top of the disk so they are
less likely to be reordered.

Giving the vendor_boot partition a fixed size allows us to enable AVB.
```

- 7915595d24dac7c65cd5db2ea6e17d0c4ba1aa1c
```
Enable chained vbmeta on cuttlefish

Remove a mainline override that disabled AVB. This change also enables
chained vbmeta so that system/system_ext/vbmeta_system can be replaced
independently of the rest of the system, which will be useful for future
build mixing.

This change works around a "feature" of libavb which always reads the
maximum vbmeta payload size of 64KB, even though the build system aligns
the vbmeta/vbmeta_system partitions to 4KB, and on cuttlefish they are
8K/4K respectively.

Finally, enable --flags 2 for now so that this new functionality is
disabled at runtime until we can provide a valid value for
'androidboot.vbmeta.digest'.
```

- e4481691f1756080363e047f58d93f0628c3c284
```
Update Cuttlefish to Gralloc 4 on Minigbm

Bug: b/123764798
Bug: b/146515640
Test: launch_cvd
Test: launch_cvd --gpu_mode=drm_virgl
```

- 60be8992a373ad70ee16f7a5225fd111a83b93b2
```
Renewed our (self-signed) certificate for another year.

Bug: 158131191
Test: launch_cvd --start_webrtc=true
```

- 3569b6f93256aec5356d15e0cdcd09d9523150c5
```
Enable lz4 ramdisks on cuttlefish

The kernels are still gzip compressed but this can be fixed later.
```

- d0f756d7d4467f0ad42ad0e0dd6de993505f6a70
```
Grant GPU access to mediaswcodec

... which will be needed for Cuttlefish with Minigbm
Gralloc 3 and Gralloc 4.

Bug: b/157902551
Test: launch_cvd
Test: launch_cvd --gpu_mode=gfxstream
```

- 4c20aa778f7a97be50284237d9afea1394b82320
```
Switch logcat_receiver from vsock to virtio-console

Remove "logcat_port" and replace it with "logcat_pipe_name". Alter the
logcat_receiver to read from a pipe instead of a vsocket. Update the
crosvm and qemu backends to create the hvc2 serial port and update the
init script to write to this new location.

In the guest, logcat can now run as logd, which fixes some long-standing
sepolicy bug_maps. Keep logcat in the root group, so it can access all
the logpersist classes. Remove extraneous sepolicy for writing to a
pipe.

Bug: 155217436
Bug: 160341724
Test: launch_cvd
Test: launch_cvd -vm_manager=qemu_cli
```

- 432259903dff0ce7ec6fbe31c43e6c0eb998f0a2
```
modem-simulator-guest: work with modem simulator on host

1. making corresponding changes in reference-libril
and refernce-ril to work with host side modem simulator
2. get it to work with emulator as well
3. add 5g support

Still no impact on current cuttlefish stack, as this
modem simulator is not enabled yet.
```

- 5c490d406e213b241dd8eb56fe59cb5157bdf06b
```
Add support for shared virtiofs mount

Create a "shared" directory under cuttlefish_runtime.X which is
unconditionally shared with the guest. This virtiofs filesystem is
mounted at /mnt/vendor/shared and is accessible only to root. This
is similar to how 'persist' works on Pixel.

Implementing this for QEMU is postponed for now; the feature was only
added in QEMU 5.0 and requires a new subprocess called virtiofsd to
be started; we can revisit this when QEMU 5.0 has rolled out to more
places. Added "nofail" to the fstab to accommodate this.
```

- 3fcbdb2bb86445feef68d6a1242bb699a7682a2d
```
Add USB host feature flag

We support USB pass through now
```

- e1dd8ea7c187bce934d9e8c0a87a76d9a05e9cd1
```
[WebRTC] Use chromium's libwebrtc to stream cuttlefish (1/2)

Create a static library that supports streaming several displays,
touch and keyboard input devices and an ADB channel.
```

- e7faaf9d4a649db16c81500255fb4f6f00016fa6
```
[WebRTC] Use chromium's libwebrtc to stream cuttlefish (2/2)

* Use the library to stream cuttlefish
* Replace the original implementation under gcastv2
* Delete unused libsource library
* Delete unnecessary public_ip configuration field, but keep the flag
  around marked as deprecated to avoid breaking current usage.
```

- 6ac1ccde15a8dcf341515811ac072f21bdea7e0a
```
Adds a Power button, sending commands over the control channel.

Test: launch_cvd, press (and/or hold) Power button
```

- 85a32becd9bc0ef0859f1a50757790533c6e09f3
```
Enable Codec 2.0 by default

Re-enabling Codec 2.0 by default for NATIVE_COVERAGE purposes.
```

- bcf8c210cf73c46c58b4840e050cc9f120acc489
```
Added bootloader binary to cf x86 based targets
```

- 478ed5a016250be4acfa10c874e19c98272ef542
```
Add boot and vendor_boot to AB_OTA_PARTITIONS

Test: apply OTA
```

- 9309f041b7be0683b8c395c1d8234bef3707cc67
```
Add bootloader to composite disk
```

- 496196207a58f8e9add9f15bfac890aaa1fadb6a
```
Enable virtio-input devices for QEMU

Even if the display isn't enabled, the devices can still be added to the
PCI bus, which makes the device list more similar to crosvm. When we add
GPU support to QEMU, we'll need these devices. Add them.

Some of the wakeup devices and the PNP RTC are shuffled around due to
the ACPI PNP assignment changes with the added devices.

Currently we enable virtio-mouse instead of virtio-tablet, as the tablet
device is ignored by InputFlinger.
```

- dbe3c6e33f16aa3d64a814ed1f4b61c5e30a452f
```
Enable bootloader flow on cf by default
```

- defea3dc195967452c2f882454cf3fe2ad787a33
```
Enable GPU mode auto detection

Enables accelerated rendering in Cuttlefish automatically if
launch_cvd can detect a discrete GPU with GLES surfaceless
support.

Introduces a new detect_graphics binary to the host package.
The launcher this detect_graphics binary in a subprocess
first as a check for some problematic configurations. For
example, GCE instances that do not have a GPU but that have
a GPU driver installed (which helps simplify our host image
creation).

Bug: b/150944551
Test: `launch_cvd` (w/o --gpu_mode on workstation with GPU)
Test: `launch_cvd` (w/o --gpu_mode on GCE instance w/ GPU)
Test: `launch_cvd` (w/o --gpu_mode on GCE instance w/o GPU)
```

- e6f40cee6ec3a10a9e9d8e5beb5f40ec7e588b41
```
Enable easier debug of bootloader

Bug: 168628037
Test: local bootloader boot with the pause_in_bootloader flag
```

- 0e13d083b084294af91c01bece06f0a462beda09
```
Make serial console configurable

Newer versions of Android detect if the console service is running and
display a permanent notification to the user if it is. Make it possible
to opt out of the serial console, then disable the serial console by
default.

We cheat a little here, because on a physical device the "console" has
a double meaning (both the kernel logs and serial console are generally
muxed over the same port), but on cuttlefish it just means the serial
console, and so we can keep the kernel logging ("console=") going out
to the virtio-console, which is a high performance device anyway. This
cheating means we always specify androidboot.console=, it just points
to an invalid device if the console is "disabled".

Disabling the console also implicitly disables kgdb, as kgdb is only
useful with kdb right now, and kdb requires an 8250-based (or ARM)
serial port to work, and this must also be a console so it can be
interactive. The bootloader feature is *not* disabled, however its
messages are now written to a read-only serial port which is appended
to by Linux when it starts, via kernel.log.

This was tested on crosvm in all modes described above, but serial
console is currently broken on QEMU so that path was not tested. It
will be brought back in another change.
```

- faa94093a121c0e9fddebadd47660e44ed794004
```
Remove Crosvm Nvidia workaround

Nvidia's EGL library will fork to run nvidia-modprobe on the
first invocation on a fresh GCE instance. Crosvm interprets
the forked modprobe child process completing as the GPU process
failing which causes failures on the first launch of a fresh
GCE instance. A workaround was added in place to launch the
nvidia-modprobe command on all launch_cvd invocations. We can
now remove this because the cuttlefish-common pacakge installed
on newer host images will run the nvidia-modprobe command on
init.

Bug: b/150633183
Test: launch_cvd --gpu_mode=gfxstream (on fresh GCE instance)
```

- 9e6788e02cb9e90c05c0df11096b68f322ab9409
```
Support enabling vhost-net acceleration

This currently must be opted into, as it requires /dev/vhost-net to be
writable by the user, and that requires an upgrade of cuttlefish-common.
```

- 7aa46f36fa9d10cb7ed37ff049ab0f8712780522
```
fastboot: add fastboot_hal 1.1 to kKnownMissingHidl list

We add a new API to fastboot_hal version 1.1, so we add new
fastboot_hal 1.1 to kKnownMissingHidl list to prevent TH testing
break.
```

- 55be4c7ae422ddcd21b8330135470137418dbbda
```
Listens and reacts to screen changed events in WebRTC.

- GceService writes screen change events to the kernel log.
- Kernel Log Monitor reads these events, writes JSON events to its
  subscribers.
- [NEW] WebRTC's Kernel Log Events Handler subscribes to kernel log
  events. It forwards screen changed events to the WebRTC client over
  the control channel.
- WebRTC client responds to these events to rotate the screen and update
  the device details view.

(Does not yet support resizing, only rotation.)

NO_TYPO_CHECK=Failing due to library method getFormatedErrorMessages.

Bug: 165944524
Test: Use a guest binary to inject accel data, observe rotation.
```

- 500ad97d46604b9a5f43051bd8b54abc4a873bc0
```
load kernel, kernel modules from updated locations

The GKI kernel is in kernel/prebuilts. The cuttlefish kernel modules
are now in device/google/cuttlefish_prebuilts/kernel. Use these new
locations.
```

- b3a6295d8f1fdc597a2836ebbd4999a55b976f90
```
Implements a screen rotation button in the control panel.

- The 'rotate' button uses the WebRTC adb socket to send an adb shell
  command to run a new cuttlefish_rotate binary.
- The WebRTC adb socket now connects automatically on
  VIRTUAL_DEVICE_BOOT_STARTED rather than waiting for user interaction.
- The cuttlefish_rotate binary lives on the /vendor partition. This
  binary triggers device rotation by injecting accelerometer data to the
  ISensors HAL.
- Removes the logcat view.

Bug: 163079832
Bug: 163079837
Test: Use the new rotate button, observe screen rotation.
```

- ebf25c1adfdf7d6a9ceb40635b25661707750950
```
cuttlefish: enable f2fs compression
```

- 1a3c7ba564ac886ef4573472af842f3a35152366
```
Package cvd_host_package.zip using soong

This change adds a new Soong module type cvd_host_package and its
instance cvd_host_package (the names are the same). The module is
expected to replace cvd-host-package.tar.gz which is built in
host_package.mk.

The need for doing this in Soong are as follows:

1) We had to track transitive dependencies (like the shared libs used by
the host tools), which is very error-prone. The soong module tracks
transitive dependencies automatically, so we need to specify the
dependencies to the top-level host tools.

2) We had mixture of x86 and arm64 binaries in the same package. This
not only increases the size of the package, but also made the structure
of the package complicated as binaries had to be stored in different sub
directories (e.g. aarch64-linux-gnu v.s. x86_64-linux-gnu) to avoid
conflict. With the new Soong module, the package is created for each
target and each pacakge has only the artifacts for the target.

One notable change is that the format has changed from tar.gz to zip.
The change was needed because the tar that we use in Soong doesn't
support appending files to an existing archive. Appending is necessary
because we have to gathe files from different base directories:

For the Linux/arm64 target, arch-specific files are gathered from under
out/soong/host/linux_bionic-arm64, while common-arch files are gathered
from out/soong/host/linux-x86. (Note: it actually has to be
out/soong/host/linux_bionic-common, but we keep the old path for compat
reason). soong_zip supports the use case.

WARNING: this does NOT yet replace cvd-host_package.tar.gz with the new
cvd_host_package.zip. The switch will be made later.

Bug: 168086242
Test: m out/soong/host/linux_bionic-arm64/cvd_host_package.zip
out/soong/host/linux-x86/cvd_host_package.zip
```

- d705e8e21269462b35285fc04c36fc90ceaa27c9
```
Config cuttlefish to generate partial update

Many partners have asked for platform support of system-only update.
Here, we config the cuttlefish as an example, that can generate a
partial ota package to update partitions included in the vbmeta_system.

Note the update_group is designed to be a subset of partitions that
can update independently. So we split the 6G google_dynamic_partitions
into the system (5G) and vendor (1G) parts.

In the merged build case, remove all update groups in the default build,
and put all dynamic partitions under the group google_dynamic_partitions.
This keeps the old behavior for merged builds.

Test step for merged build:
fetch_cvd -default_build dist1_path:aosp_cf_x86_phone -system_build dist2_path:aosp_cf_x86_phone -directory test_dir;
cd test_dir; HOME=$PWD; launch_cvd

Bug: 170921953
Test: generate & apply a partial update, launch_cvd, lpdump to check the group;
```

- be6d794f5fb4ee386d7325010e77766537869d2b
```
Enable GKI and generic boot image.

Setting this has no effect on the boot image build rules, but it is
required for GKI APEXes to be built and installed on CF.

Also add TARGET_DEDICATED_RECOVERY env var. If set, build dedicated
recovery partition instead. This is needed for testing; full support
for dedicated recovery partition isn't there.

This change does not enable GKI mainline updates. See follow up CLs.
```

- 9ea92a2c08184814b650113adafcd15bad5a5ad3
```
Remove direct boot when -use_bootloader=true

If the bootloader is enabled, we don't need to specify a kernel, initrd
or kernel cmdline on the QEMU command line. On ARM64, specifying these
parameters can confuse the boot process when -bios is specified.
```

- d1b280f8221ff6be19e7fbfa9d525452f0300c94
```
Add update_engine_sideload to recovery

Without it, we can't install OTA packages using the sideload method.

This component used to be installed unconditionally, but now appears
to be optional, and we didn't specify it.
```

- eff7e95e9396b3312b39ee54f3a8d4c373400d34
```
Use real sdcard for recovery /sdcard

For a long time we've required that the sideloadable updates are pushed
to /tmp, because we had no usable writable partition in recovery.

However, cuttlefish now supports a VFAT-formatted sdcard image which can
be used for sideloading updates. Use that instead.

Test: launch_cvd -start_vnc_server=true -gpu_mode=drm_virgl
Test: adb reboot recovery
Test: adb root
Test: adb push /path/to/ota.zip /sdcard/
Test: Installed update over VNC
```

- 95d475ad700e44195b7d2fae5d200d65f0c6abf1
```
Sync the cuttlefish fstabs with coral

- Userdata should be checked
- Userdata should be formatted if blank
- Userdata should have quota enabled
- Enable userdata checkpointing for ext4 and f2fs (differently)
- Remove obsolete fsverify usage
```

- c5b6eb9f5e760ac5a916b9669a6e3f72d2892815
```
Don't send frames from vsock hwcomposer when there are no consumers

The vsock hwcomposer is notified by the streamer of whether they want
the frames or not based on how many clients there are connected. The
hwcomposer sends the last frame not previously sent upon receiving the
notification.

Use an unsigned int for the sequential frame number in the
hwcomposer. This number can overflow which is technically undefined
behavior in c++.

Bug: 172587529
Fix: 172684162
Test: locally with vnc and webrtc, check cpu consumption
```

- 57ccc6559afe00466316180e83d02fde51dc219c
```
Add Ethernet support to cuttlefish

Add a launcher flag (e.g. "launch_cvd -ethernet=true") which adds
another Ethernet interface to the virtual device. Ethernet devices are
already "buried" for use by wireless, but this new interface is not
wrapped and can be managed by netd. This functionality is useful for
Auto targets.

This mildly refactors some code in allocd to create separate Ethernet
and Wifi bridges and tap devices.

The code is off by default because it requires a new cuttlefish-common
package (0.9.17) and we haven't made the necessary changes to the
networkAttributes to allow Ethernet and Wifi to co-exist, so enabling
Ethernet currently breaks Wifi on phone configurations.

The created ethernet interface will be eth2 for now; once we can enable
this by default, we can move Ethernet back to eth0.
```

- 2b489cfd7aa441b139ce5687898ac1e9383d42da
```
Enable fastbootd on cuttlefish

This make the RecoveryUI "fastboot" mode actually work. Modify the
CuttlefishRecoveryUI to subclass EthernetRecoveryUI so we can connect
over TCP/IP.

Bug: 172693524
Test: adb reboot fastboot
Test: fastboot flash boot
```

- 0bc6916992af23f19dafe01c4c357880369e22fd
```
Install GKI APEX.

This enables GKI mainline updates.

Test: manual with adb install
```

- e0fdcdad17948695a681ec9f78623aec5431faf4
```
Move e2fsck to vendor ramdisk.

For Virtual A/B devices that has a vendor_ramdisk,
build e2fsck in vendor ramdisk instead.

Bug: 173425293
Test: m vendorbootimage bootimage
```

- f19d0fd66b0d37e742b3fd1d80fc4203d4c46ca6
```
Switch to bootcontrol 1.2

Test: mma
```

- 6bb6f7cd204b561a2b5dbd3f50368f8c13ebc65e
```
Record the screen using a VP8 encoder and a mkv container

Bug: 174612158
Test: launch_cvd --start_webrtc
```

- a0f52023bf9d9bd23730c8d238a31c8bf50b14a1
```
Improve stability of video recording files.

 - Keyframes are produced after a minimum timeout or number of frames.
 - Keyframe locations are advertised in the output webm file.
 - The first frame starts at timestamp 0 to prevent an empty start zone.

Timestamps are also passed through the webrtc frame types.

After this change, mpv, chrome, mplayer, and firefox are able to seek
reliably but vlc gets stuck on trying to jump to random spots in the
middle.

Bug: 174882609
Test: Play a 54 minute video in vlc, mplayer, mpv, chrome, firefox
```

- 37c5c35fee6e2a5322bf52c9d73794de3969bc32
```
Add pc cuttlefish

Add pc cuttlefish with aosp_cf_x86_64_pc-userdebug lunch.

Bug: 169864301
Test: lunch aosp_cf_x86_64_pc-userdebug
```

- aeb80a572318e806265b6e2a1038d6ecf716d64d
```
Add c2 codec performance to cuttlefish configs

cuttlefish recently changed from OMX to Codec2 configurations.
replicate the OMX.google.* codec performance to the corresponding
c2.android.* codec names.

Bug: 176872713
Test: cts will tell
```

- 9126ff6d16737ccd40e995c2dbfec770636da625
```
Enable VABC on cuttlefish.
```

- 2254616ff7c332f242150bf9f92910ca341205cc
```
cuttlefish: Always use the codec2 DMA-BUF heaps allocator

Currently Codec2 falls back to the libdmabufheap-based allocator when
ION allocator support is not detected. This patch allows cuttlefish to
always use the libdmabufheap based allocator even when running kernels
with ION support. Since libdmabufheap is backwards compatible with ION,
it will not affect cuttlefish builds running with older kernels(with no
DMA-BUF heap support).

Test: boot, video playback
```

- 4e00f72ee7cf4f37f9f109b96c0e2982565629a3
```
Restructuring the screen connector implementation for confirmation UI host service

In order to take frames concurrently from multiple screen connector sources such as
the guest Android and confirmation UI renderer, screen connector should be restructured.

Bug: 175492137
Test: Run cuttlefish with webRTC and VNC locally
```

- 5e84689918fb9eca8a2f603054df5d0a616f68a5
```
EFI System Partition support

$ truncate -s 256M esp.img
$ mkfs.fat esp.img
$ sudo mkdir esp
$ sudo mount -o loop esp.img esp
$ sudo mkdir -p esp/EFI/BOOT
$ sudo cp /usr/lib/grub/<efi>/monolithic/grub<efi>.efi \
    esp/EFI/BOOT/boot<efi>.efi
$ sudo umount esp.img
$ launch_cvd -esp esp.img -console=true -pause_in_bootloader

From u-boot shell:

> env default -f -a -
> boot

U-Boot will find the EFI bootloader and chain load it.

Bug: 175204891
Test: launch_cvd on x86 and arm64
```

- 9bdff8539fc52a3c798af0d5d76c118c2e337282
```
Allow userdata image size to be overridden

Some code coverage use cases require more space, so allow those users to
specify a larger userdata size for cuttlefish on the build command line.
```

- a043f9140290c6d5eb3f6d90657cac735e7240b9
```
Add c2.android.(hevc|vp9).encoder performance to cf configs

Bug: 178844416
Test: atest CtsVideoTestCases:VideoEncoderDecoderTest
```

- b5b742ce719f0cc336a4f2367dbcc897b6de3664
```
Enabling AVB on CuttleFish

With commit Id15a25403d16b36d528dc3b8998910807e801ad2, an
unlocked device still can boot even if 'androidboot.vbmeta.digest'
in /proc/cmdline is absent or incorrect. So it's fine to enable
AVB on CuttleFish now.

Note that two: fs_mgr flags `first_stage_mount` and `avb=boot`
are needed as well. Because /boot partition in CuttelFish is a
AVB chained partition. Those flags allows the first-stage init to
generate the symlink of /dev/block/by-name/boot_[a|b], to read
vbmeta of the /boot partition for vbmeta digest calculation.

Bug: 178983355
Test: `adb -s 0.0.0.0:6520 shell dmctl list devices`, and checks
      <partition>-verity dm devices are created.
Test: no selinux denial
```

- c822aa52d816e67636a9c43cd5661fd69a66a984
```
Parameterize GKI version for Cuttlefish

Bug: 181222920
Test: $ lunch aosp_cf_x86_64_phone-userdebug; m
      $ launch_cvd -daemon -resume=false -start_vnc_server=true
      $ adb shell uname -a
      Linux localhost 5.10.17-android12-0-00810-ge146d4c5bd36-ab7166491 #1 SMP PREEMPT Wed Feb 24 00:27:45 UTC 2021 x86_64
      $ stop_cvd
      $ m installclean; GKI_VER=5.4 m
      $ launch_cvd -daemon -resume=false -start_vnc_server=true
      $ adb shell uname -a
      Linux localhost 5.4.100-android12-0-00412-g7ba24942b70e-ab7166325 #1 SMP PREEMPT Tue Feb 23 19:23:22 UTC 2021 x86_64
      $ stop_cvd
      #
      # For vsoc_arm64
      $ lunch aosp_cf_arm64_phone-userdebug; m bootimage
      $ diff $OUT/kernel kernel/prebuilts/5.10/arm64/kernel-5.10; echo $?
      0
      $ GKI_VER=5.4 m bootimage
      $ diff $OUT/kernel kernel/prebuilts/5.4/arm64/kernel-5.4; $ echo $?
      0
      #
      # For vsoc_x86_only
      $ lunch aosp_cf_x86_only_phone-userdebug; m bootimage
      $ diff device/google/cuttlefish_prebuilts/kernel/5.10-i686/kernel-5.10 $OUT/kernel; echo $?
      0
      $ GKI_VER=5.4 m bootimage
      $ diff device/google/cuttlefish_prebuilts/kernel/5.4-i686/kernel-5.4 $OUT/kernel; echo $?
      0
```

- 52aecc2a97458dc96ef49e52f57ae4bd096d6619
```
Reland: Support multi-display in ScreenView and ScreenConnector

Please compare against patchset 1 to see diffs versus the
original aosp/1518579.

The original aosp/1518579 mistakenly disabled the optimization
from aosp/1490174 which stops hwcomposer from sending frames
to the host if there is no vnc/webrtc clients connected. This
reland change adds that back in BroadcastLoop().

Also replaces the present buffer mutex and uint32_t with
atomic<uint32_t>.

Note: this does not enable multi-display yet!

Updates the ScreenConnector interface callback to have a
display number indicating which display the latest frame
is from and updates streaming servers to ignore frames
that are not from the first display.

Adds a helper class to the socket-based ScreenView and
ScreenConnector which encapsulates the existing behavior
of acquiring a buffer for the next frame and marking the
buffer as ready to be sent to host (ScreenView) or to the
streamer (ScreenConnector). Then, create one of these
helpers per display and update the send protocol from
<frame size><frame bytes> to
<display number><frame size><frame bytes>.

Removes the "on frame number after" semantics from the
ScreenConnector interface. The "consume" on ScreenConnector
will handle the case if the streamer registers to receive
a frame after it has already arrived.

Bug: b/173523482
Test: launch_cvd --start_webrtc=true --gpu_mode=guest_swiftshader
Test: launch_cvd --start_vnc_server=true --gpu_mode=guest_swiftshader
Test: launch_cvd --start_webrtc=true --gpu_mode=gfxstream
Test: launch_cvd --start_vnc_server=true --gpu_mode=gfxstream
Test: treehugger avd/avd_boot_test_cloud_tf (x 15 times)
```

- 9333c74ce68c4326d64fa58a5a01b5572b08535b
```
Add virt apex to cuttlefish 64-bit phones

Bug: 182300699
Test: local builds of 64 and 32 bit cuttlefish
```

- 4984f523db85e6c4c8dd682eb77d6b0ca8b13703
```
Bind mount /boot/efi

Fixes a package configuration issue with grub-cloud-amd64. When
installing both amd64 and ia32 binaries, the post-install runs twice,
and only the first run seems to tolerate the efi partition not being
mounted. Make sure it is mounted for both post-installs.
```

- 330d22102177b2185ab880fc100ef8352cd9442d
```
Move Cuttlefish onto Ranchu hwcomposer

... which is shared with Goldfish and uses DRM to submit frames
instead of vsock.

Bug: b/171305898
Test: m && launch_cvd --gpu_mode=guest_swiftshader
Test: cts -m CtsCameraTestCases
Test: cts -m CtsGraphicsTestCases
Test: vts -m VtsHalGraphicsComposerV2_1TargetTest
Test: vts -m VtsHalGraphicsComposerV2_2TargetTest
```

- d87eb35cb8d40fb4d912e1ffba280a083ec7dbb3
```
Initial version of Cuttlefish webrtc frontend v2.1.

Includes the following updates:
- App controls (keyboard capture, mic, audio) at the top header.
- Status message also in the top header.
- Control panel buttons and device screen below the header.
- Control panel buttons vertical, custom buttons in blue box.
- Device Details button above standard control buttons,
  clicking it shows the device details modal window.

Bug: 184577800
Test: Interact with every element in the UI,
      observe behavior consistent with mocks.
```

- 6215ea9e812772dfe3886d5b318ba8d1c8166739
```
mk_cdisk: a new tool to make a composite disk

assemble_cvd can be configured to make a custom composite disk, but it
would make it too complicated regarding the purpose of the tool, which
is to configure Cuttlefish cvd.

mk_cdisk is a lightweight tool to make a composite disk file out of
image files.

  Usage: mk_cdisk config.json cdisk.img
   or    mk_cdisk - cdisk.img   (read config from STDIN)

Bug: 181093750
Test: using mk_cdisk to launch microdroid vm
```

- d3b49af78bf4628480211c81e4e49c452929bed2
```
Introduce a new instance-specific composite disk

When we originally designed the composite disk, it was our intention
that only the disk overlay.img would be instance-specific. The
composite.img would refer to files that were effectively read-only and
usable by all multi-tenant instances. This would minimize disk space
requirements and launch times. The composite disk, and the images it
referred to, could be shared by all instances.

However, we quickly broke this when "magic state" that was being passed
through crosvm's kernel cmdline feature stopped working, after landing
the Cuttlefish Bootloader. We had to repack at least one partition on
every launch, per instance, and that meant we needed a per-instance
composite.img. For crosvm this has a small cost, because as long as the
instance-specific files are put into instance-specific locations, only
the composite.img meta file has to be rewritten. But for QEMU, it's very
expensive. It's also not a very high fidelity solution, and doesn't
match how real device clusters would be provisioned.

The u-boot environment problem was later fixed to push the changing
state into vendor_boot.img cmdline or bootconfig, but this still
involves composite disk modifications, and has all the same problems
above.

This change adds another hard disk which is explicitly for "persistent"
instance-specific data. This hard disk will not be overlayed, because
the partitions in the disk are not shared between instances, and writes
are expected to persist powerwashing, factory reset, OTA, etc. This disk
houses the recently added FRP block, and the U-Boot environment, but
later we will add a 'factory partition' to replace the config_server.

This change does not fully remove the need for a per-instance composite
disk, because we still need to repack the vendor_boot images -- but once
we can pass an instance ID or other properties through the U-Boot env
or factory partition, we can move composite.img back to the assembler
step.
```

- ff9459ea35d5ec69e68cba8ab14e926192399bba
```
Support auto-generation of ESP, add otheros_root

The existing code allowed a user to inject an ESP, but building a good
one is quite onerous, as you need to find the right GRUB binaries, put
them in the right place, make a grub.cfg to chain load, and so on.
It's better in most cases if assemble_cvd takes care of this process.

This change uses the new mtools binary to modify a FAT32 partition
without requiring root, and pulls GRUB from the host machine. A suitable
grub.cfg is injected into the ESP as well. GRUB is taken from the host
because it is difficult to build, and we can't add it to AOSP right now.

This new partition can boot a kernel / initramfs.img directly from a
static location in the ESP ("/vmlinuz" and "/initrd.img"), or it can
search for another grub installation and chain-load it.

This change removes the old -use_esp flag, and an ESP will be generated
when needed by the otheros flags. Three new flags are added:

  -otheros_kernel_path     The path to a raw vmlinuz
  -otheros_initramfs_path  The path to an initramfs.img
  -otheros_root_image      The path to a root filesystem image.

If the root filesystem is not specified, the other options have no
effect.
```

- 4757eeabb44196a0989e8234db84225b100e5a88
```
Change recovery serial console to a root shell

It is already running as the ROOT uid, however it is running in the
"u:r:shell:s0" secontext, which renders it as useful as a regular shell.

Change the secontext to "u:r:su:s0" so that it behaves more like a root
shell like "adb root && adb shell".

Test: $ launch_cvd -daemon -start_webrtc -console
  $ adb reboot recovery
  $ minicom -p $(realpath ~/cuttlefish_runtime.1/console)
    > id
    uid=0(root) gid=0(root)
    groups=0(root),1007(log),2000(shell),3009(readproc)
    context=u:r:su:s0
```

- 88f7f96f86ca3b0d2df664f550638841f2bb1368
```
Avoid excessive memory allocation in CvdVideoFrameBuffer

webRTC is spending a lot of time in
cuttlefish::CvdVideoFrameBuffer::CvdVideoFrameBuffer
according to 'perf top -p `pidof webRTC` -K'.
The constuctor allocates memory for std::vectors that
hold YUV data.
Add a simple memory pool that recycles vectors and thus
avoids excessive memory allocation.
Example CPU utilization without pool:
12.62%  webRTC [.] cuttlefish::CvdVideoFrameBuffer::CvdVid...
 7.69%  webRTC [.] vp8_denoiser_filter_sse2
Example CPU utilization with pool:
 8.81%  webRTC [.] vp8_denoiser_filter_sse2
 5.74%  webRTC [.] vp8_copy_mem8x8_mmx

Test: Play youtube videos
```

- e32156f22693ebad8951931cf4d8d4d719d5fbea
```
image_aggregator: support multiple image partition

Added a new overload for CreateCompositeDisk(), to support the case
where a partition can be composed of more than one images.

Bug: 187783952
Test: MicrodroidHostTestCases
  mk_payload uses a new function to create a partition with
  both a target file and its data file (custom)
```

- b0fdc874da72f4c9056d46745188db5b3e3d3b09
```
Add the kernel log monitor as a CommandSource
```

- 5ff563d139cf38f1072119fcdaa3b20a15bfc235
```
Add ADB features that depend on the kernel log feature
```

- 87b749fa9dab8c3203642b3aba42f7fce0920882
```
Support connecting to guest with no ADB server

Fix a runtime assert in webrtc that occurs if the graphics pipeline is
successfully initialized, but the adb proxy has not started (because the
guest did not start adbd). This can happen if the guest is not Android
and is another OS.
```

- e20109032006093dbfee160d8fa09260d641a5b2
```
Add bootloader build for go phones

When bootloader configuration makefile is splitted, the makefile is not
included in the go phones, so go phone cf is unable to boot because of
missing bootloader. This change adds bootloader build in go phones so go
phone cuttlefish can boot properly.

Bug: 186696728
Test: aosp_cf_x86_go_phone boot succeeded
```

- 2da9f1985d8578f5a6fd87b75cbd2bf97b4eb1f7
```
Create an ADB ConfigFragment.

Test: launch_cvd
```

- ca5b330a40a469addefe89dcd8f044f2e5c74154
```
Update WebRTC UI to support multiple displays

Cherry-picks aosp/1738433

Note: this does not yet enable the WebRTC server to support
multiple streams.

Bug: b/171305898
Bug: b/193657706
Test: launch Cuttlefish w/ single displays
Test: launch Cuttlefish w/ 'cuttlefish-multi-display' topic
```

- db07d0a586ab5ba0e59d8fe58653192e3efb0f3b
```
Update WebRTC server to support multiple displays

... by configuring one VideoSink per display.

Cherry-picks aosp/1764651

Bug: b/171305898
Bug: b/193657706
Test: launch Cuttlefish w/ single display
Test: launch Cuttlefish w/ multiple displays
```

- 49aa7de09a9a9f5bb528a374674f7065d4610664
```
Update ScreenConnector interface to set callback once

... instead of on a per frame basis. With multi-display,
ScreenConnector would frequently miss and drop frames from not
setting the callback in time.

Note that this slightly changes the behavior of Crosvm and
the VNC streaming server (*not WebRTC*) when there is no client
connection.

- Prior to this change, Crosvm's Wayland display would continuously
send frames to Cuttlefish's Wayland server (which runs inside of
the VNC streaming server). When no clients are connected,
the ScreenConnectorQueue becomes full after 2 frames and the
AndroidFrameFetchingLoop() thread becomes blocked waiting for the
ScreenConnectorQueue to not be full. The AndroidFrameFetchingLoop()
would not re-configure the OnNextFrame() callback and the excess
frames would be dropped in Cuttlefish's Wayland server.

- After this change, Crosvm's Wayland display will not continuously
send frames to Cuttlefish's Wayland server (which runs inside of
the VNC streaming server). When no clients are connected,
the ScreenConnectorQueue becomes full after 2 frames and the
*Wayland server's thread* becomes blocked waiting for the
ScreenConnectorQueue to not be full while trying to run the
process frame callback. This is still okay as Crosvm's
Wayland display sees that Cuttlefish's Wayland server has not
finished using any buffers and drops the next frame.

This doesn't change what VNC clients see but wanted to call it
out. Also note that VNC clients see very old frames when a user
finally connects both before and after this change - this is
problematic as a VNC client will see a boot animation frame if
they connect to VNC after the device has reached a steady state
and isn't producing new frames.

This doesn't change behavior when running with WebRTC as the WebRTC
server continues to process frames even when a client is not
connected.

Cherry-picks aosp/1690948

Bug: b/186633303
Bug: b/193657706
Test: launch Cuttlefish and start webrtc
Test: launch Cuttlefish and start vnc
```

- a19a6c90ac9261048a249a18d0c42dc7c7ca7251
```
WebRTC camera and vsock camera HAL

Add a camera that transfers client camera stream over webRTC
and vsock to the CVD.
Camera stream originated from the client getUserMedia() is
received by CameraStreamer that acts as a sink for webRTC video
track. CameraStreamer then forwards video frames in YUV420 format
to the CVD by using vsock connection.
Device control channel is used for transferring settings data (i.e.
frame rate and resolution) in Json format to the CVD.
Also an additional webRTC data channel is used for transferring
binary blobs captured by ImageCapture.takePhoto() to the CVD.
Custom camera HAL is loosely based on ExternalCamera implementation.
The HAL supports only single resolution blob and yuv streams. The
used resolution is the default coming from getUserMedia().

Example usage:
TARGET_USE_VSOCK_CAMERA_HAL_IMPL=true m -j32
launch_cvd -start_webrtc=true -camera_server_port=7600

Notes:
- If the client connection is not established during the boot,
  the camera app does not show on home screen. This is because
  CameraProvider only indicates camera presence after vsock
  connection.
- Video does not work if the camera resolution does not match the
  required video resolution. There is no scaling. For example by
  default, the camera app requires 1280x720 and messaging app
  requires 320x240 resolution.
- Image blobs from ImageCapture.takePhoto() seem to be in PNG
  format. This might confuse some HAL clients (parsing EXIF data
  fails etc.) Default gallery and camera app seem to show images
  fine though.

Test: manual
      run cts-camera (passed 1668/1732)
```

- 30e9ce1c84a89abed6ff3787f2f557478cff4c13
```
Add hostapd

The hostapd is added for wifi hotspot. And modify the parameter that
eth1 for wifi is not created when AP is shared with other instances
using openWRT.

Test: m PRODUCT_ENFORCE_MAC80211_HWSIM=true &&
      && lunch_cvd --vhost-user-mac80211-hwsim=[vhost_server] \
      --ap_rootfs_image=[rootfs_image] \
      --ap_kernel_image=[kernel_image] \
      && lunch_cvd --num_instances=2 \
      --vhost-user-mac80211-hwsim=[vhost_server] \
      && check wifi connection for wifi hotspot
```

- 9c7f00a4a7cd78b1074bb2e341f6ec4ee3c922a3
```
Enable ethernet by default, remove flag

The Ethernet feature was always intended to be like Mobile Data and
landed unconditionally, but it depended on the new cuttlefish-common
Ethernet bridge (cvd-ebr) which was blocked for a long time. Now we have
rolled this change our everywhere, remove the conditional nature of this
feature.

This also fixes various recently introduced bugs in the QEMU backend and
setup_wifi as part of the new wifi work.

The Ethernet device is shuffled after the mobile data interface and
before the wifi interface, because the wifi interface will soon not be
an Ethernet device (even optionally) which would otherwise leave a gap
and require more renaming.

Bug: 172286896
[adelva: vs aosp master, manually renamed mobile data to eth0]
```

- 14bdda18a2019c0e3aaae212caab02d9e2886966
```
Prototype implementation for Cuttlefish Confirmation UI HAL

Confirmation UI guest implementation has been added. When the
three guest HAL APIs are called, cuttlefish is now able to
somehow handle the invocation.

Test: Running locally with VTS test
```

- ada4aa52a85774c45c9367c060526af0321a5f3a
```
Adds (initially hidden) toggle in webrtc UI to record displays

... for a simple method for sharing info for bug reports.

Toggle the new video icon to start recording, the video icon turns
red to indicate recording like a video camera, and toggle again to
stop recording and start a webm download with a timestamp.

Bug: b/201099951
Test: create recording in webrtc ui
```

- 194fb9398eb91ecaaa3058c6bc2e6c6d468823b3
```
Make nvidia driver installation noninteractive

Host image creation hangs on keyboard input. This env var makes the
driver install noninteractive.

Also fixed an error in ToT image creation. There were some extraneous
command values that err'd out the final google cloud instance create.

Re-added capability to call create_base_image_hostlib so that there's
a way to check the new go script for functional differences.

Bug: 198311279
Test: cf host image creation
```

- c94bb219de5c3476defb97e5e2cb92b49068dd38
```
Move Cuttlefish shared and phone RROs to RRO modules.

This will allow these RROs to be included within an APEX.

Bug: 199200417
Test: SettingsProvider RRO: ensure autorotation still on by default
Test: core power_profile RRO: adb shell dumpsys batterystats |
                              grep capacity; # ensure same capacity
Test: core config RRO: ensure multi-user settings still set
```

- 7fe4396e07718c1a9d6fbb952f5f4322a15835fd
```
Uses a vendor APEX for cuttlefish runtime resource overlays.

Bug: 199200417
Test: Observe all RROs still enabled by default.
Test: adb shell pm create-user <name> 3 times for a total of 4 users.
      adb shell pm create-user <name> one more time fails, due to
      max user count reached.
      Change config_multiuserMaximumUsers from 4 to 6.
      m <RRO apex>; adb install <RRO apex>; adb reboot;
      adb shell pm create-user <name> 2 more times for a new total
      of 6 users.
```

- 9021c1c22d05837f81696fd595e432666067733b
```
Relands AVB on CuttleFish

This reverts commit b8fb0e24eea9d117f9e9e7031e9be7bea3183071.

Also need to specify avb_keys=/avb, so we can load vbmeta from
the end of a system.img when mixing with a GSI system.img.

Bug: 178983355
Bug: 180882685
Test: Mixed with GSI system.img and checks that dm-verity is
      enabled.
Test: acloud-dev create \
        --local-instance \
        --local-image \
        --local-system-image \
        ./out/target/product/generic_x86_64 \
        --skip-pre-run-check
```

- 9dba51e13a40b3b0eebbf3ec1ba7492b22452674
```
Remove cuttlefish VNC host service

Cuttlefish is to deprecate its VNC server. Only when Qemu is
used as the virtual machine manager, Qemu would set up its own
VNC server that could be reached from a VNC client. Otherwise,
cuttlefish offers as of this commit WebRTC connections only.

Bug: 183429124
Test: launch_cvd with crosvm and qemu
```

- 8b3ec47a2d6bea00fb923985cfdb3950683473ef
```
Disable CMA reservation on ARM/ARM64

Cuttlefish doesn't use CMA, so there's no point wasting 16MB RAM on it.
```

- f3fa7602b6e10b6839ff9cb4492256406653107f
```
Default OpenWrt image

1. adding default openwrt image and kernel
2. openwrt image is embeded in composited image, and launch openwrt with
comosited image and its overlay image.

Test: 1. m PRODUCT_ENFORCE_MAC80211_HWSIM=true && launch_cvd and then check
 if it works.
 2. Test with resume flag, setting for openwrt is preserved or cleaned
 by the flag.
```

- a02075771a635f80fbec31b3ef40b86d4853c49b
```
Demonstrate multi-installed APEXes.

1. Launch device:
     launch_cvd
2. Test passpoint feature provided:
     atest android.net.wifi.cts.WifiManagerTest#testPasspointCapability
     # Test passes
3. Change default wifi HAL vendor APEX:
     adb root;
     adb shell setprop \
       persist.vendor.apex.com.android.wifi.hal \
       com.google.cf.wifi.no-passpoint;
     adb reboot;
4. Test passpoint feature gone:
     atest android.net.wifi.cts.WifiManagerTest#testPasspointCapability
     # Test fails
5. Change default wifi HAL vendor APEX back:
     adb root;
     adb shell setprop \
       persist.vendor.apex.com.android.wifi.hal \
       com.google.cf.wifi;
     adb reboot;
6. Test passpoint feature provided:
     atest android.net.wifi.cts.WifiManagerTest#testPasspointCapability
     # Test passes
7. Stop CVD, relaunch with no-passpoint wifi HAL vendor APEX:
     stop_cvd;
     launch_cvd --noresume \
       --extra_bootconfig_args \
       "androidboot.vendor.apex.com.android.wifi.hal:=\
        com.google.cf.wifi.no-passpoint";
8. Test passpoint feature gone:
     atest android.net.wifi.cts.WifiManagerTest#testPasspointCapability
     # Test fails
```

- 430bc5c4b5e4f94a21892bda2b54629a43c2638f
```
Instance directory restructuring

This creates a new directory structure that looks like

 - cuttlefish_assembly -> symlink to cuttlefish/assembly
 - cuttlefish
   - assembly
   - instances
     - cvd-1
       - internal
       - logs
       - recording
       - shared
       - tombstones
     - cvd-2
       - internal
       - logs
       - recording
       - shared
       - tombstones
 - cuttlefish_runtime -> symlink to cuttlefish/instances/cvd-1
 - cuttlefish_runtime.1 -> symlink to cuttlefish/instances/cvd-1
 - cuttlefish_runtime.2 -> symlink to cuttlefish/instances/cvd-2

This replaces the previous directory structure which looked like

 - cuttlefish_assembly
 - cuttlefish_runtime.1
   - internal
   - logs
   - recording
   - shared
   - tombstones
 - cuttlefish_runtime.2
   - internal
   - logs
   - recording
   - shared
   - tombstones
 - cuttlefish_runtime -> symlink to cuttlefish_runtime.1

Bug: 209705054
Test: launch_cvd --noresume
Test: launch_cvd --noresume --num_instances=3
Test: ls -l $HOME, look at symlinks
```

- e5b3c92b5bc068635ae4cefc5d39383ed14a2200
```
cuttlefish: allow choosing of hwcomposer version

With mutliple virtualized composers out there (drm_minigbm,
ranchu, wl_hwcomposer), it makes sense to have a choice.

BUG=208911569
TEST=launch_cvd
```

- 6c2a3c915ee621bd22e8f70bb6eec0636e81f82d
```
Add new init_boot partition to Cuttlefish

Enables it in the build and adds the partition to be availble to the
bootloader.

This works without bootloader changes because first stage init is
also in the vendor_boot.img for recovery mode.
BOARD_MOVE_RECOVERY_RESOURCES_TO_VENDOR_BOOT is true for Cuttlefish.
The bootloader loads the vendor_boot ramdisk into memory during normal
boot as well as recovery. b/209675829 is created to prevent this
behavior by placing the recovery image into a vendor_boot ramdisk
fragment so the bootloader can choose not to load the recovery image on
a normal boot.

Test: boot with updated bootloader and boot.img without ramdisk
Test: boot with current bootloader and boot.img without ramdisk
```

- 3c07e2c7e8abb8f5f986f8594439ecda229377ea
```
Get AOSP TV targets working again

This ports the ATV targets to using the ATV "generic_system" which is
basically the same as the mainline/generic conversion we did for phone
some time ago. Refactor all the makefiles to look like the phone ones.

Dropped the TV-specific manifest.xml for now because all sections are
commented out.
```

- 06880f109497e68c8a64c3fe517d9959be65c66d
```
Add support for /system_dlkm partition for GKI modules.

Kernel generated system_dlkm.img gets mounted as read-only
erofs filesystem image at /system_dlkm which consists all
signed modules from GKI build.

Bug: 200082547
Test: Manual boot of CF with and without system_dlkm flag
Test: atest vts_system_dlkm_partition_test
```

- 99a7dc64b244b6299537a3ea21b97f80aab774aa
```
Disable ADB security and spawn by default

Allow 'user' builds to be accessed with ADB even if the user has not
gone into Developer Options to enable USB debugging. ADB is still
limited to non-root on such builds.
```

- 7c06a953bc888e352eb746c6ddf1a5b6ffaea5db
```
CF: system_dlkm: Add dynamic partiton
```

- 30a63b63e4359e2c17767f74b44c28cc82d88ea6
```
Set hypervisor boot properties

VirtualizationService will check for hypervisor capabilities through
these system properties. Cuttlefish supports traditional VMs but not
protected VMs.
```

- a695c5b5ffc44e04d4b7414c9aaec01cc7a02b79
```
Add init_boot as an OTA partition for Cuttlefish

init_boot needs to be updated in OTA as well.

Test: "m dist" then drop the OTA zip file into
android.github.io/analyseOTA to verify init_boot is present.
```

- 17e7ba3a4dce934d3b97a69fe7fe7d1def285d94
```
system_dlkm: allow insmod from /system_dlkm

Files under /system_dlkm are of system_dlkm_file;
so need to add allow rule for insmod to work for
this file types.
```

- f8c41f40a7b142401a30408552c09f6272b34e30
```
Demonstrates multi-installed APEXes with a new camera HAL APEX.

This is easier to visually verify than the existing example of wifi HAL.
The demo version cycles through day/night at a much faster rate.

Bug: 221863312
Test: launch_cvd; observe normal camera scene behavior; stop_cvd;
Test: launch_cvd with --extra_bootconfig_args to choose the
        fastscenecycle APEX; observe fast scene changes;
	stop_cvd;
Test: launch_cvd; observe normal behavior; setprop persist prop
        to choose the fastscenecycle APEX; reboot (not stop_cvd);
	observe fast scene changes; stop_cvd;
```

- 16bc6c48a04e01ecdf3c45206d9da733fdbb68f1
```
Switch cuttlefish to using T .mk files

This will move snapuserd from vendor boot to init_boot.
```
--------
End of hash: 0cfd0f5bffeaadf6a0029b5b9c0e4a54d659ea7c

- b34828cd72a3e7b5d7c7cbac758349fa3836dba4
```

```

- b34828cd72a3e7b5d7c7cbac758349fa3836dba4
```

```

- b34828cd72a3e7b5d7c7cbac758349fa3836dba4
```

```