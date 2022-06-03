# 函数-method

方法跟函数类似：都是使用 `fn` 声明，有参数和返回值。但是与函数不同的是，方法定义在结构体的上下文中(枚举、特征对象也可以定义方法)，而且方法的第一个参数一定是 `self` 或其变体 `&self` 、`&mut self`，`self` 代表了当前调用的结构体实例。

`self` 会拿走当前结构体实例(调用对象)的所有权，而 `&self` 却只会借用一个不可变引用，`&mut self` 会借用一个可变引用。

```rust
fn another_function() {
    let x = 5;

    let y = {
        let x = 1;
        x+3
    };

    println!("The value of y is: {}",y);
```

命名规则：蛇形命名法2，add\_two类似。

函数的返回值就是函数体最后一条表达式的返回值，当然我们也可以使用 `return` 提前返回

单元类型 `()`，是一个零长度的元组。它没啥作用，但是可以用来表达一个函数没有返回值：

* 函数没有返回值，那么返回一个 `()`
* 通过 `;` 结尾的表达式返回一个 `()`

## 关联函数 & 方法

关联函数的调用不是通过点操作符，而是使用 \`::\~，方法才是通过点操作符调用。

关联函数与方法最大的区别就是它第一个参数不是 `self` ，原因是它们不需要使用当前的实例，因此关联函数往往可以用于构造函数：初始化一个实例对象。

```rust

#[test]
fn impl_test() {
    let rectangle = Rectangle {
        p1: Point::origin(),
        p2: Point::new(3.0, 4.0),
    };

    // 方法才是通过点操作符调用
    println!("Rectangle perimeter: {}", rectangle.premiter());
    println!("Rectangle area: {}", rectangle.area());

    // 关联函数的调用不是通过点操作符，而是使用 `::~
    let mut square = Rectangle {
        p1: Point::origin(),
        p2: Point::new(1.0, 1.0),
    };

    square.translate(1.0, 2.0);
    let pair = Pair(Box::new(1), Box::new(2));
    pair.destroy();

    let c = TrafficLightColor::Yellow;
    assert_eq!(c.color(), "yellow");
    println!("{:?}", c);
}
```
