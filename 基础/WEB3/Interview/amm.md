

**向流动性池（Liquidity Pool）存入代币（deposit liquidity）** 的逻辑，核心功能是：

- **检查存款者的余额**，防止存入超出余额的代币。
- **确保存款的代币对（Token Pair）比例匹配现有流动性池的比例**，避免破坏池子的流动性。
- **计算流动性代币（LP Token）的数量**，用来衡量存入资金的贡献。
- **执行代币转账**，将存款者的代币存入池子。
- **铸造 LP 代币（Liquidity Token）**，作为流动性提供者的凭证。

---

## **代码详细解析**

### **1. 防止存款者存入超出自己账户的代币**

```rust
let mut amount_a = if amount_a > ctx.accounts.depositor_account_a.amount {
    ctx.accounts.depositor_account_a.amount
} else {
    amount_a
};
let mut amount_b = if amount_b > ctx.accounts.depositor_account_b.amount {
    ctx.accounts.depositor_account_b.amount
} else {
    amount_b
};
```

- 这里的 `amount_a` 和 `amount_b` 是用户尝试存入的代币数量（A/B）。
- `ctx.accounts.depositor_account_a.amount` 和 `ctx.accounts.depositor_account_b.amount` 是用户在钱包中的代币余额。
- **如果用户输入的存款金额大于钱包余额，则限制存款金额不超过余额**，防止提交无效交易。

---

### **2. 确保存款的比例与池子匹配**

```rust
let pool_a = &ctx.accounts.pool_account_a;
let pool_b = &ctx.accounts.pool_account_b;

// 判断是否是该池子的第一次流动性存入（即池子为空）
let pool_creation = pool_a.amount == 0 && pool_b.amount == 0;
(amount_a, amount_b) = if pool_creation {
    // 如果池子是新创建的，则不需要比例匹配，直接存入
    (amount_a, amount_b)
} else {
    // 计算池子当前的 A/B 代币比例
    let ratio = I64F64::from_num(pool_a.amount)
        .checked_mul(I64F64::from_num(pool_b.amount))
        .unwrap();
    
    // 确保存入的代币按照当前池子的比例存入
    if pool_a.amount > pool_b.amount {
        (
            I64F64::from_num(amount_b)
                .checked_mul(ratio)
                .unwrap()
                .to_num::<u64>(),
            amount_b,
        )
    } else {
        (
            amount_a,
            I64F64::from_num(amount_a)
                .checked_div(ratio)
                .unwrap()
                .to_num::<u64>(),
        )
    }
};
```

- **如果流动性池是新建的**（即 `pool_a.amount == 0 && pool_b.amount == 0`），那么**存款者可以自由决定代币 A/B 的比例**。
- **如果池子已经存在**，则需要按照池子当前的 A/B 比例存入：
    - `ratio = pool_a.amount * pool_b.amount`，用来计算比例。
    - 依据 **哪个代币池子数量更多** 来调整存款比例，确保不会破坏流动性池的价格稳定性。

---

### **3. 计算 LP 代币的数量**

```rust
let mut liquidity = I64F64::from_num(amount_a)
    .checked_mul(I64F64::from_num(amount_b))
    .unwrap()
    .sqrt()
    .to_num::<u64>();
```

- **LP 代币的数量采用** `sqrt(amount_a * amount_b)` **计算**，这个公式来源于 Uniswap v2 的流动性计算方法，目的是确保两种资产的数量成比例增加时，LP 代币的增长是合理的。

---

### **4. 处理最小流动性锁定**

```rust
if pool_creation {
    if liquidity < MINIMUM_LIQUIDITY {
        return err!(TutorialError::DepositTooSmall);
    }

    liquidity -= MINIMUM_LIQUIDITY;
}
```

- **Solana 上的流动性池需要最小 LP 代币锁定**（`MINIMUM_LIQUIDITY`），防止流动性池因存款极小而造成计算误差。
- **如果存入的流动性低于最小值，直接返回错误** `DepositTooSmall`。
- **如果满足最小流动性要求**，则需要 **扣除最小流动性**，通常这部分 LP 代币会被销毁，以避免套利问题。

---

### **5. 转移代币到池子**

```rust
token::transfer(
    CpiContext::new(
        ctx.accounts.token_program.to_account_info(),
        Transfer {
            from: ctx.accounts.depositor_account_a.to_account_info(),
            to: ctx.accounts.pool_account_a.to_account_info(),
            authority: ctx.accounts.depositor.to_account_info(),
        },
    ),
    amount_a,
)?;
token::transfer(
    CpiContext::new(
        ctx.accounts.token_program.to_account_info(),
        Transfer {
            from: ctx.accounts.depositor_account_b.to_account_info(),
            to: ctx.accounts.pool_account_b.to_account_info(),
            authority: ctx.accounts.depositor.to_account_info(),
        },
    ),
    amount_b,
)?;
```

- 这里使用 Solana 的 **CPI（Cross-Program Invocation）调用 `token::transfer`**，完成存款者代币的转移。
- **将 `amount_a` 和 `amount_b` 从用户钱包转移到流动性池中**。

---

### **6. 计算签名种子，铸造 LP 代币**

```rust
let authority_bump = ctx.bumps.pool_authority;
let authority_seeds = &[
    &ctx.accounts.pool.amm.to_bytes(),
    &ctx.accounts.mint_a.key().to_bytes(),
    &ctx.accounts.mint_b.key().to_bytes(),
    AUTHORITY_SEED,
    &[authority_bump],
];
let signer_seeds = &[&authority_seeds[..]];
```

- 这里构造了 `pool_authority` 账户的 **PDA（程序派生地址）**。
- `bump` 值用于确保 PDA 地址正确。
- `signer_seeds` 用于后续的 LP 代币铸造。

---

### **7. 铸造 LP 代币**

```rust
token::mint_to(
    CpiContext::new_with_signer(
        ctx.accounts.token_program.to_account_info(),
        MintTo {
            mint: ctx.accounts.mint_liquidity.to_account_info(),
            to: ctx.accounts.depositor_account_liquidity.to_account_info(),
            authority: ctx.accounts.pool_authority.to_account_info(),
        },
        signer_seeds,
    ),
    liquidity,
)?;
```

- `mint_to` **铸造 LP 代币**（`liquidity`）。
- 目标地址是 `depositor_account_liquidity`（存款者的 LP 代币账户）。
- 使用 `signer_seeds` **确保 `pool_authority` 账户能够正确签署铸造交易**。

---

## **总结**

- **防止存款者超额存款**（限制金额不超过钱包余额）。
- **确保存款的比例符合流动性池当前的比例**，防止破坏池子平衡。
- **计算 LP 代币的数量**，用于衡量存款者提供的流动性份额。
- **执行代币转移**，把用户的 `Token A` 和 `Token B` 存入流动性池。
- **铸造 LP 代币**，给存款者发放 LP 代币作为流动性提供者的权益证明。

这样，存款者就完成了一次 **向流动性池提供流动性** 的操作！ 🚀
