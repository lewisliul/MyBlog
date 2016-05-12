# Paperbox

 [**Demo**](http://sun11.me)
 
 ![](http://77g58m.com1.z0.glb.clouddn.com/responsive-design.png)

A theme modified based on [Landscape-plus](https://github.com/xiangming/landscape-plus) theme based on Landscape theme, with some papers and a paperbox. The motivation is for my personal use. Hexo version is 3.1.1, nodejs v4.2.6.

### Feature

* **Paper-like Appearance** - Make you focus on the contents.
* **Responsive Design** - min-width is 320px, ensure the normal display for iPhone 4-5s.
* **Bug fixs** - fixed many bugs, including paginator in archive, share button, mobile nav, etc.

### Modifications on Landscape-plus

* **Integrated Swiftype Search** - You need to specify `swiftype_key` in `_config.yml` to enable it.
* **Removed Bdshare** - There are conflicts between bdshare and swiftype search, for example, the `more` button in bdshare is invalid when swiftype is enabled.
* **Landscape share Enhancement** - Replaced Pinterest with Linkedin, added 4 Chinese SNS share buttons.
* **Removed mathjax module** - To fix the overflow problem in mathjax equation, [hexo-math](https://github.com/akfish/hexo-math) plugin is recommended.
* **Table of Contents (TOC)** - Should config `toc: true` in .md files, serial numbers of titles is not displayed by default.

### Browser Support

* **IE 9+ and the current versions of other browsers**

---
 
基于基于Landscape主题的Landscape-Plus主题由本人修改的主题，主要目的是自己使用，Hexo版本是3.1.1，nodejs 4.2.6

### 主题特性

* **纸张外观** - 使你专注于文章内容
* **响应式设计** - 最小宽度为320px，确保了在iPhone 4到5s的320x*分辨率的正常显示
* **诸多bug修复** - 修复了包括archive分页显示，原主题在分享按钮、mobile nav界面、分页条等的诸多细节bug

### 对Landscape-Plus的功能修改

* **增加Swiftype站内搜索** - 需在配置文件中设置`swiftype_key`
* **移除百度分享** - 因为不明原因（可能是全局变量？）与Swiftype冲突，先执行的js功能正常，后执行的js会出问题，如百度分享的`更多`按钮点击无效
* **Landscape原主题分享增强** - 将Pinterest更改为Linkedin，增加四个国内社交网站：微博，人人，QQ空间，微信
* **移除mathjax模块** - 为了解决mathjax公式显示溢出问题，在主题自带的mathjax修改css没成功，但是使用hexo-math插件修改css成功，所以推荐使用hexo-math插件，主题内已包含修正的CSS样式
* **增加文章目录（TOC）显示** - 需在文章中设置`toc: true`，默认不显示编号

### 浏览器支持

* **IE9+和目前版本的其它浏览器**
