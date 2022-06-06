# web server

包：[https://github.com/seanmonstar/warp](https://github.com/seanmonstar/warp)

vscode http插件：thunder client



框架参考：[https://course.rs/practice/third-party-libs.html](https://course.rs/practice/third-party-libs.html)

* HTTP客户端
  * [reqwest](https://github.com/seanmonstar/reqwest) 一个简单又强大的HTTP客户端，`reqwest`是目前使用最多的HTTP库
* Web框架
  * [axum](https://github.com/tokio-rs/axum) 基于Tokio和Hyper Tower打造，模块化设计较好，目前口碑很好，值得使用Ergonomic and modular web framework built with Tokio, Tower, and Hyper。4.9k star。Tokio出的，不一定是精品。
  * [Rocket](https://github.com/SergioBenitez/Rocket) 功能强大，API简单的Web框架，但是主要开发者目前因为个人原因无法进行后续开发，未来存在不确定性。文档优秀，上手简单，大而全（测试库、cookie、数据流、路由、模板、数据库、ORM等）。17.5k star。
  * [actix-web](https://github.com/actix/actix-web) 性能极高的Web框架，就是团队内部有些问题，未来存在一定的不确定性。unsafe，actor模型架构模式，可能不稳定。14.3k star。
  * 上述三个web框架都有很深的用户基础，其实都可以选用，如果让我推荐，顺序如下: `axum` > `Rocket` > `actix-web`。 不过如果你不需要多么完善的web功能，只需要一个性能极高的http库，那么`actix-web`是非常好的选择。
  * [warp](https://github.com/seanmonstar/warp)：新兴轻量级web框架，基于hyper架构实现，通过组合满足不同需求，适合写中间件。6.5k star。
  * [Tide](https://github.com/http-rs/tide)：最可惜的web框架。被借鉴，可以阅读学习代码，不建议使用。3.8k star。

Rocket：入门新手（专注业务）、选择困难、类型安全（追求迭代和稳定）。

Warp：小而美、灵活且有中间件需求。

Actix-web：对性能有极致需求。
