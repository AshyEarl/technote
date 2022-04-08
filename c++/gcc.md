### GCC Toolchain
[参考链接](https://stackoverflow.com/questions/50307733/what-is-a-gcc-toolchain)

From Wiktionary, a toolchain is:

> A set of tools for software development, often used in sequence so that the output of one tool comprises the input of the next.

GCC is the GNU Compiler Collection; i.e. a set of compilers for different languages from GNU. From the official webpage:

> The GNU Compiler Collection includes front ends for C, C++, Objective-C, Fortran, Ada, and Go, as well as libraries for these languages (libstdc++,...).

Therefore, the GCC toolchain is a set of applications and libraries to compile programs written in several languages. For instance, for the C and C++ languages, that includes tools like:

- `cpp` Preprocessor
- `gcc` C compiler
- `g++` C++ compiler
- `gcov` Test coverage program

And accompanying libraries like:
- `libbacktrace` Symbolic backtraces producer
- `libquadmath` Quad-Precision Math Library
- `libstdc++-v3` C++ Standard Library

Now, when someone refers to the GCC toolchain, typically they are also implicitly referring to other utilities that might not come from GCC's project/repository but are usually required for development. For instance, tools like:

- `ar` Archive manipulation program
- `as` Assembler
- `c++filt` C++ demangler
- `ld` Linker
- `nm` Object file symbol listing
- `objdump` Object file information dumper

If you are using the implementation of these tools from GNU, then you are using the GNU Binutils project:

> The GNU Binutils are a collection of binary tools.