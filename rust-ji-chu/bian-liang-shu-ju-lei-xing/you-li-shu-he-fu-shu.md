# 有理数和复数

有理数和复数并未包含在标准库中：

* 有理数和复数
* 任意大小的整数和任意精度的浮点数
* 固定精度的十进制小数，常用于货币相关的场景

社区已经开发出高质量的 Rust 数值库：[num](https://crates.io/crates/num)

在 `Cargo.toml` 中的 `[dependencies]` 下添加一行 `num = "0.4.0"`

```rust
fn main() {
    let a = Complex { re: 2.1, im: -1.2 };
    let b = Complex::new(11.1, 22.2);
    let result = a + b;
    println!("{} + {}i", result.re, result.im);
}
// 13.2 + 21i
```
