---
title: Markdown 简明语法手册
tags:
  - Markdown
katex: true
abbrlink: bff83912
date: 2019-05-28 21:55:56
---

# Markdown 简明语法手册
{% note info %}
什么是Markdown？Markdown是一款轻量级的文本编辑语言，使HTML语言的进阶和扩充，可以通过简单、易读易写的文本格式生成结构化的HTML文档。程序员必备！！！干掉Word不是梦！（我不是认真的）
{% endnote %}

##  斜体和粗体

使用 * 和 ** 表示斜体和粗体。

示例：

这是 *斜体*，这是 **粗体**。

## 分级标题

使用 === 表示一级标题，使用 --- 表示二级标题。

示例：

```
这是一个一级标题
============================

这是一个二级标题
--------------------------------------------------

### 这是一个三级标题
```

你也可以选择在行首加井号表示不同级别的标题 (H1-H6)，例如：# H1, ## H2, ### H3，#### H4。

## 外链接

使用 \[描述](链接地址) 为文字增加外链接。

示例：

这是去往 [本人博客](https://bigouo.github.io) 的链接。

## 无序列表

使用 *，+，- 表示无序列表。

示例：

- 无序列表项 一
- 无序列表项 二
- 无序列表项 三

## 有序列表

使用数字和点表示有序列表。

示例：

1. 有序列表项 一
2. 有序列表项 二
3. 有序列表项 三

## 文字引用

使用 > 表示文字引用。

示例：

> 野火烧不尽，春风吹又生。

## 行内代码块

使用 \`代码\` 表示行内代码块。

示例：

让我们聊聊 `html`。

## 代码块

使用 四个缩进空格 表示代码块。

示例：

    这是一个代码块，此行左侧有四个不可见的空格。

## 插入图像

使用 \!\[描述](图片链接地址) 插入图像。

示例：

![我的头像](https://s2.ax1x.com/2019/07/14/ZIPoon.jpg)

# Markdown 高阶语法手册

## 内容目录

在段落中填写 `[TOC]` 以显示全文内容的目录结构。

[TOC]

## 标签分类

在编辑区任意行的列首位置输入以下代码给文稿标签：

标签： 数学 英语 Markdown

或者

Tags： 数学 英语 Markdown

## 删除线

使用 ~~ 表示删除线。

~~这是一段错误的文本。~~

## 注脚
>部分博客支持哦

使用 [^keyword] 表示注脚。

这是一个注脚[^footnote]的样例。

这是第二个注脚[^footnote2]的样例。

## LaTeX 公式

$ 表示行内公式： 

质能守恒方程可以用一个很简洁的方程式 $E=mc^2$ 来表达。

$$ 表示整行公式：

$$\sum_{i=1}^n a_i=0$$

$$f(x_1,x_x,\ldots,x_n) = x_1^2 + x_2^2 + \cdots + x_n^2 $$

$$\sum^{j-1}_{k=0}{\widehat{\gamma} _ {kj} z_k} $$

访问 [Katex](https://katex.org/docs/supported.html) 参考更多使用方法。
Katex  [公式编号功能](https://katex.org/docs/supported.html#annotation)

## 加强的代码块

支持四十一种编程语言的语法高亮的显示，行号显示。

非代码示例：

```bash
$ sudo apt-get install vim-gnome
```

Python 示例：

```python
@requires_authorization
def somefunc(param1='', param2=0):
    '''A docstring'''
    if param1 > param2: # interesting
        print 'Greater'
    return (param2 - param1 + 1) or None

class SomeClass:
    pass

>>> message = '''interpreter
... prompt'''
```

C++ 示例：

``` cpp
/*
* 这是注释
* This is an annotation
*/
int function(n)
{
    int a = 1, b = 1;
    int tmp;
    while (--n >= 0) 
    {
        tmp = a;
        a += b;
        b = tmp;
    }
  return a;
}

cout << function(5) << endl;
```
更多效果自行体验，只需把语言标签换一下即可
## 饼图
### 示例

```
pie
    title Key elements in Product X
    "Calcium" : 42.96
    "Potassium" : 50.05
    "Magnesium" : 10.01
    "Iron" :  5
```

{% mermaid %}
pie
    title Key elements in Product X
    "Calcium" : 42.96
    "Potassium" : 50.05
    "Magnesium" : 10.01
    "Iron" :  5
{% endmermaid %}

### 更多语法参考：[饼图语法参考](https://mermaid-js.github.io/mermaid/#/pie)

## 序列图
>部分博客不支持
### 示例 1

```
sequenceDiagram
    participant John
    participant Alice
    Alice->>John: Hello John, how are you?
    John-->>Alice: Great!
```

{% mermaid %}
sequenceDiagram
    participant John
    participant Alice
    Alice->>John: Hello John, how are you?
    John-->>Alice: Great!
{% endmermaid %}

### 示例 2

```seq
Title: Here is a title
A->B: Normal line
B-->C: Dashed line
C->>D: Open arrow
D-->>A: Dashed open arrow
```

### 更多语法参考：[序列图语法参考](https://mermaid-js.github.io/mermaid/#/sequenceDiagram)

## 甘特图

甘特图内在思想简单。基本是一条线条图，横轴表示时间，纵轴表示活动（项目），线条表示在整个期间上计划和实际的活动完成情况。它直观地表明任务计划在什么时候进行，及实际进展与计划要求的对比。

```
gantt
    title A Gantt Diagram
    dateFormat  YYYY-MM-DD
    section Section
    A task           :a1, 2014-01-01, 30d
    Another task     :after a1  , 20d
    section Another
    Task in sec      :2014-01-12  , 12d
    another task      : 24d
```

{% mermaid %}
gantt
    title A Gantt Diagram
    dateFormat  YYYY-MM-DD
    section Section
    A task           :a1, 2014-01-01, 30d
    Another task     :after a1  , 20d
    section Another
    Task in sec      :2014-01-12  , 12d
    another task      : 24d
{% endmermaid %}

### 更多语法参考：[甘特图语法参考](https://mermaid-js.github.io/mermaid/#/gantt)

## Mermaid 流程图

```
graph TD
    A[Start] --> B{Is it?};
    B -->|Yes| C[OK];
    C --> D[Rethink];
    D --> B;
    B ---->|No| E[End];
```

{% mermaid %}
graph TD
    A[Start] --> B{Is it?};
    B -->|Yes| C[OK];
    C --> D[Rethink];
    D --> B;
    B ---->|No| E[End];
{% endmermaid %}

### 更多语法参考：[Mermaid 流程图语法参考](https://mermaid-js.github.io/mermaid/#/flowchart)

## 表格支持

| 项目        | 价格   |  数量  |
| --------   | -----:  | :----:  |
| 计算机     | \$1600 |   5     |
| 手机        |   \$12   |   12   |
| 管线        |    \$1    |  234  |


## 定义型列表

名词 1
:   定义 1（左侧有一个可见的冒号和四个不可见的空格）

代码块 2
:   这是代码块的定义（左侧有一个可见的冒号和四个不可见的空格）

        代码块（左侧有八个不可见的空格）

## Html 标签

本站支持在 Markdown 语法中嵌套 Html 标签，譬如，你可以用 Html 写一个纵跨两行的表格：

    <table>
        <tr>
            <th rowspan="2">值班人员</th>
            <th>星期一</th>
            <th>星期二</th>
            <th>星期三</th>
        </tr>
        <tr>
            <td>李强</td>
            <td>张明</td>
            <td>王平</td>
        </tr>
    </table>


<table>
    <tr>
        <th rowspan="2">值班人员</th>
        <th>星期一</th>
        <th>星期二</th>
        <th>星期三</th>
    </tr>
    <tr>
        <td>李强</td>
        <td>张明</td>
        <td>王平</td>
    </tr>
</table>

## 内嵌图标

可以在文档中输入

    <i class="icon-weibo"></i>

即显示微博的图标： <i class="icon-weibo icon-2x"></i>

替换 上述 `i 标签` 内的 `icon-weibo` 以显示不同的图标，例如：

    <i class="icon-renren"></i>

即显示人人的图标： <i class="icon-renren icon-2x"></i>

更多的图标和玩法可以参看 [Font Awesome（图标）](https://fontawesome.com/) 或者[Font Awesome（图标字体库）](http://fontawesome.dashgame.com/)官方网站。

## 待办事宜 Todo 列表

使用带有 [ ] 或 [x] （未完成或已完成）项的列表语法撰写一个待办事宜列表，并且支持子列表嵌套以及混用Markdown语法，例如：

    - [ ] **改进自己的博客网站**
        - [ ] 改善博客网站的配色、图片，使其更美观
        - [ ] 做好博客文章的分类、分标签工作
        - [ ] 做一个关于自己的界面
        - [x] 对文章进行美化排版
            - [x] 做到段落层次清晰明确
            - [x] 控制图文比例，避免图片过少，太枯燥，亦或图片过多太冗杂
    - [ ] **日后打算**
        - [ ] 争取做到一个月一篇博文
        - [ ] 记录自己的学习经历和经验
        - [x] 分享生活

对应显示如下待办事宜 Todo 列表：
        
- [ ] **改进自己的博客网站**
    - [ ] 改善博客网站的配色、图片，使其更美观
    - [ ] 做好博客文章的分类、分标签工作
    - [ ] 做一个关于自己的界面
    - [x] 对文章进行美化排版
        - [x] 做到段落层次清晰明确
        - [x] 控制图文比例，避免图片过少，太枯燥，亦或图片过多太冗杂
- [ ] **日后打算**
    - [ ] 争取做到一个月一篇博文
    - [ ] 记录自己的学习经历和经验
    - [x] 分享生活
      
        
[^footnote]: 这是一个 *注脚* 的 **文本**。

[^footnote2]: 这是另一个 *注脚* 的 **文本**。

