[TOC]

### 1、nvm 是什么

（1）nvm(Node.js version manager) 是一个命令行应用，可以协助您快速地 更新、安装、使用、卸载 本机的全局 node.js 版本。
（2）有时候，我们可能同时在进行多个项目开发，而多个项目所使用的node版本又是不一样的，或者是要用最新的node版本进行试验和学习。这种情况下，对于维护多个版本的node将会是一件非常麻烦的事情，而nvm就是为解决这个问题而产生的，他可以在同一台电脑上进行多个node版本之间的切换，而这正是nvm的价值所在。

### 2、安装 nvm-windows

nvm下载地址：https://github.com/coreybutler/nvm-windows/releases 点击最新版本的 nvm-setup.zip 下载到本地并安装
**安装步骤：以windows10系统为例**
**注意：nvm的安装目录不能有汉字和空格，否则会报错**
**注意：电脑之前安装过nodejs的，不需要卸载，nvm在安装的过程中会提示，是否把电脑之前安装过的nodejs交给nvm来管理，点击【是】就可以了**

**(1)、双击安装文件 nvm-setup.exe**

![img](https://pic4.zhimg.com/v2-a81d1c3c64fd195b95d5a09c4ba2281f_r.jpg)



**(2)、选择nvm安装路径**

![img](https://pic1.zhimg.com/v2-fb1df8c9af1024b2efce67fdced95f10_r.jpg)



**(3)、选择nodejs安装路径**

![img](https://pic1.zhimg.com/v2-28c51b529acdff678f3d4df06837f7ec_r.jpg)



**(4)、确认安装即可**

![img](https://pic1.zhimg.com/v2-40ac35615de8c0762c93c5a52d6165dc_r.jpg)



**(5)、安装完确认** 打开CMD，输入命令 nvm ，安装成功则如下显示，可以看到里面列出了各种命令。

![img](https://pic3.zhimg.com/v2-70cb42dbcc7ffea190412851fad67bca_r.jpg)



**(6)、修改settings.txt** 在你安装的nvm目录下找到settings.txt文件，打开settings.txt文件后，加上下面两行代码：

```
node_mirror: https://npmmirror.com/mirrors/node/
npm_mirror: https://npmmirror.com/mirrors/npm/
```



目的是将npm镜像改为淘宝的镜像，可以提高下载速度

![img](https://pic3.zhimg.com/v2-442296568d4b58bc76c09966c422508e_r.jpg)



![img](https://pic3.zhimg.com/v2-1169921918faf5be7de748134801f1be_r.jpg)

### 3、使用 nvm 管理版本（nvm常用命令）

> **nvm install latest** 安装最新版本node.js
> **nvm use 版本号** 使用某一具体版本，例如 ：nvm use 14.3.0
> **nvm list** 列出当前已安装的所有版本
> **nvm ls** 列出当前已安装的所有版本
> **nvm uninstall 版本号** 卸载某一具体版本，例如：nvm use 14.3.0
> **nvm ls-remote** Mac版本中,列出全部可以安装的node版本
> **nvm ls available** windows版本,列出全部可以安装的node版本
> **nvm current** 显示当前的版本
> **nvm alias** 给不同的版本号添加别名
> **nvm unalias** 删除已定义的别名
> **nvm reinstall-packages** 在当前版本node环境下，重新全局安装指定版本号的npm包

**注意：windows10的系统，nvm安装成功后，会自动的把对应的环境变量添加到系统上**
**注意：安装完成后，在CMD中运行 nvm， 提示 【nvm不是内部或外部命令，也不是可运行的程序或批处理文件。】就是没有配置对应的环境变量**

**环境变量的配置方法可以看下面的图片：(用户环境变量、系统环境变量都要配置)**

> 环境变量位置：打开桌面此电脑图标-->鼠标右键-->属性-->页面左侧点击 高级系统设置-->弹出框内右下角点击 环境变量

**用户环境变量**

![img](https://pic1.zhimg.com/v2-4ceee24835341f5587c45a84ed3568a8_r.jpg)



**系统环境变量**

![img](https://pic1.zhimg.com/v2-c14d3d803158bef1954e0d03a86facfc_r.jpg)

![img](https://pic2.zhimg.com/v2-7b7d27af444f78e32cf7c9bb7ce3c011_r.jpg)



### 4、其他更多nvm命令

```text
nvm arch [32|64]： 显示node是运行在32位还是64位模式。指定32或64来覆盖默认体系结构。
-nvm install [arch]：该可以是node.js版本或最新稳定版本latest。（可选[arch]）指定安装32位或64位版本（默认为系统arch）。设置[arch]为all以安装32和64位版本。在命令后面添加–insecure，可以绕过远端下载服务器的SSL验证。
nvm list [available]：列出已经安装的node.js版本。可选的available，显示可下载版本的部分列表。这个命令可以简写为nvm ls [available]。
nvm on： 启用node.js版本管理。
nvm off： 禁用node.js版本管理(不卸载任何东西)
nvm proxy [url]： 设置用于下载的代理。留[url]空白，以查看当前的代理。设置[url]为none删除代理。
nvm node_mirror [url]：设置node镜像，默认为https://nodejs.org/dist/.。可以设置为淘宝的镜像https://npm.taobao.org/mirrors/node/
nvm npm_mirror [url]：设置npm镜像，默认为https://github.com/npm/npm/archive/。可以设置为淘宝的镜像https://npm.taobao.org/mirrors/npm/
nvm uninstall ： 卸载指定版本的nodejs。
nvm use [version] [arch]： 切换到使用指定的nodejs版本。可以指定32/64位[arch]。
-nvm use ：将继续使用所选版本，但根据提供的值切换到32/64位模式
nvm root [path]： 设置 nvm 存储node.js不同版本的目录 ,如果未设置，将使用当前目录。
-nvm version： 显示当前运行的nvm版本，可以简写为nvm v
```

# 配置淘宝镜像



## nvm中：

由于nvm默认的下载地址[http://nodejs.org/dist/](https://link.jianshu.com?t=http%3A%2F%2Fnodejs.org%2Fdist%2F)是外国外服务器，国内很慢可以使用淘宝的镜像

```
# 配置node镜像：
node_mirror: https://npmmirror.com/mirrors/node/

# 配置npm镜像：
npm_mirror: https://npmmirror.com/mirrors/npm/
```



打nvm的安装路径把上面的镜像地址复制到settings.txt中就OK了。



### npm切换淘宝源镜像

```
npm config set registry https://registry.npmmirror.com
```

### npm切换官方源镜像

```
npm config set registry https://registry.npmjs.org/
```

### npm查看当前源镜像地址

```
npm config get registry
```





## npm中：

### 一、通过命令配置

#### 1、设置淘宝镜像源

```
npm config set registry http://registry.npmmirror.com
```



#### 2、设置官方镜像源

```
npm config set registry https://registry.npmjs.org
```



#### 3、查看镜像使用状态：

```
npm config get registry
```

如果返回https://registry.npm.taobao.org/，说明配置的是淘宝镜像。

如果返回https://registry.npmjs.org/，说明配置的是淘宝镜像。

### 二、通过使用cnpm安装

#### 1、安装cnpm

```
 npm install -g cnpm --registry=https://registry.npm.taobao.org

 解决安装卡顿或无法安装：

 # 注册模块镜像

 npm set registry https://registry.npm.taobao.org  
  // node-gyp 编译依赖的 node 源码镜像  
 npm set disturl https://npm.taobao.org/dist 
 // 清空缓存  
 npm cache clean --force  
 // 安装cnpm  
 npm install -g cnpm --registry=https://registry.npm.taobao.org  
```



#### 2、使用cnpm

```
cnpm install xxx
```

