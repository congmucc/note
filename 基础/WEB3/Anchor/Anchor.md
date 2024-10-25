address：```
```
// windows


//  mac

// 


```
[程序示例 | Solana中文大全](https://www.solana-cn.com/SolanaDocumention/programs/examples.html#%E5%9F%BA%E7%A1%80)

[如何在 Solana 中编写您的第一个锚点程序 - 第 2 部分 |QuickNode 快节点 --- How to Write Your First Anchor Program in Solana - Part 2 | QuickNode](https://www.quicknode.com/guides/solana-development/anchor/how-to-write-your-first-anchor-program-in-solana-part-2)

[Solana 中文开发教程 (solanazh.com)](https://www.solanazh.com/)

[anchor/examples/tutorial/basic-0 at master · coral-xyz/anchor](https://github.com/coral-xyz/anchor/tree/master/examples/tutorial/basic-0)

[Getting Test SOL | Solana](https://solana.com/developers/cookbook/development/test-sol)







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
