---
title: Git基础学习(2)
date: 2016-04-16 20:59:54
tags: git
---
&emsp;&emsp;在前面第一节里面主要讲的是一些基础的应用，这一节开始讲重点，git的分支管理。
<!-- more -->
## 3.git分支
### 3.1分支管理
1. git与传统的开发最大的区别在于分支管理上，而只有处理好了分支才可以说掌握了git的使用。
2. 有了分支管理之后，许多的开发就可以由用户去定义模式。
3. 如何创建、使用分支、冲突的解决、bug管理，补丁操作。

利用分支就可以实现多人开发的伟大模式，从而提高生产效率，在整个git中，主分支（master）主要是作为程序的发布使用，一般而言很少会在主分支上进行代码的开发，都会各自在子分支上进行。

### 3.2创建与合并分支

![分支](/images/git_br2_01.png)

![分支](/images/git_br2_02.png)

以上的操作属于项目的发布版本的执行顺序，因为最终发布的就是master主分支，其他的开发者不能够在主分支上进行，所以应该建立子分支，而子分支最起码建立应该是当前master分支的状态，一旦分支建立完毕后head指针发生变化。

![分支](/images/git_br2_03.png)

![分支](/images/git_br2_04.png)

此时master分支的版本落后于子分支，但是不管子分支再怎么开发，也不是最新的发布版本，所以新的发布版本都保存在master分支，那么就必须将子分支和master分支合并。

![分支](/images/git_br2_05.png)

当分支合并后，实际就相当于master分支的提交点修改为了子分支的提交点，而后这个合并应该在master分支上完成，而后head指针需要修改指针的位置，而指向原本的master分支。

![分支](/images/git_br2_06.png)

### 3.3分支操作
**1，创建分支**
```bash
git branch brh
```

**2, 查看当前分支**
```bash
git branch
```

![分支](/images/git_br2_07.png)

可以发现现在提示当前的工作区中有两个分支，一个是brh，一个master分支，而现在分支指向master分支。

**3，切换分支brh**
```bash
git checkout brh
```

![分支](/images/git_br2_08.png)

![分支](/images/git_br2_09.png)

![分支](/images/git_br2_10.png)

![分支](/images/git_br2_11.png)

![分支](/images/git_br2_12.png)

当子分支的数据提交之后实际上并不会去修改master分支的内容，这就证明了两个分支上内容是相对对立的。

**6，那么既然分支都已经存在了，那么现在为了更加清楚，将master和brh两个分支都提交到远程服务器上（github）**

```bash
git remote set-url origin https://github.com/yootk/a.git
git push origin master
git push origin brh
```

![分支](/images/git_br2_13.png)

快速合并式并不会产生任何的commit id。他只是利用合并子分支的commit id。此时的brh分支没有任何用处。

**8，删除brh分支**

```bash
git branch -d brh
```

**9, 提交master分支**

```bash
git push origin master
```

现在在本地已经没有子分支了，但是在远程服务器上依然存在子分支。那么下面要删除远程分支。

**10，删除远程分支**

```bash
git push origin --delete brh
```

### 3.4分支管理

在之前已经完整的演示了分支的各个操作，包括使用分支、以及合并分支，同时也清楚了对于分支有两种方式一种是本地分支，另一种是远程分支，但是对于分支在git上使用中依然会有一些问题。

**1，建立新分支**

```bash
git checkout -b brh
```

![分支](/images/git_br2_14.png)

```bash
git add .
git commit -a -m "add file"
```

3, 此时并没有进行分支数据的提交，但是总有人觉得这个brh分支名称不好。重命名lxh

```bash
git branch -m brh lxh
```

现在相当于分支的重命名。

4，将分支提交远程服务器

```bash
git push origin lxh
```

![分支](/images/git_br2_15.png)

6, 此时“lxh”分支上已经做出了修改，但是并没有合并，现在觉得这个分支不需要想删除它。

```bash
git branch -d lxh
```

![分支](/images/git_br2_16.png)

![分支](/images/git_br2_17.png)

可是现在在远程分支服务器上依然会存在此分支，那么就必须也一起删除掉，但是对于删除操作，除了之前使用过的方式之外，也可以推送一个空的分支，这样也表示删除。

![分支](/images/git_br2_18.png)

由于推送的是一个新的空分支，所以在服务器端会认为此推送的目的就是为删除分支。


### 3.5冲突自动解决

![分支](/images/git_br2_19.png)

![分支](/images/git_br2_20.png)

![分支](/images/git_br2_21.png)

![分支](/images/git_br2_22.png)

![分支](/images/git_br2_23.png)

![分支](/images/git_br2_24.png)

![分支](/images/git_br2_25.png)

![分支](/images/git_br2_26.png)

等于是现在的GIT工具帮助用户自己解决了冲突问题。

### 3.6冲突手工解决

![分支](/images/git_br2_27.png)

```bash
git commit -a -m "simple print"
```

![分支](/images/git_br2_28.png)

![分支](/images/git_br2_29.png)

7, 提交master修改

```bash
git commit -a -m "url print"
```

![分支](/images/git_br2_30.png)

此时直接提示出现冲突。

9，查看冲突的内容

```bash
git status
```

![分支](/images/git_br2_31.png)


![分支](/images/git_br2_32.png)


![分支](/images/git_br2_33.png)


![分支](/images/git_br2_34.png)

![分支](/images/git_br2_35.png)

![分支](/images/git_br2_36.png)


### 3.7分支管理策略

在之前进行分支合并的时候使用的全部都是“fast forward”方式完成的，而此种方式只是改变了master指针，可是在分支的时候也可以不使用这种快合并，即：增加上一个“--no--ff”参数，这样就表示在合并之后会自动的再生成一个新的commit id，从而保证合并数据的完整性。


![分支](/images/git_br2_37.png)

![分支](/images/git_br2_38.png)

![分支](/images/git_br2_39.png)

![分支](/images/git_br2_40.png)

![分支](/images/git_br2_41.png)

![分支](/images/git_br2_42.png)

