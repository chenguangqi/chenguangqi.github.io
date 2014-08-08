---
layout: post
title: MathJax的使用
mathjax: true
---

使用MathJax, 编写漂亮的数学公式。

### 引入MathJax项目

在github的用户GitHub Pages仓库中加入MathJax

```bash
git submodule add https://github.com/mathjax/MathJax.git mathjax
git submodule init
git commit -a -m 'support MathJax'
```

### 在有公式的博文中链接MathJax


在\_include目录中新建文件mathjax.html，内容如下：

```html
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}
  });
</script>
<script type="text/javascript" src="/mathjax/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
```

在\_include目录中head.html文件的`</head>`前添加如下内容(请删除`%`前的`\`):

```
<!-- MathJax -->
{\% include mathjax.html %}
```

这样就可以使用编写数学公式了。MathJax的是使用方法，参见最后的参考文档。


下面是一些简单测试。

矩阵的表示:

\\[
\\begin{matrix}
x & y \\\\
z & v
\\end{matrix}
\\]

\\(\\Pi\\)

\\(\\pi\\)

### 关于`\`的问题:

在有些系统上，可能需要使用两个`\`来表示一个`\`，例如：

```
\\[
\\left \\begin{array}{cc}
a & b \\\\
c & d
\\end{array} \\right
\\]
```

显示效果：

\\[
\\begin{array}{cc}
a & b \\\\
c & d
\\end{array}
\\]


**注意**:带有数学公式的网页加载可能会变慢。

#### 参考文档

* [MathJax文档][1]
* [MathJax支持的Tex/Latex格式][2]


[1]: http://mathjax-chinese-doc.readthedocs.org "MathJax文档"
[2]: http://mathjax-chinese-doc.readthedocs.org/en/latest/tex.html#tex-support "MathJax支持的Tex/Latex格式"
