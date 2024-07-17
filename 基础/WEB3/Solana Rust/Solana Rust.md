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

