---
title: git使用笔记
cover: /img/git.jpg
---

## 前言
本文是看完表严肃的视频教程后总结的笔记。
* 视频教程网址：[表严肃讲git](https://biaoyansu.com/27.x)  
* 学习教程网址：[表严肃的git精讲](https://biaoyansu.com/27.cheatsheet) 

## 安装配置
### Git下载
打开 [git官网](https://git-scm.com/)，下载git对应操作系统的版本。
![](/img/git_downloads.jpg)

### 查看版本
``` bash
$ git --version
```

### Git配置
``` bash
$ git config --global user.name "<姓名>" # 设置提交者姓名
$ git config --global user.email "<邮箱>" # 设置提交者邮箱
$ git config --list # 查看当前配置
$ git config -l # 查看当前配置
$ git # 简短说明
```
## 创建仓库
### 什么是仓库？
Git中有一个概念，叫仓库。仓库，其实就相当于“项目”，本质上就是个文件夹，就是个目录。仓库就是储存你代码的地方。既然要储存代码，那肯定要有个目录了。但不仅仅就是创建一个目录，我们要把它变成一个 Git仓库。
### 初始化Git仓库
``` bash
$ git init # 方式一：cd到某个目录执行该命令，这个目录就被初始化了，会多出一个.git文件
$ git init [目录名] # 方式二：在指定目录创建仓库，目录创建的同时会带有一个.git隐藏文件。
$ git clone <远程仓库地址> [目录名] # 方式三：从指定地址克隆仓库，若不指定目录名将默认创建与远程同名目录。
```
## 基本用法

### 查看仓库状态
假设我们我们使用git init创建了一个新的仓库，里面是没有什么东西的，只有.git文件。那么，如果我想知道它的状态，这时就可以输入git status来查看它的状态。输入后的结果是这样的：

``` bash
$ git status # 查看仓库状态
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```
这上面说，现在还没有一个是提交的。这里面有一个词：commit，意思是提交，是个历史节点。你可以把它看做是后悔药。

### 更改仓库内容
仓库里肯定要有内容,往里面新建一个test.txt文件，这时候输入git status，显示出来的是这样：

``` bash
$ git status # 查看更改后的状态
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        test.txt

nothing to commit (create/copy files and use "git add" to track)
```

“Untracked files”意思是 “未跟踪的文件” ，说明里面有了新的文件或者文件被修改，但没有提交，或者说没有造后悔药。这个文件没了就可能找不到了。

### 将文件添加到暂存区
这时我们可以输入git add . ,意思是将所有文件添加到暂存区，这个“点”就是指所有文件。然后我们在看下状态，这时它显示：

``` bash
$ git add . #将所有修改添加至暂存区
$ git status # 查看修改添加至暂存区后的状态
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   test.txt
```
### 生成“后悔药” （提交修改）
然后，我们再输入git commit -m "初始化"，显示出来是这样的：

``` bash
$ git commit -m '初始化' # 提交暂存区内容 '初始化'为提交说明
[master (root-commit) a069d11] 初始化
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test.txt
```

那么这颗后悔药就造好了,我们再看看仓库的状态，显示所有更改已经保存了，没有什么没保存的了，那你就不可能再丢失什么文件了。

``` bash
$ git status 
On branch master
nothing to commit, working tree clean

```

### “后悔药”查询 （查看历史提交）
假设你更改了或者新建了什么文件，那么你就输入git add .，然后再输入git commit -m "具体说明"，这就造出了一颗后悔药。如果你想知道到底造了什么后悔药，那么就可以输入git log，这时它显示：

``` bash
$ git log # 查看历史提交
commit a069d11fb9173655f10a7e2637e63d072b762231 (HEAD -> master)
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:12:58 2022 +0800

    初始化

```

这上面那个“初始化”你可以理解为这颗后悔药的名字，其实就是我们给它的说明。那“commit”后面那一串长的东西是什么呢？，你可以理解为这颗后悔药的身份证号。这里，我们重复上面的操作，造两颗后悔药，然后我们用git log看一下有哪些后悔药，它显示：

``` bash
$ git log
commit cc5839cd461d39740c997fef2f8118f18a5cec2d (HEAD -> master)
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:40:53 2022 +0800

    第二次修改

commit eac61cd44c4ea2342bae30809a56026270a70725
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:37:56 2022 +0800

    第一次修改

commit a069d11fb9173655f10a7e2637e63d072b762231
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:12:58 2022 +0800

    初始化
$ git log -p # 查看详细的修改内容
$ git log --online  # 显示在一行
$ git log --online --all # 显示所有节点
$ git log --all --graph # 图示全部历史记录
``` 

### 吃后悔药（版本回退）
假设我们发现第二次修改的内容很差劲，想退回，怎么办？我们就可以输入git checkout xxx，这个命令意思是切换到任意历史节点。在这里的“xxx”我们就输入后悔药的身份证号，输入后会显示一些内容，我们不管它，然后打开文件，就会发现文件的内容退回了，那么这颗后悔药就吃成功了。

``` bash
$ git checkout eac61cd # 回退到指定节点
$ git checkout - # 回退到最近的版本
```

## 三种状态
modified（已修改）=> staged（已暂存）=> commited（已提交）
只有暂存区里面的内容才会被提交。
![](/img/status.jpg)

## 标签tag
在一个重要的的历史节点上，你要做一个标记，那么这个标记就是标签。默认会在最近的提交打标签。

``` bash
$ git tag -a v1 -m '完成第一版' # $ git tag -a 标签名 -m '备注'
$ git log
commit cc5839cd461d39740c997fef2f8118f18a5cec2d (HEAD -> master, tag: v1)
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:40:53 2022 +0800

    第二次修改

commit eac61cd44c4ea2342bae30809a56026270a70725
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:37:56 2022 +0800

    第一次修改

commit a069d11fb9173655f10a7e2637e63d072b762231
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:12:58 2022 +0800

    初始化

$ git tag # 列出所有标签
$ git tag -a v0.5 -m '第一版完成中' eac61cd # 在指定节点打标签
$ git log
commit cc5839cd461d39740c997fef2f8118f18a5cec2d (HEAD -> master, tag: v1)
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:40:53 2022 +0800

    第二次修改

commit eac61cd44c4ea2342bae30809a56026270a70725 (tag: v0.5)
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:37:56 2022 +0800

    第一次修改

commit a069d11fb9173655f10a7e2637e63d072b762231
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:12:58 2022 +0800

    初始化
$ git show v1 #git show 标签名 查看某个标签的详细信息
$ git tag -a v2  -m '第二章完成' #继续提交第三第四次修改并打上标签
```

## 分支branch
### 什么是分支？
那么分支可以理解为，在一本小说里可能同时有不同的故事线，比如主线或者其他的故事线。它们也可能会随时合并。

### 创建使用分支
假设我们要在“第二次修改”处创建分支，可以输入git branch 分支名，这里的分支名就设为“cat”。如果输入后什么也没显示，那说明创建成功了。
然后我们就可以切换到“secondbranch”，这时我们输入git checkout cat，然后它会显示你成功切换到“cat”分支。

``` bash
$ git branch cat # 创建分支cat
$ git branch # 列出所有分支
$ git checkout cat # 切换至分支cat
$ git checkout -b 分支名 #  创建并切换至分支
```

在这个cat分支上我们也是可以写一些内容的，做两次修改，然后提交一下，这时我们再用git log看一下，它显示：
``` bash
$ git add .
$ git commit -m '加了cat'
$ git add .
$ git commit -m '加了fish'
$ git log
commit 325114bb6624bcdf9b135a9cf0ea6238c8ad4bf4 (HEAD -> cat)
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 20:47:19 2022 +0800

    加了fish

commit 0882ec666320759f29d1c190dc61272f191779b9
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 20:44:09 2022 +0800

    加了cat

commit cc5839cd461d39740c997fef2f8118f18a5cec2d (tag: v1)
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:40:53 2022 +0800

    第二次修改

commit eac61cd44c4ea2342bae30809a56026270a70725 (tag: v0.5)
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:37:56 2022 +0800

    第一次修改

commit a069d11fb9173655f10a7e2637e63d072b762231
Author: unknown <jocelin_oa@outlook.com>
Date:   Tue May 17 14:12:58 2022 +0800

    初始化
```
可以看到，现在所做的修改是在“cat”分支上了,如果想回到原来主分支，只需要git checkout 主分支名 就好了。

### 合并分支
开发中通常将新创建的分支上写的代码或者修复bug的代码与主分支合并然后继续开发。
![](/img/merge.jpg)

``` bash
$ git merge cat # 切换到master分支上，将cat分支与当前分支合并
Auto-merging test.txt
CONFLICT (content): Merge conflict in test.txt
Automatic merge failed; fix conflicts and then commit the result.
```

上面显示自动合并时发生了冲突，这时我们就要手动合并了，我们用编辑器打开文件，它显示：

![](/img/conflict.jpg)

这里<<<和>>>之间就是冲突的地方，===是分隔线。我们要保留“cat”分支的内容，那么我们就把不需要的内容去掉就行了，如：

![](/img/fix.jpg)

然后我们就用“add”和“commit”提交，最后就合并成功了。

## 远程仓库
版本控制光放在本地还是有一定风险，需要拷贝一份放在远程服务器上更保险。

![](/img/remote.jpg)

### 创建远程仓库
以github为例，登录进去后先创建一个仓库，复制新创建的仓库的地址,给本地仓库添加一个远程仓库。

``` bash
$ git remote add test git@github.com:MYYER/test.git # git remote add 远程名称 远程地址
$ git remote # 列出所有远程仓库
test
$ git remote -v # 列出所有远程仓库详细信息
test    git@github.com:MYYER/test.git (fetch)
test    git@github.com:MYYER/test.git (push)
```
### 上传代码
然后就可以把本地代码推到远程仓库了，git push -u可以指定他人拉代码的时候往哪个分支上合并。

``` bash
$ git push -u test master # git push -u 远程名 分支名 
```

## 拷贝仓库
如果本地的代码丢失就可以直接从远程仓库拷贝代码，默认就会将远程仓库设为克隆的仓库地址，默认名称origin，无需手动再设置。

``` bash
$ git clone git@github.com:MYYER/test.git # git clone 仓库地址 
$ git remote
origin
$ git remote -v
origin  git@github.com:MYYER/test.git (fetch)
origin  git@github.com:MYYER/test.git (push)
```

## 多人远程合作
``` bash
$ git pull # 获取远程更新
$ git fetch && git merge # 与git pull等同
```
一图胜千言,pull命令其实就是先fetch再checkout,fetch命令仅仅是将数据放在已提交的状态，不会更改编辑器里正在修改的代码，但是checkout会更改。
![](/img/fast.png)

## 其他内容
### 比对 diff
``` bash
$ git diff # 比对当前内容和暂存区内容
$ git diff HEAD # 比对当前内容和最近一次提交
$ git diff HEAD^ # 比对当前内容和倒数第二次提交
$ git diff HEAD^ HEAD # 比对最近两次提交
```
### 全面速查表
![](/img/quick.jpg)