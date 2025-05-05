## 🧠 一、关键词解释

### 1. **调 ABI 询价**
- ABI（Application Binary Interface）是合约调用的接口定义，用于和 Uniswap 合约交互。
- 你可以调用 `getAmountsOut`（V2）或 `quoteExactInputSingle`（V3）来模拟报价，即：给一个 tokenIn 数量，查询能换多少 tokenOut。

### 2. **解析事件（Event）**
- Swap 发生时合约会 emit 事件，例如 `Swap(address sender, ...)`。
- 你监听事件日志（logs），可以获知每笔交易的详情，如谁换了多少 token、换成了什么等。

### 3. **单跳路径（Single Hop Swap）**
- 只经过一个交易池（tokenA ↔ tokenB），而非多个池（tokenA → tokenB → tokenC）。
- 使用 V2 `swapExactTokensForTokens` 或 V3 的 `exactInputSingle` 方法。

### 4. **LP（Liquidity Provider）**
- 提供流动性的用户，将 tokenA 和 tokenB 存入池子，换得 LP token。
- 你的 swap 会用到他们提供的流动性，手续费会分给 LP。
### 5. **深度（Liquidity Depth）**

- 表示一个交易池中 token 的数量和分布。
- 深度越大，滑点越小，影响报价的稳定性。

### 6. **Tick**

- V3 引入的价格刻度单位。
- 池子的价格范围被划分为 ticks，例如一个 tick 表示 1.0001 的倍率。
- 你可以通过 tick 来判断当前价格、活跃流动性等。

### 7. **价格和数量对比**

- 比如你用 1 ETH 能换多少 USDC，对比链上的报价与实际成交价是否合理。
- 可用于套利监测、滑点分析。

