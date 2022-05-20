# Cargo初用

Cargo是Rust工具链中内置的构建系统及包管理器。由于它可以处理众多诸如构建代码、下载编译依赖库等琐碎但重要的任务，所以绝大部分的Rust用户都会选择它来管理自己的Rust项目。

Cargo就已经被附带在了当前的Rust工具链里

查看版本：cargo --version

创建项目：cargo new hello\_cargo

构建任务：cargo build

./target/debug/ hello\_cargo

编译运行任务：cargo run

检查是否通过编译：cargo check，速度比build快

优化发布：cargo build --release

对代码的运行效率进行基准测试：cargo run --release

* Cargo.toml：`cargo` 特有的**项目数据描述文件** ，希望 Rust 项目能够按照期望的方式进行构建、测试和运行，那么，必须按照合理的方式构建 `Cargo.toml`。
* Cargo.lock：根据同一项目的 `toml` 文件生成的**项目依赖详细清单** 。项目是一个可运行的程序时，就上传 `Cargo.lock`，如果是一个依赖库项目，那么请把它添加到 `.gitignore` 中。

将crate译为单元包，将package译为包

Cargo简化了我们复用代码的诸多流程，以至于 Rust的开发者们可以轻松地基于第三方库编写出更为轻巧的项目

本地文档：cargo doc --open
