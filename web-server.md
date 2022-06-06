# web server

包：[https://github.com/seanmonstar/warp](https://github.com/seanmonstar/warp)

vscode http插件：thunder client



框架参考：[https://course.rs/practice/third-party-libs.html](https://course.rs/practice/third-party-libs.html)

* HTTP客户端
  * [reqwest](https://github.com/seanmonstar/reqwest) 一个简单又强大的HTTP客户端，`reqwest`是目前使用最多的HTTP库
* Web框架
  * [axum](https://github.com/tokio-rs/axum) 基于Tokio和Hyper打造，模块化设计较好，目前口碑很好，值得使用Ergonomic and modular web framework built with Tokio, Tower, and Hyper
  * [Rocket](https://github.com/SergioBenitez/Rocket) 功能强大，API简单的Web框架，但是主要开发者目前因为个人原因无法进行后续开发，未来存在不确定性
  * [actix-web](https://github.com/actix/actix-web) 性能极高的Web框架，就是团队内部有些问题，未来存在一定的不确定性
  * 上述三个web框架都有很深的用户基础，其实都可以选用，如果让我推荐，顺序如下: `axum` > `Rocket` > `actix-web`。 不过如果你不需要多么完善的web功能，只需要一个性能极高的http库，那么`actix-web`是非常好的选择
