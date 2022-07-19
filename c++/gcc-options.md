GCC编译Option可以参考[GCC option](https://gcc.gnu.org/onlinedocs/gcc/Option-Index.html#Option-Index)和[Clang flags](https://clang.llvm.org/docs/DiagnosticsReference.html)，搜索具体的说明

GCC Options
----

| 字段                         | 说明                                                       |   参考链接 |
| ---------------------------- | ---------------------------------------------------------- | ---------: |
| -Dxxx                        | 添加宏定义, -D name=definition,相当于 #define xxx          |  [这里][1] |
| -Ox                          | 编译优化：-O0不优化，-O1低级优化，-O2中级优化，-O3高级优化 |  [这里][2] |
| -Wall                        | 编译期间显示所有的警告                                     |  [这里][3] |
| -Werror                      | 将所有警告作为错误，停止编译                               |  [这里][4] |
| -Wunused                     | 未使用的变量、方法等的警告，包含下面几个                   |  [这里][5] |
| -Wunused-but-set-parameter   | 方法内的参数赋值了，但未使用                               |  [这里][6] |
| -Wunused-function            | 方法未使用                                                 |  [这里][7] |
| -Wunused-but-set-variable    | 局部变量声明了但未使用                                     |  [这里][8] |
| -Wunused-const-variable      | 静态常量声明了但未使用                                     |  [这里][9] |
| -Wunused-label               | goto的标签定义了未使用                                     | [这里][10] |
| -Wunused-local-typedefs      | 方法内的typedef定义了未使用                                | [这里][11] |
| -Wunused-macros              | 宏定义定义了未使用                                         | [这里][12] |
| -Wunused-parameter           | 方法的参数未使用                                           | [这里][13] |
| -Wunused-result              | 方法的返回值未使用                                         | [这里][14] |
| -Wunused-value               | 表达式结果未使用，例如：x[i,j]                             | [这里][15] |
| -Wunused-variable            | 变量未使用                                                 | [这里][16] |
| -Wunknown-pragmas            | 未知的#pragma                                              | [这里][20] |
| -Wmissing-braces             | 数组或者union初始化括号不完整                              | [这里][21] |
| -Wmissing-declarations       | 全局方法未声明                                             | [这里][22] |
| -Wmissing-field-initializers | 结构体部分变量未初始化（不包括 struct s x = { .f = 3 };）  | [这里][23] |
| -Wlogical-not-parentheses    | 位操作没有用括号                                           | [这里][26] |
| -msse -msse2 -msse3 -mssse3  | 激活x86指定指令                                            | [这里][27] |
| -msse4 -msse4.1 -msse4.2     | 同上                                                       |            |
| -finline-functions           | 所有方法尽可能inline                                       | [这里][28] |
| -fshort-enums                | 尽可能少的为enum分配内存                                   | [这里][29] |
| -Wa                          | 给assembler传递option: `-Wa,option`                        | [这里][30] |


Clang options
----

| 字段                     | 说明                                           |   参考链接 |
| ------------------------ | ---------------------------------------------- | ---------: |
| -Wpragma-pack            | 结构体内存布局相关                             | [这里][17] |
| -Wimplicit-fallthrough   | switch未break,可以加 `[[clang::fallthrough]];` | [这里][18] |
| -Wparentheses-equality   | 使用了多余的括号进行比较运算                   | [这里][19] |
| -Wlogical-op-parentheses | '\|\|', '&&' 混用，优先级不明确                | [这里][24] |
| -Wparentheses            | 使用表达式的结果直接作为if判断条件，未加括号   | [这里][25] |


[1]: https://gcc.gnu.org/onlinedocs/gcc/Preprocessor-Options.html#index-D-1
[2]: https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html#index-O3
[3]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wall
[4]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Werror
[5]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunused
[6]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunused-but-set-parameter
[7]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunused-function
[8]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunused-but-set-variable
[9]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunused-const-variable
[10]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunused-label
[11]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunused-local-typedefs
[12]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunused-macros
[13]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunused-parameter
[14]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunused-result
[15]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunused-value
[16]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunused-variable
[17]: https://clang.llvm.org/docs/DiagnosticsReference.html#wpragma-pack-suspicious-include
[18]: https://clang.llvm.org/docs/DiagnosticsReference.html#wimplicit-fallthrough
[19]: https://clang.llvm.org/docs/DiagnosticsReference.html#wparentheses-equality
[20]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wunknown-pragmas
[21]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wmissing-attributes
[22]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wmissing-declarations
[23]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wmissing-field-initializers
[24]: https://clang.llvm.org/docs/DiagnosticsReference.html#wlogical-op-parentheses
[25]: https://clang.llvm.org/docs/DiagnosticsReference.html#wparentheses
[26]: https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#index-Wlogical-not-parentheses
[27]: https://gcc.gnu.org/onlinedocs/gcc/x86-Options.html#index-msse
[28]: https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html#index-finline-functions
[29]: https://gcc.gnu.org/onlinedocs/gcc/Code-Gen-Options.html#index-fshort-enums
[30]: https://gcc.gnu.org/onlinedocs/gcc/Assembler-Options.html#index-Wa