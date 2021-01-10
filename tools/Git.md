## Git

Git是一个开源的分布式版本控制系统，能敏捷高效地处理任何或小或大的项目。

### 概述

#### 为什么学习git

Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。Git 与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持。

#### Git安装

**下载windows git**

在使用Git前我们需要先安装 Git。Git 目前支持 Linux/Unix、Solaris、Mac和 Windows 平台上运行。

Git 各平台安装包下载地址为：http://git-scm.com/downloads

**配置用户信息**
```
$ git config --global user.name yourname
$ git config --global user.email your email
```
**config 配置指令**
`$ git config`
>config 配置有system级别、global（用户级别）、和local（当前仓库）三个，设置先从system->global->local，底层配置会覆盖顶层配置，分别使用--system/global/local，可以定位到配置文件。

查看系统config：`$ git config --system --list`
查看当前用户（global）配置：`$ git config --global  --list`
查看当前仓库配置信息：`$ git config --local  --list`

### Git仓库创建及工作流

本地仓库由 git 维护的三棵"树"组成。第一个是你的 工作目录，它持有实际文件；第二个是 暂存区（Index），它像个缓存区域，临时保存你的改动；最后是 HEAD，它指向你最后一次提交的结果。

![工作区、版本库中的暂存区和版本库之间的关系](http://www.runoob.com/wp-content/uploads/2015/02/1352126739_7909.jpg)

**初始化版本库**
`$ git init`

**添加文件到暂存区**
`$ git add`

**查看仓库状态**
`$ git status`

**回滚至上一个状态(返回至工作区)**
`$ git resrt HEAD`

**迁出分支**
`$ git checkout -- 文件名`

**添加文件到版本库**
```
$ git commit
$ git commit -m '描述'
```

**回滚**
```
$ git log
$ git log --pretty=oneline 单行显示
复制commit号码
$ git reset --hard commit号
```


**删除分支**
```
$ git rm 文件名
$ git commit
```

### Git的主要功能

#### 远程仓库(此处以github为例)

**创建SSH KEY**
`$ ssh-keygen -t rsa -C "your email"`

**判断是否与github连通**
`$ ssh -T git@github.com`

**在github上创建一个新的仓库**
```
$ echo "# test" >> README.md
$ git init
$ git add README.md
$ git commit -m "first commit"
$ git remote add origin https://github.com/iamliuyu/test.git
$ git push -u origin master
```

**第二次提交**
```
$ git add README.md
$ git commit -m "second commit"
$ git push
```

>注：git add上传本地项目所有变化的命令三种有 git add -A、git add -u、git add .

```
$ git add -A  提交所有变化
$ git add -u  提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)
$ git add .  提交新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件
```

#### 克隆仓库

`$ git clone git@github.com:iamliuyu/test.git`

#### 标签管理

**查看所有标签**
`$ git tag`

**创建标签**
`$ git tag name`

**指定提交信息**
`$ git tag -a name -m "comment"`

**删除标签**
`$ git -d name`


#### 分支管理
几乎每一种版本控制系统都以某种形式支持分支。使用分支意味着你可以从开发主线上分离开来，然后在不影响主线的同时继续工作。
![分支](http://www.runoob.com/wp-content/uploads/2014/05/branches.png)

**列出分支**
`$ git branch`

**创建分支**
`$ git branch branchname`

**切换分支**
`$ git checkout branchname`
当切换分支的时候，Git 会用该分支的最后提交的快照替换你的工作目录的内容， 所以多个分支不需要多个目录。

**合并分支**
`$ git merge`
可以多次合并到统一分支， 也可以选择在合并之后直接删除被并入的分支。

### 学习资料
*  https://www.imooc.com/learn/1052
*  http://www.runoob.com/git/git-tutorial.html
*  http://www.runoob.com/w3cnote/git-guide.html

