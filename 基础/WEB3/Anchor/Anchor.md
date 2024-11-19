address：```
```
// windows


// mac
F2qLT9dDJ6Ngd59iPhwKRcHKgBG3LQeRVUQbMe5iX1Bd

// website


```
[程序示例 | Solana中文大全](https://www.solana-cn.com/SolanaDocumention/programs/examples.html#%E5%9F%BA%E7%A1%80)

[如何在 Solana 中编写您的第一个锚点程序 - 第 2 部分 |QuickNode 快节点 --- How to Write Your First Anchor Program in Solana - Part 2 | QuickNode](https://www.quicknode.com/guides/solana-development/anchor/how-to-write-your-first-anchor-program-in-solana-part-2)

[Solana 中文开发教程 (solanazh.com)](https://www.solanazh.com/)

[anchor/examples/tutorial/basic-0 at master · coral-xyz/anchor](https://github.com/coral-xyz/anchor/tree/master/examples/tutorial/basic-0)

[Getting Test SOL | Solana](https://solana.com/developers/cookbook/development/test-sol)


[Solar](https://solanazh.notion.site/Solar-39ce384859524425859978df2c78e173)

One of the best
[Program Examples | Solana](https://solana.com/docs/programs/examples)



## Anchor用法


### 当遇见联合结构体的时候最好直接使用impl，方便一些
```rust
/// PageVisits::INIT_SPACE 当遇见联合结构体的时候最好直接使用impl，方便一些
    #[account(
        space = 8 + PageVisits::INIT_SPACE,
        seeds = [
            PageVisits::SEED_PREFIX,
            payer.key().as_ref(),
        ],
        bump,
    )]

impl PageVisits {
    pub const SEED_PREFIX: &'static [u8; 11] = b"page_visits";
}
```

```rust
/// 以下两个都是赋值给结构体
    *ctx.accounts.page_visits = PageVisits {
        page_visits: 0,
        bump: ctx.bumps.page_visits,
    };
/// `set_inner` 是 Anchor 框架中提供的一种方法，允许你设置账户内部存储的数据。它通常用于结构体账户，其中数据以某种特定的方式存储在账户中。
	ctx.accounts.favorites.set_inner(PageVisits {
		0,
		ctx.bumps.page_visits
	});
```

### 当传入的值是anchor的结构体的时候如何传参
```rust
/// 当传入的值是anchor的结构体的时候如何传参
/// 
    pub fn create_system_account(
        ctx: Context<CreateSystemAccount>,
        address_data: AddressData,
    ) -> Result<()> {}


#[derive(AnchorSerialize, AnchorDeserialize, Debug)]
pub struct AddressData {
    name: String,
    address: String,
}

/// ts

import type { RentExample } from '../target/types/rent_example';

const addressData: anchor.IdlTypes<RentExample>['addressData'] = {
  name: 'Marcus',
  address: '123 Main St. San Francisco, CA',
};

await program.methods
  .createSystemAccount(addressData)
  .accounts({
	payer: wallet.publicKey,
	newAccount: newKeypair.publicKey,
  })
  .signers([wallet.payer, newKeypair])
  .rpc();


/// 这是另一种

pub fn mint_tokens(ctx: Context<MintTokens>, quantity: u64) -> Result<()> {}

#[derive(Accounts)]
pub struct MintTokens<'info> {
    #[account(
        mut,
        seeds = [b"mint"],
        bump,
        mint::authority = mint,
    )]
    pub mint: Account<'info, Mint>,
    #[account(
        init_if_needed,
        payer = payer,
        associated_token::mint = mint,
        associated_token::authority = payer,
    )]
    pub destination: Account<'info, TokenAccount>,
    #[account(mut)]
    pub payer: Signer<'info>,
    pub rent: Sysvar<'info, Rent>,
    pub system_program: Program<'info, System>,
    pub token_program: Program<'info, Token>,
    pub associated_token_program: Program<'info, AssociatedToken>,
}
///ts
const context = {
	mint,
	destination,
	payer,
	rent: web3.SYSVAR_RENT_PUBKEY,
	systemProgram: web3.SystemProgram.programId,
	tokenProgram: anchor.utils.token.TOKEN_PROGRAM_ID,
	associatedTokenProgram: anchor.utils.token.ASSOCIATED_PROGRAM_ID,
};

const tx = await pg.program.methods
.mintTokens(new BN(mintAmount * 10 ** metadata.decimals))
.accounts(context)
.transction();

```
> [program-examples | Look at the test file](https://github.com/solana-developers/program-examples/blob/main/basics/rent/anchor/programs/rent-example/src/lib.rs)



### pda不能做签名 cpi什么时候用signer
```rust
/// **签名**: 由于 PDA 不能直接作为 signer，需要通过 `with_signer(signer_seeds)` 提供 PDA 的种子数组来生成一个有效的签名。这是为了确保在调用相关操作时，能够验证该 PDA 的合法性。


//  这里的 `mint_authority` 和 `update_authority` 被设置为 `mint_account`（一个 PDA）。这意味着铸造和更新操作的权限由这个 PDA 管理。
CpiContext::new(
    ctx.accounts.token_metadata_program.to_account_info(),
    CreateMetadataAccountsV3 {
        metadata: ctx.accounts.metadata_account.to_account_info(),
        mint: ctx.accounts.mint_account.to_account_info(),
        mint_authority: ctx.accounts.mint_account.to_account_info(), // PDA is mint authority
        update_authority: ctx.accounts.mint_account.to_account_info(), // PDA is update authority
        payer: ctx.accounts.payer.to_account_info(),
        system_program: ctx.accounts.system_program.to_account_info(),
        rent: ctx.accounts.rent.to_account_info(),
    },
)
.with_signer(signer_seeds), // 使用 PDA 来签名


/// 因为 `payer` 是一个 signer，因此不需要额外的签名上下文。只要 `payer` 在上下文中被声明为 `Signer`，它就能直接进行操作。
CpiContext::new(
    ctx.accounts.token_metadata_program.to_account_info(),
    CreateMetadataAccountsV3 {
        metadata: ctx.accounts.metadata_account.to_account_info(),
        mint: ctx.accounts.mint_account.to_account_info(),
        mint_authority: ctx.accounts.payer.to_account_info(),
        update_authority: ctx.accounts.payer.to_account_info(),
        payer: ctx.accounts.payer.to_account_info(),
        system_program: ctx.accounts.system_program.to_account_info(),
        rent: ctx.accounts.rent.to_account_info(),
    },
),

```
> 

```rust
if swap_a {
    token::transfer(
        CpiContext::new(
            ctx.accounts.token_program.to_account_info(),
            Transfer {
                from: ctx.accounts.trader_account_a.to_account_info(),
                to: ctx.accounts.pool_account_a.to_account_info(),
                authority: ctx.accounts.trader.to_account_info(),
            },
        ),
        input,
    )?;
    token::transfer(
        CpiContext::new_with_signer(
            ctx.accounts.token_program.to_account_info(),
            Transfer {
                from: ctx.accounts.pool_account_b.to_account_info(),
                to: ctx.accounts.trader_account_b.to_account_info(),
                authority: ctx.accounts.pool_authority.to_account_info(),
            },
            signer_seeds,
        ),
        output,
    )?;
}

```
> 背景是amm用户向池子进行`token`转账
> 这里为什么第一个`transfer`中的`CpiContext`使用的是`CpiContext::new`而不是`CpiContext::new_with_signer`，因为`authority`的值是`trader`本身，所以不需要`signer`
> 第二个`transfer`的`authority`是`pool_authority`所以说需要相应的`signer`.




### `Program<'info, System>`使用场景：

- 当你需要创建新的账户。
- 当你需要关闭账户并将余额转移到其他账户。
- 当你需要执行与 SOL 代币转账相关的操作。
```rust
use anchor_lang::prelude::*;
use anchor_spl::token::Token;

#[derive(Accounts)]
pub struct CreateAccount<'info> {
    #[account(mut)]
    pub payer: Signer<'info>, // 账户创建者
    #[account(
        init,
        payer = payer,
        space = 8 + std::mem::size_of::<MyAccount>(),
    )]
    pub new_account: Account<'info, MyAccount>,
    pub system_program: Program<'info, System>, // 引入系统程序
}

#[program]
pub mod my_program {
    use super::*;

    pub fn create_account(ctx: Context<CreateAccount>) -> Result<()> {
        // 可以在这里对 new_account 进行初始化
        Ok(())
    }
}

```


### `Program<'info, AssociatedToken>`使用场景：

- 当你需要创建新的关联账户以存储特定代币。
- 当你需要验证某个账户是否为特定代币的关联账户。
- 在涉及代币转移的操作中，当目标账户是关联账户时。


### **SystemAccount vs. TokenAccount**
- `SystemAccount<'info>` 表示一个普通的 Solana 账户，它可以用作转账的接收者、控制权的持有者等，但它不特定于持有任何类型的代币。
- `TokenAccount` 是一个专门的账户类型，设计用来存储和管理 SPL 代币的余额。这种账户能够执行与代币相关的操作，例如转账、查询余额等。
```rust
  
pub recipient: SystemAccount<'info>,

#[account(mut)]
pub mint_account: Account<'info, Mint>,
  
#[account(
init_if_needed,
payer = mint_authority,
associated_token::mint = mint_account,
associated_token::authority = recipient,
)]
pub associated_token_account: Account<'info, TokenAccount>,
```
> 这个意思很简单，就是用户是`SystemAccount`账户，再然后通过该账户生成对应的`TokenAccount`
> 这就不用PDA生成的账户了。
> [program-examples/tokens/transfer-tokens/anchor/programs/transfer-tokens/src/instructions/mint.rs at main · solana-developers/program-examples · GitHub](https://github.com/solana-developers/program-examples/blob/main/tokens/transfer-tokens/anchor/programs/transfer-tokens/src/instructions/mint.rs)


### 随机性VRF
[solana-co-learn/docs/Solana-Co-Learn/module6/randomness/randomising-loot-with-switchborar/README.md at main · CreatorsDAO/solana-co-learn](https://github.com/CreatorsDAO/solana-co-learn/blob/main/docs/Solana-Co-Learn/module6/randomness/randomising-loot-with-switchborar/README.md)

### 工具
[solana-co-learn/docs/awesome-solana-zh 在 Main ·创作者DAO/solana-co-learn --- solana-co-learn/docs/awesome-solana-zh at main · CreatorsDAO/solana-co-learn](https://github.com/CreatorsDAO/solana-co-learn/tree/main/docs/awesome-solana-zh)



### 使用`Box`
```rust
pub struct CreatePool<'info> {
    #[account(
        seeds = [
            amm.id.as_ref()
        ],
        bump,
    )]
    pub amm: Box<Account<'info, Amm>>,

    #[account(
        init,
        payer = payer,
        space = Pool::LEN,
        seeds = [
            amm.key().as_ref(),
            mint_a.key().as_ref(),
            mint_b.key().as_ref(),
        ],
        bump,
    )]
    pub pool: Box<Account<'info, Pool>>,

    /// CHECK: Read only authority
    #[account(
        seeds = [
            amm.key().as_ref(),
            mint_a.key().as_ref(),
            mint_b.key().as_ref(),
            AUTHORITY_SEED,
        ],
        bump,
    )]
    pub pool_authority: AccountInfo<'info>,

    #[account(
        init,
        payer = payer,
        seeds = [
            amm.key().as_ref(),
            mint_a.key().as_ref(),
            mint_b.key().as_ref(),
            LIQUIDITY_SEED,
        ],
        bump,
        mint::decimals = 6,
        mint::authority = pool_authority,
    )]
    pub mint_liquidity: Box<Account<'info, Mint>>,

    pub mint_a: Box<Account<'info, Mint>>,

    pub mint_b: Box<Account<'info, Mint>>,

    #[account(
        init,
        payer = payer,
        associated_token::mint = mint_a,
        associated_token::authority = pool_authority,
    )]
    pub pool_account_a: Box<Account<'info, TokenAccount>>,

    #[account(
        init,
        payer = payer,
        associated_token::mint = mint_b,
        associated_token::authority = pool_authority,
    )]
    pub pool_account_b: Box<Account<'info, TokenAccount>>,

    /// The account paying for all rents
    #[account(mut)]
    pub payer: Signer<'info>,

    /// Solana ecosystem accounts
    pub token_program: Program<'info, Token>,
    pub associated_token_program: Program<'info, AssociatedToken>,
    pub system_program: Program<'info, System>,
}
```

为什么使用 Box：
Box 用于优化性能和减少内存占用：
Rust 中的大型结构体会占用较多栈内存。
Box 将数据存储在堆上，而不是栈上，减少栈内存压力。
在 Solana 中，这种优化尤其重要，因为账户数据可能非常大。



### 多指令

- 合约
[Reading Multiple Instructions | Solana](https://solana.com/developers/cookbook/programs/read-multiple-instructions)
- 调用
[How to Send Bulk Transactions on Solana | QuickNode Guides](https://www.quicknode.com/guides/solana-development/transactions/how-to-send-bulk-transactions-on-solana)



## Rust用法：
### 用于安全地对整数进行加法运算，
```rust
/// 用于安全地对整数进行加法运算，
/// 加法结果不溢出，则返回 `Some(result)`，其中 `result` 是加法的结果。
    pub fn increment(&mut self) {
        self.page_visits = self.page_visits.checked_add(1).unwrap();
    }
```

### 进行指数增加
```rust
/// 进行指数增加
amount >= MIN_AMOUNT_TO_RAISE.pow(self.mint_to_raise.decimals as u32),
```

## `fixed::types::I64F64` 高精度
>`fixed::types::I64F64` 是 Rust 中用于高精度、定点数运算的一种类型，它为需要稳定和精确计算的场景（如金融计算、嵌入式系统等）提供了一个理想的工具。


## 交互

### **显式指定 `signers`**
> 显示指定signers的时候，默认链接的钱包就不会签名了。
1. **使用新生成的密钥对**
    - 当你需要使用一个新生成的密钥对（例如 `Keypair.generate()`）来签署交易时，必须显式指定 `signers`，因为这些密钥对不在当前连接的钱包中。
2. **多签名交易**
    - 当交易需要多个签名者时，每个签名者都需要显式指定。例如，一个多签名账户的交易可能需要多个私钥来签名。
3. **特定账户需要签名**
    - 当某个账户在交易中需要签名，但这个账户不是当前连接的钱包时，需要显式指定 `signers`。
4. **自定义签名逻辑**
    - 当你需要实现自定义的签名逻辑，例如使用硬件钱包或其他外部签名服务时，需要显式指定 `signers`。
- **账户创建**：需要提供签名者来支付账户的创建费用（通常是 `payer`）。
- **资金转账**：需要签名者来验证资金是否允许转移。
- **执行授权操作**：例如铸币、冻结、授权等操作，必须有账户提供签名来验证权限。
[使用不同的签名者修改帐户 - RareSkills --- Modifying accounts using different signers - RareSkills](https://www.rareskills.io/post/anchor-signer)

需要签名的场景：

1. **创建账户（`init` 操作）**
   如果一个账户是通过 `#[account(init)]` 来创建的，通常需要签名者来支付费用并确保账户的初始化。例如，`payer` 必须签署账户创建操作，因为它需要支付相关费用。签名者授权该操作，并且如果涉及到初始化多个账户，相关账户的所有者通常也需要签署。
   
   ```rust
   #[account(
       init,
       payer = payer, // 需要 payer 签署并支付费用
       mint::decimals = 9,
       mint::authority = payer.key(), // payer 是 mint_account 的 authority
   )]
   pub mint_account: Account<'info, Mint>,
   ```

  > 在上述代码中，`payer` 是必须签署交易的，因为它不仅支付费用，还设置了 `mint_account` 的所有权。

   1、**特别注意**：
   > 这里还需要一个`mint_account`签名，因为需要账户初始化，如果没有使用 `seeds` 和 `bump` 参数，那么该账户确实是一个新账户，需要一个独特的公钥（mintKeypair）来签名。
   >
   > 也就是
   > ```ts
   >   .signers([mintKeypair])  // 签名者是 mintKeypair
   > ```
   > 反过来如果是PDA账户，则不需要签名，因为PDA不能签名，可以看前面的[### pda不能做签名 cpi什么时候用signer](### pda不能做签名 cpi什么时候用signer)

2、**ATA账户不需要签名**

> **关联账户 (ATA)**: 是在特定钱包地址和 Mint 账户之间的 Token Account，用来存储特定代币余额的账户。ATA 的初始化可以通过 `init_if_needed` 自动生成，不需要额外签名。
```rust
    #[account(
        init_if_needed,
        payer = payer,
        associated_token::mint = mint_account,
        associated_token::authority = payer,
    )]
    pub associated_token_account: Account<'info, TokenAccount>,
```
> 只需要payer和mint_accout












### 为什么需要存储 `bump`？

- **PDA 是根据 `seeds` 和 `bump` 计算出来的**，而 `seeds` 是固定的。因此，如果你希望在后续指令中使用相同的 PDA，必须确保 **`bump` 一致**。否则，Anchor 会尝试自动计算新的 `bump`，而这会导致计算出的 PDA 地址不同，无法访问到正确的账户。
  
- **存储 `bump`** 使得后续指令能够准确地生成相同的 PDA 地址。




### 本地部署spl
> 参考教程：[How to Fork and Deploy Solana Accounts & Programs from Mainnet to Localhost | QuickNode Guides](https://www.quicknode.com/guides/solana-development/accounts-and-data/fork-programs-to-localnet)
> 这个是直接拉取公链上的nft进行测试，我们如果是创建nft的话不必如此。

#### 1、Clone Metaplex Token Metadata Program

在项目根目录新开一个目录`genesis`
```sh
mkdir genesis
cd genesis
```

```sh
solana program dump -u m metaqbxxUerdq28cj1RbAWkYQm3ybzjb6a8bt518x1s metadata.so
```
> 结果： `Wrote program to metadata.so`，并且会在本地生成相应的so文件


#### 2、Initiate a Local Solana Validator

```sh
solana-test-validator -r --bpf-program metaqbxxUerdq28cj1RbAWkYQm3ybzjb6a8bt518x1s metadata.so
```
> **需要在`genesis`文件夹下**运行这段代码之后会开启一启动本地的 Solana 测试验证器，并会将 Metaplex Token Metadata 程序部署到 Localnet。
> 
> 此时可以在 [Explorer | Solana](https://explorer.solana.com/?cluster=custom)进行查看带有Metaplex部署好的Solana区块链，默认端口`8900`


此时需要修改一下`Anchor.toml`，如下：
```rust
[[test.genesis]]
address = "metaqbxxUerdq28cj1RbAWkYQm3ybzjb6a8bt518x1s"
program = "genesis/metadata.so"
```
> 注意，这里`program`是一个路径，以根目录为基础。


#### 3、Coding
**Rust**
```rust
#[derive(Accounts)]
pub struct CreateToken<'info> {
    #[account(mut)]
    pub payer: Signer<'info>,

    #[account(
        init,
        payer = payer,
        mint::decimals = 9,
        mint::authority = payer.key(),
        mint::freeze_authority = payer.key(),
    )]
    pub mint_account: Account<'info, Mint>,

    /// CHECK: Validate address by deriving pda
    #[account(
        mut,
        seeds = [b"metadata", token_metadata_program.key().as_ref(), mint_account.key().as_ref()],
        bump,
        seeds::program = token_metadata_program.key(),
    )]
    pub metadata_account: UncheckedAccount<'info>,

    pub token_program: Program<'info, Token>,
    pub token_metadata_program: Program<'info, Metadata>,
    pub system_program: Program<'info, System>,
    pub rent: Sysvar<'info, Rent>,
}
```



**TS**

```ts
import * as anchor from "@coral-xyz/anchor";
import { getAssociatedTokenAddressSync } from '@solana/spl-token';
import { Keypair, PublicKey } from '@solana/web3.js';
import { TransferTokens } from "../target/types/transfer_tokens";

describe("transfer-tokens", () => {
  // Configure the client to use the local cluster.
  const provider = anchor.AnchorProvider.env();
  anchor.setProvider(provider);
  const payer = provider.wallet as anchor.Wallet;
  const program = anchor.workspace.TransferTokens as anchor.Program<TransferTokens>;
  const metadata = {
    name: 'CAT',
    symbol: 'EASON',
    uri: 'https://images.pexels.com/photos/16254138/pexels-photo-16254138/free-photo-of-a-sketch-of-a-cat.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=2',
  };
  

  const METADATA_PROGRAM_ID = new PublicKey('metaqbxxUerdq28cj1RbAWkYQm3ybzjb6a8bt518x1s');

    // Generate new keypair to use as address for mint account.
    const mintKeypair = new Keypair();

    // Generate new keypair to use as address for recipient wallet.
    const recipient = new Keypair();

    const metadataSeeds = [
      Buffer.from('metadata'),
      METADATA_PROGRAM_ID.toBuffer(), // Token Metadata Program的地址
      mintKeypair.publicKey.toBuffer(), // Mint 公钥
    ];
  
    // 获取 PDA 地址和 bump 值
    const [metadataPDA, metadataBump] = PublicKey.findProgramAddressSync(metadataSeeds, METADATA_PROGRAM_ID);

    // Derive the associated token address account for the mint and payer.
    const senderTokenAddress = getAssociatedTokenAddressSync(mintKeypair.publicKey, payer.publicKey);

    // Derive the associated token address account for the mint and recipient.
    const recepientTokenAddress = getAssociatedTokenAddressSync(mintKeypair.publicKey, recipient.publicKey);

  it('Create an SPL Token!', async () => {
    const transactionSignature = await program.methods
      .createToken(metadata.name, metadata.symbol, metadata.uri)
      .accounts({
        payer: payer.publicKey,
        mintAccount: mintKeypair.publicKey,
        metadataAccount: metadataPDA,
        tokenMetadataProgram: METADATA_PROGRAM_ID
      })
      .signers([mintKeypair])
      .rpc();

    // console.log('Success!');
    // console.log(`   Mint Address: ${mintKeypair.publicKey}`);
    // console.log(`   Transaction Signature: ${transactionSignature}`);
    // const mintAccountInfo = await program.provider.connection.getAccountInfo(mintKeypair.publicKey);
    // console.log('mintAccountInfo :>> ', mintAccountInfo);
  });
});
```


#### 4、Test

> 项目目录新开一个终端

```sh
anchor test --provider.cluster http://localhost:8899 --skip-local-validator
```



## 坑
1、合约里面不要使用f32，会有精度问题。


验证：

大家好，我目前刚刚开始solana合约开发，看solanazh文档学习到很多东西。有两个观点想向各位印证一下：
  1. rust合约并没有类似于solidity合约中的receive方法，所以不能通过单纯的转账sol来触发合约方法。
  2. 由于有网络传输字节的限制和最大单元计算的限制，无法用合约无法完成空投的功能，一次性最多给80个地址左右转账spl的token
> 1.不能 只能写指令解决 2.是的 可以写批量脚本 不知道捆绑交易能行不