# 配置用户信息

1. 针对某个仓库配置用户。。
   `git config --local user.name 'username'`。

   `git config --local user.email 'email'`。

2. 针对所有仓库配置用户

   `git config --global user.name 'username'`。

   `git config --global user.email 'email'`。

3. 配置系统用户

   `git config --system user.name 'username'`。

   `git config --system user.email 'email'`。

4. 显示配置列表

   `git config --list --作用域`。

5. 删除配置信息

   `git config --作用域 --unset 要删除的key`。



# 初始化项目信息

## 已有项目纳入git管理

切换到项目根路径下，在cmd执行`git init 项目名`。



## 初始化项目纳入git管理

切换到需要新建项目的目录下，在cmd执行`git init 项目名`，执行完成后会自动新建项目文件夹。





# 提交项目代码

## 提交commit

1. `git commit -m'本次提交说明'`提交命令git每次提交代码必须写说明。

2. `git commit --amend -m '修正消息说明' ` 修改上次提交的消息。

## 增加文件到缓存区 add

1. `git add files[.]`增加文件到缓存区命令,新增文件需要在commit之前执行该命令。.代表文件下所有文件。

## 查看文件状态 status

2. `git status [files]`查看文件状态,通过该命令可以查看当前文件在git中的状态。

## 撤销操作

1. `git reset HEAD file`移出缓存区。
2. `git checkout file`还原文件状态。



## 查看提交日志 log

1. `git log`查看gi提交日志，该命令可以查看git版本提交历史。 

2. `git log --oneline`:显示每次提交的文件

3. `git log -n`显示最近提交的n个文件

4. `git log --graph`图形显示历史信息

5. `gitk` 图形界面

## 分支

1. `git branch -v` 查看本地有多少个分支

2. `git checkout 分支名`切换分支 

3. `git checkout -b 分支名` 创建分支

4. `git branch -d` 删除分支
5. `git merge 分支名`整合分支

## 删除文件 rm

1. `git rm file`删除已被git管理的文件。

## 修改文件名

1. `git mv sourceFileName targetFileName`修改完成之后需要重新commit提交

# 特殊定义

1. 作用域可选值：local、global、system。

