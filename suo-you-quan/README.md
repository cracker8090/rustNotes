# 所有权

所有权、借用和切片的概念是Rust可以在编译时保证内存安全的关键所在。

所有权规则：

* Rust中的每一个值都有一个对应的变量作为它的所有者
* 在同一时间内，有且仅有一个所有者
* 当所有者离开自己的作用域时，它持有的值就会被释放掉，走出作用域时，自动调用drop函数



move - copy

使用包含特定规则的所有权系统来管理内存，这套规则允许编译器在编译过程中执行检查工作，而不会产生任何的运行时开销

* 内存是通过一个所有权系统来管理的，其中包含一组编译器在编译时检查的规则。
* 当程序运行时，所有权特性不会减慢程序的运行速度。



* 所有权解决的问题：
  * 跟踪代码的哪些部分正在使用heap的哪些数据
  * 最小化heap上的重复数据量
  * 清理heap上未使用的数据以避免空间不足
* 一旦了解了所有权，不需要经常去想stack或heap