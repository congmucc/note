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



**Contract deployment**

```sh
forge script script/Deploy.s.sol --rpc-url http://127.0.0.1:8545
```

> 使用自动化部署

```sh
forge script script/Deploy(名字需要更改).s.sol --rpc-url $RPC_URL --broadcast --private-key $PRIVATE_KEY
```

> 生成部署信息到文件



**Installation of dependencies**

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
> > 
> > ```
> >
> > It redirects to this file directory.

> [7.3 继续进行设置_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV13a4y1F7V3/?p=84)





**Contract Test**

```sh
forge test
```

> `--match-test function_name`: Run the corresponding function eg: `forge test --match-test test_Increment`
>
> `-vv`: Print some logs and you can add `v` to print  extra
> eg: `forge test --match-test test_Increment -vvv` It will print 3 logs
>
> `--fork-url`: add fork test

[7.7 分叉（Fork）测试_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV13a4y1F7V3/?p=88)

> This course 

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

