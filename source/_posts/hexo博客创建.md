---
title: hexo博客创建
date: 2016-04-15 12:07:51
tags: [blog,hexo] 
---
&emsp;&emsp; 现在作为一名程序猿，说自己没有blog，还是稍微有点落后的感觉，现在就简单的介绍一下如何借用一个微博框架**Hexo**来创建属于自己的博客！
<!-- more -->
## 1.hexo搭建静态博客

由于我同学他自己的**blog**有写，所以我就直接复制链接过来，大家可以去看看。
[http://wustmeiming.github.io/](http://wustmeiming.github.io/2016/04/14/hexo%E6%90%AD%E5%BB%BA%E9%9D%99%E6%80%81%E5%8D%9A%E5%AE%A2/)


## 2.hexo博客添加多说插件实现评论功能
[http://wustmeiming.github.io/](http://wustmeiming.github.io/2016/04/14/hexo%E5%8D%9A%E5%AE%A2%E6%B7%BB%E5%8A%A0%E5%A4%9A%E8%AF%B4%E6%8F%92%E4%BB%B6%E5%AE%9E%E7%8E%B0%E8%AF%84%E8%AE%BA%E5%8A%9F%E8%83%BD/)

## 3.markdown编辑器
- 安装markdown编辑器，推荐使用**MarkdownPad**和**Haroopad**，也可选用在线编辑器，如作业部落，简书等，作用：编辑发布的文章，这编译器有助于大家直接预览。

![](http://ww3.sinaimg.cn/large/ea23bca3gw1f2xaa39lemj20q50iqdpm.jpg)

- markdown简明语法
[http://ibruce.info/2013/11/26/markdown/](http://ibruce.info/2013/11/26/markdown/)

## 4.Hexo添加图片、音乐和视频
- **插入图片**：插入图片其实就是添加外部链接图片,这里使用上面插件可以获取地址。

1.这里可以使用一个谷歌插件**新浪微博图床**，简单好用的新浪微博图床,支持选择/拖拽/粘贴上传图片,并生成图片地址,HTML,UBB和Markdown等格式,支持浏览和删除历史记录。直接添加图片，生成地址。
![](http://ww1.sinaimg.cn/large/ea23bca3gw1f2xaj1y5myj20m10fajs2.jpg)
---
代码如下:

``` bash
![“图片描述”（可以不写）](“图片地址”)
```

举例：

``` bash
![说点什么](http://ww1.sinaimg.cn/large/ea23bca3gw1f2xaj1y5myj20m10fajs2.jpg)
```

2.添加本地图片
在D:\hexo\source目录下新建文件夹，命名为images或者其他你喜欢的名字，然后编辑你的md博文，插入下面的代码样式：

``` bash
![“图片描述”（可以不写）](/images/你的图片名字.JPG)
```
例如:

``` bash
![](/images/smile01.gif)
```

**注意：图片路径和图片名称必须为英文，否则不能显示图片。**

---

- **插入音乐:**比如网易云音乐，找到喜欢的歌曲，点击分享按钮，把里面的代码复制下来，直接粘贴到博文中即可。比如插入以下代码：

``` bash
iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="http://music.163.com/outchain/player?type=2&id=5406956&auto=0&height=66"></iframe>
```

效果如下：

<center>
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="http://music.163.com/outchain/player?type=2&id=5406956&auto=0&height=66"></iframe>
</center>

---

- **插入视频:** 视频也和音乐类似，先输入视频标题，回车换一行插入代码即可。

``` bash
Idina Menze和Caleb Hyles激情对唱Let It Go：

iframe 
	height=498 width=510 
	src="http://player.youku.com/embed/XNjcyMDU4Njg0" 
	frameborder=0 allowfullscreen>
</iframe>
```


效果如下：

Idina Menze和Caleb Hyles激情对唱Let It Go：

<center>
<iframe 
	height=498 width=510 
	src="http://player.youku.com/embed/XNjcyMDU4Njg0" 
	frameborder=0 allowfullscreen>
</iframe>
</center>