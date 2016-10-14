---
layout: page
title: Makrdowm
description:
header-img: "img/post_bg/11.jpg"
tags:
  - Makrdowm语法
---

目录
  - 1.[Markdown特点](#1)
  - 2.[Markdown 语法](#2)
    * [列表](#2.1)
    * [代码块](#2.2)
    * [强调](#2.3)
    * [链接](#2.4)
    * [图片](#2.5)
    * [锚点](#2.6)
    * [自动连接](#2.7)
    * [注脚](#2.8)
  - 3.[后记](#3)


# Markdown特点{#1}
>We believe that writing is about content, about what you want to say – not about fancy formatting.       — Ulysses for Mac

Markdown旨在简洁高效的文本编辑,让你专注于文字的编写，而不用花费太多时间在在文字排版上；正如Markdown作者所说：
>Markdown is intended to be as easy-to-read and easy-to-write as is feasible.

现在市面上的mardown插件很多，用于绘制流程图、时序图、公式等复杂的功能，现在想想这些是不是和Markdown的初衷相悖了呢！不管怎么样，众口难调，自己使用开心、满意就行。我心中的Markdown是简洁、高效的。

# Markdown 语法{#2}

###### 列表{#2.1}
  列表分为有序列表和无序列表，有序列表采用数字形式紧接着一个英文句点；无序列表使用星号、加号或减号作为列表标记。
###### 代码块{#2.2}
有两种方式：1、使用缩进Tab；2、使用反引号包裹代码；
要缩进多行代码，使用缩进把多行代码进行缩进，或者使用\`\`\`multi-lines code\`\`\`实现对多方代码的包裹。
###### 强调{#2.3}
  1. \*斜体\*  \_斜体\_
  2. \*\*粗体\*\*
  3. \~\~删除线\~\~
  4. 分割线，使用三个以上的星号或减号

###### 链接{#2.4}
  1. [link text][id] 例如：[markdown英文语法][1]
  2. \[link text\]\(URL "hint"\)

###### 图片: ![Alt text][id]{#2.5}
Makrdowm无法指定图片的高宽，需要的话使用img标签实现。

###### 锚点{#2.6}
  锚点时页内超链接，时链接本文档内部的某些元素，实现当前页面中的跳转，markdown只支持标题后插入锚点；常用锚点实现文章目录。

  **插入锚点**：\#目录{#category_index}

  **引用锚点**：\[目录\]\(#category_index\)

###### 自动连接: &lt;EMAIL|URL&gt;{#2.7}
  1. <gaowenwencn@163.com>
  2. <http://www.govind.space>

###### 注脚{#2.8}
在需要添加注脚的文字后面加上注脚的格式：\[\^注脚名字\]；然后再文本的任意位置添加注脚，注脚前指定对应的注脚名格式：\[\^注脚名字\]: 注脚内容。

***
# 后记{#3}
  

[1]: http://daringfireball.net/projects/markdown/syntax          "markdown英文语法"
[2]: http://wowubuntu.com/markdown/#list "markdown中文语法"
[3]: www.govind.space/img/home_bg/20.jpg "网站图片引用"
