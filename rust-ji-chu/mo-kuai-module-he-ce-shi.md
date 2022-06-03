# 模块module和测试

比模块更高级别的分组是 `crate`，我们可以将多个模块放到一个 `crate` 下面。`crate` 是 Rust 语言的基本编译单元。Rust 中的可执行二进制文件程序或者一个库就是一个 `carate`。

可执行二进制文件程序和库的最大区别，就是 **可执行二进制程序** 有一个包含 `main()` 方法作为程序入口。

而一个库 ( library crate ) 是一组可以在其他项目中重用的组件。与二进制包不同，库包没有入口函数（ `main()` 方法）。

Rust 内置了 `cargo` 作为包管理器，类似于 Python 语言中的 `pip` 。Rust 官方同时提供了 `crates.io` 用作所有第三方包的中央存储服务器。你可以使用 `cargo install` 命令从 [crates.io](https://crates.io/) 上下载你的程序所需要的 crate。

Rust 语言默认所有的模块和模块内的函数都是私有的，也就是只能在模块内部使用。

如果一个模块或者模块内的函数需要导出为外部使用，则需要添加 `pub` 关键字。

use public\_module\_name::function\_name;

## 多级嵌套

```rust
pub mod movies {
   pub mod english {
      pub mod comedy {
         pub fn play(name:String) {
            println!("Playing comedy movie {}",name);
         }
      }
   }
}
```

使用：use movies::english::comedy::play;

cargo new basic\_lib --lib

`lib.rs` 文件 **用于指定 库 `crate` 有哪些公开的模块可用** ，这是一个 **库 crate** ，因此需要在 `lib.rs` 文件中指定需要导出的模块名字。

pub mod \[库名字];

pub mod my\_string;

## 测试

cargo test默认：并行运行、所有测试、不显示打印。包含测试和文档测试。

cargo test --help

cargo test -- --help

cargo test -- --test-threads=1

cargo test -- --show-output

cargo test play\_test

\#\[ignore]忽略测试

单元测试：小、可以测试private，#\[cfg(test)]

集成测试：只能测试public，可使用多个模块，不需要标注，src同级目录新建tests目录，每个测试文件都是独立的crate，需要导入库

cargo test --test 指定文件

参考[https://github.com/cracker8090/rust\_tutorial](https://github.com/cracker8090/rust\_tutorial) 下的basic\_lib
