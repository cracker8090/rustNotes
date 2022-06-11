# 多线程



* 消息传递channel：（线程间，sync-try\_send带反馈，两套相互发送）。类似go语言 channel，被用来在线程间传递消息。mpsc::channel (多个生产者，一个消费者)，返回一个元组（snd，rec）。
* 共享状态：多个线程可以访问同一片数据。mutex（互斥锁，配合线程多所有权ARC），实现共享，try\_lock健壮性。



### 消息传递

当发送者或接收者任一被丢弃时可以认为信道被 **关闭**（_closed_）了。

使用信道来实现聊天系统，或利用很多线程进行分布式计算并将部分计算结果发送给一个线程进行聚合。

一个信道可以有多个产生值的 **发送**（_sending_）端，但只能有一个消费这些值的 **接收**（_receiving_）端。想象一下多条小河小溪最终汇聚成大河：所有通过这些小河发出的东西最后都会来到下游的大河。

try\_recv 立刻返回一个 `Result<T, E>`：`Ok` 值包含可用的信息，而 `Err` 值代表此时没有任何消息。编写一个循环来频繁调用 `try_recv`，在有可用消息时进行处理，其余时候则处理一会其他工作直到再次检查。

`send` 函数获取其参数的所有权并移动这个值归接收者所有。

异步的、无限缓冲的通道（channel）

同步的、有边界的通道（channel）。sync\_channel 函数将返回一个(SyncSender, Receiver) 。所有的发送都将通过阻塞来同步，直到有可用的缓冲区空间。请注意，允许边界为0，这将使该通道成为一个 “会合 “通道，每个发送方都会将消息原子化地交给接收方。

### 共享状态

互斥器Mutex，任意时刻，一次只允许一个线程访问数据。为了访问互斥器中的数据，线程首先需要通过获取互斥器的 **锁**（_lock_）来表明其希望访问数据。锁是一个作为互斥器一部分的数据结构，它记录谁有数据的排他访问权。

互斥器以难以使用著称，因为你不得不记住：

1. 在使用数据之前尝试获取锁。
2. 处理完被互斥器所保护的数据之后，必须解锁数据，这样其他线程才能够获取锁。

正确的管理互斥器异常复杂，这也是许多人之所以热衷于信道的原因。然而，在 Rust 中，得益于类型系统和所有权，我们不会在锁和解锁上出错。

```rust
fn test_mutex(){
    let m = Mutex::new(5);
    {
        let mut num = m.lock().unwrap();
        *num = 6;
    }
    println!("m = {:?}",m);
}
```

`new` 来创建一个 `Mutex<T>`。使用 `lock` 方法获取锁，以访问互斥器中的数据。这个调用会阻塞当前线程，直到我们拥有锁为止。

如果另一个线程拥有锁，并且那个线程 panic 了，则 `lock` 调用会失败。一旦获取了锁，就可以将返回值（在这里是`num`）视为一个其内部数据的可变引用了。`Mutex<i32>` 并不是一个 `i32`，所以 **必须** 获取锁才能使用这个 `i32` 值。

`Mutex<T>` 是一个智能指针，`lock` 调用 **返回** 一个叫做 `MutexGuard` 的智能指针，这个智能指针实现了 `Deref` 来指向其内部数据；其也提供了一个 `Drop` 实现当 `MutexGuard` 离开作用域时自动释放锁。不会忘记释放锁并阻塞互斥器为其它线程所用的风险，因为锁的释放是自动发生的。

```rust
fn test_share_mutex(){
    // let counter = Rc::new(Mutex::new(0));
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();
            *num += 1;
        });
        handles.push(handle);
    }

    for  handle in handles {
        handle.join().unwrap();
    }

    println!("Result:{}",*counter.lock().unwrap());
}
```

有两个并发概念是内嵌于语言中的：`std::marker` 中的 `Sync` 和 `Send` trait。

*   send允许多线程间转移所有权

    `Send` 标记 trait 表明实现了 `Send` 的类型值的所有权可以在线程间传送。`Rc<T>` 的值并尝试将克隆的所有权转移到另一个线程，这两个线程都可能同时更新引用计数。

    任何完全由 `Send` 的类型组成的类型也会自动被标记为 `Send`。几乎所有基本类型都是 `Send` 的，除了裸指针（raw pointer）。
*   sync允许多线程访问

    `Sync` 标记 trait 表明一个实现了 `Sync` 的类型可以安全的在多个线程中拥有其值的引用。对于任意类型 `T`，如果 `&T`（`T` 的不可变引用）是 `Send` 的话 `T` 就是 `Sync` 的，这意味着其引用就可以安全的发送到另一个线程。类似于 `Send` 的情况，基本类型是 `Sync` 的，完全由 `Sync` 的类型组成的类型也是 `Sync` 的。

    智能指针 `Rc<T>` 也不是 `Sync` 的，出于其不是 `Send` 相同的原因。`RefCell<T>`和 `Cell<T>` 系列类型不是 `Sync` 的。`RefCell<T>` 在运行时所进行的借用检查也不是线程安全的。`Mutex<T>` 是 `Sync` 的。

手动实现Send和Sync是不安全的

### 线程池

可供探索的方法有 fork/join 模型和单线程异步 I/O 模型

Mutex+channel

channel 其实是一个 multi-producer，single-consumer 的结构，也就是我们俗称的 MPSC

对于线程池来说，需要的是一个 MPMC 的 channel。如果我们给 channel 的 Receiver 套上一个 Mutex，在加上 Arc，其实就可以了。Mutex 能保证多个线程同时只能有一个线程抢到 lock，然后从队列里面拿到数据。而加上 Arc 主要是能在多个线程共享了



### 异步编程

*   Asynchronous Programming in Rust

    : Rust官方的 async book

    * [中文翻译版本](https://huangjj27.github.io/async-book/index.html)
    * [Rust 异步编程](https://learnku.com/docs/async-book/2018): 另一个翻译版本
* [async-std 中文文档](https://learnku.com/docs/rust-async-std/)

### 参考

[Rust 并发编程 - Thread Pool](https://www.jianshu.com/p/f4d853c0ef1e)

[火焰图使用](https://www.jianshu.com/p/3cdc0f05ac5d)，OpenResty 的章亦春介绍[动态追踪技术漫谈](https://link.jianshu.com/?t=https://openresty.org/posts/dynamic-tracing/)。CPU,memory,Off CPU（统的性能上不去，但 CPU 也很闲，这种的很大可能都是在等 IO ，或者 lock 这些的了，所以我们需要看到底 CPU 等在什么地方。）， diff 火焰图。[FlamGraph](https://github.com/brendangregg/FlameGraph)。
