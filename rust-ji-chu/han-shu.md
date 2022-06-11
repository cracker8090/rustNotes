# 函数-method 闭包 迭代器

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

## 闭包

move会把所有权进行转移。

增加 move 关键字，我们强制闭包获取其使用的值的所有权

move 关键字覆盖了 Rust 默认保守的借用，但它不允许我们违反所有权规则

```
|参数1, 参数2, ...| -> 返回值类型 {
    // 函数体
}
```

闭包定义会为每个参数和返回值推断一个具体类型。

可以创建一个存放闭包和调用闭包结果的结构体，该结构体只会在需要结果时执行闭包，并会缓存结果值，这样余下的代码就不必再负责保存结果并可以复用该值。你可能见过这种模式被称 _memoization_ 或 _lazy evaluation_ _（惰性求值）_。

为了让结构体存放闭包，我们需要指定闭包的类型，因为结构体定义需要知道其每一个字段的类型。每一个闭包实例有其自己独有的匿名类型：也就是说，即便两个闭包有着相同的签名，他们的类型仍然可以被认为是不同。为了定义使用闭包的结构体、枚举或函数参数，需要使用泛型和 trait bound。

`Fn` 系列 trait 由标准库提供。所有的闭包都实现了 trait `Fn`、`FnMut` 或 `FnOnce` 中的一个。

```rust
struct Cacher<T>
where
    T: Fn(u32) -> u32,
{
    calculation: T,
    value: Option<u32>,
}
```

trait bound 就是 `Fn(u32) -> u32`，存放了闭包和一个 Option 结果值的 `Cacher` 结构体。

> 注意：函数也都实现了这三个 `Fn` trait。如果不需要捕获环境中的值，则可以使用实现了 `Fn` trait 的函数而不是闭包。



为了展示闭包作为函数参数时捕获其环境的作用，迭代器。

## 迭代器

在 Rust 中，迭代器是 **惰性的**（_lazy_），这意味着在调用方法使用迭代器之前它都不会有效果。一旦创建迭代器之后，可以选择用多种方式利用它。

迭代器处理了所有这些逻辑，这减少了重复代码并消除了潜在的混乱。

```rust
#[test]
fn iter_test(){
    let v1 = vec![1,2,3];
    let mut v1_iter = v1.iter();
    // for i in v1_iter {
    //     println!("get {}",i);
    // }
    assert_eq!(v1_iter.next(),Some(&1));
    assert_eq!(v1_iter.next(),Some(&2));
    assert_eq!(v1_iter.next(),Some(&3));
    assert_eq!(v1_iter.next(),None);
}
```

迭代器的实现方式提供了对多种不同的序列使用相同逻辑的灵活性，而不仅仅是像 vector 这样可索引的数据结构。

迭代器都实现了一个叫做 `Iterator` 的定义于标准库的 trait。

```rust
pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // 此处省略了方法的默认实现
}
```

新语法：`type Item` 和 `Self::Item`，他们定义了 trait 的 **关联类型**（_associated type_）。

实现 `Iterator` trait 要求同时定义一个 `Item` 类型，这个 `Item` 类型被用作 `next` 方法的返回值类型。换句话说，`Item` 类型将是迭代器返回元素的类型。

`next` 是 `Iterator` 实现者被要求定义的唯一方法。`next` 一次返回迭代器中的一个项，封装在 `Some` 中，当迭代器结束时，它返回 `None`。

需要注意到从 `next` 调用中得到的值是 vector 的不可变引用。`iter` 方法生成一个不可变引用的迭代器。如果我们需要一个获取 `v1` 所有权并返回拥有所有权的迭代器，则可以调用 `into_iter` 而不是 `iter`。类似的，如果我们希望迭代可变引用，则可以调用 `iter_mut` 而不是 `iter`。

```rust
#[test]
fn iterator_sum() {
    let v1 = vec![1, 2, 3];
    let v1_iter = v1.iter();
    let total: i32 = v1_iter.sum();
    assert_eq!(total, 6);
}
```

`sum` 之后不再允许使用 `v1_iter` 因为调用 `sum` 时它会获取迭代器的所有权。

自定义迭代器

```rust
impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        if self.count < 5 {
            self.count += 1;
            Some(self.count)
        } else {
            None
        }
    }
}
#[test]
fn using_other_iterator_trait_methods() {
    let sum: u32 = Counter::new()
        .zip(Counter::new().skip(1))
        .map(|(a, b)| a * b)
        .filter(|x| x % 3 == 0)
        .sum();
    assert_eq!(18, sum);
}
```



