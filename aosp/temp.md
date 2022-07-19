`Enable codec2 hidl in manifest.xml`: ./device/generic/common/manifest.xml

manifest.xml only has entry point of hidl service, no impl.


frameworks/base/core/java/android/app/LoadedApk.java
 873         // Add by AshyEarl
 874         libraryPermittedPath += File.pathSeparator + "/system/lib64/";
 875         libraryPermittedPath += File.pathSeparator + "/vendor/lib64/";
 876         libraryPermittedPath += File.pathSeparator + "/system/lib64/dri/";

https://elinux.org/images/d/d6/Android_common_kernel_and_out_of_tree_patchset.pdf

https://ci.android.com/builds/submitted/8626023/linux/latest/view/soong_build.html