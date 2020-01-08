---
title: 本机利用sublime text 3编写markdown博客
date: 2017-04-22 23:39:21
tags: 
- sublime text 3
- markdown
categories: 
- sublime text 3
---

# 环境
操作系统：win10

# 安装插件

一共需要安装两个插件：  
① [MarkdownEditing](https://github.com/SublimeText-Markdown/MarkdownEditing)  
② [OmniMarkupPreviewer](https://github.com/timonwong/OmniMarkupPreviewer)

---

## 安装方法

在`sublime text 3`中`ctrl+shift+p`,在跳出的输入框里输入`install package`,
等待一会后，在输入框里输入插件名称，选择我们想要的插件就可以啦。


在这里需要说明下，我安装完插件`MarkdownEditing`后，光标显示的有问题。
![](http://oov1xablp.bkt.clouddn.com/blog/%E5%85%89%E6%A0%87.png)

## 快捷键

`markdownediting`主要的作用是语法样式优化：可以提供了一些快捷键：
先给出[官网地址](https://github.com/SublimeText-Markdown/MarkdownEditing)
windows/linux:

<style>
table td:nth-child(1) {
    white-space: nowrap; 
}
</style>

|快捷键|描述|
|--------|--|
|ctrl + win +v | Creates or pastes the contents of the clipboard as an inline link on selected text.  创建或粘贴剪贴板的内容作为选定文本的内联链接。|
|ctrl + win +R | Creates or pastes the contents of the clipboard as a reference link.创建或粘贴剪贴板的内容作为参考链接。|
|shift + win +k|Creates or pastes the contents of the clipboard as an inline image on selected text.在选定文本上创建或粘贴剪贴板的内容为内联图像。|
|alt + B、alt + I|分别为，加粗和斜体|
|ctrl + 1..6 |对于选中的内容前加对应个数#,即对选中内容字体大小设置为h1~h6;|
|alt + shift + 6|插入脚注|

## 小知识

上述的表格快捷键那一栏本来是不能一行显示的，我在表格前添加了如下代码：
```javascript
## 表格第一列单元格内容不换行，多数情况下表格首列不应该被压缩换行

table td:nth-child(1) {
    white-space: nowrap; 
}
```

## 参考链接

[Markdown 表格样式调整与自适应优化](http://moxfive.xyz/2016/03/04/markdown-table-style/):http://moxfive.xyz/2016/03/04/markdown-table-style/