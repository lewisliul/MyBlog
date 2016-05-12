---
title: Git基础学习(1)
date: 2016-04-12 12:45:58
tags: git
---
&emsp;&emsp;团队开发中，遵循一个合理、清晰的Git使用流程，是非常重要的。否则，每个人都提交一堆杂乱无章的commit，项目很快就会变得难以协调和维护。
<!-- more -->
# git基础学习教程
## 1.git
### 1.1设置开发者的个人信息
**范例：**配置全局用户及email地址.

``` bash
git config –global user.name “name”
git config –global user.email “1234@qq.com”
```
### 1.2创建仓库信息
如果要开发项目，必须先创建一个仓库（可以理解为一个文件夹）。

**范例：**创建文件夹（d:\mypro）.

``` bash
d:
md mypro
```

**范例：**将mypro文件夹定义为仓库.

``` bash
cd mypro
git init     初始化仓库（将此目录变为Git可以管理的仓库）
```

### 1.3添加文件
所有文件一定要使用utf-8编码，否则会出现问题，不要使用window自带的notepad工具。
建立一个hello.java文件,文件保存在mypro目录下，属于git管理的仓库。
**范例：**创建文件夹（d:\mypro）.

``` bash
git status    查看当前仓库的状态
```
![结果图](http://ww2.sinaimg.cn/large/ea23bca3gw1f2ymmf06cwj20nu0663zv.jpg)
在状态查询操作上的几个提示信息：

- 现在的开发属于主分支：on branch master;
- 初始化仓库的提交：Initial commit;
- 未标记的文件：untracked files
- 随后给出一些操作命令：（use ：git add<file>……）
- 未标记文件的列表，现在只有一个：hello.java

**范例：**将文件家加入到暂存区中.

``` bash
git add hello.java
```

**范例：**提交文件信息.

``` bash
git commit –m “注释信息”
```

此时这个文件就被真正的提交到了主分支上，也就是意味着程序发布成功了。  
**注意：**所有的修改代码都会被git自动检测到，所有的代码在使用commit提交之前一定要使用add添加进来，否则不会有任何的提交。如果不想两部进行可以增加一个参数。（git commit –a –m “注释”）等于上面2步。

**范例：**查看日志.

``` bash
git log  查看日志记录的时候如果到底了，请按下字母”q”就表示可以退出了
```
![结果图](/images/log.png)    
出现一个提交的信息号“commit ID”

### 1.4修改仓库文件
代码的出现就是为了修改，现在来观察git如何控制修改,修改了heoll.java文件,此时增加修改后，可以查看当前状态,git status 提示信息：
   
![结果图](/images/git_1.4.1.png)   

- git在主分支上  
- git没有保存文件到暂存区中  
- 可以提交文件，也可以直接进行文件恢复

**范例：**查看文件的区别.  

```bash
git diff hello.java
```
  
发现所有添加的内容都带有加号“+”，而被删除的信息都会使用减号“-“。然后就可以将修改的代码进行add和commit。   

### 1.5工作区和暂存区
**工作区：**就是指当前电脑的操作目录。

**仓库：**工作区有一个.git文件，这个不算工作区，而是git的仓库版本库。git版本库里面主要的东西有很多，主要包括叫stage的存储区以及为用户自动创建的主程序分支master，以及只想master的HEAD指针.

![结果图](/images/git_1.5.1.png)   

- 在之前编写的hello.java文件是保存在用户的工作区中的。
- 当使用add命令后，实际上就是将所有修改文件提交到了暂存区（stage）中。
- 使用commit命令之后，才表示真正的发出了修改，而真正可以运行的查询都保存在master分支。

#### 1.5.1工作区的操作
修改hello.java文件，添加一个新文件Demo.java，现在工作区中的代码已经发生了变化。可以使用status进行查看。

![结果图](/images/git_1.5.2.png)  

提示如下:

- 修改了hello.java文件，而这个文件给出了处理方式。
- 出现了一个未被标记的demo.java文件，询问用户是否将其加入到暂存区之中。

#### 1.5.2暂存区的操作
接着按照提示可以将文件加入到暂存区。

**范例：**添加到暂存区.

```bash
git add .
```
本次操作使用了一个“.”,那么就表示全部加入。

![结果图](/images/git_1.5.3.png)  

![结果图](/images/git_1.5.4.png) 

#### 1.5.3提交修改
数据保存在暂存区之后，下面就要进行代码的提交，将代码提交到主分支上。

![结果图](/images/git_1.5.5.png)

当把暂存区的代码提交到主分支后，会自动的清空暂存区的内容。

### 1.6版本回退
每当用户进行代码提交的时候都会自动的生成一个commit ID，而这个commit ID就是进行代码回退的主要操作方式。

**范例：**查看当前修改的日志信息.

```bash
git log
```
   
![结果图](/images/git_1.6.1.png)   

大家发现所有的id并不是顺序的1，2，3编号，而是由系统生成一个十六进制数据，这一概念就跟session id类似，有git自己控制，主要是为了防止版本号的冲突。每当用户将自己的代码提交到版本库之后都会自动的生成一个commit记录进行操作保存，每一条记录都会自动生成一个commit id进行唯一标识。

```bash
git log --pretty=oneline    （id一行显示）
```

![结果图](/images/git_1.6.2.png)  

在master分支上有一个head指针存在，而这个指针默认情况下永远指向最后一次提交的位置。

![结果图](/images/git_1.6.3.png) 

当使用回退之后发现HEAD指针出现了改变，如果回退一步，那么之前的操作不会被删除，但是所有的代码会回归到指定位置的状态。

![结果图](/images/git_1.6.4.png) 

**范例：**回退一步.

```bash
git reset --hard HEAD~1
```

![结果图](/images/git_1.6.5.png) 

此时已经回退到上一次提交的状态，那么如果现在还想恢复最新的状态呢？那就必须找到上面回退的commit id。

```bash
git reset --hard fe27af74      (commit id上图可以看到)
```

此时代码就恢复到了新的状态，也就是说利用这些id就可以方便的进行代码的历史穿梭了。


### 1.7文件修改问题
在有了暂存区和master主分支概念之后，就需要回避一个问题，只有保存在暂存区中的内容才能够真正的修改，而不是针对文件。
   
![结果图](/images/git_1.7.1.png)   

git add将文件增加到暂存区中，此时并没有提交。  
git commit进行提交，只是提交暂存区中的内容。

**总结：**如果一个文件修改多次，那么就需要执行多次的add后才可以提交，否则在add前的修改是不会被提交的。

### 1.8撤销修改
修改完后还是可以进行撤销的，发现错了，所以不想改，回到原来的状态。具体分为2种情况:
   
![结果图](/images/git_1.8.1.png)   

#### 1.8.1情况一：未增加（git add）且未提交（git commit）
如果在工作区中的代码并没有添加到暂存区中，也就是并没git add的时候是比较好恢复到原始的状态。

```bash
git checkout -- hello.java
```

执行上面代码发现hello.java文件就恢复到一个原始的状态（上一次改之前）。

#### 1.8.2情况二：已增加（git add）且未提交（git commit）
如果在工作区中的代码已经添加到暂存区中，也就是git add添加进去了，但是此时并没有git commit，并没有提交的时候，此时需要根据HEAD指针来恢复文件。  

**范例：**从暂存区中退出.

```bash
git reset HEAD hello.java
```

相当于现在已经由暂存区中保存的内容恢复到了工作区，那么就可以在使用上述第一种情况去直接恢复到原始状态。

### 1.9删除文件
现在在仓库中存在demo.java文件，但是假设这个文件从此不在使用呢，那如何删除呢，在git里面对于删除的这一个功能来说也属于一个修改操作。

**范例：**从磁盘中删除.

```bash
del Demo.java
```

接着可以查看状态。
  
![结果图](/images/git_1.9.1.png)   

这个时候文件是从当前的工作区的磁盘中删除了，同时也提示文件被删除，接着可以提交更新。

**范例：**提交文件.

```bash
git commit -a -m "delete file"
```

但是如果删除错误呢？那么应该进行恢复。

**范例：**恢复文件.

```bash
git reset --hard (commit id)
```

**总结：**以上所有的操作都只是针对于本地仓库的改善，但是这些命令不管是本地还是远程操作都是可以使用的，同时一定要掌握GIT的存储结构。

## 2.远程仓库（GITHUB）
首先注册github账号。了解github的使用。 

### 2.1SSH key
对于github中的两种链接使用前需要生成一个SSH key后才可以使用。

1.生成一个SSH key，在window平台下可以直接使用“git bash”工具创建。需要生成一个RSA编码的KEY。  

2.执行：  

```bash
cd ~/.ssh
```
 
如果返回“… No such file or directory”，说明没有生成过SSH Key，

3.生成新的Key：（引号内的内容替换为你自己的邮箱）

```bash
ssh-keygen -t rsa -C "your_email@youremail.com"
```
  
输出显示：Generating public/private rsa key pair. Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):直接回车，不要修改默认路劲。  
Enter passphrase (empty for no passphrase): Enter same passphrase again:
设置一个密码短语，在每次远程操作之前会要求输入密码短语！闲麻烦可以直接回车，不设置。

4.成功后提公鑰:找到.ssh文件夹，用文本编辑器打开“id_rsa.pub”文件，复制内容到剪贴板。打开 [https://github.com/settings/ssh](https://github.com/settings/ssh) ，点击 Add SSH Key 按钮，粘贴进去保存即可。

### 2.2添加远程仓库
- 把github与本地仓库关联

``` bash
git remote add origin http://github.com/my/a.git  (远程仓库地址)
```

此时已经和远程仓库建立了链接，但是远程仓库里面并没有本地仓库的内容。

- 推送到github

``` bash
git push -u origin master  
```
由于选择远程仓库里面并没有任何文件存在，所以进行本地文件推送，但是由于是第一次推送的为master分支，那么使用了一个“-u”的参数将远程的master和本地的master进行关联。

- 切换仓库

``` bash
git remote set-url (name path)
```
还可以切换远程其它的仓库，需要仓库名以及路径。

- 查看现在远程服务器已经存在的仓库信息：

``` bash
git remote -v
```
查看远程仓库

- 删除仓库

``` bash
git remote rm a
```
删除远程仓库a。

- 此时，再修改hello.java文件，接着提交到服务器

``` bash
git commit -a -m "修改后提交"
```
这个时候只是将其添加到本地的master分支上，没有到远程分支上面去，需要按照上面的步骤推送到服务器。

![结果图](/images/git_2.2.1.png) 

那么此时就完成了本地代码与服务器代码的同步。


### 2.3克隆远程仓库
之前讲解的实际上是先有本地仓库，而后将本地仓库提交到远程仓库上进行管理，但这是一个错误的做法，应该是先有远程仓库，而后在根据远程仓库克隆出一个新的仓库来。

- 在github上创建一个仓库：a
- 在本地磁盘上进行仓库的克隆操作

``` bash
git clone https://github.com/my/a.git
```
同理可以克隆其他开源的项目。


接下来的分支管理大家可以看下一节 
[http://lewisliul.github.io/2016/04/16/git%E5%AD%A6%E4%B9%A02/](http://lewisliul.github.io/2016/04/16/git%E5%AD%A6%E4%B9%A02/)

