# Git 基础

##  一、初识 GitHub 

### 1.1 什么是 Git

​		Git 是一个免费、开源的**版本控制软件**

### 1.2 什么是版本控制系统

 		版本控制系统是一种**记录一个或若干个文件内容变化，以便将来查阅特定版本修改情况的系统。**

### 1.3 版本控制系统具体的功能

		1. 记录文件的所有历史变化
  		2. 随时可恢复到任何一个历史状态
    		3. 多人协作开发或修改项目
      		4. 错误可恢复

### 1.4 什么是 GitHub？

​		GitHub 是全球最大的**社交编程**及**代码托管网站**

​		GitHub 可以托管各种 git 库，并提供一个 web 界面（用户名.github.io/仓库名）

### 1.5 GitHub 与 Git 的区别

- GitHub 与 Git 是完全不同的两个东西
- 在 Git 中，开发者将源码存入 `Git 仓库`的资料库中并加以使用
- GitHub 是在网络上提供 Git 仓库的一项服务
- GitHub 上公开的软件源代码全都由 Git 进行管理

---

## 二、使用 GitHub

### 2.1 使用 GitHub 的目的

​		借助 GitHub 托管项目代码

### 2.2 GitHub 中的基本概念

> a. 仓库（Repository)
>
> > 仓库用来存放项目代码，**每个项目对应一个仓库**，多个开源项目则有多个仓库。

> b. 收藏（Star)
>
> > 收藏项目，可以方便下次查看。

> c. 复制克隆项目（fork)
>
> > 查看别人项目时，如果希望将该项目保存到自己的仓库中，可以克隆复制该项目，到我们的账户下。
> >
> > <img src="assets/fork 模拟.png" style="zoom:75%;" />

**注意：**该 fork 的项目时独立存在的。

> d. 发起请求（Pull Request）
>
> > 我复制了别人的库，**我自己做了修改，我的库改动了，但是被复制人的库并没有改动。**此时，可以选择 pull request ，向库的拥有者发出请求。如果作者同意，我门书写的代码则会**合并到原来仓库中。**
> >
> > <img src="assets/pull request.png" style="zoom:75%;" />

> e. 关注（Watch)
>
> > Watch 了某个项目，那么以后这个项目有任何更新，我门都会收到通知。

> f. 事物卡片（Issue）
>
> > 发现代码 BUG，但是目前没有成型代码，需要讨论时使用。

### 2.3 创建仓库/创建新项目

说明：

- 一个 Git 仓库对应一个开源项目
- 通过 git 管理 git 仓库

<img src="assets/Create a new repository.png" style="zoom:75%;" />

<img src="assets/The created repository.png" style="zoom:75%;" />

### 2.4 仓库管理

#### 2.4.1 新建文件

<img src="assets/New File.png" style="zoom:75%;" />

<img src="assets/New File 2.png" style="zoom:75%;" />

#### 2.4.2 编辑文件

<img src="assets/Edit file.png" style="zoom:75%;" />



#### 2.4.3 删除文件

<img src="assets/delete file.png" style="zoom:75%;" />

#### 2.4.4 GitHub Issues

> 作用：发现代码 BUG ，但是目前没有成型代码，需要讨论时使用；或者使用开源项目出现问题时使用。

​		情景：张三发现李四开源的 git库，则提交了一个 Issue;李四隔天登录 GitHub 主页看到通知，可以和张三交流，最后关闭 Issue。

<img src="assets/GitHub issues.png" style="zoom:75%;" />

<img src="assets/GitHub issues2.png" style="zoom:75%;" />

### 2.5 GitHub 基本概念的实践

#### 2.5.1 GitHub 主页

<img src="assets/GitHub mainpage.png" style="zoom:75%;" />

#### 2.5.2 个人主页

<img src="assets/Personnal mainpage.png" style="zoom:75%;" />

#### 2.5.3 收藏（Star)

<img src="assets/Star.png" style="zoom:75%;" />

#### 2.5.4 关注（Watch)

<img src="assets/Watch.png" style="zoom:75%;" />

#### 2.5.5 复制克隆项目（Fork）

<img src="assets/Test Fork and clone.png" style="zoom:75%;" />

#### 2.5.6 发起请求(Pull request)

<img src="assets/Test Pull request.png" style="zoom:75%;" />

### 2.6 开源项目贡献流程

- 新建 issue
  - 提交使用中发现的问题或建议或想法
- Pull request
  - fork 项目
  - 修改自己仓库的项目代码
  - 新建 pull request
  - 等待作者操作（合并）









## Git 基本概念

## Git 常用命令

### Initialization a Repository in an Existing Directory

> cd .../my_project
>
> git init

上述命令，创建一个名为`.git`的子目录

### Git 配置

> 使用 Git 的第一件事就是设置名字和 email, 这是提交 commit 时的`签名`.

`git config --global user.name "annjeff"`

`git config --global user.email "annjeff@163.com"`

执行上述命令后，主目录(home directory)建立一个叫`~/.gitconfig`的文件.内容一般如下：

[user]

​	name = annjeff

​	email =  annjeff@163.com



### Git 使用SSH 同步代码至 GitHub

**生成 SSH 密钥命令：**`ssh-keygen -t rsa -C 任意邮箱` 其中，`rsa`为加密算法

<img src="assets/Git 生成密钥对.png" style="zoom:61%;" />

<img src="assets/SSH 生成的私钥与公钥.png" style="zoom:61%;" />

到 GitHub

<img src="assets/GitHub 新建 SSH key.png" style="zoom:61%;" />



<img src="assets/GitHub 新建 SSH key 2.png" style="zoom:61%;" />



<img src="assets/成功生成 SSH key.png" style="zoom:61%;" />

> 此时 GitHub 已经知道我们的`公钥`是什么了，无需输入用户名和密码，可以直接和服务器安全通信。



和远程仓库建立连接：`git remote add origin git@github.com:annjeff/The-Note-of-Annjeff-for-Interview.git`

- **如果出现：fatal: remote origin already exists**

>  使用命令 git remote rm origin，再执行一次 git remote add origin 项目地址

将本地仓库推送到远程：`git push origin master`

- **git push 出现：rejected master -> master(non-fast-forward)**

  <img src="assets/Git push 出错.png" style="zoom:61%;" />

> 解决方案一：[推荐] 利用强制覆盖方式，用本地代码替代 git 仓库内容
>
> `git push -f origin master`

> 解决方案二：先把 git 的代码 fetch 到本地，然后 merge ，最后 push
>
> `git fetch`
>
> `git merge`



### 使用 `https:`方式将本地代码推送到远端

> 使用 https: 方式将本地代码推送到远端，需要验证`用户名`和`密码`



