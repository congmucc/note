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

> 安装依赖，fork一下依赖，地址为：`https://github.com/smartcontractkit/chainlink-brownie-contracts`,只需要后面的即可，会自动补充，`@`选版本
>
> ```sh
> forge install smartcontractkit/chainlink-brownie-contracts@0.7.1 --no-commit
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
>
> 1. fork tests：[7.7 分叉（Fork）测试_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV13a4y1F7V3/?p=88)
> 2. integration tests：[7.19 集成测试Interactions](https://www.bilibili.com/video/BV13a4y1F7V3/?p=100)
> 3. 



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



#### Get gas price of test

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



**Foundry-devops**

>  Find your Address of the most recently deployed contract
>
> ```rust
> // foundry.toml
> 
> ffi = true
> 
> ```
>
> You can use the `Foundry-devops` command in your terminal, but you must set `ffi = true` in the `foundry.toml` file.





### make

makefile：

```makefile
-include .env
# Automatically scan the .env file for changes and apply environment variables upon modification, without needing to manually run source .env.

.PHONY: all test clean deploy fund help install snapshot format anvil zktest

DEFAULT_ANVIL_KEY := 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
DEFAULT_ZKSYNC_LOCAL_KEY := 0x7726827caac94a7f9e1b160f7ea819f172f7b6f9d2a97f992c38edeab82d4110

all: clean remove install update build

# Clean the repo
clean  :; forge clean

# Remove modules
remove :; rm -rf .gitmodules && rm -rf .git/modules/* && rm -rf lib && touch .gitmodules && git add . && git commit -m "modules"

install :; forge install cyfrin/foundry-devops@0.2.2 --no-commit && forge install smartcontractkit/chainlink-brownie-contracts@1.1.1 --no-commit && forge install foundry-rs/forge-std@v1.8.2 --no-commit

# Update Dependencies
update:; forge update

build:; forge build

zkbuild :; forge build --zksync

test :; forge test

zktest :; foundryup-zksync && forge test --zksync && foundryup

snapshot :; forge snapshot

format :; forge fmt

anvil :; anvil -m 'test test test test test test test test test test test junk' --steps-tracing --block-time 1

zk-anvil :; npx zksync-cli dev start

deploy:
	@forge script script/DeployFundMe.s.sol:DeployFundMe $(NETWORK_ARGS)

NETWORK_ARGS := --rpc-url http://localhost:8545 --private-key $(DEFAULT_ANVIL_KEY) --broadcast

ifeq ($(findstring --network sepolia,$(ARGS)),--network sepolia)
	NETWORK_ARGS := --rpc-url $(SEPOLIA_RPC_URL) --account $(ACCOUNT) --broadcast --verify --etherscan-api-key $(ETHERSCAN_API_KEY) -vvvv
endif

deploy-sepolia:
	@forge script script/DeployFundMe.s.sol:DeployFundMe $(NETWORK_ARGS)

# As of writing, the Alchemy zkSync RPC URL is not working correctly 
deploy-zk:
	forge create src/FundMe.sol:FundMe --rpc-url http://127.0.0.1:8011 --private-key $(DEFAULT_ZKSYNC_LOCAL_KEY) --constructor-args $(shell forge create test/mock/MockV3Aggregator.sol:MockV3Aggregator --rpc-url http://127.0.0.1:8011 --private-key $(DEFAULT_ZKSYNC_LOCAL_KEY) --constructor-args 8 200000000000 --legacy --zksync | grep "Deployed to:" | awk '{print $$3}') --legacy --zksync

deploy-zk-sepolia:
	forge create src/FundMe.sol:FundMe --rpc-url ${ZKSYNC_SEPOLIA_RPC_URL} --account default --constructor-args 0xfEefF7c3fB57d18C5C6Cdd71e45D2D0b4F9377bF --legacy --zksync


# For deploying Interactions.s.sol:FundFundMe as well as for Interactions.s.sol:WithdrawFundMe we have to include a sender's address `--sender <ADDRESS>`
SENDER_ADDRESS := <sender's address>
 
fund:
	@forge script script/Interactions.s.sol:FundFundMe --sender $(SENDER_ADDRESS) $(NETWORK_ARGS)

withdraw:
	@forge script script/Interactions.s.sol:WithdrawFundMe --sender $(SENDER_ADDRESS) $(NETWORK_ARGS)

```



## CEI: Checks, Effects, Interactions

```solidity
    function fulfillRandomWords(uint256 _requestId, uint256[] calldata _randomWords) internal override {
    	//Checks
        if (!s_requests[_requestId].exists) {
            revert("request not found");
        }
        // Effects
        uint256 indexOfWinner = _randomWords[0] % s_players.length;
        address payable winner = s_players[indexOfWinner];
        s_recentWinner = winner;

        s_players = new address payable[](0);
        s_lastTimeStamp = block.timestamp;
        // This line of code should be here, following the rules
        emit WinnerPicked(winner);
        
        // Interactions
        (bool success,) = winner.call{value: address(this).balance}("");
        if (!success) {
            revert Raffle_TransferFailed();
        }
    }
```







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
> >
> > [7.18 取款（withdraw）功能的gas优化（2）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV13a4y1F7V3/?p=99)



