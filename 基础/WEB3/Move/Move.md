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

```move
(vector[]: vector<bool>);
(vector[0u8, 1u8, 2u8]: vector<u8>);
(vector<u128>[]: vector<u128>);
(vector<address>[@0x42, @0x100]: vector<address>);
```

| 函数                                                       | 描述                                                         | 是否中止?            |
| ---------------------------------------------------------- | ------------------------------------------------------------ | -------------------- |
| `vector::empty<T>(): vector<T>`                            | 创建一个可以存储`T`类型值的空向量                            | 从不                 |
| `vector::singleton<T>(t: T): vector<T>`                    | 创建一个包含`t`的大小为1的向量                               | 从不                 |
| `vector::push_back<T>(v: &mut vector<T>, t: T)`            | 将`t`添加到`v`的末尾                                         | 从不                 |
| `vector::pop_back<T>(v: &mut vector<T>): T`                | 移除并返回`v`中的最后一个元素                                | 如果`v`为空          |
| `vector::borrow<T>(v: &vector<T>, i: u64): &T`             | 返回索引`i`处的`T`的不可变引用                               | 如果`i`不在范围内    |
| `vector::borrow_mut<T>(v: &mut vector<T>, i: u64): &mut T` | 返回索引`i`处的`T`的可变引用                                 | 如果`i`不在范围内    |
| `vector::destroy_empty<T>(v: vector<T>)`                   | 删除`v`                                                      | 如果`v`不为空        |
| `vector::append<T>(v1: &mut vector<T>, v2: vector<T>)`     | 将`v2`中的元素添加到`v1`的末尾                               | 从不                 |
| `vector::contains<T>(v: &vector<T>, e: &T): bool`          | 如果`e`在向量`v`中返回true。否则,返回false                   | 从不                 |
| `vector::swap<T>(v: &mut vector<T>, i: u64, j: u64)`       | 交换向量`v`中第`i`和第`j`个索引处的元素                      | 如果`i`或`j`超出范围 |
| `vector::reverse<T>(v: &mut vector<T>)`                    | 原地反转向量`v`中元素的顺序                                  | 从不                 |
| `vector::index_of<T>(v: &vector<T>, e: &T): (bool, u64)`   | 如果`e`在索引`i`处的向量`v`中,返回`(true, i)`。否则,返回`(false, 0)` | 从不                 |
| `vector::remove<T>(v: &mut vector<T>, i: u64): T`          | 移除向量`v`的第`i`个元素,移动所有后续元素。这是O(n)操作,并保持向量中元素的顺序 | 如果`i`超出范围      |
| `vector::swap_remove<T>(v: &mut vector<T>, i: u64): T`     | 将向量`v`的第`i`个元素与最后一个元素交换,然后弹出该元素。这是O(1)操作,但不保持向量中元素的顺序 | 如果`i`超出范围      |

```move
use std::vector;

let mut v = vector::empty<u64>();
vector::push_back(&mut v, 5);
vector::push_back(&mut v, 6);

assert!(*vector::borrow(&v, 0) == 5, 42);
assert!(*vector::borrow(&v, 1) == 6, 42);
assert!(vector::pop_back(&mut v) == 6, 42);
assert!(vector::pop_back(&mut v) == 5, 42);
```

**销毁和复制`vector`**

`vector<T>`的某些行为取决于元素类型`T`的能力。例如,包含没有`drop`能力的元素的向量不能像上面示例中的`v`那样被隐式丢弃--它们必须使用`vector::destroy_empty`显式销毁。

注意,除非`vec`包含零个元素,否则`vector::destroy_empty`将在运行时中止:

```move
fun destroy_any_vector<T>(vec: vector<T>) {
    vector::destroy_empty(vec) // 删除这行将导致编译器错误
}
```

但对于丢弃包含具有`drop`能力的元素的向量不会发生错误:

```move
fun destroy_droppable_vector<T: drop>(vec: vector<T>) {
    // 有效!
    // 不需要显式做任何事来销毁向量
}
```

同样,除非元素类型具有`copy`能力,否则向量不能被复制。换句话说,当且仅当`T`具有`copy`能力时,`vector<T>`才具有`copy`能力。请注意,如果需要,它将被隐式复制:

```move
let x = vector[10];
let y = x; // 隐式复制
let z = x;
(y, z)
```

请记住,复制大向量可能很昂贵。如果这是一个问题,注释`intended`用法可以防止意外复制。例如,

```move
let x = vector[10];
let y = move x;
let z = x; // 错误! x已被移动
(y, z)
```

有关更多详细信息,请参阅[类型能力](https://reference.sui-book.com/abilities.html)和[泛型](https://reference.sui-book.com/generics.html)部分。

**所有权**

如上文所述,只有当元素可以被复制时,`vector`值才能被复制。在这种情况下,可以通过[`copy`](https://reference.sui-book.com/variables.html#move-and-copy)或[解引用`*`](https://reference.sui-book.com/primitive-types/references.html#reading-and-writing-through-references)进行复制。



### 2.2.5 引用

Move有两种类型的引用:不可变引用`&`和可变引用`&mut`。不可变引用是只读的,不能修改底层值(或其任何字段)。可变引用允许通过该引用进行修改。Move的类型系统强制执行所有权规则,以防止引用错误。

Move提供了创建和扩展引用以及将可变引用转换为不可变引用的操作符。这里我们用`e: T`表示"表达式`e`的类型为`T`"。

| 语法        | 类型                                 | 描述                               |
| ----------- | ------------------------------------ | ---------------------------------- |
| `&e`        | `&T`,其中`e: T`且`T`不是引用类型     | 创建`e`的不可变引用                |
| `&mut e`    | `&mut T`,其中`e: T`且`T`不是引用类型 | 创建`e`的可变引用                  |
| `&e.f`      | `&T`,其中`e.f: T`                    | 创建结构体`e`的字段`f`的不可变引用 |
| `&mut e.f`  | `&mut T`,其中`e.f: T`                | 创建结构体`e`的字段`f`的可变引用   |
| `freeze(e)` | `&T`,其中`e: &mut T`                 | 将可变引用`e`转换为不可变引用      |

`&e.f`和`&mut e.f`操作符既可以用于创建新的引用到结构体中,也可以用于扩展现有引用:

```move
let s = S { f: 10 };
let f_ref1: &u64 = &s.f; // 可以
let s_ref: &S = &s;
let f_ref2: &u64 = &s_ref.f // 也可以
```

多字段的引用表达式只要两个结构体在同一模块中就可以工作:

```move
public struct A { b: B }
public struct B { c : u64 }
fun f(a: &A): &u64 {
    &a.b.c
}
```

最后,请注意不允许引用的引用:

```move
let x = 7;
let y: &u64 = &x;
let z: &&u64 = &y; // 错误! 无法编译
```

**通过引用读写**

可变和不可变引用都可以被读取以产生被引用值的副本。

只有可变引用可以被写入。写入`*x = v`会丢弃之前存储在`x`中的值,并用`v`更新它。

这两种操作都使用类似C的`*`语法。但请注意,读取是一个表达式,而写入是必须发生在等号左侧的变更。

| 语法       | 类型                           | 描述                 |
| ---------- | ------------------------------ | -------------------- |
| `*e`       | `T`,其中`e`是`&T`或`&mut T`    | 读取`e`指向的值      |
| `*e1 = e2` | `()`,其中`e1: &mut T`且`e2: T` | 用`e2`更新`e1`中的值 |

为了能读取引用,底层类型必须具有`copy`能力,因为读取引用会创建值的新副本。这条规则防止了资产被复制:

```move
fun copy_coin_via_ref_bad(c: Coin) {
    let c_ref = &c;
    let counterfeit: Coin = *c_ref; // 不允许!
    pay(c);
    pay(counterfeit);
}
```

相对地:为了能写入引用,底层类型必须具有`drop`能力,因为写入引用会丢弃(或"删除")旧值。这条规则防止了资源值被销毁:

```move
fun destroy_coin_via_ref_bad(mut ten_coins: Coin, c: Coin) {
    let ref = &mut ten_coins;
    *ref = c; // 错误! 不允许--会销毁10个硬币!
}
```



**`freeze`推断**

可变引用可以在期望不可变引用的上下文中使用:

```move
let mut x = 7;
let y: &u64 = &mut x;
```

这是因为在底层,编译器会在需要的地方插入`freeze`指令。这里有更多`freeze`推断的示例:

```move
fun takes_immut_returns_immut(x: &u64): &u64 { x }

// 在返回值上进行freeze推断
fun takes_mut_returns_immut(x: &mut u64): &u64 { x }

fun expression_examples() {
    let mut x = 0;
    let mut y = 0;
    takes_immut_returns_immut(&x); // 无推断
    takes_immut_returns_immut(&mut x); // 推断为freeze(&mut x)
    takes_mut_returns_immut(&mut x); // 无推断

    assert!(&x == &mut y, 42); // 推断为freeze(&mut y)
}

fun assignment_examples() {
    let x = 0;
    let y = 0;
    let imm_ref: &u64 = &x;

    imm_ref = &x; // 无推断
    imm_ref = &mut y; // 推断为freeze(&mut y)
}
```



**子类型**

通过这种`freeze`推断,Move类型检查器可以将`&mut T`视为`&T`的子类型。如上所示,这意味着在任何使用`&T`值的表达式中,也可以使用`&mut T`值。这个术语在错误消息中用于简洁地表示在需要`&T`的地方提供了`&mut T`。例如:

```move
module a::example {
    fun read_and_assign(store: &mut u64, new_value: &u64) {
        *store = *new_value
    }

    fun subtype_examples() {
        let mut x: &u64 = &0;
        let mut y: &mut u64 = &mut 1;

        x = &mut 1; // 有效
        y = &2; // 错误! 无效!

        read_and_assign(y, x); // 有效
        read_and_assign(x, y); // 错误! 无效!
    }
}
```



可变和不可变引用都可以随时被复制和扩展,*即使存在同一引用的现有副本或扩展*:

```move
fun reference_copies(s: &mut S) {
  let s_copy1 = s; // 可以
  let s_extension = &mut s.f; // 也可以
  let s_copy2 = s; // 仍然可以
  ...
}
```

这可能会让熟悉Rust所有权系统的程序员感到惊讶,Rust会拒绝上面的代码。Move的类型系统在处理复制)时更加宽松,但在写入前确保可变引用的唯一所有权方面同样严格。

**引用不能被存储**

引用和元组是_唯一_不能作为结构体字段值存储的类型,这也意味着它们不能存在于存储或对象)中。在程序执行期间创建的所有引用都会在Move程序终止时被销毁;它们完全是短暂的。这也适用于所有没有`store`能力的类型:任何非`store`类型的值必须在程序终止前被销毁。但请注意引用和元组更进一步,从一开始就不允许存在于结构体中。

这是Move和Rust的另一个区别,Rust允许在结构体中存储引用。

我们可以想象一个更复杂、更具表现力的类型系统,允许在结构体中存储引用。我们可以允许在没有`store`的结构体中使用引用,但核心困难在于Move有一个相当复杂的系统来跟踪静态引用安全性。类型系统的这个方面也需要扩展以支持在结构体中存储引用。简而言之,Move的引用安全系统需要扩展以支持存储引用,这是我们在语言演化过程中一直在关注的问题。



### 2.2.6元组与单元

Move 并未完全支持元组，这与其他将元组作为[一等公民](https://en.wikipedia.org/wiki/First-class_citizen)的语言有所不同。然而，为了支持多返回值，Move 提供了类似元组的表达式。这些表达式在运行时不会生成具体的值（字节码中不存在元组），因此它们有很大的局限性：

- 只能出现在表达式中（通常在函数的返回位置）。
- 不能绑定到局部变量。
- 不能存储在结构体中。
- 元组类型不能用于实例化泛型。

类似地，[unit `()`](https://en.wikipedia.org/wiki/Unit_type) 类型是 Move 源语言为了基于表达式的设计而创建的。单位值 `()` 在运行时不会产生任何值。可以将单位 `()` 视为一个空元组，适用于所有对元组的限制。

考虑到这些限制，在语言中使用元组可能会感到奇怪。但在其他语言中，元组最常见的用例之一是允许函数返回多个值。一些语言通过强迫用户编写包含多个返回值的结构体来解决这个问题。然而，在 Move 中，你不能在[结构体](https://reference.sui-book.com/structs.html)中放置引用。这要求 Move 支持多返回值。在字节码层面，这些多返回值全部压入堆栈。在源代码层面，这些多返回值使用元组表示。



**字面量**

元组通过在括号内使用逗号分隔的表达式列表创建。

| 语法            | 类型                                                         | 描述                                      |
| --------------- | ------------------------------------------------------------ | ----------------------------------------- |
| `()`            | `(): ()`                                                     | 单位类型，空元组，或 0 元素的元组         |
| `(e1, ..., en)` | `(e1, ..., en): (T1, ..., Tn)` 其中 `e_i: Ti` 满足 `0 < i <= n` 且 `n > 0` | `n` 元组，`n` 元素的元组，包含 `n` 个元素 |

注意 `(e)` 并没有类型 `(e): (t)`，换句话说，不存在单元素元组。如果括号内只有一个元素，则括号仅用于消除歧义，没有其他特殊含义。

有时，包含两个元素的元组称为"对"，包含三个元素的元组称为"三元组"。

```move
module 0x42::example {
    // 以下三个函数是等价的

    // 当没有提供返回类型时，假定为 `()`
    fun returns_unit_1() { }

    // 空表达式块中有一个隐式的 () 值
    fun returns_unit_2(): () { }

    // 显式版本的 `returns_unit_1` 和 `returns_unit_2`
    fun returns_unit_3(): () { () }

    fun returns_3_values(): (u64, bool, address) {
        (0, false, @0x42)
    }
    fun returns_4_values(x: &u64): (&u64, u8, u128, vector<u8>) {
        (x, 0, 1, b"foobar")
    }
}
```



**操作**

目前，对元组唯一可以执行的操作是解构。

对于任何大小的元组，都可以在 `let` 绑定或赋值中解构。

例如：

```move
module 0x42::example {
    // 以下三个函数是等价的
    fun returns_unit() {}
    fun returns_2_values(): (bool, bool) { (true, false) }
    fun returns_4_values(x: &u64): (&u64, u8, u128, vector<u8>) { (x, 0, 1, b"foobar") }

    fun examples(cond: bool) {
        let () = ();
        let (mut x, mut y): (u8, u64) = (0, 1);
        let (mut a, mut b, mut c, mut d) = (@0x0, 0, false, b"");

        () = ();
        (x, y) = if (cond) (1, 2) else (3, 4);
        (a, b, c, d) = (@0x1, 1, true, b"1");
    }

    fun examples_with_function_calls() {
        let () = returns_unit();
        let (mut x, mut y): (bool, bool) = returns_2_values();
        let (mut a, mut b, mut c, mut d) = returns_4_values(&0);

        () = returns_unit();
        (x, y) = returns_2_values();
        (a, b, c, d) = returns_4_values(&1);
    }
}
```



**子类型**

与引用一样，元组是 Move 中唯一具有[子类型](https://en.wikipedia.org/wiki/Subtyping)的类型。元组的子类型仅在引用中的协变方式存在。

例如：

```move
let x: &u64 = &0;
let y: &mut u64 = &mut 1;

// (&u64, &mut u64) 是 (&u64, &u64) 的子类型
// 因为 &mut u64 是 &u64 的子类型
let (a, b): (&u64, &u64) = (x, y);

// (&mut u64, &mut u64) 是 (&u64, &u64) 的子类型
// 因为 &mut u64 是 &u64 的子类型
let (c, d): (&u64, &u64) = (y, y);

// 错误! (&u64, &mut u64) 不是 (&mut u64, &mut u64) 的子类型
// 因为 &u64 不是 &mut u64 的子类型
let (e, f): (&mut u64, &mut u64) = (x, y);
```



**所有权**

如前所述，元组值在运行时并不真正存在。目前它们不能存储到局部变量中（但未来可能会添加此功能）。因此，元组只能移动，不能复制，因为复制它们需要首先将其放入局部变量中。



## 2.3 局部变量和作用域

**声明局部变量：**

**`let` 绑定**

Move 程序使用 `let` 将变量名称绑定到值：

```move
let x = 1;
let y = x + x;
```

`let` 也可以在不将值绑定到局部变量的情况下使用。

```move
let x;
```

然后可以在稍后为局部变量赋值。

```move
let x;
if (cond) {
  x = 1;
} else {
  x = 0;
}
```

在无法提供默认值时，这在从循环中提取值时非常有用。

```move
let x;
let cond = true;
let i = 0;
loop {
    let (res, cond) = foo(i);
    if (!cond) {
        x = res;
        break;
    };
    i = i + 1;
}
```

要在赋值后修改局部变量，或者借用它的可变引用 `&mut`，必须将其声明为 `mut`。

```move
let mut x = 0;
if (cond) x = x + 1;
foo(&mut x);
```

一些显式类型注解的例子：

```move
module 0x42::example {

    public struct S { f: u64, g: u64 }

    fun annotated() {
        let u: u8 = 0;
        let b: vector<u8> = b"hello";
        let a: address = @0x0;
        let (x, y): (&u64, &mut u64) = (&0, &mut 1);
        let S { f, g: f2 }: S = S { f: 0, g: 1 };
    }
}
```









| 方法签名                   | 调用范围                  | 返回值 |
| -------------------------- | ------------------------- | ------ |
| fun call()                 | 只能模块内调用            | 可以有 |
| public fun call()          | 全部合约能调用            | 可以有 |
| public entry fun call()    | 全部合约和Dapp(RPC)能调用 | 无     |
| entry fun call()           | 只能Dapp(RPC)调用         | 无     |
| public(package) fun call() | 只能指定的模块能调用      | 可以有 |



**init方法**

1. 只能是私有的

2. 会在发布合约的是自动调用一次

3. 只有两种形式

   ```move
   fun init(ctx: &mut TxContext){}
   
   fun init(witness: Struct, ctx: &mut TxContext){}
   ```

   