---
title: Git基础学习(3)
date: 2016-04-19 16:48:57
tags: git
---
&emsp;&emsp;在前面第一节里面主要讲的是分支基础的应用，这一节开始讲git的分支管理的策略。
<!-- more -->
## 4 git分支管理策略
&emsp;&emsp;在之前进行的分支合并的时候使用的全部都是“fast forward”方式完成的，而此种方式只是改变master指针，可是在分支的时候也可以不使用这钟方式快速合并，即：增加一个**--no-ff**参数，这样就表示在合并之后会自动的生成一个新的commit id，从而保证合并数据的完整性。

![--no-ff](/images/git_4.1.1.png)

举例说明：

```bash
1.创建一个新的分支
git checkout -b brh

2.建立一个新的java文件

3.提交修改
git commit -a -m "add new files"
(包括add和commit)

4.切换回master分支
git checkout master

5.使用非快速合并的方式进行合并,结果如图1所示
git merge --no-ff -m "no ff commit"

6.查看一下提交的日志信息,结果如图2
git log --graph --pretty=oneline --abbrev-commit
```

![1--no-ff](/images/git_4.1.2.png) 

![2--no-ff](/images/git_4.1.3.png) 

## 4.1分支管理策略
![分支管理策略](/images/git_4.1.4.png)   

## 4.2分支暂存
&emsp;&emsp;譬如说现在你正在一个分支上开发，但是突然你的领导给了你一个新的任务，并且告诉你在半个小时内完成，那怎么办？

难道那开发一半的分支要提交吗？不可能的，因为对于版本控制的基本的道德方式：你不能把有问题的代码提交，你所提交的代码一定都是正确的代码，那么为了这样的问题，在git中提供一个分支暂存的机制，可以将开发一半的分支进行保存，而后在适当的时候进行代码的恢复。

那么下面首先创建一个基本的开发场景。
```bash
1.创建切换到一个新的分支
git checkout -b brh

2.下面在分支上面编写java文件

3.将此文件保存在暂存区中，
git add .
代码还没完成，不能提交，但是新的任务给你了，你必须暂停当前的开发任务，所以需要将当前的开发进度暂存，等日后有时间了继续进行恢复开发。

4.将工作暂存,如图3所示
git stash

5.查看当前的工作区的内容,如图4所示
git status

6.要改的工作在master分支上，所以切换master
git checkout master
```

![3-分支暂存](/images/git_4.1.5.png) 

![4-状态查看](/images/git_4.1.6.png) 

表示当前的工作区中没有任何的修改。

那么现在假设说创建一个新的分支来处理老班的需求，假设分支的名称为“dev”（也有可能是一个bug调试）。接着上面来：

```bash
7.创建切换到dev新的分支
git checkout -b dev

8.修改java文件

9.提交修改
git commit -a -m "dev commit"
到此老班的要求完成，但是这个代码还是dev分支，那么现在的切回master分支进行合并。

10.切换回master分支
git checkout master

11.合并dev分支,使用no fast forward
git merge --no-ff -m "merger dev branch" dev
问题被解决了，dev分支没有任何存在的意义了，可以删除。

12.删除dev分支
git branch -d dev
```

那么需要回归到已有的工作状态，但是有可能会存在许多暂存的状态，可以直接使用如下命令进行列出：

```bash
13.暂存区列表
git stash list

14.从暂存区中进行恢复：
 a)x先恢复，在手动删除暂存：
git stash apply
git stash drop

 b）恢复的同时将stash内容删除：结果如图5
git stash pop

15.下面的任务就是代码提交
git commit -a -m "change file"

16.删除brh分支
git branch -d brh
```

![5-stash内容删除](/images/git_4.1.7.png) 

使用暂存策略可以很方便的解决代码突然暂停修改的操作，是非常方便的。


## 4.3补丁:patch
&emsp;&emsp;补丁并不是对所有的代码的修改，只是对局部的代码的修改，在很多的代码的维护之中，如果按照最早克隆的方式将代码克隆下来实际上花费的资源是非常大的，但是修改的只是可能只是修改很小的一部分代码，在这个时候就希望将一些代码的补丁信息发送给开发者。而发给开发者后，他需要知道哪些代码被修改了，这样的话就可以使用一个极低的开销实现代码的修改操作，而在git中也提供了两种简单的补丁方案。

- **使用git diff生成标准的patch:**
- **使用git format-patch生成git专用的patch**

### 4.3.1利用git diff生成标准的patch
补丁的修改一定是针对文件的修改，所以以一个java文件为例。

```bash
1.当前的emp.java文件,如图6所示

2.建立切换一个分支cbrh
git checkout -b cbrh

3.修改emp.java文件，如图7所示

4.查看文件代码的不同,如图8
git diff emp.java
```

![6-emp.java](/images/git_4.3.1.png) 

![7-修改的emp.java](/images/git_4.3.2.png) 

![8-对比emp.java](/images/git_4.3.3.png) 

此时可以发现emp.java文件修改前后的对比情况。接着后面：

```bash
5.提交到cbrh分支
git commit -a -m "add in emp.java"

此时并没有和主分支进行提交，但是代码已经改变，需要的是将代码的变化提交给开发者。

6.生成补丁文件 -- mypat
git diff master > mypat

7.然后在回到master分支上面
git checkout master

此时会自动在项目目录中生成一个mypat的补丁文件信息。是由git读的文件。完成后，需要模拟另一个开发者，假设是专门进行合并的开发者。

8.创建并切换一个分支
git checkout -b patchbrh

9.应用补丁信息
git apply mypat

此时补丁可以成功的使用了。

10.提交补丁的操作
git commit -a -m "Patch Apply"

11.切换回master分支后，再进行分支的合并
git checkout master 
git merge --no-ff -m "Merge Patch" patchbrh
```

这样如果只是将补丁数据的文件发送给开发者，那么就没有必要进行大量代码的传输，并且在创建补丁的时候也可以针对于多个文件进行补丁的创建。

### 4.3.2利用git format-patch生成专用的patch
那么还是利用分支修改emp.java文件。

```bash
1.将之前的分支强删掉
git branch -D cbrh
git branch -D patchbrh

2.创建并切换分支cbrh
git checkout -b cbrh

3.修改文件，增加一个方法

4.提交代码到cbrh
git commit -a -m "add a method"

5.比较原始master代码，比较后自动生成一个补丁文件，结果图8
git format-patch -M master
```

![9-对比emp.java后生成补丁文件](/images/git_4.3.4.png) 

此时已经生成了一个补丁文件，因为只修改了一次的内容，这个补丁文件严格来说就是一个email数据，需要将此数据发送给开发者，而后开发者可以进行补丁的应用。接着操作：

```bash
6.创建并切换到patchbrh分支上
git checkout master
git checkout -b patchbrh

7.应用补丁的信息，利用“git am”完成，结果如图10
git am 补丁名

8.补丁应用后，提交更新
git commit -a -m "method patch apply" 

9.再可以合并
git checkout master 
git merge --no-ff -m "Merge Patch method" patchbrh
```

![10-应用补丁文件](/images/git_4.3.5.png) 

那么，此时就可以直接成功的应用补丁进行代码的更正。

**关于这两种补丁方式的说明：**
- 使用第一种生成的补丁兼容性比较好的，如果你是在不是git管理的仓库上，此类方式生成的补丁是非常容易接受的；
- 但是如果你是向公共的开发社区进行代码的补丁更正，那么建议使用第二种，这样不仅标准，也可以将更正人的信息进行公布；

## 4.4多人协作开发
&emsp;&emsp;分支的处理实际上是为了更好的多人开发做出的准备，那么下面就将利用两个命令行的方式进行代码的编写模拟多人开发者。

- 一般而言，master分支项目的核心分支，只要进行代码的克隆，那么此分支一定会被保存下来：
- 开发者往往会建立一系列的分支，
- 如果要进行调试可以建立bug分支
- 如果要增加新功能可以建立feature分支。

![1](/images/git_4.4.1.png) 

![2](/images/git_4.4.2.png) 

![3-4-5](/images/git_4.4.3.png) 

![6](/images/git_4.4.4.png) 

发现现在只是将master分支copy下来了，但是brh分支并没有存在。

![7-8](/images/git_4.4.5.png) 

![9](/images/git_4.4.6.png) 

![10-11](/images/git_4.4.7.png) 

现在代码已经发送到了服务器上了，并且在brh分支上增加了新的admin.java文件。

![12](/images/git_4.4.8.png)

![12](/images/git_4.4.9.png) 

错误信息也很简单，指当前的brh分支和服务器上的分支没有关系，所以如果想要读取代码，必须让2个分支产生关联。

![12](/images/git_4.4.10.png) 

这个时候就实现了不同开发者之间的代码互相关联。


![13](/images/git_4.4.11.png) 

![14-15](/images/git_4.4.12.png)

![16](/images/git_4.4.13.png) 

![17](/images/git_4.4.14.png)

但是这个时候很明显，2个用户同时一起修改了文件。

![18](/images/git_4.4.15.jpg)

此时的程序是2位开发者修改了同一个代码，所以产生了冲突，同时一号开发者中的文件，已经变更如下情况：

![18](/images/git_4.4.16.png)

![19](/images/git_4.4.17.png)

![20](/images/git_4.4.18.png)

现在已经成功的由本地的冲突扩充到了远程的冲突，通过一系列的代码大家也可以更好的理解分支的操作问题。

- 首先，可以试图用git push origin branch-name推送自己的修改；
- 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
- 如果合并有冲突，则解决冲突，并在本地提交；
- 没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！

如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令:

```bah
git branch --set-upstream branch-name origin/branch-name
```

这就是多人协作的工作模式，一旦熟悉了，就非常简单。



## 4.5总结
&emsp;&emsp;在整个git学习中，分支的管理操作是最为麻烦的也是最为重要的操作，所以在此部分的程序，希望反复的练习。分支是进行开发使用的，最终的代码都在master分支上。

- 查看远程库信息，使用git remote -v
- 本地新建的分支如果不推送到远程，对其他人就是不可见的
- 从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交
- 在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致
- 建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name
- 从远程抓取分支，使用git pull，如果有冲突，要先处理冲突
















