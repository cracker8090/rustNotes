# Rust VS C

[Learn Rust the Dangerous Way](https://cliffle.com/p/dangerust/)

### head

```c
#include <stdint.h>
#include <stdalign.h>
#include <immintrin.h>
#include <math.h>
#include <stdio.h>
```

```rust
use std::mem;
use std::arch::x86_64::*;
use std::f64::consts::PI;
```

### strcut

```c
// intptr_t should be the native integer type on most 
// sane systems.
typedef intptr_t intnative_t;

typedef struct{
    double position[3], velocity[3], mass;
} body;
```

```rust
// Note 1
#![allow(
    non_upper_case_globals,
    non_camel_case_types,
    non_snake_case,
)]

#[repr(C)]  // Note 2
struct body {
    position: [f64; 3],
    velocity: [f64; 3],
    mass: f64,
}
```

此`allow` 属性（类似于 a `#pragma`）通知编译器我们希望允许偏离命名约定

`#[repr(C)]`标记`struct`要求 Rust 布局`struct` _与 C 完全相同_ ,没有的话它会优化它们以获得最佳的打包和对齐——这可能是你以前手工完成的。

```c
#define SOLAR_MASS (4*M_PI*M_PI)
#define DAYS_PER_YEAR 365.24
#define BODIES_COUNT 5
```

```rust
// Note 1
const SOLAR_MASS: f64 = 4. * PI * PI;
const DAYS_PER_YEAR: f64 = 365.24;
const BODIES_COUNT: usize = 5;
```

`#define`s 可以被 Rust 替换`const`，它声明了一个常量

### 函数

```c
static void offset_Momentum(body bodies[]){

    for(intnative_t i=0; i<BODIES_COUNT; ++i)
        for(intnative_t m=0; m<3; ++m)
            bodies[0].velocity[m]-=
              bodies[i].velocity[m]*bodies[i].mass/SOLAR_MASS;



}
```

```rust
// v------------------------------------ Note 1
unsafe fn offset_Momentum(bodies: *mut body) {
// ^                               ^---- Note 2
    for i in 0..BODIES_COUNT {  // <---- Note 3
        for m in 0..3 {
            (*bodies.add(0)).velocity[m] -=  // <- Note 4
                (*bodies.add(i)).velocity[m] // <- Note 5
                * (*bodies.add(i)).mass / SOLAR_MASS;
        }
    }
}
```

在 C 中，`bodies[i]`完全一样`*(bodies + i)`——它执行指针算术和解引用，仅此而已。特别是，它假定您有理由知道这`i`是数组的有效索引。这是 C 语言中的常见情况，可以使用方括号进行速记。

在 Rust 中，常见用例使用_引用_而不是指针[3](https://cliffle.com/p/dangerust/1/#ref)并进行边界检查，因此_用_例得到简写`bodies[i]`。做未经检查的指针算术是例外而不是规则，因此我们必须更加刻意地表达它。

最后，我们必须写`*bodies.add(i)`而不是`*(bodies + i)`因为 Rust 不会为指针重载算术运算符。相反，指针提供了`add`、`sub`和其他在代码审查中更容易发现的操作；如果您好奇，[这里是完整列表。](https://doc.rust-lang.org/std/primitive.pointer.html#methods)

```c
static void advance(body bodies[]){

    #define INTERACTIONS_COUNT \
        (BODIES_COUNT*(BODIES_COUNT-1)/2)
    #define ROUNDED_INTERACTIONS_COUNT \
        (INTERACTIONS_COUNT+INTERACTIONS_COUNT%2)

    //  ,--------------------------------- Note 1
    // v       v-------------------------- Note 2
    static alignas(__m128d) double
      position_Deltas[3][ROUNDED_INTERACTIONS_COUNT],
      magnitudes[ROUNDED_INTERACTIONS_COUNT];
```

```rust
unsafe fn advance(bodies: *mut body) {

    const INTERACTIONS_COUNT: usize =
        BODIES_COUNT * (BODIES_COUNT - 1) / 2;
    const ROUNDED_INTERACTIONS_COUNT: usize =
        INTERACTIONS_COUNT + INTERACTIONS_COUNT % 2;

    // Note 1
    #[repr(align(16))]
    #[derive(Copy, Clone)]
    struct Align16([f64; ROUNDED_INTERACTIONS_COUNT]);

    // Note 2
    static mut position_Deltas: [Align16; 3] =
        [Align16([0.; ROUNDED_INTERACTIONS_COUNT]); 3];
    static mut magnitudes: Align16 =
        Align16([0.; ROUNDED_INTERACTIONS_COUNT]);
```
