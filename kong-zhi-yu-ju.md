# 控制语句

## if

Rust 语言中的布尔判断条件不必使用小括号包裹，且每个条件后面都跟着一个代码块。

## 循环

Rust提供了3种循环：loop、while和for

for用的最多，安全快捷。

range，rev反转Range

### loop for

嵌套与标签

在处理嵌套循环的时候可以 `break` 或 `continue` 外层循环。在这类情形中，循环必须用一些 `'label`（标签）来注明，并且标签必须传递给 `break`/`continue` 语句。

`while` 关键字可以用作当型循环（当条件满足时循环）。



`for in` 结构可以遍历一个 `Iterator`（迭代器）。创建迭代器的一个最简单的方法是使用区间标记 `a..b`。

for n in 1..101

或者，可以使用`a..=b`表示两端都包含在内的范围

for n in 1..=100



如果想在循环中，**修改该元素**，可以使用 `mut` 关键字

| 使用方法                          | 等价使用方式                                            | 所有权   |
| ----------------------------- | ------------------------------------------------- | ----- |
| `for item in collection`      | `for item in IntoIterator::into_iter(collection)` | 转移所有权 |
| `for item in &collection`     | `for item in collection.iter()`                   | 不可变借用 |
| `for item in &mut collection` | `for item in collection.iter_mut()`               | 可变借用  |

比较

```rust
#![allow(unused)]
fn main() {
// 第一种
let collection = [1, 2, 3, 4, 5];
for i in 0..collection.len() {
  let item = collection[i];
  // ...
}

// 第二种
for item in collection {

}
}
```

第一种方式是循环索引，然后通过索引下标去访问集合，第二种方式是直接循环集合中的元素，优劣如下：

* **性能**：第一种使用方式中 `collection[index]` 的索引访问，会因为边界检查(Bounds Checking)导致运行时的性能损耗 —— Rust 会检查并确认 `index` 是否落在集合内，但是第二种直接迭代的方式就不会触发这种检查，因为编译器会在编译时就完成分析并证明这种访问是合法的
* **安全**：第一种方式里对 `collection` 的索引访问是非连续的，存在一定可能性在两次访问之间，`collection` 发生了变化，导致脏数据产生。而第二种直接迭代的方式是连续访问，因此不存在这种风险（这里是因为所有权吗？是的话可能要强调一下）
