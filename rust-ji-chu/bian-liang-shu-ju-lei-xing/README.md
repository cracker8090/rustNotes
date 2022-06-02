# 变量-数据类型

## 数据类型

四个标量类型：整数、浮点数、布尔值及字符

* 数值类型: 有符号整数 (`i8`, `i16`, `i32`, `i64`, `isize`)、 无符号整数 (`u8`, `u16`, `u32`, `u64`, `usize`) 、浮点数 (`f32`, `f64`默认)、以及有理数、复数
* 字符串：字符串字面量和字符串切片 `&str`
* 布尔类型： `true`和`false`
* 字符类型: 表示单个 Unicode 字符，存储为 4 个字节
* 单元类型: 即 `()` ，其唯一的值也是 `()`
* 复合类型：元组 （tuple）和数组 （array）

长度固定，但是数组里面的元素类型需要一样

数组没有Vector灵活，Vector由标准库提供，Vector长度可以改变。

```rust
    const MAX_POINTS: u32 = 100000;

    let guess:u32 = "42".parse().expect("Not a number");

    println!("{}",guess);

    let x = 2.0;
    let y:f32 = 3.0;


    let tup: (i32,f64,u8) = (500,6.4,1);
    let (x, y, z) = tup;
    let x: (i32,f64,u8) = (500,6.4,1);

    let five_hundred = x.0;
    let six_point_four = x.1;
    let one = x.2;

    let a = [1, 2, 3, 4, 5];
    let first = a[0]; 
    let second = a[1];

    let a:[i32;5] = [1,2,3,4,5];
```

mut可变，常量const，MAX\_POINTS

不能用mut关键字来修饰一个常量。常量不仅是默认不可变的，它还总是不可变的。

未使用的变量可用“\_”开头。

## 变量

Rust 的变量在默认情况下是**不可变的** ，可以通过 `mut` 关键字让变量变为**可变的** 。

```rust
fn main() {
    let x = 5;
    println!("The value of x is: {}", x);
    x = 6; // 错误
    println!("The value of x is: {}", x);
}
```

使用下划线开头忽略未使用的变量

Rust 允许声明相同的变量名，在后面声明的变量会遮蔽掉前面声明

```rust
fn main() {
    let x = 5;
    // 在main函数的作用域内对之前的x进行遮蔽
    let x = x + 1;

    {
        // 在当前的花括号作用域内，对之前的x进行遮蔽
        let x = x * 2;
        println!("The value of x in the inner scope is: {}", x);
    }

    println!("The value of x is: {}", x);
}
// 结果为12，6
```

`mut` 声明的变量，可以修改同一个内存地址上的值，并不会发生内存对象的再分配，性能要更好。
