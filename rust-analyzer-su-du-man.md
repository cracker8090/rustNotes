# rust analyzer速度慢

rm -rf \~/.cargo/.package-cache

在工程目录下运行`cargo metadata`

重启`vscode`

默认情况下`rust-analyzer`会在文件保存时通过运行`cargo check`来收集错误信息，`cargo check`的本质是通过编译项目获取报错的。

{ "rust-analyzer.checkOnSave.command": "clippy" }

默认情况下`rust-analyzer`会自动更新到最新版



## 其他插件

`btter toml插件：`提供了`TOML`文件的语法高亮以及`lint`功能

crates：展示出最新版本的依赖并提供了更新的快捷方式

search-crates-io：尝试帮助你补全`Cargo.toml`中依赖包的名称



``

