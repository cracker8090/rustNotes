# 字符串-切片-数组-元组

## `字符串切片`

`String` 是定义在标准库中的类型，分配在堆上，可以动态的增长。它的底层存储是动态字节数组的方式( `Vec<u8>` )，但是与字节数组不同，`String` 是 `UTF-8` 编码。

只能将 `String` 跟 `&str` 类型进行拼接，并且 `String` 的所有权在此过程中会被 move。

可以使用 `String::from` 或 `to_string` 将 `&str` 转换成 `String` 类型

## 数组

数组的类型是 `[T; Length]`，数组的长度是类型签名的一部分，因此数组的长度必须在编译期就已知。

* 数组中的所有元素必须是同一类型
* 数组的下标索引从 0 开始，越界索引会导致代码的 `panic`

## 元组

元组中的元素可以是不同的类型。元组的类型签名是 `(T1, T2, ...)`, 这里 `T1`, `T2` 是相对应的元组成员的类型。

* 可以使用索引来获取元组的成员
* 过长的元组无法被打印输出
* 元组可以用于函数的参数和返回值

```rust
fn sum_multiply(nums: (i32, i32)) -> (i32, i32) {
    (nums.0 + nums.1, nums.0 * nums.1)
}
```

## 代码参考

{% embed url="https://github.com/cracker8090/rust_tutorial/blob/main/basic_lib/src/my_string.rs" %}
