# 1 Solidity

## 1.1 基本语法

### 1.1.1 数据类型

1、**boolean**

2、**unit**

> 正数，跟c++的类似。这个比较特殊，当定义变量的时候可以使用`unit8`-`unit256`其中的数，单位是8（一个字节8比特），默认是256.
>
> `uint256 favoriteNumber = 5;`

3、**int**

> 跟unit的特殊相同，可以使用`int256`定义变量。
>
> `int256 favoriteNumber = -5;`

4、**address**

> 地址，跟metamask中的个人地址一样，类似id这种的唯一标识。
>
> `address myAddress = 0x1066618d189731Fe13897cC1;`

5、**bytes**

> 同uint特殊。
>
> `bytes32 favoriteBytes = "cat";`

6、**stirng**

>`string favoriteNumberIntext = "five";`

7、**struct**

> 结构体
>
> ```solidity
>     People public people = People({favoriteNumber: 2, name: "congmu"});
> 
>     struct People {
>         uint256 favoriteNumber;
>         string name;
>     }
> ```

8、**数组类型**

> ```solidity
>    People[] public people;
>     //添加数组元素
>     function addPeople(string memory _name, uint256 _favoriteNumber) public {
>         People memory newPeople = People({favoriteNumber: _favoriteNumber, name: _name});
>         people.push(newPeople);
>     }
> ```

9、**映射mapping**

> ```solidity
>     mapping(string => uint256) public nameToFavoriteNumber;
>         //添加数组元素
>     function addPeople(string memory _name, uint256 _favoriteNumber) public {
>         nameToFavoriteNumber[_name] = _favoriteNumber;
>     }
> ```

### 1.1.2 功能可见性说明符

- `public` ：在外部和内部可见（为存储/状态变量**创建 getter 函数**）
- `private` ：仅在当前合约中可见
- `external` ：仅在外部可见（仅对函数） - 即只能通过消息调用（通过 `this.func` ）
- `internal` ：仅在内部可见



### 1.1.3 修饰符(关键字)

- `pure` for functions：不允许修改或访问状态。
- `view` for functions：不允许修改状态。

- `payable` for functioins： 是一个关键字和修饰符，用于指示函数或合约可以接收以太币（Ether）或发送以太币。

> 1、**代码示例**
>
> ```solidity
>    function getFavoriteNumber() public view returns(uint256) {
>         return favoriteNumber;
>     } 
> ```
>
> 2、**注意**
>
> 1. `pure`和`view`标识的调用不需要消耗gas，因为只有更改状态的时候才支付gas。除非在消耗gas的函数中调用`view`和`pure`标识的函数才会使被标识的函数消耗gas。



### 1.1.4 可存储数据

1、**Stack**

> 不能被修改的临时变量

2、**Memory**

> 可以被修改的临时变量

3、**Storage**

> 可以被修改的永久变量

4、**Calldata**

5、**Code**

6、**Logs**

> **结构体**、**映射**和**数组**在作为参数被添加到不同函数时需要给定一个`memory`或`calldata`关键字



### 1.1.5 库Library

> 场景是将自己写的函数作为别的函数库
>
> 例如将ETH和USD转换封装成库，使用msg.value.getConversionRate()调用
>
> 步骤：
>
> 1、将**转换的函数**以及**相关导入**创建一个`library`封装起来，记得函数可见性改为`internal`
>
> 2、主合约导入库，并使用语句`using A for B`来进行调用

1、

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

import {AggregatorV3Interface} from "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";


library PriceConverter {
    
    function getPrice() internal view returns (uint256) {
        // ABI
        // Address  0x694AA1769357215DE4FAC081bf1f309aDC325306
        AggregatorV3Interface priceFeed = AggregatorV3Interface(0x694AA1769357215DE4FAC081bf1f309aDC325306);
        (,int256 price, , , ) = priceFeed.latestRoundData();
        // // ETH in terms of usd
        
        return uint256(uint256(price) * (10*(18 - uint(priceFeed.decimals()))));
    }

    function getVersion() internal view returns (uint256) {
        AggregatorV3Interface priceV = AggregatorV3Interface(0x694AA1769357215DE4FAC081bf1f309aDC325306);
        return priceV.version();
    }

    function getConversionRate(uint256 ethAmount) internal view returns (uint256) {
        uint256 ethPrice = getPrice();
        uint256 ethAmountInUsd = (ethPrice * ethAmount) / 1e18;
        return ethAmountInUsd;
    }
}
```

2、

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.8;

import "./PriceConverter.sol";

contract FundMe {

    using PriceConverter for uint256;

    uint256 public minimumUsd = 50 * 1e18;


    address[] public funders;

    mapping(address => uint256) public addressToAmountFunded;

    function fund() public payable  {
        // number = 5; // 如果require出现error了，这段代码也会被回退
        // require(getConversionRate(msg.value) >= minimumUsd, "Didn't send enough"); // 1e18 == 1 * 10 ** 18
        require(msg.value.getConversionRate() >= minimumUsd, "Didn't send enough"); // 1e18 == 1 * 10 ** 18
        funders.push(msg.sender);
        addressToAmountFunded[msg.sender] = msg.value;
    }


    // function withdraw() {}
}
```

> `using PriceConverter for uint256;` 语句将 `PriceConverter` 库中的功能引入了 `uint256` 类型，使得 `uint256` 类型的变量可以直接调用 `PriceConverter` 中定义的函数。



### 1.1.6 modifier 

```solidity

    function withdraw() public onlyOwner   {
        for (uint256 funderIndex=0; funderIndex < funders.length; funderIndex++){
            address funder = funders[funderIndex];
            addressToAmountFunded[funder] = 0;
        }
        funders = new address[](0);
        (bool callSuccess, ) = payable(msg.sender).call{value: address(this).balance}("");
        require(callSuccess, "Call failed");
    }

    modifier onlyOwner {
        // require(msg.sender == owner);
        if (msg.sender != i_owner) revert NotOwner();
        _; // 表示先运行使用该 modifier的函数，如果这段代码在if之上表示先运行函数后运行if
    }
```



### 1.1.7 函数相关

#### 1.1.7.1 receive & fallback

1、**receive**

> 发送交易的时候只要没有与该交易相关的数据，将被触发
>
> 当向合约发送以太币时，或者调用合约不存在的函数时，就会调用 `receive` 函数来处理这些以太币。

2、**fallback**

> 用于处理向合约发送以太币时没有匹配到任何其他函数调用的情况。
>
> 从 Solidity 0.6.0 版本开始，`fallback` 函数被标记为已弃用，不建议继续使用。

```solidity
    fallback() external payable {
        fund();
    }

    receive() external payable {
        fund();
    }
    
    // Explainer from: https://solidity-by-example.org/fallback/
    // Ether is sent to contract
    //      is msg.data empty?
    //          /   \ 
    //         yes  no
    //         /     \
    //    receive()?  fallback() 
    //     /   \ 
    //   yes   no
    //  /        \
    //receive()  fallback()

```





## 1.2 面向对象

> 合约的关键字是`contract`， 类似java的`calss`



### 1.2.1 封装

```solidity
// I'm a comment!
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.8;
// pragma solidity ^0.8.0;
// pragma solidity >=0.8.0 <0.9.0;

contract SimpleStorage {

    uint256 favoriteNumber;

    struct People {
        uint256 favoriteNumber;
        string name;
    }
    // uint256[] public anArray;
    People[] public people;

    mapping(string => uint256) public nameToFavoriteNumber;

    function store(uint256 _favoriteNumber) public {
        favoriteNumber = _favoriteNumber;
    }
    
    function retrieve() public view returns (uint256){
        return favoriteNumber;
    }

    function addPerson(string memory _name, uint256 _favoriteNumber) public {
        people.push(People(_favoriteNumber, _name));
        nameToFavoriteNumber[_name] = _favoriteNumber;
    }
}

```

> 就是正常的写函数



### 1.2.2 继承

#### 1.2.2.1 继承

```solidity
import "./SimpleStorage.sol";

contract ExtraStorage is SimpleStorage{

}
```

> 关键词是`is`，使用前需要导入

#### 1.2.2.2 重载

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.8;


contract SimpleStorage {

    uint256 favoriteNumber;
    function store(uint256 _favoriteNumber) public virtual {
        favoriteNumber = _favoriteNumber;
    }
}
```

> 父类合同  需要加`virtual`关键字



```solidity
pragma solidity ^0.8.7;

import "./SimpleStorage.sol";

contract ExtraStorage is SimpleStorage{
        function store(uint256 _favoriteNumber) public override {
            favoriteNumber = _favoriteNumber + 5;
        }
}
```

> 子类合同  需要加 `override`关键字





##  1.3 高级

### 1.3.1 chainlink的使用

在实战3.1中，需要同故宫chainlink获取ETH和USD汇率转换需要使用Data Feed

[ETH / USD | Chainlink界面](https://data.chain.link/streams/arbitrum/mainnet/eth-usd) [Using Data Feeds 开发文档](https://docs.chain.link/data-feeds/using-data-feeds)







## 1.4 实战

### 1.4.1 04发送一个合约交易

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
> [1.7 区块链发送参数](# 1.7 区块链发送参数)
>
> 4、网站：
>
> [Data feed的使用](# 1.3.1 chainlink的使用)
>
> 5、优化：
>
> [1.4.2 降低gas](# 1.4.2 降低gas)
>
> [2.1.7.1 receive & fallback](# 1.1.7.1 receive & fallback)

### 1.4.2 使用vscode编译合约

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

   

   
