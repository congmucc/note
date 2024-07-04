[学习教程](https://github.com/ABCDELabs/Understanding-Ethereum-Go-version)

# 0、初始问题回答ing

**Miner 是从什么方式获取到待打包的 Transactions?**

矿工（Miner ）从交易池（Transaction Pool）中获取待打包的交易。交易池是一个存储尚未被打包到区块中的交易的集合，通常称为内存池（Mempool）。

**Miner 是基于什么样策略从 Transaction Pool 中选择 Transaction 呢？**

矿工基于以下策略从交易池中选择交易：

1. **Gas 价格（Gas Price）**：矿工通常优先选择 Gas 价格较高的交易，因为这些交易会带来更高的手续费。
2. **交易的复杂性和大小**：交易的复杂性和大小也会影响选择策略。复杂或大的交易可能会被延后处理。
3. **交易的依赖关系**：如果某些交易相互依赖（例如 A 交易是 B 交易的输入），矿工会确保这些交易的顺序。

**被选择的 Transactions 又是以怎样的顺序(Order)被打包到区块中的呢？**

矿工根据上述策略选择交易后，通常按照以下顺序打包交易：

1. **按 Gas 价格从高到低排序**：以确保矿工获得最高的手续费。
2. **确保交易的依赖关系**：如果某些交易存在依赖关系，矿工会确保它们按正确的顺序打包。

**在执行 Transaction 的 EVM 是怎么计算 gas used，从而限定 Block 中 Transaction 的数量?**

在 EVM 中，每个操作码（Opcode）都有一个固定的 Gas 消耗。当一个交易被执行时，EVM 会逐步执行交易中的操作码，并累计消耗的 Gas。每个区块都有一个最大 Gas 限制（Gas Limit），交易执行总消耗的 Gas 不能超过这个限制。如果在执行交易过程中耗尽了 Gas，交易会失败，但已经消耗的 Gas 不会退还。

**剩余的 gas 又是怎么返还给 Transaction Proposer 的呢？**

当交易成功执行且未耗尽提供的 Gas 时，剩余的 Gas 会被返还给交易的发起者（Proposer）。返还过程如下：

1. 计算实际消耗的 Gas。
2. 用交易中指定的 Gas 价格乘以实际消耗的 Gas，计算实际的手续费。
3. 剩余的 Gas 乘以 Gas 价格，返还给交易发起者。

**EVM 是怎么解释 Contract Code 的 Message Call 并执行的呢？**

EVM 解释和执行智能合约代码时，会处理消息调用（Message Call）：

1. **解析输入数据**：根据智能合约 ABI 解析输入数据，确定要调用的函数及其参数。
2. **执行函数**：根据智能合约代码的字节码（Bytecode），逐步执行相应的操作码。
3. **修改状态**：执行过程中，会根据函数逻辑读取和修改智能合约的存储变量。
4. **返回结果**：函数执行完毕后，将结果返回给调用者。

**在执行 Transaction 时，是什么模块，怎样去修改 Contract 中持久化变量？**

在 EVM 中，智能合约的持久化变量存储在合约的存储空间（Storage）中。执行交易时，EVM 通过以下步骤修改持久化变量：

1. **加载变量**：从存储空间加载当前值。
2. **执行逻辑**：根据交易中的操作码修改变量值。
3. **存储结果**：将修改后的值写回存储空间。

**Smart Contract 中的持久化变量是以什么样的形式存储？又是存储在什么地方？**

智能合约的持久化变量以键值对（Key-Value）的形式存储在合约的存储空间中。具体来说：

- **键（Key）**：通常是变量的哈希值。
- **值（Value）**：变量的实际值。 这些键值对存储在区块链的状态数据库（State Database）中。

**当新的 Block 更新到 Blockchain 中时，World State 又是在什么时机，以什么方式更新的呢？**

新的区块被添加到区块链时，世界状态（World State）会在以下时机更新：

1. **交易执行后**：每个交易执行完毕后，EVM 会更新当前世界状态。
2. **区块验证完成后**：当所有交易执行完并且区块被验证通过后，新的世界状态会被持久化。

**哪些数据常驻内存，哪些数据需要保存在 Disk 中呢？**

- 常驻内存的数据
  - 交易池（Mempool）：存储尚未打包的交易。
  - 临时计算结果：EVM 执行过程中产生的临时数据。
- 保存在磁盘的数据
  - 区块链数据：所有区块和交易的历史记录。
  - 状态数据库：合约存储和账户余额等持久化状态。
  - 日志和索引：用于快速检索和验证



# 1、第一章

## 1.1、geth 是什么？

`geth` 是以太坊基金会基于 Go 语言开发以太坊的官方执行层客户端，它实现了 Ethereum 协议(黄皮书)中所有需要的实现的功能模块。我们可以通过启动 `geth` 来运行一个 Ethereum 的节点。在以太坊 Merge 之后，`geth` 作为节点的执行层继续在以太坊生态中发挥重要的作用。 `go-ethereum`是包含了 `geth` 客户端代码和以及编译 `geth` 所需要的其他代码在内的一个完整的代码库。在本系列中我们会通过深入 go-ethereum 代码库，从 High-level 的 API 接口出发，沿着 Ethereum 主 Workflow，逐一的理解 Ethereum 具体实现的细节。

为了方便区分，在接下来的文章中，我们用 `geth` 来表示 go-ethereum 客户端程序，用 `GETH` 来表示 go-ethereum 的代码库。

总结的来说:

1. 基于 `go-ethereum` 代码库中的代码，我们可以编译出 `geth` 客户端程序。
2. 通过运行 `geth` 客户端程序我们可以启动一个 Ethereum 的节点。





# 实战

## 安装


Linux 安装
[CentOS 7 下安装并配置 Homebrew – 陈少文的网站 (chenshaowen.com)](https://www.chenshaowen.com/blog/install-homebrew-in-centos-7.html)


[centos7 安装 Homebrew - ramlife - 博客园 (cnblogs.com)](https://www.cnblogs.com/ramlife/p/16575589.html)




[安装 Geth |去以太坊 --- Installing Geth | go-ethereum](https://geth.ethereum.org/docs/getting-started/installing-geth)



## geth

官方文档：

[JSON-RPC Server| go-Ethereum --- JSON-RPC Server | go-ethereum](https://geth.ethereum.org/docs/interacting-with-geth/rpc)

环境：

这里geth版本因为1.20不支持pow了，所以说需要1.20以下的。

检查安装是否成功：
```geth
geth --help
```
在 /Desktop/blockchain 下新建 ethereum 目录，在其中再建一个 genesis.json  
文件和一个子目录 /data。  
PoW 共识版本的 genesis.json 文件内容如下：
```json
{
   "config": {
     "chainId": 1001,
     "homesteadBlock": 0,
     "eip150Block": 0,
     "eip150Hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
     "eip155Block": 0,
     "eip158Block": 0,
     "byzantiumBlock": 0,
     "constantinopleBlock": 0,
     "petersburgBlock": 0,
     "istanbulBlock": 0,
     "ethash": {}
   },
   "nonce": "0x0",
   "timestamp": "0x5ddf8f3e",
   "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
   "gasLimit": "0x47b760",
   "difficulty": "0x00002",
   "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
   "coinbase": "0x0000000000000000000000000000000000000000",
   "alloc": { },
   "number": "0x0",
   "gasUsed": "0x0",
   "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
 }

```

由于 eth 经历过一次升级从 PoW 改为 PoS，[目前最新版的 geth 已经不再内置 ethash](https://www.odaily.news/newsflash/324601)，因此 PoS 的 genesis.json 和之前的文章会略有不同。下面这个是 PoS 版的 genesis.json 文件：
```json
{
   "config": {
     "chainId": 8888,
     "homesteadBlock": 0,
     "eip150Block": 0,
     "eip155Block": 0,
     "eip158Block": 0,
     "byzantiumBlock": 0,
     "constantinopleBlock": 0,
     "petersburgBlock": 0,
     "istanbulBlock": 0,
     "berlinBlock": 0,
     "clique": {
       "period": 5,
       "epoch": 30000
     }
   },
   "difficulty": "1",
   "gasLimit": "8000000",
   "extradata": "0x0000000000000000000000000000000000000000000000000000000000000000b7e24438A3fe363f46994feEEdfbF5ad5078378d0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
   "alloc": {
     "27883476a0a617d8e6aa40888253608a0e05cfa4": { "balance": "300000" },
     "b7e24438A3fe363f46994feEEdfbF5ad5078378d": { "balance": "300000" },
     "87edfb2aed4875144fe1c3b28870284881990418": { "balance": "300000" }
   }
 }

```

运行

```cmd
geth --datadir "data" init genesis.json
```

使用命令进行创建私链：

```cmd
geth --datadir data --networkid 8888 console --nodiscover 2>geth.log
```

老方式：
```sh

```


之后在go中进行创建账户

这里需要安装

```go
go get github.com/ethereum/go-ethereum
```

使用ethereum包下的rpc

main.go

```go
package main

import (
	"context"
	"demo/geth/cli"
	"fmt"
	"github.com/ethereum/go-ethereum/common"
	"github.com/ethereum/go-ethereum/ethclient"
	"github.com/ethereum/go-ethereum/rpc"
	"math/big"
)

func main() {
	dial, rpcErr := rpc.Dial("http://127.0.0.1:8545")
	if rpcErr != nil {
		fmt.Printf("错误是 %s\n", rpcErr)
	}
	defer dial.Close()
	// 创建账户
	//account, accountErr := cli.NewAccount(dial, "123456")
	//if accountErr != nil {
	//	fmt.Printf("错误是 %s\n", accountErr)
	//}
	//fmt.Printf("创建的账户是 %s\n", account)

	number, err := cli.GetBlockNumber(dial)
	if err != nil {
		fmt.Printf("错误是 %s\n", err)
	}

	fmt.Println("当前区块高度是 %d\n", number)

	client, ethErr := ethclient.Dial("http://127.0.0.1:8545")
	if ethErr != nil {
		fmt.Printf("错误是 %s\n", ethErr)
	}
	defer client.Close()
	balanceAt, balanceErr := client.BalanceAt(context.Background(), common.HexToAddress("0x8ced2e07d3b81fd22447648270b720fd65e61dfb"), big.NewInt(0))
	if balanceErr != nil {
		fmt.Printf("错误是 %s\n", balanceErr)
	}
	fmt.Printf("余额是 %s\n", balanceAt)
}

```



account.go

```go
package cli

import (
    "fmt"
    "github.com/ethereum/go-ethereum/rpc"
)

func NewAccount(client *rpc.Client, pass string) (string, error) {
    var res string
    err := client.Call(&res, "personal_newAccount", pass)

    if err != nil {
       fmt.Println()
    }
    return res, nil
}
```

运行结果如下

```go
创建的账户是 0x8ced2e07d3b81fd22447648270b720fd65e61dfb
```

> 这里面有两点注意：
>
> 1、使用的命令是web3.js的，详情可以去看一下
>
> 2、`client.Call(&res, "personal_newAccount", pass)`这个就是使用，注意，这里将`.`代替为了`_`。



- rpc.Dial:
  **用途**: 此函数直接通过RPC协议与以太坊节点建立连接。它提供了更底层的访问方式，允许你调用任何公开的RPC方法，不仅仅是针对以太坊的。
  **返回值**: 返回一个*rpc.Client实例。这个客户端可以用来调用任何节点支持的RPC方法，你需要自己处理JSON-RPC请求的具体结构和响应。
  **适用场景**: 当你需要直接与以太坊节点的RPC接口交互，执行非标准或自定义的RPC调用时。
- ethclient.Dial:
  **用途**: 这是ethclient包提供的一个便捷函数，专为与以太坊区块链交互设计。它内部也是基于rpc.Dial来实现，但是进一步封装了以太坊相关的功能，提供了更高层次、更易用的API。
  **返回值**: 返回一个*ethclient.Client实例。这个客户端包含了多种针对以太坊特定操作的方法，如查询账户余额、发送交易、获取区块信息等，使用起来更加方便和直观。
  **适用场景**: 当你的应用主要关注于执行标准的以太坊操作，比如读取账户状态、交易发送等，使用ethclient.Dial会更加高效和直接，因为它已经为你实现了这些操作的细节。
- **总结来说**，如果你需要进行更底层或非标准的RPC调用，可以选择使用rpc.Dial。而如果是为了进行常见的以太坊区块链操作，ethclient.Dial提供了更为便捷和针对性的接口。



## Abigen

> 部署开发的solidity文件

官方文档：[开发者文档|go-Ethereum --- Go Contract Bindings | go-ethereum](https://geth.ethereum.org/docs/developers/dapp-developer/native-bindings)



根据官方文档安装，之后在remix中编译一下获取abi

```shell
abigen --abi SimpleStorage.abi --pkg contract --type SimpleStorage --out SimpleStorage.go
```

> --abi后跟abi文件地址，pkg是abi文件的包名，type一般是合约名称，out是输出目录





这里面需要获取opts，它的是一个div模式，需要读取本地目录

主要是opts获取，这个没什么说的，因为老版本已经不支持了。

```go
package main

import (
	"demo/abigen/contract"
	"fmt"
	"github.com/ethereum/go-ethereum/accounts/abi/bind"
	"github.com/ethereum/go-ethereum/common"
	"github.com/ethereum/go-ethereum/ethclient"
	"math/big"
	"strings"
)

const contractAddr = "0xd9145CCE52D386f254917e481eB44e9943F39138"

func main() {
	// 1 获取一个geth客户端
	cli, ethErr := ethclient.Dial("http://localhost:8545")
	if ethErr != nil {
		fmt.Println("geth client error:", ethErr)
	}
	defer cli.Close()
	// 2 获取合约实例
	simpleStorage, contractErr := contract.NewSimpleStorage(common.HexToAddress(contractAddr), cli)
	if contractErr != nil {
		fmt.Println("get contract error:", contractErr)
	}

	// 3 通过合约实例调用合约
	retrieve, retrieveErr := simpleStorage.Retrieve(nil)
	if retrieveErr != nil {
		fmt.Println("get contract error:", retrieveErr)
	}
	fmt.Println("retrieve:", retrieve)

	// 获取文件内容 这里面就是通过查询本地路径的文件目录然后获取文件内容再使用newReader函数设置一个opts
	var ks string

	// 获取签名
	opts, err := bind.NewTransactor(strings.NewReader(ks), "123456")
	if err != nil {
		return
	}
	simpleStorage.Store(opts, big.NewInt(0))
}

```





## 事件订阅

- 事件订阅使用：ws://localhost:8546

- 事件订阅要开启geth参数的ws

- 事件订阅可以用FilterQuery过滤

```go
package main

import (
	"context"
	"demo/abigen/contract"
	"fmt"
	"github.com/ethereum/go-ethereum"
	"github.com/ethereum/go-ethereum/accounts/abi/bind"
	"github.com/ethereum/go-ethereum/common"
	"github.com/ethereum/go-ethereum/core/types"
	"github.com/ethereum/go-ethereum/crypto"
	"github.com/ethereum/go-ethereum/ethclient"
	"math/big"
	"strings"
	"sync"
)

const contractAddr = "0xd9145CCE52D386f254917e481eB44e9943F39138"

func main() {
	// 1 获取一个geth客户端
	cli, ethErr := ethclient.Dial("http://localhost:8545")
	if ethErr != nil {
		fmt.Println("geth client error:", ethErr)
	}
	defer cli.Close()
	// 2 获取合约实例
	simpleStorage, contractErr := contract.NewSimpleStorage(common.HexToAddress(contractAddr), cli)
	if contractErr != nil {
		fmt.Println("get contract error:", contractErr)
	}

	// 3 通过合约实例调用合约
	retrieve, retrieveErr := simpleStorage.Retrieve(nil)
	if retrieveErr != nil {
		fmt.Println("get contract error:", retrieveErr)
	}
	fmt.Println("retrieve:", retrieve)

	// 获取文件内容 这里面就是通过查询本地路径的文件目录然后获取文件内容再使用newReader函数设置一个opts
	var ks string

	// 获取签名
	opts, err := bind.NewTransactor(strings.NewReader(ks), "123456")
	if err != nil {
		return
	}
	simpleStorage.Store(opts, big.NewInt(0))

	var wg sync.WaitGroup
	wg.Add(1)
	go func() {
		subEvent()
		defer wg.Done()
	}()
	wg.Wait()

}

func subEvent() {
	// 1 拿到事件订阅客户端
	subCli, err := ethclient.Dial("ws://localhost:8546")
	if err != nil {
		fmt.Println("geth client error:", err)
	}

	defer subCli.Close()

	// 2 封装过滤条件

	filter := ethereum.FilterQuery{
		Addresses: []common.Address{common.HexToAddress(contractAddr)},
		Topics:    [][]common.Hash{{crypto.Keccak256Hash([]byte("StoreEvent(uint256)"))}}, // 这里需要写类型
	}

	logs := make(chan types.Log)
	sub, err := subCli.SubscribeFilterLogs(context.Background(), filter, logs)
	if err != nil {
		fmt.Println("geth client error:", err)
	}

	for {
		select {
		case err = <-sub.Err():
			fmt.Println("err")
			return
		case vlog := <-logs:
			json, err := vlog.MarshalJSON()
			if err != nil {
				fmt.Println("json出现错误", err)
			}
			fmt.Println(string(json))
			return
		}
	}
}

```

