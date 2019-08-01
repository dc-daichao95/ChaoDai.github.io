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
+ 加粗: 要加粗的文字左右分别用两个*号包起来

+ 斜体: 要倾斜的文字左右分别用一个*号包起来

+ 斜体加粗: 要倾斜和加粗的文字左右分别用三个*号包起来

+ 删除线: 要加删除线的文字左右分别用两个~~号包起来

举个例子：

```
**加粗**
*斜体*
***斜体加粗***
~~删除线~~
<font size="2">字体大小</font>
<font color="green">字体颜色</font>
<font face="SimHei">字体格式</font>
```

效果：
>**加粗**
  *斜体*
  ***斜体加粗***
  ~~删除线~~
  <font size="2">字体大小</font>
  <font color="green">字体颜色</font>
  <font face="SimHei">字体格式</font>

附常见的中英文字体：

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

| Font | Face |
| :-: | :-: |
| <font face="Helvetica">Helvetica</font> | Helvetica |
| <font face="Arial">Arial</font> | Arial |
| <font face="Verdana">Verdana</font> | Verdana |
| <font face="Impact">Impact</font> | Impact |
| <font face="Times New Roman">Times New Roman</font> | Times New Roman |



-----

### 3. 引用
在引用的文字前加>，引用也可以嵌套，如加>>或者>>> 。

举个例子：

```
>这是引用的内容
>>这是引用的内容
>>>这是引用的内容
```

效果：

>这是引用的内容
>>这是引用的内容
>>>这是引用的内容

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
![图片alt](图片地址 "图片title")
```
图片alt就是显示在图片下面的文字，相当于对图片内容的解释。图片title是图片的标题，当鼠标移到图片上时显示的内容（title可加可不加）。

举个例子：

```
![Markdown](https://raw.githubusercontent.com/dc-daichao95/dc-daichao95.github.io/hexo_bak/static/img/1280px-Markdown-mark.svg.png "Markdown")
```

效果：

![图片_Markdownalt](https://raw.githubusercontent.com/dc-daichao95/dc-daichao95.github.io/hexo_bak/static/img/1280px-Markdown-mark.svg.png "Markdown")

---

### 6. 超链接
格式如下
```
[超链接名](超链接地址 "超链接title")
```
title可加可不加
  举个例子：
```
[GitHub](https://github.com/)
[Google](https://www.google.com)
```
  效果：
>[GitHub](https://github.com/)
  [Google](https://www.google.com)

### 7. 列表
#### 无序列表
无序列表用 - + * (注意空格)。
  举个例子：
```
- The Fool
+ Star Platinum
* Golden Exp
```
  效果：
- The Fool
+ Star Platinum
* Golden Exp

#### 有序列表
数字加点（注意空格）。
  举个例子：
```
1. King Crimson
2. Aerosmith
```
  效果：
1. King Crimson
2. Aerosmith

#### 列表嵌套
上一级和下一级之间敲三个空格即可。

----

### 8. 表格
格式：
```
| 表头 | 表头 | 表头 | 表头 |
| - | :-: | -: | :-|
| 内容 | 内容 | 内容 | 内容 |
| 内容 | 内容 | 内容 | 内容 |
:-:居中
-:右对齐
:-左对齐
```

举个例子：
```
| Char | Stand | Power | Speed |
| - | :-: | -: | :- |
| Kujo Jotaro | Star Platinum | A | A |
| Kira Yoshikage | Killer Queen| A | B |
| Higashikata Josuke | Crazy Diamond | A | A |
```
  效果:

| Char | Stand | Power | Speed |
| - | :-: | -: | :- |
| Kujo Jotaro | Star Platinum | A | A |
| Kira Yoshikage | Killer Queen| A | B |
| Higashikata Josuke | Crazy Diamond | A | A |

注意：
  表格前一行需空出。

-----

### 9. 代码
#### 单行代码
代码之间分别用一个反引号`包起来。
  举个例子：
```
`内容`
```
效果：
  `内容`

#### 代码块
代码之间分别用三个反引号包起来，三个反引号单独占一行(头反引号加上语言可以实现语法高亮）。
  举个例子：
```
(```cpp
     加个了一个括号防止转义
     #include  <stdio.h>
     int main(void)`
     {
         printf("Hello world\n");
     }
(```
```
效果：

```cpp
   #include  <stdio.h>
   int main(void)
   {
       printf("Hello world\n");
   }
```

----

## 基于atom打造顺手的Markdown编辑器
### 1. 安装Atom
下载安装[Atom](https://atom.io/)

### 2. 有关Markdown的拓展
+ markdown-preview-plus
markdown-preview-plus对markdown-preview做了功能扩展和增强（需关闭markdown-preview）。
   + 持预览实时渲染。(Ctrl + Shift + M)
   + 支持Latex公式。(Ctrl + Shift + X)

+ markdown-table-editor
更方便的编辑Markdown的表格

+ markdown-themeable-pdf、pdf-view
提供pdf导出和预览功能
