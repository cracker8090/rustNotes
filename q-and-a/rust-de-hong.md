# Rust的宏

## 介绍

宏在类C语言中好处是很多的，特别是对于工程比较大的适合，业务比较复杂或者代码优美的情况下会使用。

Rust 宏非常不同于 C 里面的宏。Rust 宏会被应用于词法树（token tree），而 C 语言里的宏则是文本替换。

Rust中的宏参数可以使用 `()`、`[]` 以及 {}，虽然三种使用形式皆可，但是 Rust 内置的宏都有自己约定俗成的使用方式，例如 `vec![...]`、`assert_eq!(...)` 等。

在 Rust 中宏分为两大类：**声明式宏( **_**declarative macros**_** )** `macro_rules!` 和三种**过程宏( **_**procedural macros**_** )**:

* 声明式宏（Declarative macros）使得你能够写出类似 match 表达式的东西，来操作你所提供的 Rust 代码。它使用你提供的代码来生成用于替换宏调用的代码。\

* 过程宏（Procedural macros）允许你操作给定 Rust 代码的抽象语法树（abstract syntax tree, AST）。过程宏是从一个（或者两个）`TokenStream`到另一个`TokenStream`的函数，用输出的结果来替换宏调用。
* `#[derive]`，在之前多次见到的派生宏，可以为目标结构体或枚举派生指定的代码，例如 `Debug` trait
* 类属性宏(Attribute-like macro)，用于为目标添加自定义的属性
* 类函数宏(Function-like macro)，看上去就像是函数调用

宏特点：

* 元编程：左右就是复用代码，类似用宏来生成代码。

宏会被展开成其它代码，且这个展开过程是发生在编译器对代码进行解释之前。因此，宏可以为指定的类型实现某个特征：先将宏展开成实现特征的代码后，再被编译。

* 函数就做不到这一点，因为它直到运行时才能被调用，而特征需要在编译期被实现。
* 宏可以引入可变参数：println!("hello")，println!("hello {}", name)，而函数参数是固定的，不同JS/TS。

## 声明式宏

```rust
#[macro_export]
macro_rules! testVec {
    ( $($x:expr),*) => {
        let mut temp_vec = Vec::new();
        $(
            temp_vec.push($x);
        )*
        temp_vec
    };
}
fn main() {
    let a: Vec<u32> = testVec![4, 5, 6];
    println!("testVec={:?}", a);
    println!("add!(1,2)={:?}", add!(1,2));
}
```

( $( $x:expr ),\* )

> 使用圆括号 `()` 将整个宏模式包裹其中。紧随其后的是 `$()`，跟括号中模式相匹配的值(传入的 Rust 源代码)会被捕获，然后用于代码替换。在这里，模式 `$x:expr` 会匹配任何 Rust 表达式并给予该模式一个名称：`$x`。

> `$()` 之后的逗号说明在 `$()` 所匹配的代码的后面会有一个可选的逗号分隔符，紧随逗号之后的 `*` 说明 `*` 之前的模式会被匹配零次或任意多次(类似正则表达式)。

> 使用 `vec![1, 2, 3]` 来调用该宏时，`$x` 模式将被匹配三次，分别是 `1`、`2`、`3`。

1. `$()` 中包含的是模式 `$x:expr`，该模式中的 `expr` 表示会匹配任何 Rust 表达式，并给予该模式一个名称 `$x`
2. 因此 `$x` 模式可以跟整数 `1` 进行匹配，也可以跟字符串 "hello" 进行匹配: `vec!["hello", "world"]`
3. `$()` 之后的逗号，意味着`1` 和 `2` 之间可以使用逗号进行分割，也意味着 `3` 既可以没有逗号，也可以有逗号：`vec![1, 2, 3,]`
4. `*` 说明之前的模式可以出现零次也可以任意次，这里出现了三次

```rust
// 传入vec![1, 2, 3]实际代码
{
    let mut temp_vec = Vec::new();
    temp_vec.push(1);
    temp_vec.push(2);
    temp_vec.push(3);
    temp_vec
}
```

## 过程宏_procedural macros_

从形式上来看，过程宏跟函数较为相像，但过程宏是使用源代码作为输入参数，基于代码进行一系列操作后，再输出一段全新的代码。**注意，过程宏中的 derive 宏输出的代码并不会替换之前的代码，这一点与声明宏有很大的不同。**

1. 属性式宏（Attribute-like macros）
2. 派生宏（Derive macros）
3. 函数式宏（Function-like macros）

cargo new macro\_demo --lib ，在cargo.toml中新增\[lib]\n proc-macro = true

\[dependencies]\n syn = {version="1.0.57",features=\["full","fold"]} quote = "1.0.8"

```rust
// lib.rs
extern crate proc_macro;
use proc_macro::TokenStream;
use quote::quote;

// using proc_macro_attribute to declare an attribute like procedural macro
#[proc_macro_attribute]
// _metadata is argument provided to macro call and _input is code to which attribute like macro attaches
pub fn my_custome_attribute(_metadata: TokenStream, _input: TokenStream) -> TokenStream {
    // returing a simple TokenStream for Struct
    TokenStream::from(quote! {struct H{}})
}
```

lib同级目录新建tests文件夹，下面新建attribute\_macro.rs文件，然后cargo test

```rust
use macro_demo::*;

// macro converts struct S to struct H
#[my_custome_attribute]
struct S {}

#[test]
fn test_macro() {
    // due to macro we have struct H in scope
    let demo = H {};
}
```

Rust 中的自定义派生宏能够对 trait 进行自动实现。这些宏通过使用`#[derive(Trait)]`自动实现 trait。

要想在 Rust 中写一个自定义派生宏，我们可以使用`DeriveInput`来解析派生宏的输入。我们还将使用`proc_macro_derive`宏来定义一个自定义派生宏。

参考：[Rust宏：教程与示例（一）](https://zhuanlan.zhihu.com/p/353421021)[Rust宏：教程与示例（二）](https://www.cnblogs.com/praying/p/14520507.html)
