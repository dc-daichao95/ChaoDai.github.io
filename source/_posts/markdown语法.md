---
title: Markdown语法
date: 2018-07-29 20:18:40
tags:
---
## 写在开头
维基百科对[Markdown](https://zh.wikipedia.org/wiki/Markdown)的介绍如下：

>Markdown是一种轻量级标记语言，创始人为約翰·格魯伯（英语：John Gruber）。它允许人们“使用易读易写的纯文本格式编写文档，然后转换成有效的XHTML（或者HTML）文档”。这种语言吸收了很多在电子邮件中已有的纯文本标记的特性。

由于个人对一些文档和基于Hexo的博客的需要（相较于LaTeX来写日常的文档更为简单好用），记录一下基本的语法（避免遗忘）。

+ 优点： 纯文本（只要支持Markdown都能获得一样的编辑效果），排版方便而且语法简单；
+ 缺点： 有些平台不支持Markdown编辑模式。

## 基本语法
### 1. 标题
在想要设置为标题的文字前面加#来表示，一个#是一级标题，二个#是二级标题，以此类推。支持六级标题（标准语法一般在#后跟个空格再写文字）。

举个例子：
```
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```
效果：
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题

----

### 2. 字体相关
基本的字体可以进行**加粗**，*斜体*，***斜体加粗***，~~删除线~~，复杂的字体（<font size="2">字体大小</font>，<font color="green">字体颜色</font>，<font face="SimHei">字体格式</font>）需要嵌入html。
+ 加粗

  要加粗的文字左右分别用两个*号包起来

+ 斜体

  要倾斜的文字左右分别用一个*号包起来

+ 斜体加粗

  要倾斜和加粗的文字左右分别用三个*号包起来

+ 删除线

  要加删除线的文字左右分别用两个~~号包起来

举个例子：

```
**加粗** *斜体* ***斜体加粗*** ~~删除线~~ <font size="2">字体大小</font> <font color="green">字体颜色</font> <font face="SimHei">字体格式</font>
```

效果：

**加粗** *斜体* ***斜体加粗*** ~~删除线~~ <font size="2">字体大小</font> <font color="green">字体颜色</font> <font face="SimHei">字体格式</font>

#### 附常见的中英文字体：

中文

| 字体 | 名字 |
| :-: | :-: |
| <font face="PMingLiU">新细明体</font> | PMingLiU |
| <font face="MingLiU">细明体</font> | MingLiU |
| <font face="DFKai-SB">标楷体</font> | DFKai-SB |
| <font face="SimSun">宋体</font> | SimSun |
| <font face="FangSong">仿宋</font> | FangSong |
| <font face="KaiTi">楷体</font> | KaiTi |
| <font face="SimHei">黑体</font> | SimHei |
| <font face="Microsoft YaHei">微软雅黑体</font> | Microsoft YaHei |

英文

| Font | Face |
| :-: | :-: |
| <font face="Helvetica">Helvetica</font> | Helvetica |
| <font face="Arial">Arial</font> | Arial |
| <font face="Lucida Family">Lucida Family</font> | Lucida Family |
| <font face="Verdana">Verdana</font> | Verdana |
| <font face="Impact">Impact</font> | Impact |
| <font face="Times New Roman">Times New Roman</font> | Times New Roman |
| <font face="Microsoft Sans Serif">Microsoft Sans Serif</font> | Microsoft Sans Serif |
| <font face="Tahoma">Tahoma</font> | Tahoma |


-----

### 3. 引用
在引用的文字前加>，引用也可以嵌套，如加>>或者>>> 。

举个例子：

```
>这是引用的内容
>>这是引用的内容
>>>>>>>>>>这是引用的内容
```

效果：

>这是引用的内容
>>这是引用的内容
>>>>>>>>>>这是引用的内容

----

### 4. 分割线
三个或者三个以上的 - 或者 * 。

举个例子：

```
***
---
```

效果：

***
---
二者显示效果是一样

----

### 5. 插入图片

格式如下
```
![图片alt](图片地址 ''图片title'')
```
图片alt就是显示在图片下面的文字，相当于对图片内容的解释。图片title是图片的标题，当鼠标移到图片上时显示的内容（title可加可不加）。

![图片alt]( ''图片title'')


## 基于atom打造顺手的Markdown编辑器
