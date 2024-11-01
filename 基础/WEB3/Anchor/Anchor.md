address：```
```
// windows


// mac

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




```
> [program-examples | Look at the test file](https://github.com/solana-developers/program-examples/blob/main/basics/rent/anchor/programs/rent-example/src/lib.rs)



pda不能做签名
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

## Rust用法：
```rust
/// 用于安全地对整数进行加法运算，
/// 加法结果不溢出，则返回 `Some(result)`，其中 `result` 是加法的结果。
    pub fn increment(&mut self) {
        self.page_visits = self.page_visits.checked_add(1).unwrap();
    }
```






## 交互

**显式指定 `signers`**
1. **使用新生成的密钥对**
    
    - 当你需要使用一个新生成的密钥对（例如 `Keypair.generate()`）来签署交易时，必须显式指定 `signers`，因为这些密钥对不在当前连接的钱包中。
2. **多签名交易**
    
    - 当交易需要多个签名者时，每个签名者都需要显式指定。例如，一个多签名账户的交易可能需要多个私钥来签名。
3. **特定账户需要签名**
    
    - 当某个账户在交易中需要签名，但这个账户不是当前连接的钱包时，需要显式指定 `signers`。
4. **自定义签名逻辑**
    
    - 当你需要实现自定义的签名逻辑，例如使用硬件钱包或其他外部签名服务时，需要显式指定 `signers`。
```js

```
