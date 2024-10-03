[Installation - Foundry Book (getfoundry.sh)](https://book.getfoundry.sh/getting-started/installation)



# Environment Setting

**修改`.env`文件需要使用`source .env`进行更新一下**



```sh
forge fmt
```

> 格式化代码





# Command

## `forge`



```sh
forge --help
```



### **Contract deployment**

```sh
forge script script/Deploy.s.sol --rpc-url http://127.0.0.1:8545
```

> 使用自动化部署

```sh
forge script script/Deploy(名字需要更改).s.sol --rpc-url $RPC_URL --broadcast --private-key $PRIVATE_KEY
```

> 生成部署信息到文件

`forge create`

> Deploy a smart contract



### **Installation of dependencies**

```sh
forge install
```

> 安装依赖，fork一下依赖
>
> ```sh
> forge insatll smartcontranctkit/chainlink-brownie-contracts@0.7.1 --no-commit
> ```
>
> > Then modify the remappings attribute under
> >
> > ```rust
> > // /src/foundry.toml
> > 
> > 
> > remappings = ["@chainlink/contracts/=lib/chainlink-brownie-contracts/contracts/"]
> > ```
> > 
> >It redirects to this file directory.

> [7.3 继续进行设置_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV13a4y1F7V3/?p=84)





### **Contract Test**

```sh
forge test
```

> `--match-test function_name`: Run the corresponding function eg: `forge test --mt test_Increment`
>
> `-vv`: Print some logs and you can add `v` to print  extra
> eg: `forge test --match-test test_Increment -vvv` It will print 3 logs
>
> `--rpc-url`: add fork test

```sh
forge coverage --fork--url $URL
```

> It outputs test rates

[7.7 分叉（Fork）测试_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV13a4y1F7V3/?p=88)

> There are four tests in this course.



#### Cheatcodes

> [Cheatcodes - Foundry Book (getfoundry.sh)](https://book.getfoundry.sh/forge/cheatcodes)
>
> https://book.getfoundry.sh/cheatcodes/
>
> - `vm.expectRevert` : It will revert the next line of code.
> - `vm.prank`: It will set `msg.sender` to the specified address for next call. “The next call” includes static calls as well, but not calls to the cheat code address.  and use it with `makeAddr()` 
> - `makeAddr`: Creates an address derived from the provided `name`.
> - `deal`:

[7.13 了解更多的Cheatcodes_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV13a4y1F7V3/?p=94)

`vm.txGasPrice()`

> It sets the gas price of the transaction



#### Gas price of test

`forge snapshot`

> The command  will record the gas price of Individual tests and generate a `.gas-snapshot` file.
>
> eg: `forge snapshot --mt test_Increment`
> test_Increment is function name

`vm.txGasPrice`

> It's cheatcode



#### Store

`forge inspect`

> Check how the contract is stored, and it will tell you the contract layout of the contract.
>
> eg: `forge inspect Counter storageLayout`
>
> Couter is contract name







### Prompt code error

`chaisel`

> debugging test, prompt code error

```sh
> chaisel
> !help
> uint256 cat = 1;
> cat 
> unint256 catAndThree = cat + 3;
```











## `anvil`

搭建本地节点

```sh
anvil
```



New RPC URL：`http://0.0.0.0:8545`

Chain ID: `31337`(这是anvil的默认id)





## Other

```sh
cast --to-base 0x283c3 dec
```

> 将16进制转换为10进制，foundry内置了





# Gas Optimization



## Memory And Store

```solidity
    function testWithdrawFromMultipleFunders() public funded skipZkSync{
        uint160 numberOfFunders = s_funder.length;
        uint160 startingFunderIndex = 2;
        for (uint160 i = startingFunderIndex; i < numberOfFunders + startingFunderIndex; i++) {
        }
```

> **Type**: use memory variable instead of stored variables
>
> **Reason**: in 2 lines, we know we use memory variable `numberOfFunders` and used it in a for loop.
>
> ```solidity
>         for (uint160 i = startingFunderIndex; i < s_funder.length + startingFunderIndex; i++) {
>         }
> ```
>
> > Each for loop calls the `fundMe.fund` function. If it's called `loops` times, it costs `100 wei * loops`. However, if it uses the memory variable `numberOfFunders`, the cost is `3 wei * loops + 100 wei`.
