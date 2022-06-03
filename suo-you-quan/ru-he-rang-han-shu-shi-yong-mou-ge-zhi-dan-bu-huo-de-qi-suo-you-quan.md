# 如何让函数使用某个值，但不获得其所有权

## 引用 reference

&代表的就是引用 语义，它们允许你在不获取所有权的前提下使用值

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);
    println!("The length of '{}' is {}.",s1,len);
}

fn calculate_length(s:&String) -> usize {
    s.len()
}
```

## **借用**

把引用作为函数参数的这个行为叫做借用

不可以修改借用的东西

引用默认也是不可变的

## **可变引用**

有一个很大的限制：对于特定作用域中的特定数据来说，一次只能声明一个可变引用

```
fn main() {
    let mut s1 = String::from("hello");

    let len = calculate_length(&mut s1);
    println!("The length of '{}' is {}.",s1,len);
}

fn calculate_length(s: &mut String) -> usize {
    s.push_str(",world");
    s.len()
}
```

在编译时避免数据竞

数据竞争 （data race）与竞态条件十分类似，它会在指令满足以下3种情形时发生：

* 两个或两个以上的指针同时访问同一空间。
* 其中至少有一个指针会向空间中写入数据。
* 没有同步数据访问的机制。

let mut s = String::from("hello");

let r1 = \&mut s; let r2 = \&mut s; // 错误

**可以通过创建新的作用域，来允许非同时的创建多个可变引用**

let mut s = String::from("hello");

{ let r1 = \&mut s;

}

let r2 = \&mut s

**不可以同时拥有一个可变引用和一个不变引用**

```rust
    let mut s = String::from("Hello");
    let r1 = &s;
    let r2 = &s;
    let s1 = &mut s; // 错误
```

## 悬空引用

这类指针指向曾经存在的某处内存地址，但该内存已经被释放掉甚至是被重新分配另作他用了。在Rust语言中，编译器会确保引用永远不会进入这种悬垂状态。

```rust
fn main()
{ 
    let reference_to_nothing = dangle(); 
} 
fn dangle() -> &String { 
    let s = String::from("hello"); 
    &s // 悬空
}
```

**引用规则：**

* 任何时刻只能满足下列条件之一：
  * 一个可变引用
  * 任意数量不可变引用
* 引用必须一直有效
