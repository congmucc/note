[安装 - Foundry Book --- Installation - Foundry Book (getfoundry.sh)](https://book.getfoundry.sh/getting-started/installation)



```sh
forge --help
```







搭建本地节点

```sh
anvil
```



New RPC URL：`http://0.0.0.0:8545`

Chain ID: `31337`(这是anvil的默认id)



```sh
forge script script/Deploy.s.sol --rpc-url http://127.0.0.1:8545
```

> 使用自动化部署

```sh
forge script script/Deploy.s.sol --rpc-url http://127.0.0.1:8545 -boradcast --private-key 私钥
```

> 生成部署信息到文件





```sh
cast --to-base 0x283c3 dec
```

> 将16进制转换为10进制，foundry内置了
