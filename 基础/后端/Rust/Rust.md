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

创建：`cargo new project_name`

`cargo new --lib project_name `创建一个新的 Rust 库项目的

构建项目 (生成二进制可执行文件或库文件)：`cargo build`

`cargo build--release`为生成优化的可执行文件，常用于生产环境

检测：`cargo check`

运行/测试：`cargo run/cargo test`



## 1.4 项目结构

![image-20240628000841742](./assets/image-20240628000841742.png)

package

- 设置项目名
- 版本

dependencies

- 设置依赖
- [build-dependencies]列出了在构建项目时需要的依赖项
- [dev-dependencies]列出了只在开发时需要的依赖项





# 2 语言基础

## 2.1 变量与常见数据类型

2.1 变量与不可变性

2.2 常量 const 与 静态变量static

2.3 Rust基础数据类型

2.4 元组与数组





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









# 4 面向对象

Ownership 与结构体、枚举

3.1Rust的内存管理模型

3.2String与&str

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