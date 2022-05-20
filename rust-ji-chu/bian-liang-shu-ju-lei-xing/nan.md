# NaN

对于数学上未定义的结果，例如对负数取平方根 `-42.1.sqrt()` ，会产生一个特殊的结果：Rust 的浮点数类型使用 `NaN` (not a number)来处理这些情况。

所有跟 `NaN` 交互的操作，都会返回一个 `NaN`，而且 `NaN` 不能用来比较

可以使用 `is_nan()` 等方法，可以用来判断一个数值是否是 `NaN`。

```rust
fn main() {
    let x = (-42.0_f32).sqrt();
    if x.is_nan() {
        println!("未定义的数学行为")
    }
}
```
