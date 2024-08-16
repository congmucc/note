https://creatorsdao.github.io/
## 错误处理

### ProgramResult 枚举

ProgramResult是 solana 中定义的一个通用错误处理类型，它是solana_program中的一个结构体，具体如下：

```rust
use solana_program::entrypoint::ProgramResult;
```

代表着 Solana 程序中指令处理函数的返回值，该类型代表 Transaction 交易中指令的处理结果，成功时为单元类型()，即返回值为空，失败时返回值为ProgramError，它本身又是个枚举。

```rust
use std::result::Result as ResultGeneric;

pub type ProgramResult = ResultGeneric<(), ProgramError>;
```

在 ProgramError 中定义了 23 种常见的错误原因枚举值，也支持自定义的错误类型，如下：

```rust
pub enum ProgramError {
    // 用户自定义错误类型
    #[error("Custom program error: {0:#x}")]
    Custom(u32),

		// 参数无效
    #[error("The arguments provided to a program instruction were invalid")]
    InvalidArgument,

		// 指令数据无效
    #[error("An instruction's data contents was invalid")]
    InvalidInstructionData,

		// 账户数据无效
    #[error("An account's data contents was invalid")]
    InvalidAccountData,
    
		// ……
}
```

## Solana 入口点

Solana 网络上存储的所有数据都包含在所谓的帐户中。每个帐户都有自己唯一的地址，用于识别和访问帐户数据。Solana 程序只是一种特定类型的 Solana 帐户，用于存储和执行指令。

### **Solana crate** solana_program

[solana_program crate文档](https://docs.rs/solana-program/latest/solana_program/index.html)。

对于基本程序，我们需要将 solana_program 库中的以下项目纳入作用域：

```javascript
use solana_program::{
    account_info::AccountInfo,
    entrypoint,
    entrypoint::ProgramResult,
    pubkey::Pubkey,
    msg
};
```

●AccountInfo：account_info 模块中的一个结构体，允许我们访问帐户信息。

●entrypoint：声明程序入口点的宏，类似于 Rust 中的 main 函数。

●ProgramResult：entrypoint 模块中的返回值类型。

●Pubkey：pubkey 模块中的一个结构体，允许我们将地址作为公钥访问。

●msg：一个允许我们将消息打印到程序日志的宏，类似于 Rust 中的 println宏。

### **Solana 程序入口点**

Solana 程序需要单个入口点来处理程序指令。入口点是使用entrypoint!声明的宏。

通过如下方式声明程序的入口点函数：

```javascript
// 声明程序入口点函数
entrypoint!(process_instruction);
```

该指令处理函数我们在前面章节已介绍过，这里不再赘述。



```javascript
fn process_instruction(
		// 当前的程序ID
    program_id: &Pubkey,
		// 该指令涉及到的账户集合
    accounts: &[AccountInfo],
		// 该指令的参数
    instruction_data: &[u8],
) -> ProgramResult;
```



## 指令处理函数

Solana 程序帐户仅存储处理指令的逻辑。这意味着程序帐户是“只读”和“无状态”的。程序处理指令所需的“状态”（数据集）存储在数据帐户中（与程序帐户分开）

### 数据账户的定义

我们的 Solana 程序要实现计数器的累加，那就必须先定义该数据是以何种形式存储在 Solana 链上的，这里我们使用结构体CounterAccount，之所以使用Account后缀，因为它是一个数据账户（这个概念一开始对从以太坊转过来的开发者来说很困惑，但慢慢的你就会发现这的确是个巧妙的设计）。

```rust
/// 定义数据账户的结构
#[derive(BorshSerialize, BorshDeserialize, Debug)]
pub struct CounterAccount {
    pub count: u32,
}
```

该结构体中定义了u32类型的count属性，在我们每次发起交易指令时，它都会+1操作。因为该值的存储和传输都是使用的字节码，要把字节码转为Rust 类型，我们还需要（反）序列化操作，这里需要引入borsh库。

```rust
use borsh::{BorshDeserialize, BorshSerialize};
```

实际进行（反）序列化操作是通过`BorshSerialize`、`BorshDeserialize`这2个派生宏实现的，该宏的定义如下，它们都是对解析后类型为`TokenStream`的 Rust 代码元数据进行处理，并返回处理后的元数据。

```rust
#[proc_macro_derive(BorshSerialize, attributes(borsh_skip))]
pub fn borsh_serialize(input: TokenStream) -> TokenStream {
    // 序列化逻辑……
}

#[proc_macro_derive(BorshDeserialize, attributes(borsh_skip, borsh_init))]
pub fn borsh_deserialize(input: TokenStream) -> TokenStream {
    // 反序列化逻辑……
}
```



### 指令处理函数

这个函数的定义我们在前面已经介绍过，这里看下它的实现逻辑。

```rust
pub fn process_instruction(
		// 程序ID，即程序地址
    program_id: &Pubkey,
		// 该指令涉及到的账户集合
    accounts: &[AccountInfo]) -> ProgramResult {
    
    // 账户迭代器
    let accounts_iter = &mut accounts.iter();

    // 获取调用者账户
    let account = next_account_info(accounts_iter)?;

    // ……
}
```

为了处理指令，指令所需的数据帐户必须通过accounts参数显式传递到程序中。这里因为要对数据账户进行累加的操作，所以 accounts 包含了该数据账户，我们通过迭代器获取到该账户account。

●`?`：是一个错误处理操作符，如果`serialize`方法返回错误，整个表达式将提前返回，将错误传播给调用方。

account数据账户是由该程序派生出来的账户，因此当前程序为它的owner所有者，并且只有所有者才可以对其进行写操作。所以我们在这里要进行账户权限的校验。

```rust
// 验证调用者身份
if account.owner != program_id {
    msg!("Counter account does not have the correct program id");
    return Err(ProgramError::IncorrectProgramId);
}
```

全部代码：

```rust
pub fn process_instruction(
		// 程序ID，即程序地址
    program_id: &Pubkey,
		// 该指令涉及到的账户集合
    accounts: &[AccountInfo],
		// 指令数据
    _instruction_data: &[u8],
) -> ProgramResult {
    msg!("Hello World Rust program entrypoint");

    // 账户迭代器
    let accounts_iter = &mut accounts.iter();

    // 获取调用者账户
    let account = next_account_info(accounts_iter)?;

    // 验证调用者身份
    if account.owner != program_id {
        msg!("Counter account does not have the correct program id");
        return Err(ProgramError::IncorrectProgramId);
    }

    // 读取并写入新值
    let mut counter = CounterAccount::try_from_slice(&account.data.borrow())?;
    counter.count += 1;
    counter.serialize(&mut *account.data.borrow_mut())?;

    Ok(())
}
```



## 修改数据

### 读取数据账户

通过上一节的账户权限校验，我们可以确定当前的数据账户是正确的，那么接下来，就是读取该账户存储的数据了。

```javascript
let mut counter = CounterAccount::try_from_slice(&account.data.borrow())?;
```

这行代码的目的是从 Solana 数据账户中反序列化出 `CounterAccount` 结构体的实例。

1. `&account.data`：获取账户的数据字段的引用。在 Solana 中，账户的数据字段data存储着与账户关联的实际数据，对于程序账户而言，它是程序的二进制内容，对于数据账户而言，它就是存储的数据。

2. `borrow()`：使用该方法获取data数据字段的可借用引用。并通过`&account.data.borrow()`方式得到账户数据字段的引用。

3. `CounterAccount::try_from_slice(...)`：调用`try_from_slice`方法，它是`BorshDeserializetrait` 的一个方法，用于从字节序列中反序列化出一个结构体的实例。这里`CounterAccount`实现了`BorshDeserialize`，所以可以使用这个方法。

4. `?`：是一个错误处理操作符，如果`try_from_slice`返回错误，整个表达式将提前返回，将错误传播给调用方。

通过如上方式，我们获取了`CounterAccount`数据账户进行了反序列化，并获取到它的可变借用。

### 修改数据账户

接下来我们就可以对该数据账户进行修改：

```rust
counter.count += 1;
counter.serialize(&mut *account.data.borrow_mut())?;
```

●首先对`CounterAccount`结构体中的count字段进行递增操作。

●`&mut *account.data.borrow_mut()`：通过`borrow_mut()`方法获取账户数据字段的可变引用，然后使用*解引用操作符获取该data字段的值，并通过`&mut`将其转换为可变引用。

●`serialize`函数方法，它是`BorshSerialize trait` 的一个方法，用于将结构体序列化为字节数组。

●`?`：是一个错误处理操作符，如果`serialize`方法返回错误，整个表达式将提前返回，将错误传播给调用方。

通过如上的方式，将`CounterAccount`结构体中的修改后的值递增，并将更新后的结构体序列化为字节数组，然后写入 Solana 账户的可变数据字段中。实现了在 Solana 程序中对计数器值进行更新和存储。



### 总程序代码

```rust
use borsh::{BorshDeserialize, BorshSerialize};
use solana_program::{
    account_info::{next_account_info, AccountInfo},
    entrypoint,
    entrypoint::ProgramResult,
    msg,
    program_error::ProgramError,
    pubkey::Pubkey,
};

/// 定义数据账户的结构
#[derive(BorshSerialize, BorshDeserialize, Debug)]
pub struct CounterAccount {
    pub count: u32,
}

// 定义程序入口点函数
entrypoint!(process_instruction);

pub fn process_instruction(
		// 程序ID，即程序地址
    program_id: &Pubkey,
		// 该指令涉及到的账户集合
    accounts: &[AccountInfo],
		// 指令数据
    _instruction_data: &[u8],
) -> ProgramResult {
    msg!("Hello World Rust program entrypoint");

    // 账户迭代器
    let accounts_iter = &mut accounts.iter();

    // 获取调用者账户
    let account = next_account_info(accounts_iter)?;

    // 验证调用者身份
    if account.owner != program_id {
        msg!("Counter account does not have the correct program id");
        return Err(ProgramError::IncorrectProgramId);
    }

    // 读取并写入新值
    let mut counter = CounterAccount::try_from_slice(&account.data.borrow())?;
    counter.count += 1;
    counter.serialize(&mut *account.data.borrow_mut())?;

    Ok(())
}
```



部署：

部署成功后，我们可以在 Solana 区块链浏览器中查看[该笔交易信息](https://explorer.solana.com/tx/n4rQU85FmLjB82RAkRvRwj9jYYSseUYfvRi4jY6ZpxuoNGd8qFCiV4UsDbyKVviY5GWcBm7hwNVzMvr5JhvYaux?cluster=devnet)。其中5CiS...为我们的wallet钱包公钥，而CHZn.…为程序账户，它具有executable可执行属性，并且updateable可升级，该程序ID下对应的7Lxk…账户存储了程序的二进制文件（请注意：二进制文件并不是直接存储在程序账户中，而是下面的子账户中）。

![image](./assets/ae64a64e-8d36-47b1-b973-e84bca85f9f7.webp)

这里展示了程序账户和子账户（存储程序二进制文件）之间的关系。

![image](./assets/bfeaba22-1c19-4925-9f2d-17cd8b69976e.webp)

## 状态管理
### 租金计算
```rust
// 计算存储结构体 NoteState 所需的账户大小
// 4字节用于存储后续动态数据（字符串）的大小
// 8字节用于存储64位整数ID
let account_len: usize = (4 + title.len()) + (4 + body.len()) + 8;

// 计算所需租金
let rent = Rent::get()?;
let rent_lamports = rent.minimum_balance(account_len);
```


### 跨程序调用CPI

`CPI`可以使用 `invoke` 或 `invoke_signed` 来实现。

```
pub fn invoke(
    instruction: &Instruction,
    account_infos: &[AccountInfo],
) -> ProgramResult
```

```
pub fn invoke_signed(
    instruction: &Instruction,
    account_infos: &[AccountInfo],
    signers_seeds: &[&[u8]],
) -> ProgramResult
```

当你不需要签署交易时，使用 `invoke`。当你需要签署交易时，使用 `invoke_signed`。在我们的例子中，我们是唯一可以为`PDA`签署的人，因此我们将使用 `invoke_signed`。
![](assets/Pasted%20image%2020240810220338.png)



# Anchor

Anchor 是一个用于**快速**、**安全**的构建 Solana 程序的框架。它为您编写大量的样板代码，比如（反）序列化帐户和指令数据等，使您更专注于业务逻辑的开发。同时，它也会执行特定的安全检查、账号验证等，当然，也支持您轻松地实现自定义的其他检查。

Anchor 也为前端项目提供了一系列的库和工具，简化了跟链上程序交互的复杂度。它也对 PDA （程序衍生账户）、CPI（跨程序调用） 提供了一系列的支持。



## 安装

Anchor 本身是 Rust 写的 Solana 开发框架，同时也支持前端项目，因此它的安装涉及到一系列的依赖，比如 Rust、Solana、Yarn，在完成依赖的安装后，再安装 Anchor 版本管理工具(Anchor Version Manager) avm，如果你熟悉 Nodejs，他就像管理 nodejs 版本的 nvm。 通过avm，我们能更加灵活的使用和管理 Anchor。[安装指南可以看这里](https://www.anchor-lang.com/docs/installation)。



```sh

C:\Users\Administrator>solana-keygen new
Generating a new keypair

For added security, enter a BIP39 passphrase

NOTE! This passphrase improves security of the recovery seed phrase NOT the
keypair file itself, which is stored as insecure plain text

BIP39 Passphrase (empty for none):


Wrote new keypair to C:\Users\Administrator\.config\solana\id.json
=====================================================================
pubkey: GhuT9vp4JnLEYBsDFFAShUXekw4nSEq8XZyrsc8efPNu
=====================================================================
Save this seed phrase and your BIP39 passphrase to recover your new keypair:
coyote honey kit valve excuse nominee farm fan trick board quote tone
=====================================================================
```





### 常用指令

1、**创建新项目**：这个命令用于创建一个新的 Anchor 项目，包含了 demo 代码，你可以用自己的项目名称替换 **my_project** 。

```rust
anchor init my_project
```

2、**构建新的程序（智能合约）**：这个命令用于构建和编译程序。它会在target/deploy目录下生成编译后的合约二进制文件。如果在项目目录下可以省略项目名称。

```bash
anchor build [my_project]
```

**3**、**测试程序逻辑**：运行这个命令会执行程序的测试套件，确保程序的功能正常。

```bash
anchor test
```

4**、部署程序到指定网络**：Solana 的 devnet 是一个专门用于开发和测试的网络，通常我们会把项目先部署在本地或者开发测试网进行验证，验证通过后部署到主网 mainnet-beta。

```rust
// 部署到开发测试网
anchor deploy --env devnet
// 部署到主网
anchor deploy --env mainnet-beta
```

### 项目目录结构

创建完项目 **my_project** 后，我们进入 **my_project** 文件夹， 可以看到 Anchor 自动生成的文件和目录：

```bash
my_project/
├── Anchor.toml
├── programs/
│   └── my_program/
│       ├── Cargo.toml
│       ├── src/
│       │   └── lib.rs
│       └── tests/
│           └── program_test.rs
├── target/
└── tests/
    └── integration_test.rs
```

这是一个简化的结构，提供了一个基本的框架，使你能够开始编写、测试和部署程序。在具体的项目中，你可能需要根据需求添加其他文件和目录，例如配置文件、文档等。以下是一些关键文件和目录的说明：

●**Anchor.toml****：** 项目的配置文件，包含项目的基本信息、依赖关系和其他配置项。

●**programs**目录**：** 包含你的程序的目录。在这个例子中，有一个名为**my_program**的子目录。

●**Cargo.toml****：** 程序的Rust项目配置文件。

●**src**目录**：** 包含实际的程序代码文件，通常是**lib.rs**，在实际的项目中我们会根据模块划分，拆的更细。

●**tests**目录**：** 包含用于测试程序的测试代码文件。

●**target**目录**：** 包含构建和编译生成的文件。

●**tests**目录**：** 包含整合测试代码文件，用于测试整个项目的集成性能。



`anchor help`

```sh
anchor help

available commands:
  init      初始化一个工作空间。
  build     构建整个工作空间。
  expand    展开宏（cargo expand 的包装）。
  verify    验证链上字节码是否与本地编译的构件匹配。在程序子目录中运行此命令，即包含程序的 Cargo.toml 文件的目录。
  test      在本地网络运行集成测试。
  new       创建一个新的程序。
  idl       与接口定义语言（IDL）交互的命令。
  clean     从目标目录中删除除程序密钥对之外的所有构建产物。
  deploy    部署工作空间中的每个程序。
  migrate   运行部署迁移脚本。
  upgrade   部署、初始化接口定义并一次性迁移所有内容的命令。升级单个程序。配置的钱包必须是升级权限。
  cluster   集群命令。
  shell     启动一个带有 anchor 客户端设置的节点 shell。
  run       运行由当前工作空间的 anchor.toml 定义的脚本。
  login     将来自注册表的 API 令牌保存到本地。
  publish   将经过验证的构建发布到 anchor 注册表。
  keys      密钥对命令。
  localnet  本地网络命令。
  account   使用提供的接口定义获取并反序列化帐户。
  help      打印此消息或给定子命令的帮助信息。
```

```rust
anchor help deploy
```



### **anchor init** **和** **anchor new** **有什么区别？**

它们都是用于创建 Anchor 项目的命令，但它们有不同的目的和用法：

1.`anchor init` ：初始化工作空间，该命令会创建一个包含基本项目结构和配置文件的工作空间。你需要指定一个工作空间的名称，例如 `anchor init my_workspace` 中的 `my_workspace`。

2.`anchor new`：创建新程序，创建一个新的 Anchor 程序（智能合约），包括相关的目录和文件。你需要指定一个程序的名称，例如 `anchor new my_program` 中的`my_program`。

通常，你会首先使用anchor init初始化一个工作空间，然后使用anchor new在该工作空间中创建一个或多个程序。

### anchor verify 指令有什么作用？

该指令用于验证链上部署的程序的字节码是否与本地编译的构件（artifact）匹配。这个命令通常在开发者部署程序到 Solana 区块链之后使用，以确保链上的合约与本地开发环境中的代码一致。

在使用这个命令时，你需要在包含程序程序的目录中运行，即包含程序的Cargo.toml文件的目录。执行anchor verify会比较链上合约的字节码和本地编译的构件，确保它们一致。

验证的过程通常包括以下步骤：

1.检查链上合约的程序 ID 是否与本地编译的程序 ID 一致。

2.对比链上合约的字节码和本地编译的构件，确保它们相匹配。

通过这个命令，开发者可以确保在本地开发环境中编写的合约与实际部署到链上的合约是一致的，减少了由于编译或其他环境问题引起的潜在错误。



### anchor upgrade 指令有什么作用？

该命令是用于一次性升级程序的命令。它的作用是部署、初始化接口定义（IDL），并将执行所有迁移的过程集成在一起。这个命令通常用于升级一个单独的程序，而不是整个工作空间。这个命令会执行以下步骤：

1.**部署最新版本的程序：**意味着将程序的最新版本上传到 Solana 区块链网络上，并在链上创建一个新的程序实例。这个过程涉及将新的程序二进制文件部署到链上，以便替换先前部署的程序版本。

2.**初始化接口定义**（IDL）：IDL 是一种描述程序接口的语言，用于定义合约的数据结构、方法和事件。初始化接口定义的过程是将这些接口定义部署到链上，以便客户端能够与程序进行交互，并能够正确地解析合约的数据结构和方法。

3.**执行所有必要的迁移脚本**：迁移脚本是用于在合约升级或修改时执行的脚本。它包含了在链上执行的逻辑，例如更新存储结构、迁移数据等。迁移脚本的目的是确保链上合约状态的平滑过渡，使新版本的合约能够与旧版本兼容。





## 程序结构

在执行完anchor init my_project命令后，会自动生成 Anchor 示例项目，其中的lib.rs文件是 Anchor 框架的核心模块，包含了许多macros宏，这些宏为我们的程序生成 Rust 模板代码以及相应的安全校验。这里主要用到的宏是：

●**declare_id!**: 声明**程序地址**。该宏创建了一个存储程序地址program_id的字段，你可以通过一个指定的program_id访问到指定的链上程序。

●**#[program]**: 程序的**业务逻辑代码**实现都将在#[program]模块下完成。

●**#[derive(Accounts)]**: 由于Solana 账户模型的特点，大部分的参数将以**账户集合**的形式传入程序，在该宏修饰的结构体中定义了程序所需要的账户集合。

●**#[account]**：该宏用来修饰程序所需要的自定义账户。

### Anchor 框架的结构

我们以下面代码为例，该程序使用instruction_one指令函数接收u64类型的数据，并保存在链上Counter结构体中。当然，Solana 中一切皆账户，所以Counter结构体也是该程序的派生账户 PDA。

```rust
// 引入 anchor 框架的预导入模块
use anchor_lang::prelude::*;

// 程序的链上地址
declare_id!("3Vg9yrVTKRjKL9QaBWsZq4w7UsePHAttuZDbrZK3G5pf");

// 指令处理逻辑
#[program]
mod anchor_counter {
    use super::*;
    pub fn instruction_one(ctx: Context<InstructionAccounts>, instruction_data: u64) -> Result<()> {
        ctx.accounts.counter.data = instruction_data;
        Ok(())
    }
}

// 指令涉及的账户集合
#[derive(Accounts)]
pub struct InstructionAccounts<'info> {
    #[account(init, seeds = [b"my_seed", user.key.to_bytes().as_ref()], payer = user, space = 8 + 8)]
    pub counter: Account<'info, Counter>,
    #[account(mut)]
    pub user: Signer<'info>,
    pub system_program: Program<'info, System>,
}

// 自定义账户类型
#[account]
pub struct Counter {
    data: u64
}
```





**1、导入框架依赖：**这里导入了 Anchor 框架的预导入模块，其中包含了编写Solana 程序所需的基本功能，比如AnchorDeserialize和AnchorSerialize（反序列化和序列化）、Accounts（用于定义和管理程序账户的宏）、Context（提供有关当前程序执行上下文的信息，包括账户、系统程序等）…**…**



```rust
1// 引入相关依赖
2use anchor_lang::prelude::*;
```

**2、** **declare_id!宏**：指定 Solana 链上程序地址。当你首次构建 Anchor 程序时，框架会自动生成用于部署程序的默认密钥对，其中相应的公钥即为declare_id!宏所声明的程序ID（program_id）。

通常情况下，每次构建 Anchor 框架的 Solana 程序时，program_id 都会有所不同。但是通过declare_id!宏指定某个地址，我们就能保证程序升级后的地址不变。这种升级方式比起以太坊中智能合约的升级（使用代理模式），要简单很多。



```rust
1// 这里只是示意，实际的 program_id 会有所不同
2declare_id!("Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS");
```

**3、** **#[program]宏**：修饰包含了所有程序 instructions 指令的模块，该模块中实现了处理指令的具体业务逻辑，每个pub修饰的公共函数，都是一个单独的指令。函数的参数有以下2种：

●第一个参数为 Context 类型，包含了处理该指令的上下文信息。

●第二个参数为指令的数据，可选。

**4、** **#[derive(Accounts)] 派生宏**：定义了 instruction 指令所要求的账户列表。该宏实现了给定结构体InstructionAccounts（反）序列化的 Trait 特征，因此在获取账户时不再需要手动迭代账户以及反序列化操作。并且实现了账户满足程序安全运行所需要的安全检查，当然，需要`#[account]`宏配合使用。

**5、** **#[account]**：该宏用来修饰程序所需要的自定义账户，它支持定义账户的属性并实现相应的安全校验。这里我们的自定义了一个计数器Counter。当然，可以有更复杂的结构，取决于我们的具体业务逻辑。



### #[program]宏

> 该宏定义一个 Solana 程序模块，其中包含了程序的指令（instructions）和其他相关逻辑。它包含如下的功能：

**1、定义处理不同指令的函数：**在程序模块中，开发者可以定义处理不同指令的函数。这些函数包含了具体的指令处理逻辑。上节只有1个指令函数instruction_one，本节我们在 #[program] 宏中实现了2个指令函数：initialize和increment，用来实现计数器的相关逻辑，使其更接近于实际的业务场景。

```rust
#[program]
mod anchor_counter {
    use super::*;
		// 初始化账户，并以传入的 instruction_data 作为计数器的初始值
    pub fn initialize(ctx: Context<InitializeAccounts>, instruction_data: u64) -> Result<()> {
				ctx.accounts.counter.count = instruction_data;
        Ok(())
    }

		// 在初始值的基础上实现累加 1 操作
    pub fn increment(ctx: Context<UpdateAccounts>) -> Result<()> {
        let counter = &mut ctx.accounts.counter;
        msg!("Previous counter: {}", counter.count);
        counter.count = counter.count.checked_add(1).unwrap();
        msg!("Counter incremented. Current count: {}", counter.count);
        Ok(())
    }
}
```

**2、提供与** **Solana SDK** **交互的功能：**通过 #[program] 宏，Anchor 框架提供了一些功能，使得与 Solana SDK 进行交互变得更加简单。例如，可以直接使用 declare_id、Account、Context、Sysvar 等结构和宏，而不必手动编写底层的 Solana 交互代码，本单元第一节我们没有使用 Anchor 框架，所以需要手动迭代账户、判断账户权限等操作，现在 Anchor 已经替我们实现了这些功能。

**3、自动生成 IDL（接口定义语言）：**#[program] 宏也用于自动生成程序的 IDL。IDL 是用于描述 Solana 程序接口的一种规范，它定义了合约的数据结构、指令等。Anchor 框架使用这些信息生成用于与客户端进行交互的 Rust 代码。

Solana 的 IDL（接口定义语言）和以太坊的 ADSL（Application Binary Interface Description Language）有一些相似之处。它们都是一种用于描述智能合约接口的语言规范，包括合约的数据结构、指令等信息。

### Context

Context是 Anchor 框架中定义的一个结构体，用于封装与 Solana 程序执行相关的上下文信息，**包含了 instruction 指令元数据以及逻辑中所需要的所有账户信息**。它的结构如下：

```rust
// anchor_lang::context
pub struct Context<'a, 'b, 'c, 'info, T> {
    /// 当前的program_id
    pub program_id: &'a Pubkey,
    /// 反序列化的账户集合accounts
    pub accounts: &'b mut T,
    /// 不在 accounts 中的账户，它是数组类型
    pub remaining_accounts: &'c [AccountInfo<'info>],
    /// ...
}
```

Context 使用泛型`T`指定了指令函数所需要的账户集合，在实际的使用中我们需要指定泛型 `T` 的具体类型，如`Context<InitializeAccounts>`、`Context<UpdateAccounts>`等，通过这个参数，指令函数能够获取到如下数据：

●`ctx.program_id`：程序ID，当前执行的程序地址。它是一个 Pubkey 类型的值。

●`ctx.accounts`：账户集合，它的类型为泛型 T 所指定的具体类型，如初始化操作所需的账户集合`InitializeAccounts`，更新操作所需的账户集合`UpdateAccounts`，通过派生宏 `#[derive(Accounts)]` 生成。并且 Anchor 框架会为我们自动进行反序列化。

●`ctx.remaining_accounts`：剩余账户集合，包含了当前指令中未被 `#[derive(Accounts)]` 明确声明的账户。它提供了一种灵活的方式，使得程序能够处理那些在编写程序时不确定存在的账户，或者在执行过程中动态创建的账户。其中的账户不支持直接的反序列化，需要手动处理。

#### `Context<T>` 泛型 T

我们先看下第一个指令函数的泛型T：`InitializeAccounts`，该账户集合有3个账户，第1个为数据账户`pda_counter`，它是该程序的衍生账户，用于存储计数器数据；第2个参数为对交易发起签名的账户`user`，支付了该笔交易费；第3个参数为 Solana 系统账户`system_program`，因为PDA账户需要由系统生成，所以我们也需要这个系统账户。

```rust
#[derive(Accounts)]
pub struct InitializeAccounts<'info> {
		// pda 账户
    #[account(init, seeds = [b"my_seed", user.key.to_bytes().as_ref()], payer = user, space = 8 + 8)]
    pub pda_counter: Account<'info, Counter>,
		// 交易签名账户
    #[account(mut)]
    pub user: Signer<'info>,
    pub system_program: Program<'info, System>,
}
```

#### 指令参数（可选）

在 Anchor 框架中，指令函数的第一个参数ctx是**必须**的，而第二个参数是指令函数执行时传递的额外数据，是**可选**的，是否需要取决于指令的具体逻辑和需求。在initialize中，它被用于初始化计数器的初始值；而在increment中，该指令不需要额外的数据，所以只有ctx参数。



### Accounts

使用ctx.accounts可以获取指令函数的账户集合InitializeAccounts，它是一个实现了#[derive(Accounts)]派生宏的结构体。该派生宏为结构体生成与 Solana 程序账户相关的处理逻辑，以便开发者能够更方便地访问和管理其中的账户。

```rust
// anchor_lang::context
pub struct Context<'a, 'b, 'c, 'info, T> {
    pub accounts: &'b mut T,
    // ...
}

#[program]
mod anchor_counter {
    pub fn initialize(ctx: Context<InitializeAccounts>, instruction_data: u64) -> Result<()> {
        ctx.accounts.counter.count = instruction_data;
        Ok(())
    }
}

#[derive(Accounts)]
pub struct InitializeAccounts<'info> {
    #[account(init, payer = user, space = 8 + 8)]
    pub counter: Account<'info, Counter>,
    // ...
} 
```



#### #[derive(Accounts)] 宏的介绍

该宏应用于指令所要求的账户列表，实现了给定 struct 结构体数据的反序列化功能，**因此在获取账户时不再需要手动迭代账户以及反序列化操作，并且实现了账户满足程序安全运行所需要的安全检查**，当然，需要#[account]宏配合使用。

1、下面我们看下示例中的InitializeAccounts结构体，当initialize指令函数被调用时，程序会做如下2个校验：

```rust
#[derive(Accounts)]
pub struct InitializeAccounts<'info> {
    #[account(init, seeds = [b"my_seed", user.key.to_bytes().as_ref()], payer = user, space = 8 + 8)]
    pub pda_counter: Account<'info, Counter>,
    #[account(mut)]
    pub user: Signer<'info>,
    pub system_program: Program<'info, System>,
}
```

●**账户类型校验：**传入的账户是否跟InitializeAccounts定义的账户类型相匹配，例如Account、Singer、Program等类型。

●**账户权限校验**：根据账户标注的权限，框架会进行相应的权限校验，例如检查是否有足够的签名权限、是否可以修改等。

如果其中任何一个校验失败，将导致指令执行失败并产生错误。

2、InitializeAccounts结构体中有如下3种账户类型：

2.1、 **Account类型**：它是AccountInfo类型的包装器，**可用于验证账户所有权并将底层数据反序列化为Rust类型**。对于结构体中的counter账户，Anchor 会实现如下功能：

```rust
pub pda_counter: Account<'info, Counter>,
```


① 该账户类型的 Counter 数据自动实现反序列化。

② 检查传入的所有者是否跟 Counter 的所有者匹配。

2.2、**Signer类型**：这个类型会检查给定的账户是否签署了交易，但并不做所有权的检查。只有在并不需要底层数据的情况下，才应该使用Signer类型。**表示这个账户是一个签名者，即它拥有进行交易或操作的权限。**

```rust
pub user: Signer<'info>,
```

2.3、**Program类型**：验证这个账户是个特定的程序。对于system_program 字段，Program 类型用于指定程序应该为系统程序，Anchor 会替我们完成校验。

system_program 属性是用于执行Solana上的基本操作，**如账户的创建和管理。这个属性是程序与Solana系统级功能交互的桥梁。**

`<'info> `确保这些引用在整个 **Initialize** 结构体的生命周期内都是有效的。这意味着，只要 **Initialize** 结构体存在，其中的账户数据就可以安全地被访问和使用。

```rust
pub system_program: Program<'info, System>,
```

完整代码



```rust
// 引入 anchor 框架的预导入模块
use anchor_lang::prelude::*;

// 程序的链上地址
declare_id!("3Vg9yrVTKRjKL9QaBWsZq4w7UsePHAttuZDbrZK3G5pf");

// 指令处理逻辑
#[program]
mod anchor_counter {
    use super::*;
    pub fn initialize(ctx: Context<InitializeAccounts>, instruction_data: u64) -> Result<()> {
        ctx.accounts.counter.count = instruction_data;
        Ok(())
    }

    pub fn increment(ctx: Context<UpdateAccounts>) -> Result<()> {
        let counter = &mut ctx.accounts.counter;
        msg!("Previous counter: {}", counter.count);
        counter.count = counter.count.checked_add(1).unwrap();
        msg!("Counter incremented. Current count: {}", counter.count);
        Ok(())
    }
}

// 指令涉及的账户集合
#[derive(Accounts)]
pub struct InitializeAccounts<'info> {
    #[account(init, seeds = [b"my_seed", user.key.to_bytes().as_ref()], payer = user, space = 8 + 8)]
    pub pda_counter: Account<'info, Counter>,
    #[account(mut)]
    pub user: Signer<'info>,
    pub system_program: Program<'info, System>,
}

#[derive(Accounts)]
pub struct UpdateAccounts<'info> {
    #[account(mut)]
    pub counter: Account<'info, Counter>,
    pub user: Signer<'info>,
}

// 自定义账户类型
#[account]
pub struct Counter {
    count: u64
}
```



## account核心

 Anchor实现的**账户属性约束**：**#[account(..)]**。

```rust
#[derive(Accounts)]
struct ExampleAccounts {
  #[account(
    seeds = [b"example_seed"],
    bump
  )]
  pub pda_account: Account<'info, AccountType>,
  
	#[account(mut)]
  pub user: Signer<'info>,
}
```

### **#[account(..)]** 宏的介绍

它是 Anchor 框架中的一个属性宏，提供了一种声明式的方式来指定账户的初始化、权限、空间（占用字节数）、是否可变等属性，从而简化了与 Solana 程序交互的代码。也可以看成是一种账户属性约束。

**1、初始化一个派生账户地址 PDA ：**它是根据seeds、program_id以及bump动态计算而来的，其中的bump是程序在计算地址时自动生成的一个值（Anchor 默认使用符合条件的第一个 bump 值），不需要我们手动指定。

```rust
#[account(
	init, 
	seeds = [b"my_seed"], 
	bump,
	payer = user, 
	space = 8 + 8
)]
pub pda_counter: Account<'info, Counter>,
pub user: Signer<'info>,
```

●**init：**Anchor 会通过相关属性配置初始化一个派生帐户地址 PDA 。

●**seeds：**种子（seeds）是一个任意长度的字节数组，通常包含了派生账户地址 PDA 所需的信息，在这个例子中我们仅使用了字符串my_seed作为种子。当然，也可以包含其他信息：如指令账户集合中的其他字段user、指令函数中的参数instruction_data，示意代码如下：

```rust
#[derive(Accounts)]
#[instruction(instruction_data: String)]
pub struct InitializeAccounts<'info> {
		#[account(
			init, 
			seeds = [b"my_seed", 
			user.key.to_bytes().as_ref(),
			instruction_data.as_ref()]
			bump,
			payer = user, 
			space = 8 + 8
		)]
		pub pda_counter: Account<'info, Counter>,
		pub user: Signer<'info>,
}
```

●**payer：**指定了支付账户，即进行账户初始化时，使用user这个账户支付交易费用。

●**space：**指定账户的空间大小为16个字节，前 8 个字节存储 Anchor 自动添加的鉴别器，用于识别帐户类型。接下来的 8 个字节为存储在Counter帐户类型中的数据分配空间（count为 u64 类型，占用 8 字节）。

**2、验证派生账户地址 PDA ：**有些时候我们需要在调用指令函数时，验证传入的 PDA 地址是否正确，也可以采用类似的方式，只需要传入对应的seeds和bump即可，Anchor就会按照此规则并结合program_id来计算 PDA 地址，完成验证工作。注意：这里不需要init属性。

```rust
#[derive(Accounts)]
#[instruction(instruction_data: String)]
pub struct InitializeAccounts<'info> {
		#[account(
			seeds = [b"my_seed", 
							 user.key.to_bytes().as_ref(),
							 instruction_data.as_ref()
							]
			bump
		)]
		pub pda_counter: Account<'info, Counter>,
		pub user: Signer<'info>,
}
```

**3、** **#[account(mut)]** **属性约束：**

●**mut：**表示这是一个可变账户，，即在程序的执行过程中，这个账户的数据（包括余额）可能会发生变化。在Solana 程序中，对账户进行写操作需要将其标记为可变。





```rust
#[derive(Accounts)]
pub struct InstructionAccounts {
		// 账户属性约束
    #[account(init, seeds = [b"mySeeds"], payer = user, space = 8 + 8)]
    pub account_name: Account<'info, MyAccount >,
    ...
}

// 账户结构体上的 #[account] 宏
#[account]
pub struct MyAccount {
    pub my_data: u64,
}
```

### **#[account]** 宏的介绍

Anchor 框架中，#[account]宏是一种特殊的宏，它用于处理账户的**（反）序列化**、**账户识别器、所有权验证**。这个宏大大简化了程序的开发过程，使开发者可以更专注于业务逻辑而不是底层的账户处理。它主要实现了以下几个 Trait 特征：

●**（反）序列化**：Anchor框架会自动为使用 #[account] 标记的结构体实现序列化和反序列化。这是因为 Solana 账户需要将数据序列化为字节数组以便在网络上传输，同时在接收方需要将这些字节数组反序列化为合适的数据结构进行处理。

●**Discriminator（账户识别器）**：它是帐户类型的 8 字节唯一标识符，源自帐户类型名称 SHA256 哈希值的前 8 个字节。在实现帐户序列化特征时，前 8 个字节被保留用于帐户鉴别器。因此，在对数据反序列化时，就会验证传入账户的前8个字节，如果跟定义的不匹配，则是无效账户，账户反序列化失败。

●**Owner（所有权校验）**：使用 #[account] 标记的结构体所对应的 Solana 账户的所有权属于程序本身，也就是在程序的**declare_id!**宏中声明的程序ID。上面代码中MyAccount账户的所有权为程序本身。







## Demo

### 猜数游戏


```rust
use anchor_lang::prelude::*;
use solana_program::clock::Clock;

declare_id!("11111111111111111111111111111111");

#[program]
pub mod anchor_bac {
    use super::*;
    use std::cmp::Ordering;

    pub fn initialize(ctx: Context<AccountContext>) -> Result<()> {
        let guessing_account = &mut ctx.accounts.guessing_account;
        guessing_account.number = generate_random_number();
        Ok(())
    }

    pub fn guess(ctx: Context<AccountContext>, number: u32) -> Result<()> {
        let guessing_account = &mut ctx.accounts.guessing_account;
        let target = guessing_account.number;

        match number.cmp(&target) {
            Ordering::Less => return err!(MyError::NumberTooSmall),
            Ordering::Greater => {
                return err!(MyError::NumberTooLarge);
            }
            Ordering::Equal => return Ok(()),
        }
    }
}

fn generate_random_number() -> u32 {
    let clock = Clock::get().expect("Failed to get clock");
    let last_digit = (clock.unix_timestamp % 10) as u8;
    let result = (last_digit + 1) as u32;
    result
}

#[account]
pub struct GuessingAccount {
    pub number: u32,
}

#[derive(Accounts)]
pub struct AccountContext<'info> {
    #[account(
        init_if_needed,
        space=32,
        payer=payer,
        seeds = [b"guessing pda"],
        bump
    )]
    pub guessing_account: Account<'info, GuessingAccount>,

    #[account(mut)]
    pub payer: Signer<'info>,

    pub system_program: Program<'info, System>,
}

#[error_code]
pub enum MyError {
    #[msg("Too small")]
    NumberTooSmall,
    #[msg("Too larget")]
    NumberTooLarge,
}
```



```ts
const program = pg.program;
const seeds = Buffer.from("guessing pda");
const guessingPdaPubkey = anchor.web3.PublicKey.findProgramAddressSync(
  [seeds],
  program.programId
);

async function initialize() {
  try {
    const initializeTx = await program.methods
      .initialize()
      .accounts({
        guessingAccount: guessingPdaPubkey[0],
        payer: pg.wallet.publicKey,
        systemProgram: web3.SystemProgram.programId,
      })
      .rpc();

    console.log(
      "Initialize successfully!\n Your transaction signature is:",
      initializeTx
    );
  } catch (errors: any) {
    console.log(errors);
  }
}

async function guessing(number: number) {
  try {
    const guessingTx = await program.methods
      .guess(number)
      .accounts({
        guessingAccount: guessingPdaPubkey[0],
        payer: pg.wallet.publicKey,
        systemProgram: web3.SystemProgram.programId,
      })
      .rpc();
    console.log("Congratulation you're right!");
  } catch (errors: any) {
    console.log(errors.error.errorMessage);
  }
}

// initialize();
guessing(3);

```





### 带有权限验证的计数器项目

```rust
use anchor_lang::prelude::*;
use std::ops::DerefMut;

declare_id!("4rYU4LZNaM1smqPx3omxBrKkRdUEMQvfFoJ2F3nNYVv5");

#[program]
pub mod counter {
    use super::*;

    pub fn initialize(ctx: Context<Initialize>) -> Result<()> {
        let counter = ctx.accounts.counter.deref_mut();
        let bump = ctx.bumps.counter;

        *counter = Counter {
            authority: *ctx.accounts.authority.key,
            count: 0,
            bump,
        };

        Ok(())
    }

    pub fn increment(ctx: Context<Increment>) -> Result<()> {
        require_keys_eq!(
            ctx.accounts.authority.key(),
            ctx.accounts.counter.authority,
            ErrorCode::Unauthorized
        );

        ctx.accounts.counter.count += 1;
        Ok(())
    }
}

#[derive(Accounts)]
pub struct Initialize<'info> {
    #[account(
        init,
        payer = authority,
        space = Counter::SIZE,
        seeds = [b"counter"],
        bump
    )]
    counter: Account<'info, Counter>,
    #[account(mut)]
    authority: Signer<'info>,
    system_program: Program<'info, System>,
}

#[derive(Accounts)]
pub struct Increment<'info> {
    #[account(
        mut,
        seeds = [b"counter"],
        bump = counter.bump
    )]
    counter: Account<'info, Counter>,
    authority: Signer<'info>,
}

#[account]
pub struct Counter {
    pub authority: Pubkey,
    pub count: u64,
    pub bump: u8,
}

impl Counter {
    pub const SIZE: usize = 8 + 32 + 8 + 1;
}

#[error_code]
pub enum ErrorCode {
    #[msg("You are not authorized to perform this action.")]
    Unauthorized,
}
```



用户的调用代码

```rust
const wallet = pg.wallet;
const program = pg.program;
const counterSeed = Buffer.from("counter");

const counterPubkey = await web3.PublicKey.findProgramAddressSync(
  [counterSeed],
  pg.PROGRAM_ID
);

const initializeTx = await pg.program.methods
  .initialize()
  .accounts({
    counter: counterPubkey[0],
    authority: pg.wallet.publicKey,
    systemProgram: web3.SystemProgram.programId,
  })
  .rpc();

let counterAccount = await program.account.counter.fetch(counterPubkey[0]);
console.log("account after initializing ==> ", Number(counterAccount.count));

const incrementTx = await pg.program.methods
  .increment()
  .accounts({
    counter: counterPubkey[0],
    authority: pg.wallet.publicKey,
  })
  .rpc();

counterAccount = await program.account.counter.fetch(counterPubkey[0]);
console.log("account after increasing ==>", Number(counterAccount.count));
```





- nft

使用**Solana CLI** 将网络设置为 **devnet**

```sh
solana config set --url devnet
```

确认网络是否设置生效

```sh
Config File: /Users/anoushkkharangate/.config/solana/cli/config.yml
RPC URL: https://api.devnet.solana.com
WebSocket URL: wss://api.devnet.solana.com/ (computed)
Keypair Path: /Users/anoushkkharangate/.config/solana/id.json
Commitment: confirmed
```

**使用 Anchor CLI 来** **创建项目**

```sh
anchor init metaplex_nft
```

找到Anchor.toml 文件，其中 provider 的网络是否设置为 **devnet**

```rust
[features]
seeds = false
[programs.devnet]
metaplex_nft = "Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS"

[registry]
url = "https://anchor.projectserum.com"

[provider]
cluster = "devnet"
wallet = "/Users/<user-name>/.config/solana/id.json"

[scripts]
test = "yarn run ts-mocha -p ./tsconfig.json -t 1000000 tests/**/*.ts"
```

**项目导入依赖：**

找到 **programs** 的文件夹，转到 programs/metaplex_nft/Cargo.toml 并添加这些依赖项。

```rust
[dependencies]
anchor-lang = "0.24.2"
anchor-spl = "0.24.2"
mpl-token-metadata = { version = "1.2.5", features = ["no-entrypoint"] }
```



```rust
use anchor_lang::prelude::*;
use anchor_lang::solana_program::program::invoke;
use anchor_spl::token;
use anchor_spl::token::{MintTo, Token};
use mpl_token_metadata::instruction::{create_master_edition_v3, create_metadata_accounts_v2};

declare_id!("3bfaUxYjL8PhJbCiw9rxjaijdgyUs8cJNGSufPuaPuKu");

#[program]
pub mod metaplex_nft {
    use super::*;

    pub fn mint_nft(
        ctx: Context<MintNFT>,
        creator_key: Pubkey,
        uri: String,
        title: String,
    ) -> Result<()> {
        msg!("Initializing Mint Ticket");
        let cpi_accounts = MintTo {
            mint: ctx.accounts.mint.to_account_info(),
            to: ctx.accounts.token_account.to_account_info(),
            authority: ctx.accounts.payer.to_account_info(),
        };
        msg!("CPI Accounts Assigned");
        let cpi_program = ctx.accounts.token_program.to_account_info();
        msg!("CPI Program Assigned");
        let cpi_ctx = CpiContext::new(cpi_program, cpi_accounts);
        msg!("CPI Context Assigned");
        token::mint_to(cpi_ctx, 1)?;
        msg!("Token Minted !!!");
        let account_info = vec![
            ctx.accounts.metadata.to_account_info(),
            ctx.accounts.mint.to_account_info(),
            ctx.accounts.mint_authority.to_account_info(),
            ctx.accounts.payer.to_account_info(),
            ctx.accounts.token_metadata_program.to_account_info(),
            ctx.accounts.token_program.to_account_info(),
            ctx.accounts.system_program.to_account_info(),
            ctx.accounts.rent.to_account_info(),
        ];
        msg!("Account Info Assigned");
        let creator = vec![
            mpl_token_metadata::state::Creator {
                address: creator_key,
                verified: false,
                share: 100,
            },
            mpl_token_metadata::state::Creator {
                address: ctx.accounts.mint_authority.key(),
                verified: false,
                share: 0,
            },
        ];
        msg!("Creator Assigned");
        let symbol = std::string::ToString::to_string("symb");
        invoke(
            &create_metadata_accounts_v2(
                ctx.accounts.token_metadata_program.key(),
                ctx.accounts.metadata.key(),
                ctx.accounts.mint.key(),
                ctx.accounts.mint_authority.key(),
                ctx.accounts.payer.key(),
                ctx.accounts.payer.key(),
                title,
                symbol,
                uri,
                Some(creator),
                1,
                true,
                false,
                None,
                None,
            ),
            account_info.as_slice(),
        )?;
        msg!("Metadata Account Created !!!");
        let master_edition_infos = vec![
            ctx.accounts.master_edition.to_account_info(),
            ctx.accounts.mint.to_account_info(),
            ctx.accounts.mint_authority.to_account_info(),
            ctx.accounts.payer.to_account_info(),
            ctx.accounts.metadata.to_account_info(),
            ctx.accounts.token_metadata_program.to_account_info(),
            ctx.accounts.token_program.to_account_info(),
            ctx.accounts.system_program.to_account_info(),
            ctx.accounts.rent.to_account_info(),
        ];
        msg!("Master Edition Account Infos Assigned");
        // 这是发起cpi调用
        invoke(
            &create_master_edition_v3(
                ctx.accounts.token_metadata_program.key(),
                ctx.accounts.master_edition.key(),
                ctx.accounts.mint.key(),
                ctx.accounts.payer.key(),
                ctx.accounts.mint_authority.key(),
                ctx.accounts.metadata.key(),
                ctx.accounts.payer.key(),
                Some(0),
            ),
            master_edition_infos.as_slice(),
        )?;
        msg!("Master Edition Nft Minted !!!");

        Ok(())
    }
}

#[derive(Accounts)]
pub struct MintNFT<'info> {
    #[account(mut)]
    pub mint_authority: Signer<'info>,

    /// CHECK: This is not dangerous because we don't read or write from this account
    #[account(mut)]
    pub mint: UncheckedAccount<'info>,
    // #[account(mut)]
    pub token_program: Program<'info, Token>,
    /// CHECK: This is not dangerous because we don't read or write from this account
    #[account(mut)]
    pub metadata: UncheckedAccount<'info>,
    /// CHECK: This is not dangerous because we don't read or write from this account
    #[account(mut)]
    pub token_account: UncheckedAccount<'info>,
    /// CHECK: This is not dangerous because we don't read or write from this account
    pub token_metadata_program: UncheckedAccount<'info>,
    /// CHECK: This is not dangerous because we don't read or write from this account
    #[account(mut)]
    pub payer: AccountInfo<'info>,
    pub system_program: Program<'info, System>,
    /// CHECK: This is not dangerous because we don't read or write from this account
    pub rent: AccountInfo<'info>,
    /// CHECK: This is not dangerous because we don't read or write from this account
    #[account(mut)]
    pub master_edition: UncheckedAccount<'info>,
}
```

**构建并部署程序**

运行 anchor build && anchor deploy ，您应该会看到已部署的 **Program ID**

将 **Program ID** 粘贴到 Anchor.toml 和 lib.rs 文件中的临时 ID。



**导入调用 Solana 程序并铸造 NFT 的代码**

在您的项目中，找到 **tests** 的文件夹，转到 tests/metaplex_nft.ts 并粘贴下面的代码：



```ts
import * as anchor from '@project-serum/anchor'
import { Program, Wallet } from '@project-serum/anchor'
import { MetaplexAnchorNft } from '../target/types/metaplex_nft'
import { TOKEN_PROGRAM_ID, createAssociatedTokenAccountInstruction, getAssociatedTokenAddress, createInitializeMintInstruction, MINT_SIZE } from '@solana/spl-token' // IGNORE THESE ERRORS IF ANY
const { SystemProgram } = anchor.web3

describe('metaplex_nft', () => {
  // Configure the client to use the local cluster.
  const provider = anchor.AnchorProvider.env();
  const wallet = provider.wallet as Wallet;
  anchor.setProvider(provider);
  const program = anchor.workspace.MetaplexAnchorNft as Program<MetaplexAnchorNft>

  it("Is initialized!", async () => {
    // Add your test here.

    const TOKEN_METADATA_PROGRAM_ID = new anchor.web3.PublicKey(
      "metaqbxxUerdq28cj1RbAWkYQm3ybzjb6a8bt518x1s"
    );
    const lamports: number =
      await program.provider.connection.getMinimumBalanceForRentExemption(
        MINT_SIZE
      );
    const getMetadata = async (
      mint: anchor.web3.PublicKey
    ): Promise<anchor.web3.PublicKey> => {
      return (
        await anchor.web3.PublicKey.findProgramAddress(
          [
            Buffer.from("metadata"),
            TOKEN_METADATA_PROGRAM_ID.toBuffer(),
            mint.toBuffer(),
          ],
          TOKEN_METADATA_PROGRAM_ID
        )
      )[0];
    };

    const getMasterEdition = async (
      mint: anchor.web3.PublicKey
    ): Promise<anchor.web3.PublicKey> => {
      return (
        await anchor.web3.PublicKey.findProgramAddress(
          [
            Buffer.from("metadata"),
            TOKEN_METADATA_PROGRAM_ID.toBuffer(),
            mint.toBuffer(),
            Buffer.from("edition"),
          ],
          TOKEN_METADATA_PROGRAM_ID
        )
      )[0];
    };

    const mintKey: anchor.web3.Keypair = anchor.web3.Keypair.generate();
    const NftTokenAccount = await getAssociatedTokenAddress(
      mintKey.publicKey,
      wallet.publicKey
    );
    console.log("NFT Account: ", NftTokenAccount.toBase58());

    const mint_tx = new anchor.web3.Transaction().add(
      anchor.web3.SystemProgram.createAccount({
        fromPubkey: wallet.publicKey,
        newAccountPubkey: mintKey.publicKey,
        space: MINT_SIZE,
        programId: TOKEN_PROGRAM_ID,
        lamports,
      }),
      createInitializeMintInstruction(
        mintKey.publicKey,
        0,
        wallet.publicKey,
        wallet.publicKey
      ),
      createAssociatedTokenAccountInstruction(
        wallet.publicKey,
        NftTokenAccount,
        wallet.publicKey,
        mintKey.publicKey
      )
    );

    const res = await program.provider.sendAndConfirm(mint_tx, [mintKey]);
    console.log(
      await program.provider.connection.getParsedAccountInfo(mintKey.publicKey)
    );

    console.log("Account: ", res);
    console.log("Mint key: ", mintKey.publicKey.toString());
    console.log("User: ", wallet.publicKey.toString());

    const metadataAddress = await getMetadata(mintKey.publicKey);
    const masterEdition = await getMasterEdition(mintKey.publicKey);

    console.log("Metadata address: ", metadataAddress.toBase58());
    console.log("MasterEdition: ", masterEdition.toBase58());

    const tx = await program.methods.mintNft(
      mintKey.publicKey,
      "https://arweave.net/y5e5DJsiwH0s_ayfMwYk-SnrZtVZzHLQDSTZ5dNRUHA",
      "NFT Title",
    )
      .accounts({
        mintAuthority: wallet.publicKey,
        mint: mintKey.publicKey,
        tokenAccount: NftTokenAccount,
        tokenProgram: TOKEN_PROGRAM_ID,
        metadata: metadataAddress,
        tokenMetadataProgram: TOKEN_METADATA_PROGRAM_ID,
        payer: wallet.publicKey,
        systemProgram: SystemProgram.programId,
        rent: anchor.web3.SYSVAR_RENT_PUBKEY,
        masterEdition: masterEdition,
      },
      )
      .rpc();
    console.log("Your transaction signature", tx);
  });
});
```



运行 `anchor test`，铸造NFT

```bash
1Account:  4swRFMNovHCkXY3gDgAGBXZwpfFuVyxWpWsgXqbYvoZG1M63nZHxyPRm7KTqAjSdTpHn2ivyPr6jQfxeLsB6a1nX
2Mint key:  DehGx61vZPYNaMWm9KYdP91UYXXLu1XKoc2CCu3NZFNb
3User:  7CtWnYdTNBb3P9eViqSZKUekjcKnMcaasSMC7NbTVKuE
4Metadata address:  7ut8YMzGqZAXvRDro8jLKkPnUccdeQxsfzNv1hjzc3Bo
5MasterEdition:  Au76v2ZDnWSLj23TCu9NRVEYWrbVUq6DAGNnCuALaN6o
6Your transaction signature KwEst87H3dZ5GwQ5CDL1JtiRKwcXJKNzyvQShaTLiGxz4HQGsDA7EW6rrhqwbJ2TqQFRWzZFvhfBU1CpyYH7WhH
7    ✔ Is initialized! (6950ms)
8
9
10  1 passing (7s)
11
12✨  Done in 9.22s.
```

**查看 NFT**

用前面铸造完成输出的 Mint key 替换掉下面链接的 **Token**，打开链接即可查看：

https://solscan.io/token/DehGx61vZPYNaMWm9KYdP91UYXXLu1XKoc2CCu3NZFNb?cluster=devnet





评论区：

- lib.rs

```rust
use solana_program::{
    entrypoint,
    entrypoint::ProgramResult,
    pubkey::Pubkey,
    msg,
    account_info::AccountInfo,
};
pub mod instruction;
use instruction::{MovieInstruction};


entrypoint!(process_instruction);

pub fn add_movie_review(
    program_id: &Pubkey,
    accounts: &[AccountInfo],
    title: String,
    rating: u8,
    description: String
) -> ProgramResult {

    msg!("正在添加电影评论...");
    msg!("标题: {}", title);
    msg!("评分: {}", rating);
    msg!("描述: {}", description);

    Ok(())
}


pub fn process_instruction(
    program_id: &Pubkey,
    accounts: &[AccountInfo],
    instruction_data: &[u8]
) -> ProgramResult {

    let instruction = MovieInstruction::unpack(instruction_data)?;

    match instruction {
        MovieInstruction::AddMovieReview { title, rating, description } => {
            add_movie_review(program_id, accounts, title, rating, description)
        }
    }
}
```

- instruction.rs

```rust
use borsh::{BorshDeserialize};
use solana_program::{program_error::ProgramError};

pub enum MovieInstruction {
    AddMovieReview {
        title: String,
        rating: u8,
        description: String
    }
}

#[derive(BorshDeserialize)]
struct MovieReviewPayload {
    title: String,
    rating: u8,
    description: String
}

impl MovieInstruction {
    pub fn unpack(input: &[u8]) -> Result<Self, ProgramError> {

        let (&variant, rest) = input.split_first().ok_or(ProgramError::InvalidInstructionData)?;

        let payload = MovieReviewPayload::try_from_slice(rest).unwrap();

        Ok(match variant {
            0 => Self::AddMovieReview {
                title: payload.title,
                rating: payload.rating,
                description: payload.description },
            _ => return Err(ProgramError::InvalidInstructionData)
        })
    }
}
```

