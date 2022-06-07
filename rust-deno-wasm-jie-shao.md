# Rust deno wasm介绍

Deno 是一个简单、现代、安全的 [JavaScript](https://www.javascript.com/)、[TypeScript](https://www.typescriptlang.org/)、[Webassembly](https://webassembly.org/) 运行时环境。

[Deno](https://deno.land/)项目[终于发布了1.0版本](https://www.infoq.com/news/2020/06/deno-1-ready-production/)！Deno 是由 Node.js 的创始人 Ryan Dahl 创建的，旨在解决他所说的“[我为Node.js感到遗憾的十件事](https://www.youtube.com/watch?v=M3BM9TB-8yA)”。

Deno 抛弃了 NPM 和臭名昭著的 node\_modules。它是单个二进制可执行文件，可运行以 TypeScript 和 JavaScript 编写的应用程序。

建立在：

* [Rust](https://www.rust-lang.org/zh-CN/)（Deno 的底层是用 Rust 开发，而 Node 是用 C++）
* [Tokio](https://tokio-zh.github.io/)（Deno 的事件机制是基于 Tokio，而 Node 是基于 libuv）
* [TypeScript](https://www.typescriptlang.org/)
* [V8](https://v8.dev/)

[这个Deno入门项目模板](https://github.com/second-state/ssvm-deno-starter)。按照说明操作，只需 5 分钟你就能在 Deno 中运行第一个 WebAssembly 函数（由 Rust 编写）。

Node.js：JavaScript 的易用性（尤其是编写基于事件的异步应用程序时）以及 C/C++的高性能。Node.js 应用程序是用 JavaScript 编写的，但会在基于 C/C++的原生运行时上执行，这些运行时包括谷歌 V8 JavaScript 引擎和许多原生库模块。Deno 希望能复制这种成功路径，但在这个过程中它使用了 TypeScript 和 Rust 支持的现代技术栈。

* Deno 是单个二进制可执行文件。
* 应用程序是使用 TypeScript 或 JavaScript 编写的，在代码中将依赖项明确声明为 import 语句，并带有完整的 URL，链接到依赖项的源代码。
* Deno 与 Node.js 模块不兼容。

在毫秒级别执行复杂神经网络模型运算的 AI 即服务应用程序？在 Deno 和 Node.js 中，许多函数都是通过 TypeScript 或 JavaScript API 调用，但以 Rust 或 C 语言编写的原生代码执行。

Deno 内部使用谷歌 V8 引擎。V8 不仅是一个 JavaScript 运行时，还是一个 WebAssembly 虚拟机。Deno 对 WebAssembly 提供了开箱即用的支持。Deno 为你的 TypeScript 应用程序提供了一个 API，以调用 WebAssembly 中的函数。

实际上，WebAssembly 中已经实现了一些流行的 Deno 组件。例如，Deno 中的[sqlite module](https://deno.land/x/sqlite/)是使用 Emscripten 将 sqlite 的 C 源代码编译到 WebAssembly 中的成果。[Deno WASI组件](https://github.com/caspervonb/deno-wasi/blob/master/README.md)使 WebAssembly 应用程序可以访问操作系统的底层资源，例如文件系统。

### 配置

[安装Deno](https://deno.land/manual/getting\_started/installation)

curl -fsSL [https://deno.land/x/install/install.sh](https://deno.land/x/install/install.sh) | sh

curl --proto '=https' --tlsv1.2 -sSf [https://sh.rustup.rs](https://sh.rustup.rs) | sh

[ssvmup](https://www.secondstate.io/articles/ssvmup/)工具可以自动执行构建过程并生成所有工件，以使你的 Deno 应用程序轻松调用 Rust 函数。

curl [https://raw.githubusercontent.com/second-state/ssvmup/master/installer/init.sh](https://raw.githubusercontent.com/second-state/ssvmup/master/installer/init.sh) -sSf | sh

安装后配置export环境

[hello world源代码和应用程序模板](https://github.com/second-state/ssvm-deno-starter)

rustwasmc build --target deno --no-wasi

deno run --allow-read --allow-env --unstable deno/test.ts

deno run --allow-read --allow-net --allow-env --unstable deno/server.ts

curl [http://localhost:8000/](http://localhost:8000/)



## 参考

[聊聊Deno的那些事](https://www.zoo.team/article/talk-about-deno)
