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

  `ssh-keygen -t rsa -C atguiguyueyue@aliyun.com`

  这个发起人是岳不群,看视频的时候要想明白谁是发起者,谁是组织外的人


# 7 实战

背景：正在写代码的时候需要修改线上的一个bug

操作：先暂存起来，然后切换分支进行拉去代码，具体操作为：

1. git stash （不要add）

2. git fetch origin
3. git pull origin main
4. 后面如果回来的话可以使用git stash apply

相关命令为：

`git stash`：

`git stash pop`

`git stash apply`

`git stash list`

`git stash drop`
