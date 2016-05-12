---
title: Git基础学习(5)
date: 2016-04-25 19:41:09
tags: git
---
&emsp;&emsp; 讲解如何忽略特殊文件，为命令定义别名，搭建自己的git服务器。
<!-- more -->
## 6 自定义git
对于服务器的搭建严格来讲在linux下比较好，但是为了讲解方便，本次还是在windows平台下完成，不过我个人认为，对此部分的知识肯定在你的团队中应该由专门的人员负责。

## 6.1 忽略特殊文件
&emsp;&emsp; 在编写完程序之后，肯定要对程序进行编译，但是很多情况下需要提交到服务器上去，服务器应该保留源程序才对。例如现在项目中有个emp.class文件。

**范例：**查询状态.

```bash
git status
```

![1](/images/git_6.1.png)

其中的文件还没有被添加，但是不需要提交该文件。用户可以在工作区中建立一个.gitignore 文件。如果想要编写文件的内容可以直接访问：[https://github.com/github/gitignore](https://github.com/github/gitignore)

**范例：**编写“gitignore”.

![2](/images/git_6.2.jpg)

下面保存此文件并提交。

```bash
git add .gitignore
git commit -m "add gitignore"
```

有了此文件后，再次查询状态，发现就不会再提示文件未被加入。

## 6.2 定义命令的别名
别名就相当于简化命令，最早如果要查询状态：git status

**范例：**定义别名--将status定义为ss.

```bash
git config -global alias.ss status 
```

下面直接可以直接用别名设置。

![3](/images/git_6.3.jpg)

所有的别名信息都可以再配置文件中找到。

![4](/images/git_6.4.png)

之前设置的全局信息都可以再此处配置。

## 6.3 搭建自己的git服务器
如果某公司项目的开发使用了github，那么可能遇到如下的问题：

- 网络的速度不稳定，时快时慢。
- 代码是公开状态的，实在不安全。
- 如果想安全一点，就需要适当的花费一些费用。

所以要搭建属于自己的git服务器，使用OpenSSH完成或者CopSSH，如果需要获取此工具可以直接访问：[http://pan.baidu.com/s/1hq3TeEK](http://pan.baidu.com/s/1hq3TeEK)。

相关准备：

```bash
1.需要定义一个新的用户，但是有哦写要求：

- 版本的管理员，建议名称是“verman_用户名”
- 代码的开关人员，建议名称是“coder_用户名”
- 测试人员，建议名称是“tester_用户名”

2.生成一个SSH密钥，
- 如果想要生成必须使用“git bash” （ssh-keygen -C "邮箱" -t rsa）

3.安装copssh工具
- 设置一个管理员

4.配置SSH用户
直接打开copssh的控制面板，然后选择“用户（user）”即可，之后添加。

5.为用户配置公钥
- 已经保存的路径为C:\Users..
- 需要在copssh安装目录下：..home\username\.ssh
- 建立一个文件“authorized_keys”(将复制过来的公钥文件改名即可)
修改完后可以再copssh中找到此配置。（keys）

```
![5](/images/git_6.5.png)

表示成功搭建完成，接下来如何使用。

![6](/images/git_6.6.png)

![7](/images/git_6.7.png)

![8](/images/git_6.8.png)

![9](/images/git_6.9.png)

## 6.4 总结
如果日后在你自己的团队中，搭建服务器就可以采取上面的方式完成。