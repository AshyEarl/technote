适配到Android平台过程简单记录，参考： https://github.com/intel/media-driver

1. 在linux主机上安装`libva`开发包
    ```bash
    sudo apt install libva-dev
    ```
2. 从github clone `media-driver`, 并保证下载后的目录名称为`media-driver`,内部的编译写死了目录名称：
    ```bash
    git clone https://github.com/intel/media-driver.git
    ```
3. 在`media-driver`同级目录clone `GmmLib`，保证下载后的名称为`gmmlib`:
    ```bash
    git clone https://github.com/intel/gmmlib.git
    ```
4. 在`media-driver`同级目录创建目录`build_media`,完事目录结构如下：
    ```bash
    <root>
       |- media-driver
       |- build_media
       |- gmmlib
    ```
5. 进入`build_media`目录用cmake生成MakeFile文件，用于后续自动生成`Android.mk`
    ```bash
    cd <root>/build_media
    cmake ../media-driver

    // 期间遇到缺少igdgmm的问题，可以快速安装解决
    sudo apt install libigdgmm-dev
    ```
6. 使用自带的python工具生成`Android.mk`
    ```bash
    <root>/media-driver/Tools/MediaDriverTools/Android/GenerateAndroidMk.py
    
    // 正常会输出下面的log
    root = xxx
    generated gmm to <root>/gmmlib
    generated cmrt to <root>/media-driver/cmrtlib
    generated media_driver to <root>/media-driver/media_driver
    ```
7. 放入到AOSP目录下，进行编译，首先编译`gmmlib`：
    ```bash
    cd <AOSP-root>
    source build/envsetup.sh
    lunch xx

    cd hardware/intel/xxx/gmmlib
    mm

    ```
8. 编译出现的问题解决：

    编译时报错类似`error: unused parameter 'pTexInfo' [-Werror,-Wunused-parameter]`，需要参考`media-driver/Tools/MediaDriverTools/Android/build/gmm/Source/GmmLib/CMakeFiles/igfx_gmmumd_dll.dir/flags.make`添加必要的CXX flag(这些option的说明可以参见[这里](../c++/gcc-options.md)), 如下：
    
    ```bash 
    MY_IGNORE_WARNNING = \
      -Wno-reorder \
      -Wno-unused \
      -Wno-unused-parameter \
      -Wno-unused-variable \
      -Wno-unknown-pragmas \
      -Wno-missing-field-initializers \
      -Wno-missing-braces \
      -Wno-pragma-pack-suspicious-include \
      -Wno-implicit-fallthrough \
      -Wno-parentheses \
      -Wno-parentheses-equality \
      -Wno-logical-op-parentheses \
      -Wno-logical-not-parentheses \
    
    MY_OPTS = \
      -mpopcnt -msse -msse2 -msse3 -mssse3 -msse4 -msse4.1 -msse4.2 \
      -mfpmath=sse \
      -DUSE_MMX -DUSE_SSE -DUSE_SSE2 -DUSE_SSE3 -DUSE_SSSE3 \
      -finline-functions \
      -fno-short-enums \
      -fno-strict-aliasing \
      -Wa,--noexecstack \

    LOCAL_CFLAGS = \
      ${MY_IGNORE_WARNNING} \
      ${MY_OPTS} \
      -fexceptions \
      \
      others...
    ```