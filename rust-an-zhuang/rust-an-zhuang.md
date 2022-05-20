# Rust安装

### linux安装

curl [https://sh.rustup.rs](https://sh.rustup.rs) -sSf | sh

下载并执行一个脚本来安装rustup工具，进而安装最新的Rust稳定版本

上面的安装过程会自动将Rust工具链添加到环境变量PATH中，并在下一次登录终端时生效。假如你想要立即开始使用Rust而不用重新启动终端

source $HOME/.cargo/env

也可以向～/.bash\_profile 文件中添加下面的语句，手动将Rust添加到环境变量PATH中

export PATH="$HOME/.cargo/bin:$PATH

### windows安装

Rust官方网站：[https://www.rust-lang.org/](https://www.rust-lang.org/)

更新：rustup update

卸载：rustup self uninstall

检测版本：rustc --version

本地文档：rustup doc

检查：rustc -V cargo -V
