# lib分类

bin、lib

rlib Rust Library 特定静态中间库格式。如果只是纯 Rust 代码项目之间的依赖和调用，那么，用 rlib 就能完全满足使用需求。

dylib 动态库。生成动态库（Linux 上为 .so, MacOS 上为 .dylib, Windows 上为 .dll）。

cdylib C规范动态库。与 dylib 类似，也会生成 .so, .dylib 或 .dll 文件。但是这种动态库可以被其它语言调用（因为几乎所有语言都有遵循 C 规范的 FFI 实现），也就是跨语言 FFI 使用。这个动态库可能依赖于其它动态库（比如，Linux 下用 C 语言写的 PostgreSQL 的 libpq.so）。

staticlib 静态库。编译会生成 .a 文件（在 Linux 和 MacOS 上），或 .lib 文件（在 Windows 上）。

编译器会把所有实现的 Rust 库代码以及依赖的库代码全部编译到一个静态库文件中，也就是对外界不产生任何依赖了。这特别适合将 Rust 实现的功能封装好给第三方应用使用。

**proc-macro** 过程宏 crate。这种 crate 里面只能导出过程宏，被导出的过程宏可以被其它 crate 引用

```rust
[lib]
name = "foobar"
crate-type = ["staticlib"]
```
