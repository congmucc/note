## 3 chainlink的使用

在实战3.1中，需要同故宫chainlink获取ETH和USD汇率转换需要使用Data Feed

[ETH / USD | Chainlink界面](https://data.chain.link/streams/arbitrum/mainnet/eth-usd) [Using Data Feeds 开发文档](https://docs.chain.link/data-feeds/using-data-feeds)

## 4.1 04发送一个合约交易

```solidity
// SPDX-License-Indentifitier: MIT
pragma solidity ^0.8.8;

contract FundMe {

    uint256 public minimumUsd = 50;

    function fund() public payable  {
        // number = 5; // 如果require出现error了，这段代码也会被回退
        
        require(msg.value >= minimumUsd, "Didn't send enough"); // 1e18 == 1 * 10 ** 18
    }

        function withdraw() public payable  {
        for (uint256 funderIndex=0; funderIndex < funders.length; funderIndex++){
            address funder = funders[funderIndex];
            addressToAmountFunded[funder] = 0;
        }
        funders = new address[](0);
        // 三种发送比特币的方式
        // transfer
        // payable(msg.sender).transfer(address(this).balance);
        // // send
        // bool sendSuccess = payable(msg.sender).send(address(this).balance);
        // require(sendSuccess, "Send failed");  //必须要有，防止不回退
        // call
        (bool callSuccess, ) = payable(msg.sender).call{value: address(this).balance}("");
        require(callSuccess, "Call failed");
    }
}
```

> 功能：
>
> 1、payable
>
> 2、msg.value
>
> > 这个意思是获取发送合约时的`value`
>
> 3、`require(A,B)`
>
> > 这是一个用于判断的函数，如果A == false 则控制台报错B
> >
> > 并且会回退前面的代码，如`number = 5`
>
> 3、发送其他合约
>
> [1.7 区块链发送参数](# 7 区块链发送参数)
>
> 4、网站：
>
> [Data feed的使用](# 3.1 chainlink的使用)
>
> 5、优化：
>
> [1.4.2 降低gas](# 4.2 降低gas)
>
> [2.1.7.1 receive & fallback](# 7.1 receive & fallback)

## 4.2 使用vscode编译合约

> 1. 使用yarn
> 2. 导入依赖solc编译
> 3. 使用ganache
> 4. ethers和fs-extra

1. **使用yarn**

   ```
   npm install -g yarn
   ```

2. **导入依赖solc编译**

   ````
   yarn add solc@0.8.8
   ````

   ```
   "scripts" : {
   	"compile": "yarn solcjs --bin --abi --include-path node_modules/ --base-path . -o . SimpleStorage.sol"
   }
   ```

   

   > 这是以太坊编译的一个文件 solc需要使用fixed（固定）版本
   >
   > 在package.json中添加命令，用于编译

3. **使用ganache**

   > 下载ganache，然后使用其中的虚拟RPC,虚拟钱包

4. **ethers和fs-extra**

   ```
   yarn add ethers
   yarn add fs-extra
   ```

   > ethers 是获取一个虚拟的钱包
   >
   > fs-extra是读取2.编译之后的文件

   ```
   const fs = require("fs-extra");
   
   
   
   async function main() {
       // http://127.0.0.1:7545
       const provider = new ethers.JsonRpcProvider("http://127.0.0.1:7545");
       const wallet = new ethers.Wallet(
           "0x77a3f487dc5af1fc7b590818cf21fc4161e1184928aa7250efed67184588d18f",
           provider
       )
       const abi = fs.readFileSync("./SimpleStorage_sol_SimpleStorage.abi", "utf8");
       const binary = fs.readFileSync(
           "./SimpleStorage_sol_SimpleStorage.bin",
           "utf8"
       );
   
       // deploy a contract
       const contractFactory = new ethers.ContractFactory(abi, binary, wallet);
       const contract = await contractFactory.deploy()
       console.log("contract: ", contract);
   
   }
   
   main()
       .then(() => process.exit(0))
       .catch((error) => {
           console.error(error);
           process.exit(1);
       })
   ```

   > 这是一个基本的合约

5. 

6. 

   