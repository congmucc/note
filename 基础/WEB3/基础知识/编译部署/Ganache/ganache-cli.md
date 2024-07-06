> 用于测试的区块链客户端，和`Hardhat`差不多
# 1 初始化客户端

用Go初始化以太坊客户端是和区块链交互所需的基本步骤。首先，导入go-etherem的`ethclient`包并通过调用接收区块链服务提供者URL的`Dial`来初始化它。

若您没有现有以太坊客户端，您可以连接到infura网关。Infura管理着一批安全，可靠，可扩展的以太坊[geth和parity]节点，并且在接入以太坊网络时降低了新人的入门门槛。

```
client, err := ethclient.Dial("https://cloudflare-eth.com")
```

若您运行了本地geth实例，您还可以将路径传递给IPC端点文件。

```
client, err := ethclient.Dial("/home/user/.ethereum/geth.ipc")
```

对每个Go以太坊项目，使用ethclient是您开始的必要事项，您将在本书中非常多的看到这一步骤。

## 1.1 使用Ganache

[Ganache](https://github.com/trufflesuite/ganache-cli)(正式名称为testrpc)是一个用Node.js编写的以太坊实现，用于在本地开发去中心化应用程序时进行测试。现在我们将带着您完成安装并连接到它。

首先通过[NPM](https://www.npmjs.com/package/ganache-cli)安装ganache。

```
npm install -g ganache-cli
```

然后运行ganache cli客户端。

```
ganache-cli
```

```
Ganache CLI v6.12.2 (ganache-core: 2.13.2)

Available Accounts
==================
(0) 0x6803806A356666E98D963Bf1b768ab99513522fd (100 ETH)
(1) 0x190F8E12E63Ba96107ba2c47bF67202321bd6f4C (100 ETH)
(2) 0x5Af6b0173f806b3EdB23e62Ab2efcC407d5aa891 (100 ETH)
(3) 0x324F18A2417bDbb021eEB65c5032aF14BDFEB899 (100 ETH)
(4) 0x097D6Ee145e8Dd8d44ec3D1cD234B517f3D80Aa8 (100 ETH)
(5) 0xfD6b0be02316a5CE1b781E5a0877c0028fF663BA (100 ETH)
(6) 0x380e1d50d9cba0aDf0f7f2c9da4A9E297Ef39956 (100 ETH)
(7) 0xE47ce64E696E59BC9B631E7db324aCa852b9dBc5 (100 ETH)
(8) 0xFd71A1D1e6Eddf170E5B212604A755Ee94D0859c (100 ETH)
(9) 0xa82b0e12Ec51EBF3fc9d58DbFe3A5149289E1Cf9 (100 ETH)

Private Keys
==================
(0) 0x20073e72d42ffd76b989f046fd4efc1d60d93715bf8ec0863c2e039b9d9bf4e7
(1) 0x61a24b1b6a7b3ffd5cf7e9fc52d7abf17788584c161bf143b45f4d1d478aa948
(2) 0xa23f642e6595ec9c3b7a8ce07d3e430eba7934d623f5c1ba1a47e201689df655
(3) 0xf3373ee016505f2b4ae043fd4ccde98466b1a1545a1b31cdadf48c1dfe8488db
(4) 0x05767a4c854c66a041b0ea79488b64695b2337e27ac3aee3c07d7adfe1c9a36e
(5) 0xc71078d37bd3af10fdefd22754c5931739d6ca74ad9c5d70b87c5123a368b56c
(6) 0xcf2913526fc2fd95e3da557f6a9c20ae70858b466e9a25c3de127938e0409655
(7) 0xcbe3b613ddec84e7b85765dff75ee11f9734f471498d6a9994f9db4d06cd38db
(8) 0x9a34d9d6df67717e14b9215da3088bdec554b345a00ec08f8bae2691da04c61f
(9) 0x91cb58f86f2e2b4b4a7c2ee328058286ef4722f0975f7378bad7950f412c3b75

HD Wallet
==================
Mnemonic:      filter monster dust loop head air settle piece position already three ignore
Base HD Path:  m/44'/60'/0'/0/{account_index}

Gas Price
==================
20000000000

Gas Limit
==================
6721975

Call Gas Limit
==================
9007199254740991

Listening on 127.0.0.1:8545

```

现在连到`http://localhost:8584`上的ganache RPC主机。

```
client, err := ethclient.Dial("http://localhost:8545")
if err != nil {
  log.Fatal(err)
}
```

在启动ganache时，您还可以使用相同的助记词来生成相同序列的公开地址。

```
ganache-cli -m "much repair shock carbon improve miss forget sock include bullet interest solution"
```

我强烈推荐您通过阅读其[文档](http://truffleframework.com/ganache/)熟悉ganache。

---

### 完整代码

[client.go](https://github.com/miguelmota/ethereum-development-with-go-book/blob/master/code/client.go)

```
package main

import (
    "fmt"
    "log"

    "github.com/ethereum/go-ethereum/ethclient"
)

func main() {
    client, err := ethclient.Dial("https://cloudflare-eth.com")
    if err != nil {
        log.Fatal(err)
    }

    fmt.Println("we have a connection")
    _ = client // we'll use this in the upcoming sections
}
```