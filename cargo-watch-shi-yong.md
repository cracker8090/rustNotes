# cargo watch使用

cargo install cargo-watch

每当对代码进行更改时，通过在后台自动生成项目，cargo-watch可以有助于缩短修复、编译和运行周期。

默认情况下，该工具只运行Rust的**类型检查器**(cargo check命令)，而不进行代码生成阶段(这需要时间)，便缩短了编译时间；还可以使用**-x flag** 提供一个定制命令来代替cargo check。

一般用于微服务或者本地调试。

cargo watch -q -c -w src -x run

\-w 检测watch

\-c Change working directory before running command \[default: crate root]

\-q Suppress output from cargo-watch itself

\-x Cargo command(s) to execute on changes \[default: check]
