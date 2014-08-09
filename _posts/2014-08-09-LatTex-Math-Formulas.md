---
layout: articles_item
title: LaTex数学公式
author: chenguangqi
categories: [Math]
---


有三种环境使LaTex进入数学模式：数学，显示数学和方程式。数学环境是为了使公式中的文本正确显示。显示数学环境是为了使文本显示在自己的行。方程式环境与显示数学环境相同，除了在右边添加方程式的编号。

熟悉环境可以用在段落模式和行模式中，而显示数学和方程式只可以用在段落模式中。
由于数学和显示数学经常使用，所有他们有简单格式：

`\(...\)`替代`\begin{math}...\end{math}`

`\[...\]`替代`\begin{displaymath}...\end{displaymath}`

其实，数学环境通常还有更短的格式：

`$ ... $`替代`\(...\)`

`$$ ... $$`替代`\[...\]`

一些数学排版的构建块：

* 下标和上标
* 数学模式中的空格
* `\frac`排版分数
* `\sqrt`做平方根
* `\overline`,`\underline`
* `\overbrace`,`\underbrace`
* 各种数学符号

#### 下标和上标

`_`显示为下标，`^`显示为上标。

`$ a\_x+a\_y+a\_z $`： $ a\_x+a\_y+a\_z $

`$ a^1+b^2+c^3 $`：$ a^1+b^2+c^3 $

`$ x\_1^2+y\_1^2 $`：$ x\_1^2+y\_1^2 $

#### 数学模式中的空格

在数学环境中，LaTex忽略空格，自动加入适当的空格。如果想使用不同空格，可以使用下面的四个命令。

* `\;`
* `\:`
* `\,`
* `\!`

`  $ a\_x+a\_y+a\_z $ `： $ a\_x+a\_y+a\_z $

`$ \;a\_x+a\_y+a\_z $`： $ \\;a\_x+a\_y+a\_z $

`$ \:a\_x+a\_y+a\_z $`： $ \\:a\_x+a\_y+a\_z $

`$ \,a\_x+a\_y+a\_z $`： $ \\,a\_x+a\_y+a\_z $

`$ \!a\_x+a\_y+a\_z $`： $ \\!a\_x+a\_y+a\_z $


#### `\frac`排版分数

$\frac{13}{100}$


#### `\sqrt`做平方根

$\sqrt[3]{x+y}$

#### `\overline`,`\underline`

$\overline{x}$

$\underline{y}$

#### `\overbrace`,`\underbrace`

$\overbrace{x}$

$\underbrace{y}$

#### 各种数学符号

Tex提供几乎任何你可能需要数学符号，可以使用命令生成它们只有在数学模式。
例如,如果你的来源包括`$\pi$`，你会得到$\pi$。这是部分的列表:

* `\cdots`行居中水平省略号
* `\ddots`对角线省略号
* `\ldots`一般水平省略号
* `\vdots`垂直省略号
* 希腊字母，从`alpha`到`\omega`
* 各种箭头，如：`\leftarrow`(单)，`\Leftarrow`(双)，`\longleftarrow`(长), `\uparrow`, `\Longleftarrow`以及其他的组合命令
* `\cap`, `\cup`
* 数学函数: `\sin`,`\cos`,`\ln`,`\log`,`\tan`等等。

一些例子：

省略号: $\cdots \ddots \ldots \vdots$

希腊字母: $\alpha \omega$

短箭头: $\leftarrow \Leftarrow \uparrow \downarrow$

长箭头: $\longleftarrow \Longleftarrow$

交集与并集: $\cap \cup$

数学函数:

\begin{eqnarray}
\cos^2\theta \& = \& \cos^2 \theta - \sin^2 \theta \\\\
\& = \& 2 \cos^2 \theta - 1
\end{eqnarray}

**注意**: 其中\&是对其点，表示在此对齐。

平移矩阵：
$ \left \( \begin{matrix}
1 \& 0 \& 0 \& x \\\\
0 \& 1 \& 0 \& y \\\\
0 \& 0 \& 1 \& z \\\\
0 \& 0 \& 0 \& 1
\end{matrix} \right \)$

缩放矩阵：
$ \left \( \begin{matrix}
x \& 0 \& 0 \& 0 \\\\
0 \& y \& 0 \& 0 \\\\
0 \& 0 \& z \& 0 \\\\
0 \& 0 \& 0 \& 1
\end{matrix} \right \)$

旋转绕X轴矩阵: 
$ \left \( \begin{matrix}
1 \& 0 \& 0 \& 0 \\\\
0 \& \cos(\theta) \& \sin(\theta) \& 0 \\\\
0 \& -\sin(\theta) \& \cos(\theta) \& 0 \\\\
0 \& 0 \& 0 \& 1
\end{matrix} \right \)$

旋转绕Y轴矩阵: 
$ \left \( \begin{matrix}
\cos(\theta) \& 0 \& -\sin(\theta) \& 0 \\\\
0 \& 1 \& 0 \& 0 \\\\
\sin(\theta) \& 0 \& \cos(\theta) \& 0 \\\\
0 \& 0 \& 0 \& 1
\end{matrix} \right \)$

旋转绕Z轴矩阵: 
$ \left \( \begin{matrix}
\cos(\theta) \& -\sin(\theta) \& 0 \& 0 \\\\
\sin(\theta) \& \cos(\theta) \& 0 \& 0 \\\\
0 \& 0 \& 1 \& 0 \\\\
0 \& 0 \& 0 \& 1
\end{matrix} \right \)$

### 参考文档

1. [LaTex Math Formulas](http://www.personal.ceu.hu/tex/math.htm)
2. [LaTeX Math Symbols](http://inform.bwi.unibw-muenchen.de/infoc/info.aspx?f=1571239)
3. [Tex Reference](http://web.ift.uib.no/Teori/KURS/WRK/TeX/latexsource.html)
