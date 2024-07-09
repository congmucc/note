# 1 介绍

## 1.1 介绍

Move起源于Facebook（现在Meta）的一个非常明星的项目[Diem](https://github.com/diem/diem)（前身为 Libra ）,可能早期的Rust爱好者和区块链爱好者会看到这个项目，就算没看到过，现在diem代码库的Star数量依然能证明曾经的辉煌。Move就是诞生在Diem（Libra）这样一个明星项目里面哪为什么Diem需要创造一个新的Move编程语言，而不是用以前已经有的东西，而是选择新造了一个轮子，那就要从Diem想做什么开始开始说起了

## 1.2 特点

Move诞生的里面我们总结几个关键点:

- 面向资产
- 安全
- 用于大规模系统
- 借鉴Rust语法和特性
- rust语言开发
- 内核完全从0设计，不是基于rust



1. 定义资产

2. 读，写，删除，转移资产
3. 权限检查，访问权限控制



## 1.3 和其他编程语言有什么不同

- 每一次运行程序都是一个**完整的事务**，要么全部成功要么全部失败
- 不用考虑并发执行资源的处理，底层会自动处理并发资源的排序
- 和链的结合屏蔽了数据层的概念，语言本身的操作就是数据的操作，极大的简化了需要学习数据层的处理



## 1.4 安装

- 安装Move编译器(Sui Cli)
- 安装Sui钱包
- 获取测试SUI
- 使用Sui区块链浏览器查看
- 学习Move编译器的使用(Sui Cli)
- 安装IDE开发环境

[安装 Sui |Sui 文档 --- Install Sui | Sui Documentation](https://docs.sui.io/guides/developer/getting-started/sui-install)

安装Chocolatey  [Chocolatey 安装指南](https://docs.chocolatey.org/en-us/choco/setup#installing-chocolatey-cli)

**注意：** Chocolatey 的默认安装路径通常是 `C:\ProgramData\chocolatey`

测试Chocolatey  ：

```sh
choco upgrade chocolatey
```

安装sui

```sh
choco install sui
```

> 路径为：`Deployed to 'C:\ProgramData\chocolatey\lib\sui'`



**docker 方式的安装**

pull 镜像 `devnet` 可以换成 `testnet` `mainnet`

```
  docker pull mysten/sui-tools:devnet
```

运行镜像

```
  docker run --name suidevcontainer -itd mysten/sui-tools:devnet

  docker exec -it suidevcontainer bash
```

**如何验证自己是否安装好**

```
sui --version
```

输出

```
sui 1.22.0-0362997459
```



## 1.5 sui常用命令

1. **创建一个新的基于move的工程**

   ```sh
   sui move new <project_name>
   ```

2. 将move文件与sui网络互联

   ```sh
   sui client publish --gas-budget 1000000
   ```

3. 获取自己的地址

   ```sh
   sui client addresses
   ```

   





# 2 基础

## 2.1 Modules(模块)

模块 是定义类型及操作这些类型的函数的核心程序单元。结构类型定义了 Move 存储的模式，而模块函数定义了与这些类型的值交互的规则。虽然模块本身也存储在存储中，但在 Move 程序内部是不可访问的。在区块链环境中，模块存储在链上，通常称为 "发布" 过程。在发布后，可以根据特定 Move 实例的规则调用 [`entry`](https://reference.sui-book.com/functions.html#entry-modifier) 和 [`public`](https://reference.sui-book.com/functions.html#visibility) 函数。

模块具有以下语法：

```text
module <address>::<identifier> {
    (<use> | <type> | <function> | <constant>)*
}
```

其中 `<address>` 是指定模块所属包的有效 [地址](https://reference.sui-book.com/primitive-types/address.html)。

```sh
module test_addr::test {
    public struct Example has copy, drop { a: address }

    friend test_addr::another_test;

    public fun print() {
        let example = Example { a: @test_addr };
        debug::print(&example)
    }
}

```

> `module test_addr::test` 部分指定了模块 `test` 将在名称为 `test_addr` 的包设置中分配的数字地址值下进行发布。

通常应使用命名地址来声明模块（而不是直接使用数字值）。

这些命名地址通常与包的名称匹配。

因为命名地址仅存在于源语言级别和编译过程中，在字节码级别上，命名地址将完全替换为其值。例如，如果我们有以下代码：

```move
fun example() {
    my_addr::m::foo(@my_addr);
}
```

并且将其编译时设置 `my_addr` 设置为 `0xC0FFEE`，则其操作上等效于：

```move
fun example() {
    0xC0FFEE::m::foo(@0xC0FFEE);
}
```

尽管在源级别上这两种访问方式是等效的，但最佳实践是始终使用命名地址而不是分配给该地址的数字值。

通常，模块名以小写字母开头。名为 `my_module` 的模块应存储在名为 `my_module.move` 的源文件中。



## 2.2 基本数据类型

### 2.2.1 整型

Move 语言支持六种无符号整数类型：`u8`、`u16`、`u32`、`u64`、`u128` 和 `u256`。这些类型的值范围从 0 到根据类型大小决定的最大值。



| 类型                      | 值范围        |
| ------------------------- | ------------- |
| 无符号 8 位整数，`u8`     | 0 到 28 - 1   |
| 无符号 16 位整数，`u16`   | 0 到 216 - 1  |
| 无符号 32 位整数，`u32`   | 0 到 232 - 1  |
| 无符号 64 位整数，`u64`   | 0 到 264 - 1  |
| 无符号 128 位整数，`u128` | 0 到 2128 - 1 |
| 无符号 256 位整数，`u256` | 0 到 2256 - 1 |

这些类型的字面值可以用数字序列表示（例如 `112`）或十六进制表示（例如 `0xFF`）。可以选择性地在字面值后加上类型后缀（例如 `112u8`）。如果未指定类型，编译器会尝试从字面值所在的上下文推断类型。如果无法推断，则默认假设为 `u64`。

数字字面值可以用下划线分隔以增强可读性（例如 `1_234_5678`、`1_000u128`、`0xAB_CD_12_35`）。

如果字面值超出了指定（或推断）类型的范围，会报告错误。

Move不支持类型的隐式转换



**Move没有负数和小数**

**如何在Move中表示小数和负数**：

**小数**的定义 是a／b，所以只要我选择放大整数的倍数可以用来表示小数

标准库中：https://github.com/MystenLabs/sui/blob/main/crates/sui-framework/packages/move-stdlib/sources/fixed_point32.move

**负数**我们可以用前端展现和约定的方式来表示

比如U8 类型的1-127表示负数、128-255表示正数





### 2.2.2 布尔类型

`bool` 是 Sui Move 语言中用于表示布尔值 `true` 和 `false` 的原始类型。



### 2.2.3 地址类型

`address`是Move语言中的一个内置类型

一个`address`值是一个256位（32字节）的标识符、Move使用地址来区分[模块](https://reference.sui-book.com/modules.html)的包，每个包都有自己的地址和模块。特定的Move部署也可能使用`address`值进行[存储](https://reference.sui-book.com/abilities.html#key)操作。

> 对于Sui来说，`address`用于表示"账户"，也通过强类型包装器（使用`sui::object::UID`和`sui::object::ID`）表示对象。

虽然`address`在底层是一个256位整数，但Move地址是有意不透明的——它们不能从整数创建，不支持算术运算，也不能被修改。特定的Move部署可能有`native`函数来启用其中一些操作（例如，从字节`vector<u8>`创建一个`address`），但这些不是Move语言本身的一部分。

虽然存在运行时地址值（`address`类型的值），但它们在运行时_不能_用于访问模块。

**地址及其语法**

地址有两种形式，命名的或数字的。命名地址的语法遵循Move中任何命名标识符的相同规则。数字地址的语法不限于十六进制编码的值，任何有效的`u256`数值都可以用作地址值，例如，`42`、`0xCAFE`和`10_000`都是有效的数字地址字面量。

为了区分地址是否在表达式上下文中使用，使用地址的语法根据使用的上下文而有所不同：

- 当地址作为表达式使用时，地址必须以`@`字符为前缀，即`@<数值>`或`@<命名地址标识符>`。
- 在表达式上下文之外，地址可以不带前导`@`字符书写，即`<数值>`或`<命名地址标识符>`。

一般来说，你可以把`@`看作是一个操作符，它将地址从命名空间项转换为表达式项。

**命名地址**

命名地址是一个特性，允许在使用地址的任何地方使用标识符来代替数值，而不仅仅是在值级别。命名地址在Move包中被声明和绑定为顶层元素（在模块和脚本之外），或作为参数传递给Move编译器。

命名地址只存在于源语言级别，在字节码级别将完全被其值替代。因此，应该通过模块的命名地址而不是编译期间分配给命名地址的数值来访问模块和模块成员。所以虽然`use my_addr::foo`等同于`use 0x2::foo`（如果`my_addr`被分配为`0x2`），但最佳实践是始终使用`my_addr`名称。

```move
// 0x0000000000000000000000000000000000000000000000000000000000000001的简写
let a1: address = @0x1;
// 0x0000000000000000000000000000000000000000000000000000000000000042的简写
let a2: address = @0x42;
// 0x00000000000000000000000000000000000000000000000000000000DEADBEEF的简写
let a3: address = @0xDEADBEEF;
// 0x000000000000000000000000000000000000000000000000000000000000000A的简写
let a4: address = @0x0000000000000000000000000000000A;
// 将命名地址`std`的值赋给`a5`
let a5: address = @std;
// 任何有效的数值都可以用作地址
let a6: address = @66;
let a7: address = @42_000;

module 66::some_module {   // 不在表达式上下文中，所以不需要@
    use 0x1::other_module; // 不在表达式上下文中，所以不需要@
    use std::vector;       // 可以使用命名地址作为命名空间项
    ...
}

module std::other_module {  // 声明模块时可以使用命名地址
    ...
}

```





### 2.2.4 数组

`vector<T>`是Move提供的唯一原始集合类型。`vector<T>`是`T`类型元素的同类集合,可以通过在"末端"推入/弹出值来增长或缩小。

`vector<T>`可以用任何类型`T`实例化。例如,`vector<u64>`,`vector<address>`,`vector<0x42::my_module::MyData>`,和`vector<vector<u8>>`都是有效的向量类型。



任何类型的向量都可以用`vector`字面量创建。

| 语法                  | 类型                                                         | 描述                         |
| --------------------- | ------------------------------------------------------------ | ---------------------------- |
| `vector[]`            | `vector[]: vector<T>`,其中`T`是任何单一的非引用类型          | 空向量                       |
| `vector[e1, ..., en]` | `vector[e1, ..., en]: vector<T>`,其中`e_i: T`且`0 < i <= n`且`n > 0` | 有`n`个元素的向量(长度为`n`) |

在这些情况下,`vector`的类型是从元素类型或向量的使用中推断出来的。如果无法推断类型,或者为了更清晰,可以显式指定类型:

```move
vector<T>[]: vector<T>
vector<T>[e1, ..., en]: vector<T>
```