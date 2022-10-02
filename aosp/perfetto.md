- [Android命令行参考这里]([perfetto](https://developer.android.com/studio/command-line/perfetto))
- [使用文档参考这里](https://perfetto.dev/docs/quickstart/trace-analysis)
- [网页查看trace的工具](https://ui.perfetto.dev/)
- [ATrace的分类列表参见这里](https://android.googlesource.com/platform/frameworks/native/+/refs/tags/android-q-preview-5/cmds/atrace/atrace.cpp#100)
## 命令行使用
`perfetto`可以使用两种模式来收集trace：
- 轻量模式(`light mode`): 通过指定`atrace`和`ftrace`中的一些分类来指定收集的来源
- 正常模式(`normal mode`): 通过配置文件来指定收集来源

简单示例：
```bash
adb shell perfetto -o /sdcard/test.trace -t 20s sched freq idle am wm gfx view binder_driver hal dalvik
```