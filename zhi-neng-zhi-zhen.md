# 智能指针

## 介绍

通过`Box`在堆中分配内存空间，而不是`new`和`delete`。标准库中还定义了其他#的一些[智能指针](https://doc.rust-lang.org/book/ch15-00-smart-pointers.html)，如果你需要使用引用数量或者弱引用时就可以直接使用。

常见指针是引用 &，除了引用数据没有任何其他特殊功能，它们也没有任何额外开销，所以应用得最多。

**智能指针**（_smart pointers_）是一类数据结构，表现类似指针，但是也拥有额外的元数据和功能。Rust 标准库中不同的智能指针提供了多于引用的额外功能。

在 Rust 中，普通引用和智能指针的一个额外的区别是引用是一类只借用数据的指针；相反，在大部分情况下，智能指针 **拥有** 他们指向的数据。

> 智能指针：\*显示解引用；.号自动解引用。
>
> **内部可变性**（_interior mutability_）模式，这是不可变类型暴露出改变其内部值的 API。
>
> **引用循环**（_reference cycles_）会如何泄漏内存，以及如何避免。

智能指针往往是基于结构体实现，它与我们自定义的结构体最大的区别在于它实现了 `Deref` 和 `Drop` trait：

* `Deref` trait 可以让智能指针像引用那样工作，这样你就可以写出同时支持智能指针和引用的代码，例如 `*T`
* `Drop` trait 允许你指定智能指针超出作用域后自动执行的代码，例如做一些数据清除等收尾工作

标准库几个最常用智能指针：

* `Box<T>`，可以将值分配到堆上
* `Rc<T>`，引用计数智能指针，允许多所有权存在，当没有任何所有者时负责清理数据，只读，非线程安全，Arc\<T>。
* `Ref<T>` 和 `RefMut<T>`，通过 `RefCell<T>` 访问。（ `RefCell<T>` 是一个在运行时而不是在编译时执行借用规则的类型）。允许将借用规则检查从编译期移动到运行期进行。

标准库几个最常用智能指针：

* `Box<T>`，可以将值分配到堆上
* `Rc<T>`，引用计数智能指针，允许多所有权存在，当没有任何所有者时负责清理数据，只读，非线程安全，ARC
* `Ref<T>` 和 `RefMut<T>`，通过 `RefCell<T>` 访问。（ `RefCell<T>` 是一个在运行时而不是在编译时执行借用规则的类型）。允许将借用规则检查从编译期移动到运行期进行。

Box、、(Vec、String)、(Cell、RefCell)、(Rc、Arc)、RWLock、Mutex。

Arc、RWLock、Mutex是线程安全的。

(Cell(Copy,get性能)、RefCell)——借用规则，但是还是想要多个可变引用，内部可变引用。

Cell，RefCell是在UnsafeCell基础之上构建的。

## Box\<T>

实现了deref和drop trait，box 允许你将一个值放在堆上而不是栈上。留在栈上的则是指向堆数据的指针。box 没有性能损失，不过也没有很多额外的功能。

堆栈的性能比较：

* 小型数据，在栈上的分配性能和读取性能都要比堆上高
* 中型数据，栈上分配性能高，但是读取性能和堆上并无区别，因为无法利用寄存器或 CPU 高速缓存，最终还是要经过一次内存寻址
* 大型数据，只建议在堆上分配和使用

总之，栈的分配速度肯定比堆上快，但是读取速度往往取决于你的数据能不能放入寄存器或 CPU 高速缓存。 因此不要仅仅因为堆上性能不如栈这个印象，就总是优先选择栈，导致代码更复杂的实现。

多用于如下场景：

* 类型的大小在编译期无法确定，但是我们又需要固定大小的类型时
  * [“box 允许创建递归类型”](https://kaisery.github.io/trpl-zh-cn/ch15-01-box.html#box-%E5%85%81%E8%AE%B8%E5%88%9B%E5%BB%BA%E9%80%92%E5%BD%92%E7%B1%BB%E5%9E%8B)
* 当有大量数据并希望在确保数据不被拷贝的情况下转移所有权的时候
  * 转移大量数据的所有权可能会花费很长的时间，因为数据在栈上进行了拷贝。为了改善这种情况下的性能，可以通过 box 将这些数据储存在堆上。接着，只有少量的指针数据在栈上被拷贝。
* 当希望拥有一个值并只关心它的类型是否实现了特定 trait 而不是其具体类型的时候
  * **trait 对象**。[“顾及不同类型值的 trait 对象”](https://kaisery.github.io/trpl-zh-cn/ch17-02-trait-objects.html#%E4%B8%BA%E4%BD%BF%E7%94%A8%E4%B8%8D%E5%90%8C%E7%B1%BB%E5%9E%8B%E7%9A%84%E5%80%BC%E8%80%8C%E8%AE%BE%E8%AE%A1%E7%9A%84-trait-%E5%AF%B9%E8%B1%A1)

### Box实现递归

Rust 需要在编译时知道类型占用多少空间。一种无法在编译时知道大小的类型是 **递归类型**（_recursive type_），其值的一部分可以是相同类型的另一个值。

`Box<T>` 是一个指针，我们总是知道它需要多少空间：指针的大小并不会根据其指向的数据量而改变。usize

```rust
enum List {
    Cons(i32, Box<List>),
    Nil,
}

use crate::List::{Cons, Nil};

fn main() {
    let list = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(Nil))))));
}
```

`Cons` 成员将会需要一个 `i32` 的大小加上储存 box 指针数据的空间。`Nil` 成员不储存值，所以它比 `Cons` 成员需要更少的空间。现在我们知道了任何 `List` 值最多需要一个 `i32` 加上 box 指针数据的大小。box 只提供了间接存储和堆分配。

### deref

```rust
let x = 5;
let y = &x;
assert_eq!(5, *y);

// 智能指针解引用
let x = Box::new(1);
let sum = *x + 1;

// 自定义智能指针 *(y.deref())
use std::ops::Deref;
impl<T> Deref for MyBox<T> {
    type Target = T;
    fn deref(&self) -> &Self::Target {
        &self.0
    }
}
```

需要注意的是，`*` 不会无限递归替换，从 `*y` 到 `*(y.deref())` 只会发生一次，而不会继续进行替换然后产生形如 `*((y.deref()).deref())` 的怪物。

Rust 还支持将一个可变的引用转换成另一个可变的引用以及将一个可变引用转换成不可变的引用，规则如下：

* 当 `T: Deref<Target=U>`，可以将 `&T` 转换成 `&U`，也就是我们之前看到的例子
* 当 `T: DerefMut<Target=U>`，可以将 `&mut T` 转换成 `&mut U`
* 当 `T: Deref<Target=U>`，可以将 `&mut T` 转换成 `&U`

`Deref` 可以说是 Rust 中最常见的隐式类型转换，而且它可以连续的实现如 `Box<String> -> String -> &str` 的隐式转换，只要链条上的类型实现了 `Deref` 特征。

> 可以为自己的类型实现 `Deref` 特征，但是原则上来说，只应该为自定义的智能指针实现 `Deref`。例如，虽然你可以为自己的自定义数组类型实现 `Deref` 以避免 `myArr.0[0]` 的使用形式，但是 Rust 官方并不推荐这么做，特别是在你开发三方库时。

### drop

`Drop` 顺序：

* **变量级别，按照逆序的方式**，`_x` 在 `_foo` 之前创建，因此 `_x` 在 `_foo` 之后被 `drop`
* **结构体内部，按照顺序的方式**，结构体 `_x` 中的字段按照定义中的顺序依次 `drop`

std::mem:drop，可以提前释放。

BOX背后是调用 `jemalloc` 来做内存管理，所以堆上的空间无需我们的手动管理。**一切皆对象 = 一切皆 Box。**

## Rc\<T> Arc\<T>

引用计数智能指针，多重所有权，需要预导入。`Rc` 和 `Arc`，前者适用于单线程，后者适用于多线程。只读。

Rust 所有权机制要求一个值只能有一个所有者，在大多数情况下，都没有问题，但是考虑以下情况：

* 在图数据结构中，多个边可能会拥有同一个节点，该节点直到没有边指向它时，才应该被释放清理
* 在多线程中，多个线程可能会持有同一个数据，但是你受限于 Rust 的安全机制，无法同时获取该数据的可变引用

```rust
let s = String::from("hello, world");
    // s在这里被转移给a
    let a = Box::new(s);
    // 报错！此处继续尝试将 s 转移给 b
    let b = Box::new(s);
use std::rc::Rc;
let a = Rc::new(String::from("hello, world"));
let b = Rc::clone(&a);
assert_eq!(2, Rc::strong_count(&a));
assert_eq!(Rc::strong_count(&a), Rc::strong_count(&b))
```

智能指针 `Rc<T>` 在创建时，还会将引用计数加 1，此时获取引用计数的关联函数 `Rc::strong_count` 返回的值将是 `1`。

不要被 `clone` 字样所迷惑，以为所有的 `clone` 都是深拷贝。这里的 `clone` **仅仅复制了智能指针并增加了引用计数，并没有克隆底层数据**，因此 `a` 和 `b` 是共享了底层的字符串 `s`，这种**复制效率是非常高**的。当然你也可以使用 `a.clone()` 的方式来克隆，但是从可读性角度，我们更加推荐 `Rc::clone` 的方式。

通过clone方法共享所有权的引用称作**强引用，**Rust还为我们提供了另一种智能指针Weak，你可以把它当作是Rc的另一个版本。它提供的引用属于**弱引用**。它共享的指针没有所有权。但他可以帮助我们有效的避免循环引用。

Leak：**需要一个在运行期初始化的值，但是可以全局有效，也就是和整个程序活得一样久**，那么就可以使用 `Box::leak`，例如有一个存储配置的结构体实例，它是在运行期动态插入内容，那么就可以将其转为全局有效，虽然 `Rc/Arc` 也可以实现此功能，但是 `Box::leak` 是性能最高的。

**总结：**

* `Rc/Arc` 是不可变引用，你无法修改它指向的值，只能进行读取，如果要修改，需要配合后面章节的内部可变性 `RefCell` 或互斥锁 `Mutex`
* 一旦最后一个拥有者消失，则资源会自动被回收，这个生命周期是在编译期就确定下来的
* `Rc` 只能用于同一线程内部，想要用于线程之间的对象共享，你需要使用 `Arc`
* `Rc<T>` 是一个智能指针，实现了 `Deref` 特征，因此你无需先解开 `Rc` 指针，再使用里面的 `T`，而是可以直接使用 `T`，例如上例中的 `gadget1.owner.name`

## RefCell 单线程

内部可变性RefCell，Refcell所有的变量可以改变属性把不可变引用变为可变引用。对于Refcell修饰的变量就不会有编译器的引用检查了。

运行时检查，只能用于单线程程序，只有一个所有者。

Refcell允许在运行期可变引用value，所以即使是Refcell是不可变的，我们也可以改变value。（**内部可变性**）

```rust
struct User {
    id: i32,
    name: str,
    age: u8,
}
```

通常情况下，只能修改一个人的名称或者年龄，而不能修改用户的id。如果我们把User的实例设置成了可变状态，那就不能保证别人不会去修改id。

Rust为我们提供了`Cell<T>`和`RefCell<T>`。它们本质上不属于智能指针，而是可以提供内部可变性的容器。内部可变性实际上是一种设计模式，它的内部是通过一些`unsafe`代码来实现的。

```rust
struct User {
    id: u32,
    age: Cell<u32>,
}
fn main(){
    let foo = User {
        id: 1,
        age: Cell::new(3),
    };
    assert_eq!(1, foo.id);
    assert_eq!(3, foo.age.get());
    foo.age.set(5);
    assert_eq!(5, foo.age.get());
}
```

### `Cell 单线程`

`Cell<T>`中包裹的T必须要实现Copy才能够使用get方法，如果没有实现Copy，则需要使用Cell提供的get\_mut方法来返回可变借用，而set方法在任何情况下都可以使用。由此可见Cell并没有违反借用规则。

对于没有实现Copy的类型，使用`Cell<T>`还是比较不方便的，还好Rust还提供了`RefCell<T>`。

```rust
use std::cell::RefCell;
fn main() {
    let x = RefCell::new(vec![1, 2, 3]);
    println!("{:?}", x.borrow());
    x.borrow_mut().push(5);
    println!("{:?}", x.borrow());
}

```

`RefCell<T>`的borrow\_mut和borrow方法对应了`Cell<T>`中的set和get方法。

`RefCell<T>`和`Cell<T>`还有一点区别是：`Cell<T>`没有运行时开销（不过也不要用它包裹大的数据结构），而`RefCell<T>`是有运行时开销的，这是因为使用`RefCell<T>`时需要维护一个借用检查器，如果违反借用规则，则会引起线程恐慌。

## 其他

内部可变性的 `RefCell<T>` 类型以及互斥锁 `Mutex<T>`，在多线程编程中，`Arc` 跟 `Mutext` 锁的组合使用非常常见，它们既可以让我们在不同的线程中共享数据，又允许在各个线程中对其进行修改。

`Rc` 和 `Arc` 的区别在于，后者是原子化实现的引用计数，因此是线程安全的，可以用于多线程中共享数据。

这两者都是只读的，如果想要实现内部数据可修改，必须配合内部可变性 `RefCell` 或者互斥锁 `Mutex` 来一起使用。

Box在编译时检查，RefCell在运行时检查，都只能用于单线程程序

将所有权、借用规则与这些智能指针做一个对比：

| Rust 规则            | 智能指针带来的额外规则              |
| ------------------ | ------------------------ |
| 一个数据只有一个所有者        | `Rc/Arc`让一个数据可以拥有多个所有者   |
| 要么多个不可变借用，要么一个可变借用 | `RefCell`实现编译期可变、不可变引用共存 |
| 违背规则导致**编译错误**     | 违背规则导致**运行时`panic`**     |

## 组合

要实现一个同时存在多个不同所有者，但每个所有者又可以随时修改其内容，且这个内容类型 `T` 没有实现 `Copy` 的情况该怎么办？使用 `Rc<T>` 可以满足第一个要求，但是由于其是不可变的，要修改内容并不可能；使用 `Cell<T>` 直接死在了 `T` 没有实现 `Copy` 上；使用 `RefCell<T>` 由于无法满足多个不同所有者的存在，也无法实施。可以看到各个智能指针可以解决其中一个问题，既然如此，为何我们不把 `Rc<T>` 与 `RefCell<T>` 组合起来使用。

```rust
use std::rc::Rc;
use std::cell::RefCell;

fn main() {
    let shared_vec: Rc<RefCell<_>> = Rc::new(RefCell::new(Vec::new()));
    // Output: []
    println!("{:?}", shared_vec.borrow());
    {
        let b = Rc::clone(&shared_vec);
        b.borrow_mut().push(1);
        b.borrow_mut().push(2);
    }
    shared_vec.borrow_mut().push(3);
    // Output: [1, 2, 3]
    println!("{:?}", shared_vec.borrow());
}
```

参考：[Rust 中几个智能指针的异同与使用场景](https://rustcc.cn/article?id=ac75148b-6eb0-4249-b36d-0a14875b736e)

[Rust：智能指针](https://zhuanlan.zhihu.com/p/125770192)
