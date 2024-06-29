# 1 介绍



## 1.1 Rust特点

1. 具有独一无二的所有权机制，可以防止悬垂引用，并发中产生竞争
2. 没有垃圾回收，具有高性能
3. 零成本抽象
4. 完备的函数式编程，如模式匹配，高阶函数，闭包，迭代



## 1.2 安装

[Rust官网安装](https://www.rust-lang.org/)：一般会直接安装`Rustup`

1.Stable：这是最稳定和可靠的版本，适用于大多数生产环境的应用程序。Rust的稳定版经过了广泛测试和验证，确保了向后兼容性，这意味着你编写的代码在未来的稳定版本中仍然可以运行。

2.Nightly：这是每天构建的最新版本，包含最新的功能和实验性质的特性。夜版是Rust的开发版本，通常包含最新的语言特性和实验性质的改进，但也可能包含一些不稳定的内容。因此，它不适用于生产环境，但可以用于尝试最新的语言功能或为Rust的发展做贡献。



`Rustup`的安装与使用：

- 更新rust： `Rustrustup update`
- 卸载：`rustup self uninstall`
- 添加组件 ：`rustup component add rustfmt`
- 查看版本： `rustup --version`
- 安装rust：`rustup install stable/nightly`
- 切换rust：`rustup default stable/ngihtly`
- 帮助：`rustup --help`



vscode插件

- rust-analyzer

- Error Lens





## 1.3 rust包管理工具Cargo

隐式地使用rustc进行编译

**命令：**

**创建**：`cargo new project_name`

`cargo new --lib project_name `创建一个新的 Rust 库项目的

**构建项目** (生成二进制可执行文件或库文件)：`cargo build`

`cargo build--release`为生成优化的可执行文件，常用于生产环境

**检测**：`cargo check`

**运行/测试**：`cargo run/cargo test`



## 1.4 项目结构

![image-20240628000841742](./assets/image-20240628000841742.png)

对于Cargo.toml文件：

> package
>
> - 设置项目名
> - 版本
>
> dependencies
>
> - 设置依赖
> - [build-dependencies]列出了在构建项目时需要的依赖项
> - [dev-dependencies]列出了只在开发时需要的依赖项



## 1.5 如何获取Rust的库和国内源

Rust第三方库：[crates.io: Rust Package Registry](https://crates.io/)

更推荐安装Cargo插件 cargo-edit

> - 安装
>
>   - `cargo install cargo-edit`
>
> - 添加库
>
>   - `cargo add dependency_name`
>
>   - 安装指定版本
>
>     - `cargo add dependency_name@1.2.3`
>
>   - 添加开发时用的依赖库
>     - `cargo add --dev dev_dependency_name`
>   - 添加构建时用的依赖库
>     - `cargo add --build build_dependency_na-me`
> - 删除库
>   `cargo rm dependency_name`



国内源：

推荐[RsProxy](https://rsproxy.cn/)

windows进入`./cargo/config`

```toml
[source.crates-io]
replace-with = 'rsproxy-sparse'
[source.rsproxy]
registry = "https://rsproxy.cn/crates.io-index"
[source.rsproxy-sparse]
registry = "sparse+https://rsproxy.cn/index/"
[registries.rsproxy]
index = "https://rsproxy.cn/crates.io-index"
[net]
git-fetch-with-cli = true
```







## 1.6 开发规则

变量命名：小写下划线`let nice_count = 100;`







# 2 语言基础

## 2.1 变量与常见数据类型

### 2.1.1 变量与不可变性

1. 在 Rust 中声明变量时，使用 `let` 关键字。

2. Rust 支持类型推导，但你也可以显式指定变量的类型：  

   ```rust
   let x: i32 = 5; // 显式指定 x 的类型为 i32
   ```

3. 变量名蛇形命名法（Snake Case）：**小写加下划线**，而枚举和结构体命名使用帕斯卡命名法（Pascal Case）：小驼峰。如果变量没有用到可以前置下划线，消除警告。

4. 强制类型转换 (Casting a Value to a Different Type)：  

   ```rust
   let a = 3.1;
   let b = a as i32;
   ```

5. 打印变量 (`{}` 与 `{:?}` 需要实现特质之后章节会介绍，基础类型默认实现)：  

   ```rust
   println!("val: {}", x);
   println!("val: {:?}", x);
   ```

**Rust中的变量是默认不可变的**

> 不可变性是Rust实现其可靠性和安全性目标的关键
>
> 它迫使程序员更深入地思考程序状态的变化，并明确哪些部分的程序状态可能会发生变化的
>
> 不可变性有助于防止一类常见的错误，如数据竞争和并发问题

**使用mut关键字进行可变声明**

如果你希望一个变量是可变的，你需要使用mut关键字进行明确声明

```rust
let mut y=10；//可变变量
y = 20;//合法的修改
```

**Shadowing Variables并不是重新赋值**

>  Rust允许您隐藏一个变量，这意味着您可以声明一个与现有变量同名的新变量，从而有效地隐藏前一个变量。
>
> 作用：
>
> 可以改变值
>
> 可以改变类型
>
> 可以改变可变性

```rust
let a = 10;
let a = 20; // Shadowing Variables
```





### 2.1.2 常量 const 与 静态变量static

#### 2.1.2.1 Const 常量

- 常量的值必须是在编译时已知的常量表达式，必须指定类型与值
- 与C语言的宏定义（宏替换）不同，Rust 的const常量的值被直接嵌入至生成的底层机器代码中，而不是进行简单的字符替换
- 常量的作用域是块级作用域，它们只在声明他们的作用域内可见

#### 2.1.2.2 static静态变量

- 与const常量不同，static 变量是在运行时分配内存的
- 并不是不可变的，可以使用unsafe修改
- 静态变量的生命周期为整个程序的运行时间

#### 2.1.2.3 总结

- 常量名与静态变量命名必须全部大写，单词之间加入下划线









## 2.2 Rust基础数据类型

如果不自动设置类型的话，rust默认的类型是i32
**1、Integer**

- Integer types 默认推断为 i32:
  - i8、i16、i32、i64、i128
- Unsigned Integer Types:
  - u8、u16、u32、u64、u128
- Platform-Specific Integer Type(由平台决定):
  - usize
  - isize

**2、Float**

- Float Types:
  - f32 与 f64
  - 尽量用 f64，除非你清楚边界需要空间

**3、Boolean**

- Boolean Values:
  - true
  - false

**4、Char**

- Character Types:
  - Rust 支持 Unicode 字符
  - 表示 char 类型使用单引号

**5、元组和数组**

- 元组

  > 元组是固定长度的异构集合
  >
  > `EmptyTuple()`：为函数默认返回值
  >
  > 元组获取元素：`tup.index`没有`len ()`

- 数组

  > 组是固定长度的同构集合
  >
  > 创建方式：`[a, b, c]`或者`[value; size]`
  >
  > 获取元素：`arr[index]`
  >
  > 获取长度：`arr.Ien ()`

- 元组与数组异同

> - 相同点
>  1. 元组和数组都是Compound Types，而Vec和Map都是Collection Types
>   2. 元组和数组长度都是固定的
>   3. 可以设置可变
> 
> - Tuples不同类型的数据类型
>
> - Arrays 同一类型的数据类型



**6、String与&str**

- `String`是一个堆分配的可变字符串类型

  源码:
  ```rust
  pub struct String {vec: Vec<u8>}
  ```

- `&str`是指字符串切片引用，是在栈上分配的

  - 不可变引用，指向存储在其他地方的 UTF-8编码的字符串数
  - 由指针和长度构成



## 2.3 引用数据类型



### 2.3.1 move与copy

copy类似值引用，move如下：

在 Rust 编程语言中，`move` 所有权转移是一种机制，当值的所有权从一个变量转移到另一个变量时发生。具体来说，`move` 发生在以下几种情况：

1. **变量赋值**: 当你将一个拥有堆分配数据（如 `String`、`Vec` 或自定义的动态大小类型）的变量赋值给另一个变量时，原始变量的所有权会转移到新变量，原始变量将不再有效。

```
rust   let s1 = String::from("hello");
   let s2 = s1; // s1的所有权转移到s2，s1不能再使用
```

1. **作为参数传递给函数或闭包**: 如果函数或闭包参数采用值传递（即没有 `&` 或 `&mut`），那么调用时传递的变量的所有权会转移到函数内部，函数结束后该值将被清理。

```
rust   fn take_ownership(s: String) {
       println!("{}", s);
   }
   let s = String::from("hello");
   take_ownership(s); // s的所有权转移到take_ownership函数内
```

1. **结构体和枚举的字段**: 当你将拥有堆分配数据的变量放入结构体或枚举时，也会发生所有权转移。
2. **从函数返回拥有堆数据的值**: 函数返回拥有堆数据的值时，该值的所有权会从函数内部转移到调用者。

```
rust   fn returns_string() -> String {
       String::from("hello")
   }
   let s = returns_string(); // 返回值的所有权转移到s
```

`move` 操作的核心在于确保任何时候只有一个变量拥有对某个数据的控制权，这有助于避免数据竞争和内存泄漏，是 Rust 安全和高效内存管理策略的基础。



**copy类型所有权转移**

```rust
let x = 5;
let y = x.clone(); 
```

> 使用`variable.clone()`函数将copy类型的所有权转移给另一个，上面示例就是将x所有权转移给y



### 2.3.2 move类型

1. **字符串 (`String`)**: `String` 类型在堆上分配内存来存储可变长度的字符串数据。当一个 `String` 被赋值给另一个变量或作为参数传递给函数时，所有权会转移。
2. **向量 (`Vec<T>`)**: `Vec<T>` 类似于动态数组，也在堆上分配内存来存储元素。所有权转移规则同样适用。
3. **自定义结构体和枚举**: 当结构体或枚举中包含上述可变大小类型或其他所有权类型时，整个结构体会在所有权转移时受到影响。
4. **Box<T>**: `Box<T>` 是一个智能指针，用于在堆上分配单个值。所有权转移规则适用于 `Box<T>` 实例。
5. **其他堆分配类型**: 任何在堆上分配的类型，或者包含堆分配数据的复合类型，都可能涉及所有权的转移。

**copy类型：**

整数（`i32`, `u8` 等）、浮点数、布尔值和元组（只要元组中的每个元素都是 Copy 类型）



## 2.4 输入输出

有两种输出

```rust
let x = 10;
println!("x : {}", x);
println!("x : {x}");
```

> 两种输出都可



MAX & MIN 打印方式

```rust
println!("u32 max: {}", u32::MAX);
println!("u32 min: {}", i32::MIN);
println!("isize is {} bytes", std::mem::size_of::<isize>()); // 查看数据的字节数


let f1: f32 = 1.23234
println!("Float are {:.2}", f1); // 1.23 打印两位

let tup3 = ();
println!("tup3 {:?}", tup3);
```











# 3 进阶

## 3.1 函数

流程控制与函数
4.1if 流程控制 与 match 模式匹配
2.1 变量与不可变性
4.2 循环 与 break continue 以及与迭代的区别
4.3 函数基础与Copy值参数传递

4.4 函数值参数传递、不可变借用参数传递、可变借用参数传递

4.5 函数返回值与所有权机制
4.6 高阶函数 函数作为参数与返回值







## 3.2 错误处理

5.1错误处理之：Result、Option以及panic!宏

5.2错误处理之：unwrap()与'?'

5.3自定义一个Error类型



## 3.3 Rust的内存管理模型

> 内存分配，内存释放，内存管理单元，堆栈，指针，内存保护，虚拟内存，内存碎片化处理。

> ​	"Stoptheworld"是与垃圾回收（GarbageCollection）相关的术语，它指的是在进行垃圾回收时系统暂停程序的运行。
>
> ​	这个术语主要用于描述一种全局性的暂停，即所有应用线程都被停止，以便垃圾回收器能够安全地进行工作。这种全局性的停止会导致一些潜在的问题，特别是对于需要低延迟和高性能的应用程序。
>
> ​	需要注意的是，并非所有的垃圾回收算法都需要"stop the world"，有一些现代的垃圾回收器采用了一些技术来减小全局停顿的影响，比如并发垃圾回收和增量垃圾回收



**模型：**

- 所有权系统（Ownership System)

- 借用（Borrowing)·

  - 不可变引用 (不可变借用)
  - 可变引用 (可变借用)

- 生命周期（Lifetimes)

- 引用计数 (Reference Counting)



- 所有权系统

1、

```rust
fn get_length(s: String) -> usize {
    println!("{}", s);
    s.len()
}

fn main() {
    let s1 = String::from("vlaue");
    let s1_len = get_length(s1);

    println!("{}", s1_len);
}
```

> 这里面首先和其他语言不同的是s1在get_length()中随着函数被销毁销毁了，类似s1的所有权被传进去了，而不是像其他语言一样创建一个变量啥的，而是直接被销毁了。
>
> 通过usize返回值返回。



```rust
fu dangle() -> &str{}
```

> 这个返回一个引用会报错，因为rust的所有权机制，它不知道改引用的生命周期是什么，什么时候销毁。
>
> 解决：
>
> ```rust
> fn dangle() -> String{}
> ```
>
> 这个是直接返回一个类型。
>
>
> ```rust
> fn dangle () -> &'static str {
>     "hello"
> }
> ```
>
> 这个不推荐，因为将这个函数设置为了静态的生命周期，污染了全局变量





# 4 面向对象

Ownership 与结构体、枚举





3.3枚举与匹配模式

3.4结构体、方法、关联函数、关联变量

3.5Ownership与结构体

3.6堆与栈、Copy与Move





# 5 高阶应用

## 5.1 Borrowing借用&&Lifetime生命周期



6.1Borrowing&&BorrowChecker&&Lifetime6.2Lifetime与函数6.3Lifetime与 Struct







## 5.2 泛型 

7.1 Generic Structures

7.2 Generic Function



## 5.3 特质

8.1 Trait特质

8.2Trait Object与Box

8.3TraitObject与泛型

8.4重载操作符（Operator)

8.5Trait与多态和继承

8.6常见的Trait



## 5.4 迭代器

9.1选代与循环

9.2Intolterator、Iterator和Iter之间的关系泛型

9.3获取选代器的三种方法iter()、iter_mut()和lnto_iter()

9.4自定义类型实现Iter()、iter_mut()和into_iter()





## 5.5 闭包

10.1闭包基础概念

10.2闭包获取参数byreference与byvalue特质

10.3闭包是怎么工作的

10.4闭包类型FnOnce、FnMut和Fn做函数参数的实例