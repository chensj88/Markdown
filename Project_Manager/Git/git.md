# Git常用设置:

## Git 全局设置:

```bash
git config --global user.name "chensj.dev"
git config --global user.email "chenshijie1988@yeah.net"

git config --global user.name "chensj"
git config --global user.email "chensj@winning.com.cn"
git config --global credential.helper store
# 文件名称超出问题
git config --global core.longpaths true
# unsafe repository
git config --global --add safe.directory "*"
```

## Git仓库关联

### 新的git 仓库:

```bash
mkdir Markdown
cd Markdown
git init
touch README.md
git add README.md
git commit -m "first commit"
git remote add origin https://gitee.com/chensj881008/Markdown.git
git push -u origin master
```

### 已有仓库?

```bash
cd existing_git_repo
git remote add origin https://gitee.com/chensj881008/Markdown.git
git push -u origin master
```

### 设置跟踪远程分支：

```bash
git pull origin master
git branch --set-upstream-to=origin/master master
```

### 修改远程仓库的关联

查看本地远程分支

```bash
git remote -v
```

删除本地与远程分支关联

```bash
git remote remove xxx	
```

修改关联的远程仓库的方法，主要有三种。

* 使用 git remote set-url 命令，更新远程仓库的 url

  ```bash
  git remote set-url origin <newurl>
  ```

* 先删除之前关联的远程仓库，再来添加新的远程仓库关联

  * 删除关联的远程仓库

    ```bash
    git remote remove <name>
    ```

  * 添加新的远程仓库关联

    ```bash
    git remote add <name> <url>
    ```

    远程仓库的名称推荐使用默认的名称 origin 。

* 直接修改项目目录下的 .git 目录中的 config 配置文件。

  1. 进入git_test/.git

  2. vim config 

     ```bash
     [core] 
     repositoryformatversion = 0 
     filemode = true 
     logallrefupdates = true 
     precomposeunicode = true 
     [remote "origin"] 
     url = http://192.168.100.235:9797/shimanqiang/assistant.git 
     fetch = +refs/heads/*:refs/remotes/origin/* 
     [branch "master"] 
     remote = origin 
     merge = refs/heads/master
     ```

     修改`[remote "origin"] `下面的url即可

## 删除远程文件

### 删除文件

* 对需要删除的文件、文件夹进行如下操作

  ```bash
  git rm test.txt (删除文件)
  git rm -r test (删除文件夹)
  ```

* 提交修改

  ```bash
  git commit -m "Delete some files."
  ```

* 将修改提交到远程仓库的xxx分支:

  ```bash
  git push origin xxx
  ```

### 删除远程仓库 但不删本地资源

```bash
git rm -r --cached xxx.iml　　//-r 是递归的意思   当最后面是文件夹的时候有用
git add xxx.iml    　        //若.gitignore文件中已经忽略了xxx.iml则可以不用执行此句
git commit -m "ignore xxx.xml"
git push
```

## 分支

### 常规操作

#### 查看

```bash
git branch
```

#### 创建

```bash
# 创建+切换分支
git checkout -b dev
# 等同于
# 创建分支
git branch dev
# 切换分支
git checkout dev
```

#### 删除

```bash
git branch -d dev
# 删除远程分支
git push origin --delete dev
```

#### 合并

合并某分支到当前分支

```bash
git merge <name>
```

### 拉取远程分支并创建本地分支

```bash
# 查看所有远程分支
git branch -r
```

#### 方法1

```bash
# 拉取远程分支并创建本地分支
git checkout -b 本地分支名x origin/远程分支名x
```

> 使用该方式会在本地新建分支x，并自动切换到该本地分支x。
>
> 采用此种方法建立的本地分支会和远程分支建立映射关系。

#### 方法2

```bash
git fetch origin 远程分支名x:本地分支名x
```

> 使用该方式会在本地新建分支x，但是不会自动切换到该本地分支x，需要手动checkout。
>
> 采用此种方法建立的本地分支不会和远程分支建立映射关系。

### 分支映射

建立本地分支与远程分支的映射关系（或者为跟踪关系track）。这样使用`git pull`或者`git push`时就不必每次都要指定从远程的哪个分支拉取合并和推送到远程的哪个分支了

#### 查看本地分支与远程分支的映射关系

使用以下命令（注意是双v）：

```bash
git branch -vv
```

#### 建立当前分支与远程分支的映射关系

```bash
git branch -u origin/addFile
git branch --set-upstream-to origin/addFile
```

#### 撤销本地分支与远程分支的映射关系

```bash
git branch --unset-upstream
```

## Git密码问题

### fatal:Authentication failed fot 'http://xxxx.com'

#### 1、修改全局配置**用户名** 和 **邮箱**；

查询用户信息：

```bash
git config --list

//查看一下你的信息修改的信息对不对
user.name=yuhua
user.email=xxx@xxx.com
```

如果不对就重新配置一下：

```bash
git config --global user.name [username]  如：git config --global user.name yh
git config --global user.email [email]         如：git config --global user.email 111@163.com
```

#### 2、修改凭证(具体问题具体分析)；

这个就坑了，这个是存在本机的，其实就跟浏览器保存密码道理一样。
 如图，进入【**凭据管理器**】：

![img](git.assets/11160165-3d824bcc2a445dca.webp)


 点击进入【**windows凭据**】

![img](git.assets/11160165-680335b50434933c.webp)



在【**普通凭据**】一栏，找到你项目的托管平台地址,点击最后的小图标，可以展开信息：

![img](git.assets/11160165-738364ba65d46e7a.webp)

#### 3.重置和保存

##### 3.1 重置

```bash
git config --system --unset credential.helper
```

##### 3.2 保存(避免每次都输入账号密码)

```bash
git config --global credential.helper store
```

### Git每次都需要输入用户名和密码

首先，如果我们git clone的下载代码的时候是连接的https://而不是git@git (ssh)的形式，当我们操作git pull/push到远程的时候，总是提示我们输入账号和密码才能操作成功，频繁的输入账号和密码会很麻烦，也特别烦恼。

解决办法：

git bash进入你的项目目录，输入：

```bash
git config --global credential.helper store
```

然后你会在你本地生成一个文本，上边记录你的账号和密码。当然这些你可以不用关心。

然后你使用上述的命令配置好之后，再操作一次git pull，然后它会提示你输入账号密码，这一次之后就不需要再次输入密码了。

## git删除本地分支重新更新

```
git checkout master
git branch -D wxpdev
git checkout -b wxpdev
git pull origin wxpdev
git branch --set-upstream-to origin/wxpdev
```

## submodule

### 添加

```shell
git submodule add <url> <path>
```

* url：替换为自己要引入的子模块仓库地址
* path：要存放的本地路径

执行添加命令成功后，可以在当前路径中看到一个.gitsubmodule文件，里面的内容就是我们刚刚add的内容

如果在添加子模块的时候想要指定分支，可以利用 -b 参数

```shell
git submodule add -b <branch> <url> <path>
```

#### 例子

##### 未指定分支

```shell
git submodule add https://github.com/tensorflow/benchmarks.git 3rdparty/benchmarks
```

`.gitsubmodule`内容

```shell
[submodule "3rdparty/benchmarks"]
	path = 3rdparty/benchmarks
	url = https://github.com/tensorflow/benchmarks.git
```

##### 指定分支

```shell
git submodule add -b cnn_tf_v1.10_compatible https://github.com/tensorflow/benchmarks.git 3rdparty/benchmarks
```

`.gitsubmodule`内容

```shell
[submodule "3rdparty/benchmarks"]
	path = 3rdparty/benchmarks
	url = https://github.com/tensorflow/benchmarks.git
	branch = cnn_tf_v1.10_compatible
```

### 使用

当我们add子模块之后，会发现文件夹下没有任何内容。这个时候我们需要再执行下面的指令添加源码。

```shell
git submodule update --init --recursive
```

这个命令是下面两条命令的合并版本

```shell
git submodule init
git submodule update
```

### 更新

我们引入了别人的仓库之后，如果该仓库作者进行了更新，我们需要手动进行更新。即进入子模块后，执行

```shell
git pull
```

进行更新。

### 删除

1. 删除子模块目录及源码

```shell
rm -rf 子模块目录
```

1. 删除.gitmodules中的对应子模块内容

```shell
vi .gitmodules
```

1. 删除.git/config配置中的对应子模块内容

```shell
vi .git/config
```

1. 删除.git/modules/下对应子模块目录

```shell
rm -rf .git/modules/子模块目录
```

1. 删除git索引中的对应子模块

```shell
git rm --cached 子模块目录
```
