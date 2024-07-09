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