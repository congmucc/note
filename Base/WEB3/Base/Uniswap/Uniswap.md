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
- 矿池当前价格的报价。
- V3 引入的价格刻度单位。
- 池子的价格范围被划分为 ticks，例如一个 tick 表示 1.0001 的倍率。
- 你可以通过 tick 来判断当前价格、活跃流动性等。
### 7. **sqrtPriceX96**
**Uniswap V3 的核心价格表示方式**。矿池的当前价格，以 token0 和 token1 之间的比率计算（即 tokenIn/tokenOut）并放大了 2962^{96}296 倍来提高精度
### 8. **Router**
> 封装了常见的交易路径与流动性添加/移除的操作逻辑。单跳、多跳 token 兑换,添加或移除 LP 流动性, 支持 WETH ↔ ETH 的包装/解包

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

[获取报价 |统一交换 --- Getting a Quote | Uniswap](https://docs.uniswap.org/sdk/v3/guides/swaps/quoting)
## ⚒️ 工具推荐

|工具|用途|
|---|---|
|Alchemy / Infura|RPC 节点|
|Ethers.js|JS 合约交互|
|Foundry / Hardhat|测试合约调用|
|The Graph|获取 LP、tick 数据|
|Tenderly|事务调试、模拟|
|Uniswap V3 SDK|交易路径规划、tick 模拟|


```ts
import { ethers } from 'ethers';
import IUniswapV3PoolABI from '@uniswap/v3-core/artifacts/contracts/interfaces/IUniswapV3Pool.sol/IUniswapV3Pool.json'
import { abi as QuoterABI } from '@uniswap/v3-periphery/artifacts/contracts/lens/Quoter.sol/Quoter.json';


const provider = new ethers.providers.JsonRpcProvider('https://eth-mainnet.g.alchemy.com/v2/KEY');

const WETH_ADDRESS = ethers.utils.getAddress("0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2");
const USDC_ADDRESS = ethers.utils.getAddress("0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48");
const UNISWAP_V3_POOL_ADDRESS = '0x88e6A0c2dDD26FEEb64F039a2c41296FcB3f5640'; // USDC/WETH 0.05%
const QUOTER_ADDRESS = '0xb27308f9F90D607463bb33eA1BeBb41C27CE5AB6';


const main = async () => {
	
	const poolContract = new ethers.Contract(UNISWAP_V3_POOL_ADDRESS, IUniswapV3PoolABI.abi, provider);
	const quoterContract = new ethers.Contract(QUOTER_ADDRESS, QuoterABI, provider);
	
	// 1. 获取 slot0（包括 sqrtPriceX96 和当前 tick）
	const [sqrtPriceX96, tick, , , , ,] = await poolContract.slot0();
	console.log('✅ 当前 sqrtPriceX96:', sqrtPriceX96.toString());
	console.log('✅ 当前 Tick:', tick);
	
	// 2. 获取当前流动性（Liquidity）
	const liquidity = await poolContract.liquidity();
	console.log('✅ 当前 LP 流动性:', liquidity.toString());
	
	// 3. Swap 报价（输入 1 WETH 换 USDC）
	const amountIn = ethers.utils.parseEther('1');
	const quotedAmountOut = await quoterContract.callStatic.quoteExactInputSingle(
		WETH_ADDRESS,
		USDC_ADDRESS,
		3000, // fee: 0.05%
		amountIn,
		0
	);
	console.log(`✅ 估算报价: 1 WETH 可得约 ${ethers.utils.formatUnits(quotedAmountOut, 6)} USDC`);
	
	// 4. 监听 Swap 事件
	poolContract.on('Swap', (
		sender,
		recipient,
		amount0,
		amount1,
		sqrtPriceX96,
		liquidity,
		tick
	) => {
		console.log(`📢 Swap Event:`);
		console.log(`From ${sender} → ${recipient}`);
		console.log(`Token0 Change: ${ethers.utils.formatUnits(amount0)}`);
		console.log(`Token1 Change: ${ethers.utils.formatUnits(amount1)}`);
		console.log(`New Tick: ${tick}`);
		console.log(`New sqrtPriceX96: ${sqrtPriceX96}`);
		console.log(`New liquidity: ${liquidity}`);
		console.log('---------------------------');
	});
};

main();



✅ 当前 sqrtPriceX96: 1864234913218244291503370571162558
✅ 当前 Tick: 201330
✅ 当前 LP 流动性: 6084222501667747663
✅ 估算报价: 1 WETH 可得约 1797.841328 USDC
📢 Swap Event:
From 0x5050e08626c499411B5D0E0b5AF0E83d3fD82EDF → 0x5050e08626c499411B5D0E0b5AF0E83d3fD82EDF
Token0 Change: 0.000000016264101854
Token1 Change: -8.999692493060258515
New Tick: 201329
New sqrtPriceX96: 1864117560196013696886026851855178
New liquidity: 6066937332023339941
---------------------------
```
- 查询某个池子（如 `WETH/USDC`）的当前价格和 tick
- 估算从输入 token（如 WETH）换到输出 token（如 USDC）得到的数量
- 获取当前 LP 池储备（间接通过合约）
- 使用 ABI 调用 `slot0()` 获取当前 tick 和 sqrtPriceX96
- 解析 `Swap` 事件

|需求项|实现方式|
|---|---|
|✅ 查询当前价格|使用 `slot0()` 获取 `sqrtPriceX96` 计算|
|✅ 获取 LP 深度|查询 `liquidity` 变量|
|✅ 获取 Tick|使用 `slot0()` 获取当前 tick|
|✅ ABI 调用合约|用 ABI 读取合约数据|
|✅ Swap 报价|使用 V3 SDK 构建 `Trade`|
|✅ 事件解析|监听并解析 `Swap` 事件|




```ts
import { ethers } from "ethers";
import {
	Pool, TickMath, nearestUsableTick, Trade, Route, SwapQuoter, SwapRouter,
	TickListDataProvider
} from "@uniswap/v3-sdk";
import {
	Token, CurrencyAmount, TradeType, Percent
} from "@uniswap/sdk-core";
import IUniswapV3PoolABI from "@uniswap/v3-core/artifacts/contracts/UniswapV3Pool.sol/UniswapV3Pool.json";
import { JSBI } from "@uniswap/sdk";
  
// ✅ 参数
const provider = new ethers.providers.JsonRpcProvider("https://eth-mainnet.g.alchemy.com/v2/KEY");
const poolAddress = "0x8ad599c3a0ff1de082011efddc58f1908eb6e6d8"; // USDC/WETH 0.3%

// ✅ Token 定义（注意 checksum）
const USDC = new Token(1, ethers.utils.getAddress("0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48"), 6, "USDC", "USD Coin");
const WETH = new Token(1, ethers.utils.getAddress("0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2"), 18, "WETH", "Wrapped Ether");

async function main() {
	const poolContract = new ethers.Contract(poolAddress, IUniswapV3PoolABI.abi, provider);
	
	const [slot0, liquidity] = await Promise.all([
		poolContract.slot0(),
		poolContract.liquidity()
	]);
	
	const sqrtPriceX96 = slot0.sqrtPriceX96;
	const tick = slot0.tick;
	const tickSpacing = 60; // USDC/WETH 的 feeTier = 3000 -> spacing = 60
	
	console.log("✅ 当前 sqrtPriceX96:", sqrtPriceX96.toString());
	console.log("✅ 当前 Tick:", tick);
	console.log("✅ 当前 LP 流动性:", liquidity.toString());
	
	const ticks = [
		{
			index: 201300,
			liquidityNet: JSBI.BigInt("123456789"),
			liquidityGross: JSBI.BigInt("123456789")
		},
		{
			index: 201360,
			liquidityNet: JSBI.BigInt("-123456789"),
			liquidityGross: JSBI.BigInt("123456789")
		}
	];
	
	const tickProvider = new TickListDataProvider(ticks, tickSpacing);
	const pool = new Pool(
		WETH,
		USDC,
		3000,
		sqrtPriceX96.toString(),
		liquidity.toString(),
		tick,
		tickProvider
	);
	
	// 构建 Route 和 Trade
	const amountIn = CurrencyAmount.fromRawAmount(WETH, ethers.utils.parseEther("1").toString());
	const route = new Route([pool], WETH, USDC);
	const trade = await Trade.fromRoute(route, amountIn, TradeType.EXACT_INPUT);
	
	console.log("Mid Price:", route.midPrice.toSignificant(6)); //池中当前的中间价格（理论价格）
	console.log("Execution Price:", trade.executionPrice.toSignificant(6)); //实际成交价格
	console.log("Expected Output:", trade.outputAmount.toSignificant(6)); //你预计能拿到多少 USDC
	
	// 解析事件（模拟 swap 日志）
	poolContract.on("Swap", (sender, recipient, amount0, amount1, sqrtPriceX96, liquidity, tick, event) => {
		console.log("🔥 Swap Event Detected:");
		console.log({ sender, recipient, amount0: amount0.toString(), amount1: amount1.toString() });
		event.removeListener(); // 只监听一次
	});
}

main().catch(console.error);

✅ 当前 sqrtPriceX96: 1865736113605630736735858196288758
✅ 当前 Tick: 201346
✅ 当前 LP 流动性: 7266794844411932770
Mid Price: 1803.26
Execution Price: 1797.84
Expected Output: 1797.84
```

