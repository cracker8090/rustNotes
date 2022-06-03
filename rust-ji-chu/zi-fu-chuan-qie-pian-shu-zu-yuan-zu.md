# 字符串-切片-数组-元组-hashpmap

## `字符串切片`

`String` 是定义在标准库中的类型，分配在堆上，可以动态的增长。它的底层存储是动态字节数组的方式( `Vec<u8>` )，但是与字节数组不同，`String` 是 `UTF-8` 编码。

`&str` 和 `String` 的关系类似于 `&[T]` 和 `Vec<T>` 。

let s = "Hello, world!";

实际上，`s` 的类型是 `&str`

let s: \&str = "Hello, world!";

`str` 类型是硬编码进可执行文件，也无法被修改，但是 `String` 则是一个可增长、可改变且具有所有权的 UTF-8 编码字符串，**当 Rust 用户提到字符串时，往往指的就是 `String` 类型和 `&str` 字符串切片类型，这两个类型都是 UTF-8 编码**。

除了 `String` 类型的字符串，Rust 的标准库还提供了其他类型的字符串，例如 `OsString`， `OsStr`， `CsString` 和`CsStr` 等，注意到这些名字都以 `String` 或者 `Str` 结尾了吗？它们分别对应的是具有所有权和被借用的变量。

只能将 `String` 跟 `&str` 类型进行拼接，并且 `String` 的所有权在此过程中会被 move。

### **String 与 \&str 的转换**

可以使用 `String::from` 或 `to_string` 将 `&str` 转换成 `String` 类型。

从 `&str` 类型生成 `String` 类型

* `String::from("hello,world")`
* `"hello,world".to_string()`

如何将 `String` 类型转为 `&str` 类型? 取引用即可

### **字符串索引**

```rust
#![allow(unused)]
fn main() {
   let s1 = String::from("hello");
   let h = s1[0]; // 错误
}

#![allow(unused)]
fn main() {
let hello = "中国人";

let s = &hello[0..2];
}
```

切片的索引是通过字节来进行，但是字符串又是 UTF-8 编码，因此你无法保证索引的字节刚好落在字符的边界上。

因此在通过索引区间来访问字符串时，**需要格外的小心**，一不注意，就会导致你程序的崩溃。

### **操作字符串**

* push

在字符串尾部可以使用 `push()` 方法追加字符 `char`，也可以使用 `push_str()` 方法追加字符串字面量。这两个方法都是**在原有的字符串上追加，并不会返回新的字符串**。由于字符串追加操作要修改原来的字符串，则该字符串必须是可变的，即**字符串变量必须由 `mut` 关键字修饰**。

```rust
fn main() {
    let mut s = String::from("Hello ");
    s.push('r');
    println!("追加字符 push() -> {}", s);

    s.push_str("ust!");
    println!("追加字符串 push_str() -> {}", s);
}
```

* Insert

**字符串变量必须由 `mut` 关键字修饰**

```rust
fn main() {
    let mut s = String::from("Hello rust!");
    s.insert(5, ',');
    println!("插入字符 insert() -> {}", s);
    s.insert_str(6, " I like");
    println!("插入字符串 insert_str() -> {}", s);
}
```

* Replace

replace：第一个参数是要被替换的字符串，第二个参数是新的字符串。该方法会替换所有匹配到的字符串。**该方法是返回一个新的字符串，而不是操作原来的字符串**。

replacen：前两个参数与 `replace()` 方法一样，第三个参数则表示替换的个数。**该方法是返回一个新的字符串，而不是操作原来的字符串**。

replace\_range：第一个参数是要替换字符串的范围（Range），第二个参数是新的字符串。**该方法是直接操作原来的字符串，不会返回新的字符串。该方法需要使用 `mut` 关键字修饰**。

* Delete

pop()：删除并返回字符串的最后一个字符。**该方法是直接操作原来的字符串**。但是存在返回值，其返回值是一个 `Option` 类型，如果字符串为空，则返回 `None`。

remove()：删除并返回字符串中指定位置的字符。**该方法是直接操作原来的字符串**。但是存在返回值，其返回值是删除位置的字符串，只接收一个参数，表示该字符起始索引位置。remove(0)。按照字节来处理字符串的

truncate ()：删除字符串中从指定位置开始到结尾的全部字符。**直接操作原来的字符串**。无返回值，truncate(3)。按照字节来处理字符串的

clear()：清空字符串。**直接操作原来的字符串**，相当于 `truncate()` 方法参数为 0 的时候。clear()。

* 连接

使用 `+` 或者 `+=` 连接字符串，要求右边的参数必须为字符串的切片引用（Slice)类型，调用 `+` 的操作符时，相当于调用了 `std::string` 标准库中的 [`add()`](https://doc.rust-lang.org/std/string/struct.String.html#method.add) 方法。**`+` 和 `+=` 都是返回一个新的字符串。所以变量声明可以不需要 `mut` 关键字修饰**。

使用 `format!` 连接字符串，`format!` 的用法与 `print!` 的用法类似

let s = format!("{} {}!", s1, s2);

### **转义**

如果字符串包含双引号，可以在开头和结尾加 #

### **UTF-8**

以 Unicode 字符的方式遍历字符串，最好的办法是使用 `chars` 方法——字符

```rust
#![allow(unused)]
fn main() {
for c in "中国人".chars() {
    println!("{}", c);
}
}
```

返回字符串的底层字节数组表现形式——字节

```rust
#![allow(unused)]
fn main() {
for b in "中国人".bytes() {
    println!("{}", b);
}
}
```

## 数组

数组的类型是 `[T; Length]`，数组的长度是类型签名的一部分，因此数组的长度必须在编译期就已知。

* 数组中的所有元素必须是同一类型
* 数组的下标索引从 0 开始，越界索引会导致代码的 `panic`

``

第一种是速度很快但是长度固定的 `array`，第二种是可动态增长的但是有性能损耗的 `Vector`

* 长度固定
* 元素必须有相同的类型
* 依次线性排列

```rust
fn main() {
    let a = [9, 8, 7, 6, 5];

    let first = a[0]; // 获取a数组第一个元素
    let second = a[1]; // 获取第二个元素
}

let a: [i32; 5] = [1, 2, 3, 4, 5];

let slice: &[i32] = &a[1..3];

assert_eq!(slice, &[2, 3]);
```

let v: Vec = Vec::new();

> 如果预先知道要存储的元素个数，可以使用 `Vec::with_capacity(capacity)` 创建动态数组，这样可以避免因为插入大量新数据导致频繁的内存分配和拷贝，提升性能。

* 切片的长度可以与数组不同，并不是固定的，而是取决于你使用时指定的起始和结束位置
* 创建切片的代价非常小，因为切片只是针对底层数组的一个引用
* 切片类型\[T]拥有不固定的大小，而切片引用类型&\[T]则具有固定的大小，因为 Rust 很多时候都需要固定大小数据类型，因此&\[T]更有用,`&str`字符串切片也同理

几个要注意的点：

* **数组类型容易跟数组切片混淆**，\[T;n]描述了一个数组的类型，而\[T]描述了切片的类型， 因为切片是运行期的数据结构，它的长度无法在编译期得知，因此不能用\[T;n]的形式去描述
* `[u8; 3]`和`[u8; 4]`是不同的类型，数组的长度也是类型的一部分
* **在实际开发中，使用最多的是数组切片\[T]**，我们往往通过引用的方式去使用`&[T]`，因为后者有固定的类型大小

`deref` 隐式强制转换，具体我们会在 [`Deref` 特征](https://course.rs/advance/smart-pointer/deref.html)进行详细讲解。

```rust
let v = vec![1, 2, 3, 4, 5];

let does_not_exist = &v[100];
let does_not_exist = v.get(100);
```

`&v[100]` 的访问方式会导致程序无情报错退出，因为发生了数组越界访问。 但是 `v.get` 就不会，它在内部做了处理，有值的时候返回 `Some(T)`，无值的时候返回 `None`，因此 `v.get` 的使用方式非常安全。

当你确保索引不会越界的时候，就用索引访问，否则用 `.get`。例如，访问第几个数组元素并不取决于我们，而是取决于用户的输入时，用 `.get` 会非常适合。

### 存储不同类型

* 枚举来处理
* trait

在实际使用场景中，特征对象数组要比枚举数组常见很多，主要原因在于特征对象非常灵活，而编译器对枚举的限制较多，且无法动态增加类型。

```rust

trait IpAddr {
    fn display(&self);
}

struct V4(String);
impl IpAddr for V4 {
    fn display(&self) {
        println!("ipv4: {:?}", self.0)
    }
}
struct V6(String);
impl IpAddr for V6 {
    fn display(&self) {
        println!("ipv6: {:?}", self.0)
    }
}

#[test]
fn vec_diff_test() {
    let v: Vec<Box<dyn IpAddr>> = vec![
        Box::new(V4("127.0.0.1".to_string())),
        Box::new(V6("::1".to_string())),
    ];

    for ip in v {
        ip.display();
    }
}
```

## 元组

元组中的元素可以是不同的类型。元组的类型签名是 `(T1, T2, ...)`, 这里 `T1`, `T2` 是相对应的元组成员的类型。

* 可以使用索引来获取元组的成员
* 过长的元组无法被打印输出
* 元组可以用于函数的参数和返回值

```rust
fn sum_multiply(nums: (i32, i32)) -> (i32, i32) {
    (nums.0 + nums.1, nums.0 * nums.1)
}

fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    println!("The length of '{}' is {}.", s2, len);
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len(); // len() 返回字符串的长度

    (s, length)
}
```

## hashmap

使用 `HashMap` 需要手动通过use std::collections::HashMap;

`HashMap` 也是内聚性的，即所有的 `K` 必须拥有同样的类型，`V` 也是如此。

> 跟 `Vec` 一样，如果预先知道要存储的 `KV` 对个数，可以使用 `HashMap::with_capacity(capacity)` 创建指定大小的 `HashMap`，避免频繁的内存分配和拷贝，提升性能

`HashMap` 的所有权规则与其它 Rust 类型没有区别：

* 若类型实现 `Copy` 特征，该类型会被复制进 `HashMap`，因此无所谓所有权
* 若没实现 `Copy` 特征，所有权将被转移给 `HashMap` 中

**如果你使用引用类型放入 HashMap 中**，请确保该引用的生命周期至少跟 `HashMap` 活得一样久。

f32 和 f64 浮点数，没有实现 `std::cmp::Eq` 特征，因此不可以用作 `HashMap` 的 `Key。`

> 目前，`HashMap` 使用的哈希函数是 `SipHash`，它的性能不是很高，但是安全性很高。`SipHash` 在中等大小的 `Key` 上，性能相当不错，但是对于小型的 `Key` （例如整数）或者大型 `Key` （例如字符串）来说，性能还是不够好。若你需要极致性能，例如实现算法，可以考虑这个库：[ahash](https://github.com/tkaitchuck/ahash)

先将 `Vec` 转为迭代器，接着通过 `collect` 方法，将迭代器中的元素收集后，转成 `HashMap:`

`into_iter` 方法将列表转为迭代器，接着通过 `collect` 进行收集

```rust

#[test]
fn vec_to_hashmap_test() {
    let teams_list = vec![
        ("中国队".to_string(), 100),
        ("美国队".to_string(), 10),
        ("日本队".to_string(), 50),
    ];

    let mut teams_map = HashMap::new();
    for team in &teams_list {
        teams_map.insert(&team.0, &team.1);
    }
    println!("{:?}", teams_map);

    // 先将 Vec 转为迭代器，接着通过 collect 方法，将迭代器中的元素收集后，转成 HashMap
    let teams_map2: HashMap<_, _> = teams_list.into_iter().collect();

    println!("{:?}", teams_map2);
}
```

## 代码参考

{% embed url="https://github.com/cracker8090/rust_tutorial/blob/main/basic_lib/src/my_string.rs" %}
