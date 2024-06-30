# 1 介绍



## 1.1 Rust特点

1. 具有独一无二的所有权机制，可以防止悬垂引用，并发中产生竞争
2. 没有垃圾回收，具有高性能
3. 零成本抽象
4. 完备的函数式编程（面向函数，而不是面向对象），如模式匹配，高阶函数，闭包，迭代







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





## 1.7 相比其他语言独特

1. 变量不可变性
2. 所有权，所有权系统实参进入函数后会被销毁，move和copy
3. 





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

### 2.2.1 基本数据类型

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

  

- **注意`String`是具有所有权的，而`&str`并没有**
  
- `Struct`中属性推荐使用`String`

  - 对于`&str`，如果不使用显式声明生命周期无法使用`&str`，不只是麻烦，还有更多的隐患，如下：

  ```rust
  struct Person<'a> {
      name: &'a str,
      color: String,
      age:i32,
  }
  ```

> 如果不使用`&str`就不需要标注生命周期

- 函数形参参数推荐使用`&str`（如果不想交出所有权）

  - `&str`为参数，可以传递`&str`和`&String`
  - `&String`为参数，只能传递`&String`不能传递`&str`

  ```rust
  // &String &str 两个都可以传输
  fn print(data: &str) {
      println!("{}", data);
  }
  
  // &String 只能传输这个
  fn print_string_borrow(data: &String) {
      println!("{}", data);
  }
  ```

  

- 两者相互转换

  ```rust
  let strings = String::from("value c++");
  let str = "rust".to_owned();
  let str = "rust".to_string();
  ```

  > 使用`"rust".to_owned()`或者`"rust".to_string(())`将`&str`转化为`String`



**7、枚举**

枚举（enums）是一种用户自定义的数据类型，用于表示具有一组离散可能值的变量

- 每种可能值都称为"variant"（变体)

- 枚举名::变体名

```rust
enum Shape {
    Circle(f64),
    Rectangle(f64, f64),
    Square(f64),
    Unknown, // 表示不知道是否有这个类型，什么类型也不知道
}
```

常用枚举

```rust
pub enum Option<T> {
    None,
    Some(T),
}

pub enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

枚举的好处

- 可以使你的代码更严谨、更易读
- More robust programs

常与匹配模式一起使用：

1. match 关键字实现

2. 必须覆盖所有的变体
3. 可以用`_、..=、三元（if)`等来进行匹配

```rust
match number {
    0=> println!("Zero"),
    1|2 => println!("One or Two"),
    3..=9 => println!("From Three to Nine"),
    n if n % 2 == 0 => println!("Even number"),
    _ => println!("Other"),
}
```



```rust
enum Color {
    Red,
    Yellow,
    Blue,
    Block,
}

fn print_color(my_color: Color) {
    match my_color {
        Color::Red => println!("Red"),
        Color::Yellow => println!("Yellow"),
        Color::Blue => println!("Blue"),
        _ => println!("Other"),
    }
}

fn main() {
    print_color(Color::Yellow);
}
```



```rust
enum BuildingLocation {
    Number(i32),
    Name(String), // 不用&str等来进行匹配
    Unknown,
}

impl BuildingLocation {
    fn print_location(&self) {
        match self {
            BuildingLocation::Number(c) => println!("building number{}", c),
            BuildingLocation::Name(s) => println!("building name{}", s),
            BuildingLocation::Unknown => println!("unknown"),
        }
    }
}

// 调用
let house: BuildingLocation = BuildingLocation::Name("fdgd".to_string());
house.print_location();
```

> - **`impl BuildingLocation`**: `impl` 关键字用于实现特定类型（这里是 `BuildingLocation`）的方法或trait。这意味着下面定义的函数是属于 `BuildingLocation` 类型的。
> - **`fn print_location(&self)`**: 定义了一个名为 `print_location` 的方法，它接受一个引用到 `self`（即枚举实例自身）作为参数。`&self` 是 Rust 中的方法自引用的惯用写法，意味着这个方法不会获取 `self` 的所有权，仅借用它来读取数据。
>
> 这里面有一个关键的思想就是面向函数式编程，而不是面向对象





### 2.2.2 集合



Rust 提供了几种集合类型来存储和操作一系列值，主要包括向量（`Vec<T >`）、哈希映射（`HashMap<K, V>`）、集合（`HashSet<T >`）和链接列表（`LinkedList<T >`）等。下面是对这些集合的基本介绍及使用示例：

#### 2.2.2.1. 向量（`Vec<T >`）

向量是一种动态数组，可以自动调整其大小。它是存储同类型元素序列的最常用方式。

#### 基本使用

```
rustuse std::vec::Vec;

fn main() {
    // 创建并初始化向量
    let mut vec = Vec::new(); // 创建一个空向量
    vec.push(1); // 添加元素
    vec.push(2);
    vec.push(3);

    // 访问元素
    println!("{:?}", vec[0]); // 输出第一个元素

    // 遍历向量
    for elem in &vec {
        println!("{}", elem);
    }

    // 删除最后一个元素
    vec.pop();

    // 获取长度
    println!("Length: {}", vec.len());
}
```

#### 2.2.2.2. 哈希映射（`HashMap<K, V>`）

哈希映射是一种键值对的集合，其中键（Key）是唯一的，用于快速查找对应的值（Value）。

#### 基本使用

```
rustuse std::collections::HashMap;

fn main() {
    let mut map = HashMap::new();
    map.insert("one", 1);
    map.insert("two", 2);

    // 访问值
    match map.get(&"one") {
        Some(value) => println!("The value for 'one' is: {}", value),
        None => println!("No entry found for 'one'"),
    }

    // 遍历哈希映射
    for (key, value) in &map {
        println!("{}: {}", key, value);
    }
}
```

#### 2.2.2.3. 集合`（HashSet<T >）`

集合是一种不包含重复元素的集合类型，常用于成员检查、交集、并集等操作。

#### 基本使用

```
rustuse std::collections::HashSet;

fn main() {
    let mut set: HashSet<i32> = HashSet::new();
    set.insert(1);
    set.insert(2);
    set.insert(3);

    // 检查成员
    if set.contains(&2) {
        println!("Set contains the number 2");
    }

    // 遍历集合
    for &num in &set {
        println!("{}", num);
    }
}
```

#### 2.2.2.4. 链接列表`（LinkedList<T >）`

链接列表是一种线性集合，其中元素在内存中不是连续存放的，而是通过指针链接起来。它适用于频繁的插入和删除操作。

#### 基本使用

```
rustuse std::collections::LinkedList;

fn main() {
    let mut list: LinkedList<i32> = LinkedList::new();
    list.push_back(1);
    list.push_back(2);
    list.push_front(0);

    // 遍历链接列表
    for elem in &list {
        println!("{}", elem);
    }
}
```

这些集合类型提供了丰富的API来执行各种操作，包括但不限于添加、删除、查找、遍历等。选择合适的集合类型对于编写高效、清晰的 Rust 代码至关重要。





## 2.3 ‘引用数据类型’

### 2.3.1 move与copy与clone

Move：所有权转移

Clone：深拷贝

Copy：Copy是在Clone的基础建立的markertrait(Rust中最类似继承的关系)

1. trait（特质）是一种定义共享行为的机制。Clone也是特质

2. markertrait是一个没有任何方法的trait，它主要用于向编译器传递某些信息，以改变类型的默认行为然



**move如下：**

在 Rust 编程语言中，`move` 所有权转移是一种机制，当值的所有权从一个变量转移到另一个变量时发生。具体来说，`move` 发生在以下几种情况：

1. **变量赋值**: 当你将一个拥有堆分配数据（如 `String`、`Vec` 或自定义的动态大小类型）的变量赋值给另一个变量时，原始变量的所有权会转移到新变量，原始变量将不再有效。

```rust
   let s1 = String::from("hello");
   let s2 = s1; // s1的所有权转移到s2，s1不能再使用
```

1. **作为参数传递给函数或闭包**: 如果函数或闭包参数采用值传递（即没有 `&` 或 `&mut`），那么调用时传递的变量的所有权会转移到函数内部，函数结束后该值将被清理。

```rust
fn take_ownership(s: String) {
   println!("{}", s);
}
   
let s = String::from("hello");
take_ownership(s); // s的所有权转移到take_ownership函数内
```

1. **结构体和枚举的字段**: 当你将拥有堆分配数据的变量放入结构体或枚举时，也会发生所有权转移。
2. **从函数返回拥有堆数据的值**: 函数返回拥有堆数据的值时，该值的所有权会从函数内部转移到调用者。

```rust
fn returns_string() -> String {
   String::from("hello")
}

let s = returns_string(); // 返回值的所有权转移到s
```

`move` 操作的核心在于确保任何时候只有一个变量拥有对某个数据的控制权，这有助于避免数据竞争和内存泄漏，是 Rust 安全和高效内存管理策略的基础。





### 2.3.2 move类型

1. **字符串 (`String`)**: `String` 类型在堆上分配内存来存储可变长度的字符串数据。当一个 `String` 被赋值给另一个变量或作为参数传递给函数时，所有权会转移。
2. **向量 (`Vec<T>`)**: `Vec<T>` 类似于动态数组，也在堆上分配内存来存储元素。所有权转移规则同样适用。
3. **自定义结构体和枚举**: 当结构体或枚举中包含上述可变大小类型或其他所有权类型时，整个结构体会在所有权转移时受到影响。
4. **Box`<T >`**: `Box<T>` 是一个智能指针，用于在堆上分配单个值。所有权转移规则适用于 `Box<T>` 实例。
5. **其他堆分配类型**: 任何在堆上分配的类型，或者包含堆分配数据的复合类型，都可能涉及所有权的转移。

**copy类型：**

整数（`i32`, `u8` 等）、浮点数、布尔值和元组（只要元组中的每个元素都是 Copy 类型）





## 2.4 类型位置

**stack**

1. 基础类型 
2. `tuple`和`array`
3. `struct`与枚举等也是存储在栈上 如果属性有`String`等在堆上的数据类型会有指向堆的

**heap**

`Box` `Rc` `String/Vec`等

一般来说在栈上的数据类型都默认实现了copy，但struct等默认为move，需要Copy只需要设置数据类型实现Copy特质即可，或是调用Clone函数（需要实现Clone特质）

栈上的数据基本都有所有权  [move所有权查看](# 2.3.2 move类型)

```rust
    let x = "ss".to_string();
    let y = x;
    print!("{:?}", x); //会报错，所有权转移了

    // 解决方案 clone()
    let y = x.clone();
```





## 2.5 输入输出

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





## 2.6 循环迭代器

基础循环

```rust
   loop {
      println!("Hello, world!");
      std::thread::sleep(std::time::Duration::from_secs(1));)
   }
```

> 无限循环



**遍历数组**

for遍历的一种方式

```rust
    let numbers = [1, 2, 3, 4, 5].to_vec();
    for i in 0..numbers.len() {
        println!("{}", numbers[i]);
    }

    for i in 0..=numbers.len() {
        println!("{}", numbers[i]);
    }
```

> 注意，一个有`=`，一个没有。



迭代器遍历数组

```rust
    let numbers = [1, 2, 3, 4, 5].to_vec();
    let numbers = numbers.iter().map(|x| x * 2).collect::<Vec<_>>();
```











# 3 进阶

## 3.1 函数

### 3.1.1 函数基础与Copy值参数传递

- 如果数据类型实现Copy特质，则在函数传参时会实现Copy by value操作

- 会将实参拷贝为形参，形参改变并不会影响实参

- 如果要改变形参需要加mut



- Struct、枚举、集合等并没有实现Copytrait，会实现move操作失去所有权
- 为数据类型实现Copy trait，就可实现Copy by value



### 3.1.2 函数值参数传递、不可变借用参数传递、可变借用参数传递

**函数值参数传递**

​	函数的代码本身通常是存储在可执行文件的代码段，而在调用时函数会在栈上开辟一个新的stack frame（栈空间），用于存储函数的局部变量、参数和返回地址等信息，而当函数结束后会释放该空间。

而当传入non-Copy value(Vec、String等)

- 传入函数时实参会转移value的所有权给形参，实参会失去value的所有权而在函数结束时，value的所有权会释放



**不可变借用参数传递**

如果你不想失去value的所有权，你又没有修改value的需求，你可以使用不可变借用

在Rust中，你可以将不可变引用作为函数的参数，从而在函数内部访问参数值但不能修改它。这有助于确保数据的安全性，防止在多处同时对数据进行写操作，从而避免数据竞争。

如何应用不可变借用

- `Use ＊ to deference`，去获取其的值

```rust
fn string_func_borrow(s: &String) {
    println!("{}", (*s).to_uppercase()) // *获取值
}


let s = String::from("hello");
string_func_borrow(&s); // 使用&传递
```







**可变借用参数传递**

如果你有修改值的需求你可以使用可变借用，以允许在函数内部修改参数的值。这允许函数对参数进行写操作，但在同一时间内只能有一个可变引用。

需要在形参前加`&mut`

如何应用可变借用

- 同样使用`Use * to deference`，去获取其的值

```rust
struct Point {
    x: i32,
    y: i32,
}

fn modify_point(point: &mut Point) {
    (*point).x += 2; // 原始
    point.y += 2; // 编译器会自动处理解引用的过程
}


let mut p = Point { x: 1, y: 2 };
modify_point(&mut p);
```





### 3.1.3 函数返回值与所有权机制

**返回Copy与Non-Copy都可以返回**

但是要注意Non-Copy是在堆上的

性能：

​	在一般情况下，返回Copy类型的值通常具有更好的性能。这是因为Copy类型的值是通过复制进行返回的，而不涉及堆上内存的分配和释放，通常是在栈上分配。这样的操作比涉及堆上内存的分配和释放更为高效



**返回引用**

- 在只有传入一个引用参数，只有一个返回引用时，生命周期不需要声明
- 其他情况下需要声明引用的生命周期
- 慎用‘static



### 3.1.4 高阶函数 函数作为参数与返回值

**高阶函数与集合**

1、`map`函数：`map`函数可以用于对一个集合中的每个元素应用一个函数，并返回包含结果的新集合。

2、`filter`函数：`filter`函数用于过滤集合中的元素，根据一个谓词函数的返回值。

3、`fold`：`fold`函数（有时也称为`reduce`）可以用于迭代集合的每个元素，并将它们累积到一个单一的结果中







## 3.2 错误处理

### 3.2.1 错误处理之：Result、Option以及panic!宏

Rust中的错误可以分为两种

- Recoverable error：有返回类型
  - 返回Result类型
  - 返回Option类型

- Unrecoverable type：没有返回类型，直接崩溃
  - panic macro将终止当前线程

**Result、Option处理返回的都是枚举，需要使用match**





**Result**

Result是一个枚举类型，有两个变体：Ok和Err。它通常用于表示函数的执行结果，其中ok表示成功的结果，Err表示出现了错误

```rust
 pub enum Result<T, E> {
     Ok (T),
     Err (E),
}
```



```rust
fn divide(a: i32, b: i32) -> Result<f64, String> {
    if b == 0 {
        return Err(String::from("cannot be zero"));
    }

    let a: f64 = a as f64;
    let b: f64 = b as f64;
    Ok(a / b)
}

fn main() {
    // result
    match divide(a: 1, b: 2) {
        Ok(number) => println!("{}", number),
        Err(err) => println!("{}", err),
    }
}
```

> 返回的Result是一个枚举，需要使用match进行匹配





**Option**

Option也是一个枚举类型，有两个变体：Some和None。它通常用于表示一个可能为空的值

```rust
pub enum Option<T> {
    None,
    Some (T),
}
```



```rust
fn find_element(array: &[i32], target: i32) -> Option<usize> {
    for (index, &item) in array.iter().enumerate() {
        if item == target {
            return Some(index);
        }
    }
    None
}

fn main() {
    let arr = [1, 2, 3, 4, 5];

    match find_element(array: &arr, target: 4) {
        Some(index) => println!("found at index {}", index),
        None => println!("None"),
    }
}
```



**panic!**

当程序遇到无法继续执行的错误时，可以使用`panic!`宏来引发恐慌。恐慌会导致程序立即终止，并显示一条错误消息。





```rust
// 第一种系统自带的
let arr = vec![1, 2, 3, 4, 5];
arr[34]; // 数组越界就是
```









### 3.2.2 错误处理之：unwrap()与'?'



**`unwrap()`**

注意：该方法并不安全

`unwrap()`是`Result`和`Option` 类型提供的方法之一。它是一个简便的方法，用于获取` Ok `或 `Some` 的值，如果是 `Err` 或 `None` 则会引发 `panic`

```rust
fn main() {
    let result_ok: Result<i32, &str> = Ok(32);
    let value: i32 = result_ok.unwrap();
    println!("{}", value);


    let result_err: Result<i32, &str> = Err("ff");
    let value: i32 = result_err.unwrap();
}
```

> 注意：使用 unwrap 解包 Err 类型的结果会导致 panic 然后导致程序崩溃





**`?`运算符**

`?`用于简化 `Result` 或 `Option`类型的错误传播。它只能用于返回`Result` 或`Option` 的函数中，并且在函数内部可以像使用`unwrap（）`一样访问`Ok` 或 `Some`的值，**但是如果是 `Err` 或 `None` 则会提前返回**



```rust
fn test() {
    let result_ok: Result<i32, &str> = Ok(32);
    let value: i32 = result_ok?;
    println!("{}", value);
}
```

> 这里如果是`result_ok`为空或者错误的时候会直接返回。下面的打印将不会再执行



### 3.2.3 传递错误

```rust
fn parse_numbers(input: &str) -> Result<i32, ParseIntError> {
    let val= input.parse::<i32>()?;
    Ok(val);
}

match parse_numbers("d"){
    Ok(i） => println!("parsed {}",i),
        Err(err) => println!("failed to parse: {}", err),
    }
```

> 这里面错误传递是在函数中加了`ParseIntError`这个类型，如果





### 3.2.4 自定义一个Error类型

步骤:

1. 定义错误类型结构体：创建一个结构体来表示你的错误类型，通常包含一些字段来描述错误的详细信息。
2. 实现`std::fmt::Display trait`：实现这个trait以定义如何展示错误信息。这是为了使错误能够以人类可读的方式打印出来。
3. 实现`std::error::Error trait`：实现这个 trait以满足Rust 的错误处理机制的要求





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

## 4.1 结构体

### 4.1.1 定义使用

结构体是一种用户定义的数据类型，用于创建自定义的数据结构。

- 每条数据(x和y)称为属性 (字段 field)
- 通过点(.)来访问结构体中的属性



### 4.1.2 关联函数

关联函数（方法）是指，通过实例调用（`&self`、`&mut self`、`self`)

```rust
impl Point {
    fn distance(&self, other: &Point) -> f64 {
        let dx = (self.x - other.x) as f64;
        let dy = (self.y - other.y) as f64;
        (dx * dx + dy * dy).sqrt()
    }
}
```

这里 `self` 指代的是结构体 `Point` 的实例。在 Rust 中，有三种常见的 `self` 调用方式：

1. `&self`: 表示方法接受一个 `Point` 结构体的只读引用。在这种情况下，方法不能修改 `self` 对象的内容。
2. `&mut self`: 表示方法接受一个 `Point` 结构体的可变引用。这种方法可以修改 `self` 对象的内容。
3. `self`: 表示方法接受 `Point` 结构体的所有权。在方法结束后，所有权会转移回调用方。

相关请看：[4.2.2 结构体中调用函数的形参参数](# 4.2.2 结构体中调用函数的形参参数)



### 4.1.3 调用函数

调用时为结构体名::函数

```rust
impl Point {
    fn name(x: u32, y: u32) -> Self {
        Point { x, y }
    }
}

// 调用
Point::name(x, y);
```

> 注意，这里面的`Self`指的是Point



### 4.1.4 调用变量

这里的变量是指，和结构体类型相关的变量，也可以在特质或是枚举中

```rust
impl Point {
    const PI: f64 = 3.14;
}

// 调用
Point::PI
```



## 4.2 Ownership与结构体

### 4.2.1 所有权机制的规则：

1. Rust 中的每个值都有一个所有者

2. 一次可以有一个所有者

3. 当所有者超出范围时，Values 会自动删除





每当将值从一个位置传递到另一个位置时，borrow checker都会重新评估所有权。

1．ImmutableBorrow使用不可变的借用，值的所有权仍归发送方所有，接收方直接接收对该值的引l用，而不是该值的副本。但是，他们不能使用该引用来修改它指向的值，编译器不允许这样做。释放资源的责任仍由发送方承担。仅当发件人本身超出范围时，才会删除该值

2．MutableBorrow使用可变的借用所有权和删除值的责任也由发送者承担。但是接收方能够通过他们接收的引用来修改该值。

3．Move 这是所有权从一个地点转移到另一个地点。borrow checker关于释放该值的决定将由该值的接收者（而不是发送者）通知。由于所有权已从发送方转移到接收方，因此发送方在将引用移动到另一个上下文后不能再使用该引用，发送方在移动后对vlaue的任何使用都会导致错误。



所有权会涉及到堆栈，请看[4.3 堆与栈](# 4.3 堆与栈)，然后看[2.3 ‘引用数据类型’](# 2.3 ‘引用数据类型’)的move





### 4.2.2 结构体中调用函数的形参参数

`&self(self: &Self)`
不可变引用

`&mut self (self: &mut Self)`
可变引用

`self(self: Self)`
Move(Self大写)

```rust
impl Point {
    get(self: Self) -> i32 {
        self.x
    }
}
```

> `self` 参数相当于 `Point::get(point)`，调用后丧失所有权（point 为实例对象）
>
> `&self` 参数相当于 `Point::get(&point)`，
> `&mut self` 参数相当于 `Point::get(&mut point)`

实例请看：[4.1.3 调用函数](# 4.1.3 调用函数)



### 4.2.3 所有权转移

转移请看：[2.3 "引用数据类型"](# 2.3 ‘引用数据类型’)



**通过 `as_ref()`、`as_mut()` 或 `.clone()` 避免所有权转移**



## 4.3 堆与栈

**stack**

1. 堆栈将按照获取值的顺序存储值，并以相反的顺序删除值
2. 操作高效，**函数作用域就是在栈上**
3. 堆栈上存储的所有数据都必须具有已知的固定大小数据

**heap**

1. 堆的规律性较差，当你把一些东西放到你请求的堆上时，你请求，请求空间，并返回一个指针，这是该位置的地址
2. 长度不确定

请通过这个查看[2.4 类型位置](# 2.4 类型位置)





## 4.4 Box

Box是一个智能**指针**，它提供对堆分配内存的所有权。**它允许你将数据存储在堆上而不是栈上**，并且在复制或移动时**保持对数据的唯一拥有权**。使用Box可以避免一些内存管理问题，如悬垂指针和重复释放。

1. 所有权转移
2. 释放内存
3. 解引用
4. 构建递归数据结构



```rust
struct Point {
    x: i32,
    y: i32,
}

// 使用new将数据存储在堆上
let boxed_point = Box::new(Point { x: 1, y: 2 });
```



```rust
    let mut boxed_point = Box::new(32);
    *boxed_point += 10;
```

> 这里主要说明box是一个指针





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