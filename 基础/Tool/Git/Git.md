### 1. Git 概述:

 Git 是一个免费的、开源的分布式版本控制系统，可以快速高效地处理从小型到大型的各种 项目。 Git 易于学习，占地面积小，性能极快。 它具有廉价的本地库，方便的暂存区域和多个工作 流分支等特性。其性能优于 Subversion、CVS、Perforce 和 ClearCase 等版本控制工具。

- 从安装开始：

[Git 详细安装教程（详解 Git 安装过程的每一个步骤）\_git 安装\_mukes 的博客-CSDN 博客](https://blog.csdn.net/mukes/article/details/115693833)

- 一般的：

[如何通过 git 提交代码到远程仓库（github） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/152332683)

- [【Git】---工作区、暂存区、版本库、远程仓库 - hanease - 博客园 (cnblogs.com)](https://www.cnblogs.com/hanease/p/15920205.html)
- [删除 commit 的三种方法_删除 commit-CSDN 博客](https://blog.csdn.net/qq_34977392/article/details/110817621#:~:text=删除 commit 的三种方法 1 1. git rm ,2 2. git commit -m "" %2F%2F 再次提交到仓库)

- [公司使用 Gitlab 管理项目实践指南 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/80115683)

- [超详细图解说明：一个代码仓库如何管理多个项目、且代码提交互不影响。orphan 分支的使用\_一个 git 仓库管理多个项目-CSDN 博客](https://blog.csdn.net/weixin_43304253/article/details/132031248)

### 2. Git 概念

**Workspace**： 工作区，就是你平时存放项目代码的地方

**Index / Stage**： 暂存区，用于临时存放你的改动，事实上它只是一个文件，保存即将提交到文件列表信息

**Repository**： 仓库区（或版本库），就是安全存放数据的位置，这里面有你提交到所有版本的数据。其中 HEAD 指向最新放入仓库的版本

 **Remote**： 远程仓库，托管代码的服务器，可以简单的认为是你项目组中的一台电脑用于远程数据交换

### 3. Git 的常用命令:

- 常用命令

[git - the simple guide - no deep shit! (rogerdudler.github.io)](https://rogerdudler.github.io/git-guide/index.zh.html)

1. `git config --global user.name 用户名` 设置用户签名

```
git config --global user.name "main"
```

1. `git config --global user.email 邮箱` 设置用户签名

```
git config --global user.email "2562907972@qq.com"
```

1. `git init` 初始化本地库
2. `git status` 查看本地库状态
3. `git add 文件名` 添加暂存区 `git rm --cached 文件名` 删除暂存区
4. `git commit -m "日志信息" 文件名` 提交本地库
5. `D` 查看日志信息 `git log` 查看详细日志信息
6. `git reset --hard 版本号` 版本穿梭

### linux:

1. `ll` 查看命令
2. `ll -a` 查看隐藏文件夹
3. `yy` 复制
4. `p` 粘贴
5. `vim ` 创建文件
6. `i` 进入编辑状态
7. `esc` 退出
8. `shift : wq` 保存文件
9. `cat 文件名` 查看文件详细内容

---

### 4. Git 分支操作:

1. `git branch -v` 查看分支
2. `git branch 分支名` 创建分支
3. `git checkout 分支名` 切换分支
4. `git merge 分支名` 合并分支(把分支名合并到当前分支)

### 5. Git 团队协作机制:

### 6. 远程库操作:

- 创建远程库

  1. `git remote -v` 查看当前所有远程地址别名
  2. `git remote add 别名 远程地址` 将 github 文件存入到本地

- 推送本地分支到远程仓库

  1. `git push 别名 分支` 推送本地分支到远程仓库

  2. `git push -f 别名 分支` 强制推送

  3. `git push -u 别名 分支`

     ```
     -u 参数相当于是让你本地的仓库和远程仓库进行了关联。

     git push -u origin --all

     这代表是将本地已存在的git项目的所有分支推送到远程仓库名为origin的仓库。
     ```

  4. `git pull 别名 分支` 拉取远程库到本地分支

- 克隆远程仓库到本地

  1. `git clone 远程地址` 克隆远程仓库到本地

     克隆会做的三件事

     1. 拉取代码
     2. 初始化本地库
     3. 创建别名(一般为 origin)

- SSH 免密登录

  `ssh-keygen -t rsa -C congmu@aliyun.com`

- `git fetch` 是一个Git命令，用于从远程仓库中下载最新的数据到本地仓库，但不自动合并到当前工作分支。这个命令使得你能查看远程仓库的最新状态，包括新的分支和标签，而无需直接修改你的工作目录或暂存区的内容。



### 7 LF & CRLF

在Windows平台上，git默认的core.autocrlf是true，可以通过`git config --list`命令查看。

Git可以在你提交时自动地把行结束符CRLF转换成LF，而在签出代码时把LF转换成CRLF。用`core.autocrlf`来打开此项功能， 如果是在Windows系统上，把它设置成`true`（默认配置），这样当签出代码时，LF会被转换成CRLF：

```csharp
git config --global core.autocrlf true
```

Linux或Mac系统使用LF作为行结束符，因此你不想Git在签出文件时进行自动的转换；当一个以CRLF为行结束符的文件不小心被引入时你肯定想进行修正， 把`core.autocrlf`设置成input来告诉Git在提交时把CRLF转换成LF，签出时不转换：

```verilog
git config --global core.autocrlf input
```

这样会在Windows系统上的签出文件中保留CRLF，会在Mac和Linux系统上，包括仓库中保留LF。

如果你是Windows程序员，且正在开发仅运行在Windows上的项目，可以设置`false`取消此功能，把回车符记录在库中：

```csharp
git config --global core.autocrlf false
```



# 7 实战

## git stash

背景：正在写代码的时候需要修改线上的一个bug

操作：先暂存起来，然后切换分支进行拉去代码，具体操作为：

1. git stash （不要add）

2. git fetch origin
3. git pull origin main
4. 后面如果回来的话可以使用git stash apply

相关命令为：

`git stash`：会把所有未提交的修改（包括暂存的和非暂存的）都保存起来，用于后续恢复当前工作目录。

`git stash pop [名字]`：命令恢复之前缓存的第一个工作目录，这个指令将缓存堆栈中的第一个stash删除。名字是第几个，默认第一个。

`git stash apply [名字]`：和pop一样，只不过这个不会删除

`git stash list`：查看所有的stash

`git stash drop [名字]`：删除指定的stash工作目录





## git cherry-pick



```bash
git cherry-pick <commitHash>
```

上面命令就会将指定的提交`commitHash`，应用于当前分支。这会在当前分支产生一个新的提交，当然它们的哈希值会不一样。

举例来说，代码仓库有`master`和`feature`两个分支。

> ```bash
>     a - b - c - d   Master
>          \
>            e - f - g Feature
> ```

现在将提交`f`应用到`master`分支。

> ```bash
> # 切换到 master 分支
> $ git checkout master
> 
> # Cherry pick 操作
> $ git cherry-pick f
> ```

上面的操作完成以后，代码库就变成了下面的样子。

> ```bash
>     a - b - c - d - f   Master
>          \
>            e - f - g Feature
> ```

从上面可以看到，`master`分支的末尾增加了一个提交`f`。

`git cherry-pick`命令的参数，不一定是提交的哈希值，分支名也是可以的，表示转移该分支的最新提交。

> ```bash
> $ git cherry-pick feature
> ```

上面代码表示将`feature`分支的最近一次提交，转移到当前分支。
