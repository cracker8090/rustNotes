# 所有权基础

## 字符串

一个指向存放字符串内容的指针（ptr）、一个长度（len）及一个容量（capacity）

let s1 = String::from("Hello"); let s2 = s1;

为了确保内存安全，同时也避免复制分配的内存，Rust在这种场景下会简单地将s1废弃，不再视其为一个有效的变量

**克隆(深拷贝)**

> 隐含了另外一个设计原则：Rust永远不会自动地创建数据的深度拷贝。因此在Rust中，任何自动的赋值操作都可以被视为高效的。

let s1 = String::from("hello"); let s2 = s1.clone();

```rust
fn main() {
    // let mut s = String::from("Hello");
    // s.push_str(", World");

    // let s1 = String::from("Hello");
    // let s2 = s1;
    let s1 = String::from("hello");
    let s2 = s1.clone();
    println!("{},{}",s1,s2);
}
```

### Copy

* 一旦某种类型拥有了Copy这种trait，那么它的变量就可以在赋值给其他变量之后保持可用性
* 如果一种类型本身或这种类型的任意成员实现了Drop这种trait，那么Rust就不允许其实现Copy这种trait

哪些类型是Copy的呢？你可以查看特定类型的文档来确定，不过一般来说，任何简单标量的组合类型都可以是Copy的，任何需要分配内存或某种资源的类型都不会是Copy的

一些拥有Copy这种trait的类型：

* 所有的整数类型，诸如u32
* 仅拥有两种值（true和false）的布尔类型：bool
* 字符类型：char
* 所有的浮点类型，诸如f64
* 如果元组包含的所有字段的类型都是Copy的，那么这个元组也是Copy的。例如，(i32, i32)是Copy的，但(i32, String)则不是

• 如果元组包含的所有字段的类型都是Copy的，那么这个元组也是Copy的。例如，(i32, i32)是Copy的，但(i32, String)则不是。

### 所有权实例

```rust
fn main() {
    let s = String::from("hello");  // s 进入作用域

    takes_ownership(s);             // s 的值移动到函数里 ...
                                    // ... 所以到这里不再有效

    let x = 5;                      // x 进入作用域

    makes_copy(x);                  // x 应该移动函数里，
                                    // 但 i32 是 Copy 的，所以在后面可继续使用 x

} // 这里, x 先移出了作用域，然后是 s。但因为 s 的值已被移走，
  // 所以不会有特殊操作

fn takes_ownership(some_string: String) { // some_string 进入作用域
    println!("{}", some_string);
} // 这里，some_string 移出作用域并调用 `drop` 方法。占用的内存被释放

fn makes_copy(some_integer: i32) { // some_integer 进入作用域
    println!("{}", some_integer);
} // 这里，some_integer 移出作用域。不会有特殊操作
```
