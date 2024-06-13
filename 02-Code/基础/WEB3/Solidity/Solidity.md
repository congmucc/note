

[solidity学习-一些基础攻击_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1th4y1V7iG/)

# 0 介绍

[中文版 Solidity develop 文档 (solidity-cn.readthedocs.io)](https://solidity-cn.readthedocs.io/zh/develop/introduction-to-smart-contracts.html)



# 1 基本语法

## 1.1 数据类型

### 1.1.3 数据类型

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

> 地址
>
> 地址类型有两种基本相同的类型：
>
> - `address`: 保存一个20字节的值（一个以太坊地址的大小）。
> - `address payable`: 与 `address` 类型相同，但有额外的方法 `transfer` 和 `send`。
>
> 这种区别背后的想法是， `address payable` 是一个您可以发送以太币的地址， 而您不应该发送以太币给一个普通的 `address`，例如，因为它可能是一个智能合约， 而这个合约不是为接受以太币而建立的。
>
> 类型转换：
>
> 允许从 `address payable` 到 `address` 的隐式转换， 而从 `address` 到 `address payable` 的转换必须通过 `payable(<address>)` 来明确。
>
> `address myAddress = 0x1066618d189731Fe13897cC1;`

5、**bytes**

> 字节数组`bytes`分两种，一种定长（`byte`, `bytes8`, `bytes32`），另一种不定长。定长的属于数值类型，不定长的是引用类型。 定长`bytes`可以存一些数据，消耗`gas`比较少。
>
> 同uint特殊。
>
> `bytes32 public _byte32 = "MiniSolidity"; `
>
> `bytes1 public _byte = _byte32[0]; `
>
> `MiniSolidity`变量以字节的方式存储进变量`_byte32`，转换成16进制为：`0x4d696e69536f6c69646974790000000000000000000000000000000000000000`
> `_byte`变量存储`_byte32`的第一个字节，为`0x4d`

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

**10、枚举 enum**

> 枚举（`enum`）是`solidity`中用户定义的数据类型。它主要用于为`uint`分配名称，使程序易于阅读和维护。它与`C语言`中的`enum`类似，使用名称来代替从`0`开始的`uint`：
>
> ```solidity
>     // 用enum将uint 0， 1， 2表示为Buy, Hold, Sell
>     enum ActionSet { Buy, Hold, Sell }
>     // 创建enum变量 action
>     ActionSet action = ActionSet.Buy;
> ```
>
> 
>
> 它可以显式的和`uint`相互转换，并会检查转换的正整数是否在枚举的长度内，不然会报错：
>
> ```solidity
>     // enum可以和uint显式的转换
>     function enumToUint() external view returns(uint){
>         return uint(action);
>     }
> ```
>
> 
>
> `enum`是一个比较冷门的变量，几乎没什么人用。

### 1.1.2 Solidity中的引用类型

**引用类型(Reference Type)**：包括数组（`array`），结构体（`struct`）和映射（`mapping`），这类变量占空间大，赋值时候直接传递地址（类似指针）。由于这类变量比较复杂，占用存储空间大，我们在使用时必须要声明数据存储的位置。

## 1.2 数据存储的位置

### 1.2.1 存储位置

1、**Stack**

> 不能被修改的临时变量

2、**Memory**

> 可以被修改的临时变量，数据在函数调用结束后会被清除。

3、**Storage**

> 可以被修改的永久变量，数据在合约的整个生命周期内保持不变，除非显式修改。状态变量默认存储在 `storage` 中。
>
> 如`uint public value;`

4、**Calldata**

> 不能被修改的临时数据。
>
> **作用**: 函数参数的只读存储位置，特别是对于 `external` 函数。
>
> **用途**: 传递函数调用中的参数数据，特别适用于传递较大的数组或字符串。

5、**Code**

6、**Logs**

> **结构体**、**映射**和**数组**在作为参数被添加到不同函数时需要给定一个`memory`或`calldata`关键字



solidity数据存储位置有三类：`storage`，`memory`和`calldata`。不同存储位置的`gas`成本不同。`storage`类型的数据存在链上，类似计算机的硬盘，消耗`gas`多；`memory`和`calldata`类型的临时存在内存里，消耗`gas`少。大致用法：

1. `storage`：合约里的状态变量默认都是`storage`，存储在链上。
2. `memory`：函数里的参数和临时变量一般用`memory`，存储在内存中，不上链。
3. `calldata`：和`memory`类似，存储在内存中，不上链。与`memory`的不同点在于`calldata`变量不能修改（`immutable`），一般用于函数的参数。例子：

```solidity
    function fCalldata(uint[] calldata _x) public pure returns(uint[] calldata){
        //参数为calldata数组，不能被修改
        // _x[0] = 0 //这样修改会报错
        return(_x);
    }
```





### 1.2.2 赋值规则

1. `storage`（合约的状态变量）赋值给本地`storage`（函数里的）时候，会创建引用，改变新变量会影响原变量。例子：

   ```solidity
       uint[] x = [1,2,3]; // 状态变量：数组 x
   
       function fStorage() public{
           //声明一个storage的变量 xStorage，指向x。修改xStorage也会影响x
           uint[] storage xStorage = x;
           xStorage[0] = 100;
       }
   ```

2. `storage`赋值给`memory`，会创建独立的副本，修改其中一个不会影响另一个；反之亦然。例子：

   ```solidity
       uint[] x = [1,2,3]; // 状态变量：数组 x
       
       function fMemory() public view{
           //声明一个Memory的变量xMemory，复制x。修改xMemory不会影响x
           uint[] memory xMemory = x;
           xMemory[0] = 100;
           xMemory[1] = 200;
           uint[] memory xMemory2 = x;
           xMemory2[0] = 300;
       }
   ```

3. `memory`赋值给`memory`，会创建引用，改变新变量会影响原变量。
4. 其他情况，变量赋值给`storage`，会创建独立的副本，修改其中一个不会影响另一个。



## 1.5 库Library

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



## 1.6 modifier 

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



# 2 高阶

## 2.1 函数基础

基于这个例子进行讲解：

```solidity
function <function name>(<parameter types>) {internal|external|public|private} [pure|view|payable] [returns (<return types>)]
```

```solidity
function test() public pure returns (unint256) {

}
```



1. `function`：声明函数时的固定用法，想写函数，就要以function关键字开头。
2. `<function name>`：函数名。
3. `(<parameter types>)`：圆括号里写函数的参数，也就是要输入到函数的变量类型和名字。
4. `[returns ()]`：函数返回的变量类型和名称。

### 2.1.1 可见性

`{internal|external|public|private}`：函数可见性说明符，一共4种。没标明函数类型的，默认`public`。合约之外的函数，即"自由函数"，始终具有隐含`internal`可见性。

- `public` ：在外部和内部可见（为存储/状态变量**创建 getter 函数**）
- `private` ：仅在当前合约中可见
- `external` ：仅在外部可见（仅对函数） - 即只能通过消息调用（通过 `this.func` ）
- `internal` ：仅在内部可见

**Note 1**: 没有标明可见性类型的函数，默认为`public`。

**Note 2**: `public|private|internal` 也可用于修饰状态变量。 `public`变量会自动生成同名的`getter`函数，用于查询数值。

**Note 3**: 没有标明可见性类型的状态变量，默认为`internal`。



### 2.1.2 修饰符（关键字）

- `pure` for functions：不允许修改或访问链上状态。
- `view` for functions：能看但不允许修改状态。

- `payable` for functioins： 是一个关键字和修饰符，用于指示函数或合约可以接收以太币（Ether）或发送以太币。

> 1、**代码示例**
>
> ```solidity
> function getFavoriteNumber() public view returns(uint256) {
>      return favoriteNumber;
>  } 
> ```
>
> 2、**注意**
>
> 1. `pure`和`view`标识的调用不需要消耗gas，因为只有更改状态的时候才支付gas。除非在消耗gas的函数中调用`view`和`pure`标识的函数才会使被标识的函数消耗gas。



### 2.1.3 返回值

`Solidity`有两个关键字与函数输出相关：`return`和`returns`，他们的区别在于：

- `returns`加在函数名后面，用于声明返回的变量类型及变量名；
- `return`用于函数主体中，返回指定的变量。



#### 2.1.3.1 命名式返回

我们可以在`returns`中标明返回变量的名称，这样`solidity`会自动给这些变量初始化，并且自动返回这些函数的值，不需要加`return`。

```solidity
    // 命名式返回
    function returnNamed() public pure returns(uint256 _number, bool _bool, uint256[3] memory _array){
        _number = 2;
        _bool = false; 
        _array = [uint256(3),2,1];
    }
```

#### 2.1.3.2 解构式赋值

`solidity`使用解构式赋值的规则，支持读取函数的全部或部分返回值。

- 读取所有返回值：声明变量，并且将要赋值的变量用`,`隔开，按顺序排列。

```solidity
        uint256 _number;
        bool _bool;
        uint256[3] memory _array;
        (_number, _bool, _array) = returnNamed();
```

> 这里，`returnNamed()`这里是上面的函数，就是获取函数返回值。

- 读取部分返回值：声明要读取的返回值对应的变量，不读取的留空。下面这段代码中，我们只读取`_bool`，而不读取返回的`_number`和`_array`：

```solidity
        (, _bool2, ) = returnNamed();
```







## 2.2 receive & fallback

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







# 3 面向对象

> 合约的关键字是`contract`， 类似java的`calss`



## 3.1 封装

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



## 3.2 继承

### 3.2.1 继承

```solidity
import "./SimpleStorage.sol";

contract ExtraStorage is SimpleStorage{

}
```

> 关键词是`is`，使用前需要导入

### 3.2.2 重载

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







# 3.1 chainlink的使用

在实战3.1中，需要同故宫chainlink获取ETH和USD汇率转换需要使用Data Feed

[ETH / USD | Chainlink界面](https://data.chain.link/streams/arbitrum/mainnet/eth-usd) [Using Data Feeds 开发文档](https://docs.chain.link/data-feeds/using-data-feeds)







# 4 实战

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

   

   
# 5 编译
1. **ABI（Application Binary Interface）**：ABI 描述了合约的外部接口，包括合约的函数、事件等信息。它定义了合约如何与其他合约或者外部调用者进行交互。ABI 在 JSON 文件中以键值对的形式表示，包含了**函数的名称**、**参数类型**、**返回值类型**等信息。
   
2. **Data（Bytecode）**：这部分数据是合约的字节码（bytecode），它是合约编译后的二进制代码。字节码包含了合约的**实际执行代码**，包括**函数实现**、**变量初始化**等。部署合约时，需要将字节码发送到以太坊网络上，以供网络执行合约部署操作。
   
3. **Deploy**：这个部分包含了合约的部署相关信息，例如合约的**构造函数参数**、**合约作者**等。这些信息在部署合约时可能会用到，以确保合约能够正确部署到以太坊网络上。