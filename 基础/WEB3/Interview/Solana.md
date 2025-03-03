## （1） MPL

好的，Solana 的 MPL（Metaplex Protocol Library）主要涉及 NFT 生态，包含了 Candy Machine、Token Metadata、Auction House 等核心组件。我会以面试官的身份提出几个 MPL 相关的问题，并提供一个标准化的回答。

---

### **面试问题 1：什么是 Metaplex？它在 Solana 生态中的作用是什么？**

**回答：**  
Metaplex 是 Solana 生态中的 NFT 基础设施，它提供了一整套工具和智能合约，使开发者能够创建、管理和交易 NFT，而无需从头构建 NFT 相关的智能合约。Metaplex 主要包括以下几个核心组件：

1. **Candy Machine**：一个常用的 **NFT Mint** 机制，支持公平分发 NFT，防止 Bot 滥用铸造。
2. **Token Metadata**：为 NFT 赋予标准化的**元数据**，使其在市场和钱包中可识别。
3. **Auction House**：一个去中心化的 **NFT 交易市场**协议，支持点对点交易，不需要中心化的市场合约托管资产。

Metaplex 使 Solana 上的 NFT 生态更加高效，并提供了一种低成本、高吞吐的解决方案。

---

### **面试问题 2：Candy Machine 是什么？它如何防止机器人滥用 NFT Mint？**

**回答：**  
Candy Machine 是 Metaplex 提供的一个 NFT Minting 机制，主要用于大规模 NFT 系列的**公平分发**。它采用以下方式防止机器人滥用：

1. **Whitelist（白名单）**：只有在预先登记的地址才可以优先 Mint NFT。
2. **Gatekeeper**：集成 Solana Pay 的 CAPTCHA 机制，验证用户是否为真人。
3. **Bot Tax（机器人税）**：如果检测到异常交易行为，Candy Machine 会自动销毁该交易并收取一定的 SOL 作为惩罚。
4. **Minting Start Date（启动时间）**：确保所有用户在相同时间开始 Mint，防止有人提前获取 NFT。

Candy Machine 2.0 进一步优化了抗 Bot 机制，使 NFT 发行更加公平。

---

### **面试问题 3：Token Metadata 在 Metaplex 中的作用是什么？**

**回答：**  
Token Metadata 是 Metaplex 用于 NFT 标准化的核心协议，它提供了一种通用的方式来存储和管理 NFT 的元数据，使 NFT 在不同钱包、市场和 dApp 之间可以互操作。它主要由以下几个部分组成：

- **Mint Address**：NFT 的唯一标识符，对应 Solana Token。
- **Metadata Account**：存储 NFT 相关信息，如名称、描述、图片 URI 等。
- **Master Edition**：用于控制 NFT 是否可复制（如 1-of-1 NFT 或可变供应的 NFT）。

Token Metadata 确保了 Solana NFT 生态中的 NFT 具有统一的标准，便于交易和展示。

---

### **面试问题 4：Auction House 和 OpenSea 这样的中心化市场有何不同？**

**回答：**  
Auction House 是 Metaplex 提供的一个去中心化 NFT 交易协议，与 OpenSea 等中心化 NFT 交易市场的主要区别在于：

1. **无需托管（Escrow-less）**：NFT 不需要存入市场合约，而是保留在用户钱包中，交易直接发生在买卖双方之间。
2. **无需市场抽成**：不像 OpenSea 这样的中心化市场会收取交易费用，Auction House 允许卖家设定自己的交易规则。
3. **更透明**：所有交易在 Solana 链上执行，数据可公开查询，避免中心化市场的潜在操控。

这种去中心化的设计使得 Auction House 更加符合 Web3 精神，提升了 NFT 交易的安全性和透明度。

---


## （2） SPL
**Solana Program Library（SPL）** 是 Solana 生态系统中的代币和 DeFi 相关标准，相当于以太坊的 ERC 标准。SPL 代币是 Solana 上的原生代币格式，支持高吞吐量、低成本的交易。


### **2. SPL Associated Token Account（SPL ATA，代币账户标准）**

**简介**：SPL ATA 规定了 **Solana 账户如何存储代币**，用于**减少复杂性和 Gas 费**。  
**特点**：

- 兼容 **SPL Token**，每个钱包地址只有一个**默认代币账户**。
- 避免手动管理多个代币账户。
- **降低 Gas 费**，更高效地执行代币操作。

---

### **3. SPL Token 2022（改进版 SPL 代币）**

**简介**：SPL Token 2022 是 **SPL Token** 的增强版，类似于以太坊的 **ERC-777**，添加了**扩展功能**。  
**新增功能**：

- **自动化转账（Transfer Hooks）**：支持在转账时执行代码（类似 ERC-777 Hooks）。
- **可配置手续费**：允许项目方设定代币交易的手续费。
- **代币冻结/销毁功能**：提供可选的中央控制能力。
- **多签权限（Multisig）**：增强安全性。

---

### **4. SPL Memo Program（交易备注标准）**

**简介**：SPL Memo 允许在交易中附加 **自定义文本数据**，类似于比特币的 **OP_RETURN** 或以太坊的 **Transaction Input**。  
**应用场景**：

- 记录交易信息（如订单号、签名）。
- 便于链上数据分析（如 Solscan、Phantom）。

---

### **5. SPL Governance（去中心化治理）**

**简介**：SPL Governance 提供了一套**去中心化治理**（DAO）框架，类似于**以太坊的 ERC-20 + Snapshot** 机制。  
**功能**：

- **创建和管理 DAO**（Decentralized Autonomous Organization）。
- **治理投票**（持有治理代币的用户可参与）。
- **资金管理**（Treasury 管理）。

**示例：使用 Solana DAO 进行投票**

- **成员创建提案（Proposal）**
- **治理代币持有者投票**
- **执行治理决策**（如更新智能合约）

---




## （3） NFT协议

好的，关于 **NFT 协议**，我将以面试官的身份提出一些关键问题，并提供标准化的答案。问题将涵盖 **NFT 的基本概念、技术标准、协议、智能合约实现以及 Solana 生态中的 NFT 发展**。

---

### **面试问题 1：什么是 NFT？它与 FT（Fungible Token，可替代代币）有什么区别？**



NFT（Non-Fungible Token，非同质化代币）是一种 **独一无二的数字资产**，它存储在区块链上，常用于艺术品、游戏道具、虚拟土地、会员权益等领域。

| **特性**   | **NFT（非同质化代币）**                     | **FT（可替代代币）**    |
| -------- | ----------------------------------- | ---------------- |
| **唯一性**  | 每个 NFT 具有独特的 Token ID               | 所有代币是相同的         |
| **可分割性** | 不可分割（通常为 1 个单位）                     | 可分割（如 0.001 ETH） |
| **标准**   | ERC-721、ERC-1155、SPL Token Metadata | ERC-20、SPL Token |
| **应用场景** | 数字艺术、游戏、音乐、身份凭证                     | 货币、治理代币、稳定币      |

NFT 之所以独特，是因为它在区块链上存储了元数据（Metadata），如名称、描述、图片链接等，使其可验证且不可篡改。

---

### **面试问题 2：NFT 常见的区块链标准有哪些？它们有何不同？**

^91929f

| **协议名称**     | **功能特点**                                                            | **适用场景**                  |
| ------------ | ------------------------------------------------------------------- | ------------------------- |
| **ERC-721**  | - 最基础的 NFT 标准，每个代币都是唯一的，不可分割。  <br>- 通过 `tokenURI` 关联元数据，链上仅存储指针。   | 艺术品、收藏品                   |
| **ERC-1155** | - 可同时支持 **FT（同质化代币）和 NFT（非同质化代币）**。  <br>- 批量铸造、转移，降低 Gas 费。        | 游戏资产、NFT 交易市场             |
| **ERC-2612** | - ERC20Permit，支持使用签名进行授权                                            | EIP-2612标准 提出             |
| **ERC4626**  | - 收益金库（Yield Vaults）的标准，提供一种**统一的存款、提取、收益计算和代币化收益**的方法              | DeFi 收益金库，借贷协议，流动性质押，指数基金 |
| **ERC-998**  | - 允许 NFT 组合（NFT 持有 NFT 或 FT）。  <br>- 适用于资产层级结构，例如装备和角色绑定。           | 游戏、虚拟世界                   |
| **ERC-1201** | - 解决 NFT 资产的部分所有权问题，使多个用户共同持有一个 NFT。                                | 共享资产、合资购买                 |





| **对比维度**                | **Ethereum 协议**                    | **Solana 对应协议**              | **对比分析**                                                                                                                                            |
| ----------------------- | ---------------------------------- | ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| **单一 NFT 标准**           | **ERC-721**                        | **Metaplex Token Metadata**  | - **ERC-721** 是最早的 NFT 标准，定义了不可替代代币的基础规范。- **Metaplex（Token Metadata）** 是 Solana 的主流 NFT 规范，提供类似的功能，并增加了**链上存储**和**更低的 Gas 费用**。                    |
| **多 NFT 标准**            | **ERC-1155**                       | **Metaplex Edition（可选）**     | - **ERC-1155** 允许同一合约管理多种 NFT 和 FT（类似游戏道具）。- Solana **Metaplex** 也支持 Edition（多个副本），但不如 ERC-1155 复杂。                                                 |
| **批量铸造（Batch Minting）** | **ERC-721A、ERC-2309**              | **Candy Machine、Bubblegum**  | - **ERC-721A（Azuki 方案）** 允许以低 Gas 费批量铸造 NFT。- **Candy Machine** 是 Solana 的核心 NFT 批量发行方案，具备更高效率和更低的成本。- **Bubblegum** 允许压缩 NFT，大幅降低 Mint 成本，适合大规模项目。 |
| **动态 NFT（升级/组合）**       | **ERC-3664、ERC-998**               | **Fusion**                   | - **ERC-3664** 允许 NFT 具有可变属性，如升级、变化。- **ERC-998** 允许 NFT 作为“容器”，装入其他 NFT。- **Fusion** 在 Solana 上实现了类似功能，支持 **NFT 组合、升级**，可用于游戏角色装备。                 |
| **存储方式**                | **IPFS、Arweave、链下存储**              | **Metaplex、Bubblegum（压缩存储）** | - 以太坊 NFT **通常存储在 IPFS、Arweave**，数据仍然可能链下存储。- Solana **支持链上存储**，Bubblegum 通过**Merkle 树结构**优化存储，使 NFT 存储成本更低。                                        |
| **铭刻 NFT（Inscription）** | **Ethereum L2（如 Ordinals on ETH）** | **Inscription**              | - **ETH 生态中没有原生铭刻 NFT 方案**，但 Layer2（如 Ordinals on ETH）开始尝试。- **Solana 的 Inscription** 允许 NFT **直接铭刻到区块链上**，数据 100% 链上。                              |
| **访问权限控制**              | **ERC-4907（租赁）、NFT gating**        | **Token Auth Rules**         | - **ERC-4907** 支持 NFT **租赁功能**。- **Token Auth Rules** 允许 Solana NFT 设置**访问权限、租赁规则、交易限制**等，可更精细化控制 NFT 交互。                                           |
| **NFT 账户模型**            | **需 ERC-6551 扩展**                  | **原生支持**                     | - **ERC-6551** 允许 NFT 作为钱包，存储代币、NFT 等。- **Solana NFT 本身就是一个账户（Account Model）**，无需额外扩展。                                                              |
| **版税机制**                | **ERC-2981（但可被绕过）**                | **Metaplex 版税（强制执行）**        | - **ERC-2981** 允许 NFT 设定版税，但**市场可以绕过**。- **Solana Metaplex 版税机制** **强制执行**，保护创作者利益。                                                                 |
| **收益共享（NFT 分成）**        | **未原生支持，需额外合约**                    | **Hydra（Fanout Wallets）**    | - 以太坊上 NFT 需要额外智能合约来实现收益共享。- **Hydra** 允许 NFT **收益自动分配**，适合 DAO、游戏项目。                                                                               |

---


NFT 在不同区块链上的实现方式有所不同，以下是最常见的 NFT 标准：

| **区块链**      | **标准**                        | **特点**                       |
| ------------ | ----------------------------- | ---------------------------- |
| **Ethereum** | ERC-721                       | 早期 NFT 标准，每个 NFT 独立存在        |
|              | ERC-1155                      | 支持 NFT + FT，多种资产可共享合约        |
| **Solana**   | Metaplex Token Metadata       | 类似 ERC-721，提供 NFT 元数据        |
|              | Metaplex Compressed NFT（cNFT） | 低成本 NFT，适用于大规模发行             |
| **Flow**     | Flow NFT                      | 可升级 NFT，适用于 NBA Top Shot 等应用 |
| **Polygon**  | ERC-721 / ERC-1155            | 以太坊兼容，交易费用更低                 |


对比 SPL 与 ERC

| **功能** | **SPL（Solana）**                  | **ERC（Ethereum）**                 |
| ------ | -------------------------------- | --------------------------------- |
| 可替代代币  | **SPL Token**                    | **ERC-20**                        |
| 非同质化代币 | **SPL Token + Metadata**         | **ERC-721 / ERC-1155**            |
| 代币账户管理 | **SPL Associated Token Account** | **ERC-20 余额存储在合约内**               |
| 交易备注   | **SPL Memo**                     | **交易 Input Data**                 |
| 代币扩展   | **SPL Token 2022**               | **ERC-777（Hooks）、ERC-4626（收益代币）** |
| 去中心化治理 | **SPL Governance**               | **ERC-20 + Snapshot**             |
| 域名服务   | **SPL Name Service**             | **ENS（Ethereum Name Service）**    |




Solana 的 **Metaplex Token Metadata** 是 Solana 生态中的主要 NFT 标准，它允许 NFT 具有唯一的 **Mint Address** 和存储在链上的元数据。

---

### **面试问题 3：在 Solana 上创建 NFT 需要哪些步骤？**



在 Solana 上创建 NFT 主要涉及 **SPL Token + Metaplex Token Metadata**，具体步骤如下：

1. **创建 NFT 代币（SPL Token）**：使用 Solana Token Program 生成唯一的 **Mint Address**，并确保总供应量为 1。
2. **添加元数据（Metadata）**：使用 Metaplex Token Metadata 协议，将 **名称、描述、图像 URI 等信息** 绑定到 NFT。
3. **设置 Master Edition（可选）**：如果 NFT 需要 **版次控制**（如限量 1000 份），可以使用 Master Edition 限制数量。
4. **部署 NFT**：将 NFT 上传到链上，并让用户在钱包或市场上可见。

开发者可以使用 **Metaplex CLI 或 Rust SDK** 来完成这些操作，例如：

```bash
metaplex upload ./nft-assets --env mainnet-beta
metaplex create_candy_machine --env mainnet-beta
```

或者使用 Solana Web3.js 进行 Mint 操作。

---


### **面试问题 4：如何在 Solana 上实现 NFT 交易市场？**



Solana 上的 NFT 交易市场通常基于 **Auction House Protocol**，它与 OpenSea 等中心化市场的最大区别是 **去中心化托管（Escrow-less）**。

**实现 NFT 交易市场的关键步骤：**

1. **列出 NFT（Listing）**：卖家将 NFT 列出，价格信息存储在合约中。
2. **买家出价（Bidding）**：买家提交 SOL 出价，合约会记录出价情况。
3. **交易执行（Execute Sale）**：当买家和卖家达成一致后，智能合约会自动完成交易，并转移 NFT 和资金。

Solana 生态中的 NFT 交易市场主要有：

- **Magic Eden**（主流去中心化 NFT 交易市场）
- **Tensor**（基于 AMM 机制的 NFT 市场）
- **Yawww**（点对点 NFT 交易市场）

开发者可以使用 **Metaplex Auction House SDK** 直接创建自己的 NFT 交易平台。

---

### **面试问题 6：什么是 Compressed NFT（cNFT）？它与传统 NFT 有何不同？**

**Compressed NFT（cNFT）** 是 Solana 生态中的一种 **低成本 NFT 解决方案**，主要用于大规模 NFT 发行（如游戏资产、身份凭证）。

### **与普通 NFT 的区别：**

| **特性**     | **普通 NFT（Metaplex Token Metadata）** | **cNFT（Compressed NFT）**    |
| ---------- | ----------------------------------- | --------------------------- |
| **存储方式**   | 元数据存储在链上                            | 采用 Merkle Tree 进行链下存储       |
| **Gas 费用** | 需要支付较高的 Solana 交易费                  | 交易费用极低（适用于大规模应用）            |
| **适用场景**   | 1-of-1 艺术品、限量 NFT                   | 游戏资产、身份凭证、门票等               |
| **交易方式**   | 兼容所有 Solana NFT 市场                  | 需要支持 cNFT 的市场（如 Magic Eden） |

cNFT 主要由 **Metaplex Bubblegum 程序** 处理，适用于 **Web3 游戏、企业级 NFT 发行、社交 NFT** 等。

---

### **面试问题 7：NFT 生态面临哪些安全挑战？如何防范？**



NFT 生态面临的主要安全挑战包括：

1. **智能合约漏洞**：如 OpenSea 上的 "Listing Bug"，导致用户低价卖出 NFT。
    - **防范措施**：使用开源、经过审计的智能合约，如 Metaplex Token Metadata。
2. **钓鱼攻击（Phishing）**：黑客伪造 NFT 市场，骗取用户授权转移资产。
    - **防范措施**：用户需检查合约地址，避免随意点击未知链接。
3. **版税绕过（Royalty Bypass）**：部分市场允许用户绕过 NFT 版税，使创作者无法获得收益。
    - **防范措施**：采用 **Royalty Enforcement（版税强制）** 机制，如 Metaplex 的 **Programmable NFTs（pNFTs）**。

---










## （4）Defi协议

好的，我会以面试官的身份针对 **DeFi（去中心化金融）协议** 提出一些面试问题，并提供标准化的答案。问题涵盖 DeFi 的基本概念、核心协议（如 AMM、借贷、衍生品）、安全性和 Solana 生态中的 DeFi 发展。

---

### **面试问题 1：什么是 DeFi？它相较于传统金融（TradFi）的核心优势是什么？**

**回答：**  
DeFi（Decentralized Finance，去中心化金融）是一种基于区块链的金融体系，它不依赖于传统银行等中心化机构，而是使用智能合约来提供金融服务，如借贷、交易、衍生品等。

DeFi 相较于传统金融（TradFi）的核心优势包括：

1. **无需许可（Permissionless）**：任何人都可以访问 DeFi，无需银行账户或身份验证。
2. **透明性（Transparency）**：所有交易数据和合约逻辑都公开可查，减少黑箱操作和金融腐败的可能性。
3. **可组合性（Composability）**：不同 DeFi 协议可以像“乐高积木”一样组合在一起，形成复杂的金融产品。
4. **全球可用性（Borderless）**：DeFi 允许全球用户 24/7 访问，不受地域和银行系统限制。
5. **自托管（Self-Custody）**：用户可以完全控制自己的资产，无需依赖银行或第三方机构。

---

### **面试问题 2：自动做市商（AMM）是如何工作的？与传统订单簿交易有什么不同？**

**回答：**  
AMM（Automated Market Maker，自动做市商）是去中心化交易所（DEX）中最常见的流动性提供机制，它使用流动性池（Liquidity Pool）来撮合交易，而不是依赖买卖双方的订单匹配。

AMM 的核心机制包括：

- **流动性池（Liquidity Pool）**：用户存入两种代币作为流动性，例如 SOL/USDC，供交易者兑换。
- **定价机制**：常见的 AMM 使用 **恒定乘积公式** x×y=kx \times y = k（如 Uniswap 和 Raydium）来决定交易价格。
- **流动性提供者（LP）**：向池子存入资产的用户会获得交易手续费作为奖励。

**与传统订单簿的主要区别**：

|特性|AMM|传统订单簿|
|---|---|---|
|**撮合方式**|依靠流动性池|依靠买卖挂单匹配|
|**流动性来源**|LP 存入资产|交易者主动下单|
|**滑点（Slippage）**|价格基于公式计算，可能有滑点|深度足够时滑点较低|
|**适用场景**|适用于 DeFi、流动性碎片化市场|适用于高频交易市场|

在 Solana 生态中，Raydium、Orca 和 Serum 是最主要的 DEX，其中 **Raydium 结合了 AMM 和 Serum 订单簿，提高了资本效率**。

---

### **面试问题 3：去中心化借贷协议的工作原理是什么？**

**回答：**  
去中心化借贷协议（如 Aave、Compound、Solend）允许用户在无需中介的情况下进行借贷交易，核心机制包括：

1. **超额抵押（Overcollateralization）**：用户必须存入比借款价值更高的资产，以防止坏账（例如，存入 $150 USDC 借出 $100 SOL）。
2. **借贷利率（Interest Rate）**：通常基于供需算法动态调整。
3. **清算机制（Liquidation）**：如果借款人的抵押率跌破一定阈值，协议会自动清算其部分资产，以保证资金安全。
4. **去中心化治理（Governance）**：很多借贷协议通过治理代币（如 AAVE、COMP）让社区成员投票决定协议的调整和升级。

在 Solana 生态中，**Solend 和 Jet Protocol 是最主要的去中心化借贷协议**。它们提供低成本、高速的借贷服务，适用于高频交易者和流动性提供者。

---

### **面试问题 4：在 DeFi 协议中，闪电贷（Flash Loan）是什么？它有哪些潜在风险？**

**回答：**  
**闪电贷（Flash Loan）** 是一种**无需抵押**的 DeFi 借贷机制，借款人可以在同一笔交易中借款和偿还资金。如果在交易结束前未能偿还，则交易会被回滚。

**用途：**

- **套利（Arbitrage）**：利用不同 DEX 之间的价格差赚取利润。
- **清算（Liquidation）**：通过闪电贷偿还欠款并获取清算奖励。
- **抵押品更换（Collateral Swap）**：在不卖出资产的情况下调整抵押组合。

**潜在风险：**

- **智能合约漏洞**：如果 DeFi 协议代码存在漏洞，攻击者可能利用闪电贷进行攻击。
- **价格操纵**：闪电贷可以短暂控制 AMM 的流动性，导致预言机价格偏移，造成损失（如 2020 年 bZx 闪电贷攻击）。

Solana 生态中，**Solend 和 Tulip Protocol 支持闪电贷**，但由于 Solana 交易执行速度快，攻击风险较以太坊低。

---

### **面试问题 5：Solana 生态中有哪些主要的 DeFi 协议？**

**回答：**  
Solana 生态的 DeFi 协议涵盖多个领域，主要包括：

|领域|协议|作用|
|---|---|---|
|**去中心化交易所（DEX）**|Raydium, Serum, Orca|提供 AMM 和订单簿交易|
|**借贷（Lending）**|Solend, Jet Protocol|允许用户抵押资产进行借贷|
|**收益聚合（Yield Aggregator）**|Tulip Protocol, Francium|通过自动复利优化收益|
|**稳定币（Stablecoin）**|UXD, Saber, Mercurial|提供去中心化稳定币交易|
|**衍生品（Derivatives）**|Drift Protocol, Mango Markets|期货、杠杆交易等|

这些协议构成了 Solana DeFi 生态的重要基石，并不断发展。

---

你对这些面试问题是否满意？或者你希望更深入讨论某个具体的 DeFi 主题，例如 **跨链互操作性、DeFi 安全性、流动性质押（LSTs）** 等？

## （5）报价算法

---

| **算法**                 | **适用场景**         | **优点**     | **缺点**      |
| ---------------------- | ---------------- | ---------- | ----------- |
| **恒定乘积做市商（AMM）**       | DEX（Uniswap）     | 低 Gas、无订单簿 | 滑点高，价格偏离    |
| **订单簿模式**              | 高流动性市场（Serum）    | 精确定价       | 需要 LP 提供流动性 |
| **预言机定价**              | 合成资产、借贷协议        | 准确         | 依赖外部数据      |
| **NFT 报价（AMM + 机器学习）** | NFT 交易（Sudoswap） | 自动化定价      | 适应性低        |
| **机器学习**               | 预测价格             | 动态调整       | 训练成本高       |


**MEV 报价算法（Miner Extractable Value）** ：

套利利润=(前置买入价−后置卖出价)×交易量

### **基础问题**

#### **1. 你能解释一下 AMM（自动做市商）模型是如何定价的吗？**

**答案：**  
AMM 主要通过恒定乘积公式：
$$

x⋅y=kx \cdot y = k
$$
来计算报价。其中：

- xx 和 yy 代表池中的两种代币数量。
- kk 作为常数，在没有额外流动性加入时保持不变。

当用户想要用 $$Δx\Delta x $$交换$$ Δy\Delta y$$：
$$
ynew=kxnewy_{new} = \frac{k}{x_{new}} Δy=yold−ynew\Delta y = y_{old} - y_{new}

$$
这种模型的优点是**无需订单簿**，但缺点是**价格滑点高**，特别是在流动性较低时。

---

#### **2. AMM 模型相比传统订单簿有哪些优势？**

**答案：**

- **无需做市商**：用户提供流动性即可交易，无需传统的买卖订单匹配。
- **低 Gas 费**：相比订单簿，AMM 交易可以直接通过流动性池完成，而非匹配多个订单。
- **流动性持续可用**：即使市场深度不足，也可以完成交易，而订单簿在流动性不足时可能面临订单无法匹配的情况。

---

#### **3. 订单簿模型的报价是如何确定的？**

**答案：** 订单簿模式依赖于买单（Bid）和卖单（Ask）：

- **最高买单**（Best Bid）表示当前市场愿意支付的最高价格。
- **最低卖单**（Best Ask）表示市场愿意卖出的最低价格。
- 交易价格通常在 **Bid 和 Ask 之间**，具体取决于市场深度和撮合机制。

例如：

|买单（Bid）|卖单（Ask）|
|---|---|
|99.5 USDT|100.0 USDT|
|99.0 USDT|101.0 USDT|

当前市场的报价区间为 **99.5 - 100 USDT**。

---

### **进阶问题**

#### **4. 你如何降低 AMM 交易中的滑点？**

**答案：**

- **增加流动性**：池子越大，滑点越低。
- **改进 AMM 公式**：如 Uniswap v3 采用**集中流动性（Concentrated Liquidity）**，让 LP 资金集中在特定价格范围，提高资本效率。
- **使用预言机喂价**：结合 Chainlink 等预言机，提供更稳定的价格基准。

---

#### **5. 预言机（Oracle）在 DeFi 报价中有什么作用？**

**答案：** 预言机提供链下市场的实时价格数据，防止 DEX 价格偏离实际市场。例如：

- **Chainlink** 提供汇总后的 ETH/USD 价格。
- **Pyth** 提供低延迟的价格数据，适用于高频交易。
- **预言机 + AMM** 结合，可修正 AMM 价格偏差，减少套利空间。

---

#### **6. 你如何防止预言机喂价攻击？**

**答案：**

- **时间加权平均价格（TWAP）**：使用多个时间窗口计算价格，减少单次操控的影响。
- **多预言机聚合**：采用多个数据源，避免单个预言机失准。
- **价格变化阈值**：如果价格变化超出设定范围，则拒绝执行交易。

---

### **高级问题**

#### **7. Uniswap v3 的报价机制是如何优化的？**

**答案：** Uniswap v3 采用**集中流动性（Concentrated Liquidity）**：

- 允许 LP 选择一个价格范围提供流动性，而非整个市场价格范围。
- 交易价格按照**曲线定价**，价格公式：
$$
P=(yx)P = \left(\frac{y}{x}\right)
$$
- LP 在其选定价格范围内提供更高资本效率，减少价格滑点。

---

#### **8. 在 NFT 交易市场中，Sudoswap 如何进行报价？**

**答案：** Sudoswap 采用 AMM 模型为 NFT 定价：

- **线性定价**：每次 NFT 交易后，价格按固定值增加/减少。
- **指数定价**：NFT 价格按百分比增长。
- **流动性池（NFT + ETH）**：允许 NFT 作为资产直接加入做市池，实现自动报价。

示例：

- 初始价格：1 ETH
- 线性增长模式（步长 0.1 ETH）：下一次交易价格 = 1.1 ETH
- 指数增长模式（增长 5%）：下一次交易价格 = 1.05 ETH

---

### **开放性问题**

#### **9. 如果你要设计一个新的报价算法，你会怎么做？**

**答案：** 我会结合多种模型：

1. **AMM + 预言机**：结合恒定乘积做市商与预言机价格，减少价格偏差。
2. **动态滑点调整**：根据市场流动性动态调整手续费，减少大额交易对价格的影响。
3. **机器学习优化**：使用 LSTM 预测市场波动，动态调整 AMM 价格曲线。

---



## （6）Uniswap V1 vs. V2 vs. V3 

以下是一些关于 **Uniswap 各版本区别** 的面试问题，并附带示例答案。

---

### **基础问题**

#### **1. 你能简要介绍 Uniswap V1、V2 和 V3 的主要区别吗？**

**答案：**

| **特性**       | **V1**                    | **V2**              | **V3**                |
| ------------ | ------------------------- | ------------------- | --------------------- |
| **流动性池**     | ETH-Token                 | 任意 ERC-20 交易对       | 集中流动性（CLMM）           |
| **定价公式**     | 恒定乘积公式 x⋅y=kx \cdot y = k | 同 V1                | 采用 Tick-based 机制优化流动性 |
| **交易路径**     | 仅支持 Token 与 ETH 交换        | 直接支持 Token-Token 交易 | 同 V2                  |
| **价格预言机**    | 无                         | TWAP 预言机            | 低 Gas 成本的 TWAP        |
| **LP 资金效率**  | 低                         | 较高                  | 最高（LP 选择流动性区间，提高资本效率） |
| **手续费模式**    | 固定 0.3%                   | 可变 0.05% - 1%       | 多档费率，LP 自定义           |
| **闪电贷**      | 无                         | 支持                  | 支持                    |
| **NFT 化流动性** | 无                         | 无                   | 是（LP 头寸是 NFT）         |

---

### **进阶问题**

#### **2. 为什么 Uniswap V2 取消了 V1 的 ETH 交易对限制？**

**答案：**  
在 V1 中，每个流动性池都是 **Token-ETH**，如果用户想直接交换 **TokenA -> TokenB**，必须经过 ETH，例如：

1. TokenA -> ETH
2. ETH -> TokenB

这样增加了交易成本和滑点。V2 允许直接创建 **TokenA-TokenB** 交易对，减少交易步骤和费用，提高流动性利用率。

---

#### **3. Uniswap V2 引入了什么新功能来提升价格预言机的准确性？**

**答案：**  
Uniswap V2 引入 **时间加权平均价格（TWAP）** 预言机：

$$
TWAP=∑Pt⋅Δt∑Δt\text{TWAP} = \frac{\sum P_t \cdot \Delta t}{\sum \Delta t}
$$
- 记录过去的价格累计值，防止短时间内操纵价格。
- 解决了 V1 无法提供可靠预言机数据的问题。

但 V2 计算 **TWAP 需要消耗 Gas**，V3 进一步优化了 Gas 成本。

---

#### **4. Uniswap V3 的“集中流动性”如何提升资本效率？**

**答案：**  
V3 允许 LP 在 **自定义价格范围内提供流动性**，而不是像 V2 一样将流动性均匀分布在整个价格区间：
$$
Pmin≤P≤PmaxP_{\text{min}} \leq P \leq P_{\text{max}}
$$
- 在 LP 选择的价格范围内，资金利用率提升 **4,000 倍**。
- **减少无常损失**（Impermanent Loss），因为 LP 资金不会一直处于非最优价格区间。

> **无常损失**:是流动性提供者（LP）在 AMM（自动做市商）协议中存入资产后，由于价格变动而导致的**相对损失**。它被称为“无常”，是因为如果价格回归初始状态，损失会消失。但如果价格持续偏离，损失就变成**永久损失**。


**示例：**

- V2：如果 ETH/USDC 价格范围为 **$500-$5000**，资金分布均匀，可能大部分资金是非活跃的。
- V3：LP 可选择 **$900-$1100** 区间提供流动性，确保资金主要用于实际交易，提高收益。

---

#### **5. 为什么 Uniswap V3 的 LP 头寸是 NFT 而不是 ERC-20？**

**答案：**

- 在 V2 中，所有 LP 共享整个价格区间，流动性池份额是 **可替代的（ERC-20）**。
- V3 允许 LP 自定义价格区间，因此每个 LP 的**流动性头寸是独特的**，无法标准化为 ERC-20，而是用 **NFT** 来代表。
- 这也意味着 V3 LP 头寸不能像 V2 一样直接转让，而需要通过 NFT 交易或提取流动性后重新存入。

---

### **高级问题**

#### **6. Uniswap V3 如何影响“无常损失”？**

**答案：** 无常损失（Impermanent Loss, IL）是指 LP 资金因价格波动导致的未实现亏损。V3 通过集中流动性减少 IL 影响：

1. **LP 资金分布更精准**：如果 LP 只提供在 $1000-$1100 的 ETH 价格范围，ETH 价格如果在该范围波动，IL 会更小。
2. **提高 LP 交易费用收入**：由于资金集中，单位资金收益更高，能抵消一部分 IL。
3. **但 IL 仍然存在**：如果 ETH 价格超出 LP 设置的范围，流动性会被冻结，LP 需要手动调整价格范围，否则无法继续收取费用。

---

#### **7. Uniswap V3 的 Tick 机制是如何工作的？**

**答案：**

- V3 采用 **Tick-based 定价机制**，将价格划分为**固定间隔（Ticks）**，每个 Tick 代表一个最小价格单位。
- 每个 Tick 只能激活部分流动性，确保流动性更加精准分布在特定价格范围。
- 计算公式： Ptick=Pmin×(1.0001)tick indexP_{\text{tick}} = P_{\text{min}} \times (1.0001)^{\text{tick index}} 其中：
    - 1.00011.0001 是最小价格增量。
    - Tick Index 代表价格刻度。

**好处：**

- 通过 Tick 机制，流动性可以更细粒度地管理，减少资金浪费。
- LP 头寸可以跨多个 Tick 进行分布，提高资本效率。

---

#### **8. V3 的“自定义手续费机制”如何影响 LP 收益？**

**答案：** V2 的交易手续费固定为 **0.3%**，而 V3 允许 LP 选择不同的手续费档位：

- **0.05%** - 适用于稳定币交易对（USDC/USDT）。
- **0.3%** - 适用于大多数交易对（ETH/USDC）。
- **1.0%** - 适用于低流动性或高风险资产（SHIBA/ETH）。

**影响：**

- **LP 可以根据市场需求选择最优费率**，提高收益。
- **高风险资产采用更高手续费**，补偿 LP 风险。
- **稳定币交易采用更低手续费**，提高交易量。

---

### **开放性问题**

#### **9. 你如何选择在 Uniswap V2 还是 V3 提供流动性？**

**答案：**

- **如果是被动 LP，选择 V2**，因为 V2 的流动性分布是自动的，不需要调整。
- **如果希望提高收益，可以选择 V3**，但需要手动管理价格范围，否则可能面临“流动性冻结”问题。
- **如果交易对波动较小（如稳定币对），V3 是更优选择**，因为集中流动性可以提高资本效率。
- **如果交易对波动较大（如 MEME 币），V2 可能更适合**，因为 LP 资金不会被锁定在特定区间。

---

#### **10. 你如何优化 Uniswap V3 LP 策略，以最大化收益？**

**答案：**

1. **选择合适的价格范围**：如果市场波动不大，LP 可选择狭窄的价格范围，赚取更多交易手续费。
2. **动态调整流动性**：如果价格超出范围，LP 需要调整头寸，否则资金无法获得收益。
3. **结合预言机优化流动性分布**：例如，结合 Chainlink 预言机调整价格范围，减少 IL 影响。
4. **选择合适的手续费档位**：对于高频交易对（如稳定币对），选择 **0.05%**，对于低流动性交易对，选择 **1%** 以赚取更多费用。

---




## （7）**Solana 计算单元（Compute Unit, CU）优化**

Solana 交易的执行成本由 **计算单元（CU）** 决定，每个智能合约指令（Instruction）消耗一定量的 CU。优化 CU 使用可以降低交易成本，提高 TPS。以下是常见的面试题及答案。

---

### **基础问题**

#### **1. 什么是 Solana 计算单元（Compute Unit, CU）？**

**答案：**  
计算单元（CU）是 **衡量 Solana 交易计算成本的单位**，类似以太坊的 Gas。Solana 交易执行时，每个智能合约指令（Instruction）都会消耗 CU。

- 每个区块的 CU 限制：**最大 12.5 亿 CU**
- 默认交易 CU 限制：**200,000 CU**
- 超过默认 CU 需要 **手动提升上限**

---

#### **2. 计算单元（CU）与交易费用（TX Fee）的关系？**

**答案：**  
Solana 交易费用由以下公式决定：

交易费用=CU 消耗×CU 价格\text{交易费用} = \text{CU 消耗} \times \text{CU 价格}

- CU 价格由 **网络拥堵情况动态调整**
- 交易可以 **指定愿意支付的 CU 价格**（类似以太坊 EIP-1559 提供小费）
- **优化 CU 既能降低 Gas 费，也能提高交易成功率**

---

### **进阶问题**

#### **3. 如何提高 CU 限制，确保交易不会失败？**

**答案：**  
Solana 交易有默认 CU 限制（200,000 CU）。如果交易超出 CU 限制，会 **Transaction error: exceeded compute budget**。

#### **提升 CU 限制的方法**

1. **手动增加 CU 限制**
    
    ```rust
    let increase_budget_ix = ComputeBudgetInstruction::set_compute_unit_limit(500_000);
    ```
    
    - 设置计算单元上限（500,000 CU）
2. **增加愿意支付的 CU 价格**
    
    ```rust
    let set_price_ix = ComputeBudgetInstruction::set_compute_unit_price(10_000);
    ```
    
    - 提高 CU 价格，提高交易优先级
3. **交易前插入 CU 优化指令**
    
    ```rust
    let priority_fee_ix = ComputeBudgetInstruction::request_units(1_000_000, 10_000);
    ```
    
    - 确保交易获取足够 CU，提高被打包的概率

---

#### **4. 哪些 Solana 操作最消耗 CU？**

**答案：**

- **创建新账户**（Solana 账户的初始化）
- **哈希计算**（SHA256、Keccak256 等）
- **BPF 程序调用**（复杂智能合约）
- **跨合约调用**（多个合约交互）
- **序列化 / 反序列化数据**（Account 解析）

示例：

```rust
msg!("Before expensive computation");
invoke(&expensive_instruction, &account_infos)?;
msg!("After expensive computation");
```

如果 `After expensive computation` 没有被打印，说明 CU 耗尽，交易失败。

---

### **优化策略**

#### **5. 如何优化 CU 使用？**

| **优化方法**          | **说明**                            |
| ----------------- | --------------------------------- |
| **减少跨合约调用**       | 合约间调用成本高，尽量在一个合约内完成逻辑             |
| **优化数据结构**        | 使用 `ZeroCopy` 代替 Borsh 序列化，减少解析成本 |
| **使用 ALT（地址查找表）** | 避免重复传输公钥，减少交易体积                   |
| **减少账户访问**        | 避免访问无关账户（`account_infos` 数量影响 CU） |
| **使用合适的哈希算法**     | `SHA256` 比 `Keccak256` 便宜         |
| **缓存计算结果**        | 避免重复计算相同的数据                       |

示例：  
**错误（高 CU 消耗）**

```rust
let mut account_data = account.data.borrow();
let deserialized_data: MyStruct = BorshDeserialize::deserialize(&mut account_data)?;
```

**优化（低 CU 消耗）**

```rust
let account_data = ZeroCopy::load::<MyStruct>(&account.data.borrow())?;
```

---

#### **6. CU 优化在 DeFi / NFT 交易中的作用？**

**DeFi 场景：**

- AMM 交易（如 Raydium、Orca）涉及多个账户，ALT 优化账户访问减少 CU 消耗
- 订单撮合逻辑中，减少不必要的 `invoke()`，降低 CU

**NFT 场景：**

- Mint NFT 需要创建新账户（高 CU 消耗），可以优化 `Metaplex` 代码
- 交易多个 NFT 时，批量处理减少 CU

示例（优化 Mint NFT）

```rust
ComputeBudgetInstruction::set_compute_unit_limit(400_000);
```

---

#### **7. 如何调试 CU 使用？**

1. **本地模拟执行**
    
    ```bash
    solana-test-validator
    ```
    
2. **开启日志**
    
    ```rust
    solana_logger::setup();
    msg!("Transaction started");
    ```
    
3. **使用 `solana logs` 监控 CU**
    
    ```bash
    solana logs
    ```
    

---

### **高级问题**

#### **8. 如何在 Anchor 框架中优化 CU？**

**答案：**

1. **减少 `accounts` 访问**
    
    ```rust
    #[account(mut)]
    pub my_account: Account<'info, Data>,
    ```
    
    - 仅使用 `mut` 修改必要的账户，避免不必要的写操作
2. **使用 `seeds` 代替显式公钥**
    
    ```rust
    #[account(
        init,
        payer = user,
        seeds = [b"vault", user.key().as_ref()],
        bump
    )]
    ```
    
    - 避免存储完整公钥，减少数据读取 CU

---

#### **9. Solana 1.16 版本后，CU 计算有哪些优化？**

**答案：**

- **动态 CU 价格调整**
- **CU 限制提高**（默认 200k → 400k）
- **BPF VM 优化**（减少 CPU 消耗）

---

#### **10. Solana CU 未来发展方向？**

**可能的改进**

- **更智能的 CU 定价**
- **交易批量处理优化**
- **账户压缩减少 CU**

---

### **总结**

|题目|难度|
|---|---|
|什么是 CU？|🌟🌟|
|CU 如何影响交易费用？|🌟🌟🌟|
|如何增加 CU 限制？|🌟🌟🌟|
|CU 优化方法？|🌟🌟🌟🌟|
|DeFi / NFT 场景 CU 优化？|🌟🌟🌟🌟|
|如何调试 CU 使用？|🌟🌟🌟🌟🌟|
|Solana 1.16 CU 优化|🌟🌟🌟🌟|


在 Solana 中优化 **计算单元（Compute Unit, CU）** 使用，可以通过多种方法来降低交易成本、提高吞吐量，并确保智能合约的高效执行。以下是一些实际的优化方式，包括 **代码层面** 和 **其他层面** 的优化建议。

---

### **代码层面的优化**

#### 1. **减少账户访问**

每个账户的访问都需要消耗 CU，因此要尽量减少不必要的账户读取或写入操作。

- **优化前：**
    
    ```rust
    let mut data = account.data.borrow_mut();
    let deserialized_data: MyStruct = MyStruct::try_from_slice(&data)?;
    ```
    
- **优化后：** 使用更高效的内存访问方式，例如 **零拷贝（ZeroCopy）**，避免重复序列化/反序列化。
    
    ```rust
    let account_data = ZeroCopy::load::<MyStruct>(&account.data.borrow())?;
    ```
    

#### 2. **避免无关账户的传递**

在交易中仅传递需要操作的账户。每增加一个账户，交易的计算开销就会增加。

- **优化前：**
    
    ```rust
    let accounts = vec![
        account1, account2, account3, account4, account5
    ];
    invoke(&instruction, &accounts)?;
    ```
    
- **优化后：** 只传递当前需要操作的账户，避免冗余账户的传递。
    
    ```rust
    let accounts = vec![account1, account2];  // 只传递必要的账户
    invoke(&instruction, &accounts)?;
    ```
    

#### 3. **减少跨合约调用**

每次跨合约调用都可能会增加计算开销，尤其是在多个合约之间传递数据时。可以通过将多个操作合并到一个合约中来减少跨合约调用。

- **优化前：**
    
    ```rust
    invoke(&contract1_instruction, &account_infos)?;
    invoke(&contract2_instruction, &account_infos)?;
    ```
    
- **优化后：** 合并合约操作为一个交易流程，减少跨合约调用。
    
    ```rust
    invoke(&merged_contract_instruction, &account_infos)?;
    ```
    

#### 4. **避免重复计算**

如果某些计算是重复进行的，可以将计算结果缓存或通过其他方式减少重复计算。

- **优化前：**
    
    ```rust
    let result1 = expensive_calculation(data);
    let result2 = expensive_calculation(data);
    ```
    
- **优化后：** 使用缓存变量来避免重复计算。
    
    ```rust
    let result = expensive_calculation(data);
    let result1 = result;
    let result2 = result;
    ```
    

#### 5. **简化智能合约逻辑**

智能合约中的一些复杂逻辑或不必要的循环会消耗大量 CU。确保合约逻辑尽可能简洁，并避免不必要的循环或条件判断。

- **优化前：**
    
    ```rust
    for i in 0..1000 {
        let temp = complex_operation(i);
        if temp > threshold {
            break;
        }
    }
    ```
    
- **优化后：** 精简代码逻辑，减少不必要的循环。
    
    ```rust
    let temp = complex_operation(0);  // 只进行一次计算
    if temp > threshold {
        return Ok(());
    }
    ```
    

---

### **其他层面的优化**

#### 1. **使用地址查找表（ALT）**

Solana 引入了地址查找表（Address Lookup Table, ALT）来优化交易中存储账户地址的方式。在处理多个账户时，使用 ALT 可以减少交易体积，从而节省 CU。

- **实现 ALT：** 使用 `AddressLookupTableProgram` 创建和扩展 ALT，确保合约可以通过索引而不是公钥直接访问账户。

#### 2. **交易分批**

对于涉及大量账户或数据的交易，考虑将交易分批提交，而不是一次性提交所有内容。这样可以避免由于计算过多导致 CU 超出限制。

- **示例：**
    - **批量交易**：例如在 NFT 交易中，可以将多个 NFT 的 mint 操作分为多个交易，而不是一个交易执行所有操作。

#### 3. **动态调整 CU 上限**

在某些情况下，交易可能需要更多的计算资源。通过 `ComputeBudgetInstruction` 提高交易的 CU 限制，确保复杂交易能够顺利执行。

- **示例：** 提高 CU 限制到 500,000：
    
    ```rust
    let ix = ComputeBudgetInstruction::set_compute_unit_limit(500_000);
    ```
    

#### 4. **网络层面的优化**

Solana 的 **吞吐量** 和 **CU 使用** 也受到网络状态的影响，保持网络稳定和高效是系统级的优化：

- **增加集群节点数**：更多节点可以处理更多并发交易。
- **使用更低延迟的网络连接**：确保交易数据传输的速度。
- **优化交易验证节点**：提高节点处理能力和效率。

#### 5. **合约升级和优化**

通过不断改进合约，优化计算逻辑，减少不必要的计算和存储操作，来降低整体的 CU 消耗。

- **示例：**
    - **替换复杂的 `if` 判断**：避免在合约中执行复杂的条件判断，而是通过更简洁的数学逻辑来实现。
    - **合并多个指令**：将合约中的多个指令合并成一个更高效的执行流程。

#### 6. **减少存储操作**

Solana 中的 **存储操作**（如写入账户数据）是非常昂贵的，尽量减少不必要的存储操作，特别是避免在每个交易中都进行写入操作。

- **优化前：** 每次操作后都写入账户数据：
    
    ```rust
    account.data.borrow_mut()[0] = 1;
    ```
    
- **优化后：** 在必要时才进行写操作：
    
    ```rust
    if account.data.borrow_mut()[0] != 1 {
        account.data.borrow_mut()[0] = 1;
    }
    ```
    

---

### **总结**

| 优化策略         | 解释                         |
| ------------ | -------------------------- |
| 减少账户访问       | 减少不必要的账户访问，避免冗余的账户传递       |
| 优化计算逻辑       | 简化智能合约逻辑，避免复杂的循环和条件判断      |
| 使用地址查找表（ALT） | 对多个账户的操作使用 ALT，减少交易体积和计算成本 |
| 动态调整 CU 上限   | 对需要大量计算的交易动态增加 CU 限制       |
| 交易分批         | 对多个操作分批提交，避免超出 CU 上限       |
| 合约升级和优化      | 精简合约代码，提高执行效率，减少计算和存储开销    |

这些优化策略可以帮助你在 Solana 上开发时提高合约性能，降低交易费用，并有效控制计算资源的消耗。如果你有更多问题，或希望深入某个优化策略，欢迎继续提问！

