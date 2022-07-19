

- 应用Android编译环境
  ```bash
  source build/envsetup.sh
  ```
  > 这个操作会把`build/envsetup.sh`中所有的shell方法都公开到命令行

- `hmm`: 可以用于查看编译系统的帮助，下面有一些常见的命令：
  - `cgrep`: 在本地 `C/C++` 文件中搜索
  - `ggrep`: 在本地 `gradle` 文件中搜索
  - `jgrep`: 在本地 `java` 文件中搜索
  - `rsgrep`: 在本地 `Rust` 文件中搜索
  - `sepgrep`: 在本地 `sepolicy` 文件中搜索
  - `godir`: 跳转到包含指定文件的目录
  - `allmod`: 列出所有模块
  - `pathmod`: 获取指定模块的路径
  - `gomod`: 跳转到指定的模块所在目录
  - `outmod`: 获取指定模块的输出目录
  - `refreshmod`: 刷新 `allmod/gomod/pathmod/outmod/installmod` 关联的模块
  
- `AndroidProducts.mk`
   
   这个文件用于设置`PRODUCT_MAKEFILES`的值，它用于将设备的makefile暴露给编译系统。
    > 编译系统在查找引入它时，会自动设置`LOCAL_DIR`的值，不要使用条件判断或者其他的编译变量
- `PRODUCT_MAKEFILES`：
    ```MakeFile
    // 格式为：
    <product_name>:<path_to_the_product_makefile>
    // 例如：
    PRODUCT_MAKEFILES := \
       my_device: ${LOCAL_DIR}/my_device.mk
    
    // product_name 和 makefile同目录并且名称为 <product_name>.mk 时可省略前面的 <product_name>:, 例如上面的可以写成：
    PRODUCT_MAKEFILES := \
       ${LOCAL_DIR}/my_device.mk
    ```
- `COMMON_LUNCH_CHOICES`
   
   用于将 `PRODUCT_MAKEFILES` 的`product_name`公开给`lunch`命令，当然可以lunch未公开到COMMON_LUNCH_CHOICES里的设备

- `PRODUCT_COPY_FILES`
   
   用于复制文件，格式为
   ```bash
   PRODUCT_COPY_FILES += <source_file>:<dest_file>
   
   <source_file> 为AOSP源码根目录
   <dest_file> 为out目录下设备路径的子路径，例如： out/target/product/x86/

   // 例如
   PRODUCT_COPY_FILES += system/core/rootdir/init.zygote64_32.rc:system/etc/init/hw/init.zygote64_32.rc
   是把 `~/aosp/system/core/rootdir/init.zygote64_32.rc` 复制到 `~/aosp/out/target/product/x86/system/etc/init/hw/init.zygote64_32.rc`

   ```