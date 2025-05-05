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
### 8. **Router**
**Router（路由器合约）** 是用来真正执行交易（swap）的合约。它会自动寻找最佳路径来兑换，比如：
- 单跳：WETH → USDC
- 多跳：WETH → DAI → USDC

不同版本的 Router：

|版本|Router 地址|作用|
|---|---|---|
|V2|`0x7a250d...`|简单 token ↔ token 交换|
|V3|`0xE59242...`|更复杂，支持 tick、单跳、多跳路径交换|

👉 在 **模拟报价** 时我们调用的是 **Quoter** 合约  
👉 在 **真实下单交易** 时会调用 **Router** 合约

## 二、实战学习路线

### ✅ Step 1：基础了解与官方资源

|内容|推荐资源|
|---|---|
|Uniswap V2 & V3 白皮书|https://uniswap.org/whitepaper|
|Uniswap V3 Core Docs|https://docs.uniswap.org/|
|ABI 接口文档|https://docs.uniswap.org/reference/contracts|

---

### ✅ Step 2：合约交互与报价（模拟交易）

1. 了解 V2/V3 合约地址：Router 和 Pool
    - V2 Router：`0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D`
    - V3 Router：`0xE592427A0AEce92De3Edee1F18E0157C05861564`
2. 使用脚本或 Ethers.js/Web3.js：
    - `getAmountsOut(amountIn, path)`（V2）
    - `quoteExactInputSingle`（V3）
3. 工具推荐：
    - Ethers.js（推荐）
    - Viem（轻量新库）
    - Hardhat + Alchemy/Infura/Ankr 节点
---

### ✅ Step 3：监听事件（解析 swap）

1. 使用 Ethers.js 或 Web3.js `contract.on("Swap", callback)`
2. 或者解析历史 logs：
   ```js
		provider.getLogs({
		  address: poolAddress,
		  topics: [swapTopicHash],
		  fromBlock,
		  toBlock
		})
     ```
3. 分析字段：
    - sender, recipient, amount0/1In/Out
    - 可以计算 token 价格变化、swap 成本等

---

### ✅ Step 4：理解深度、LP、Tick（Uniswap V3）

1. 使用 GraphQL/Uniswap Subgraph：
    - https://thegraph.com/explorer/subgraph/uniswap/uniswap-v3
2. 关键字段：
    - `liquidity`：当前池子深度
    - `tick`：当前 tick index
    - `sqrtPriceX96`：价格根号，计算真实价格用
3. Tick 价格换算公式：
    `price = 1.0001 ^ tick`

---

### ✅ Step 5：构建你自己的脚本/应用

目标示例：
- 获取 ETH → USDC swap 报价（V2 和 V3 对比）
- 监听某交易对的实时交易事件
- 分析某池当前 tick、深度、活跃 LP 区间
- 模拟一笔交易，记录其滑点与真实价格

---

## ⚒️ 工具推荐

|工具|用途|
|---|---|
|Alchemy / Infura|RPC 节点|
|Ethers.js|JS 合约交互|
|Foundry / Hardhat|测试合约调用|
|The Graph|获取 LP、tick 数据|
|Tenderly|事务调试、模拟|
|Uniswap V3 SDK|交易路径规划、tick 模拟|
