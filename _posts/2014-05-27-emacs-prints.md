---
layout: articles_item
title:  "Emace Print函数"
author: chenguangqi
categories: [Emacs]
---


这是一个关于emacs lisp打印功能的简短教程。如果你不知道elisp，
首先看看[Emacs Lisp Basic](http://ergoemacs.org/emacs/elisp_basics.html).

## 用message简单打印
`message`是最基本的打印函数，这是一个例子：

{% highlight lisp linenos %}
; printing
(message "hi")

; printing variable values
(message "Her age is: %d " 16)              ;%d is for number
(message "Her name is: %s " "Vicky")        ;%s is for string
(message "Her mid init is: %c " 86)         ;%c is for character in ASCII code
{% endhighlight %}

在`*Mssages*`缓冲区中可以看到message的输出。你可以使用`view-echo-area-messages`查看这个
缓冲区。

`*Message`缓冲区是一个特殊的缓存区，因为它是`emacs`任何消息的输出位置。

例如，当缓冲区达到一定大小它会自动截断顶部的项目。还有，当一条消息重复多次时，它自动
压缩重复的行。如果一个消息是太长的一行，它自动的截断。这是一个例子。

``` lisp
(setq xx 1)
(while (< xx 20)
  (message "yay")
  (setq xx (1+ xx)))
(switch-to-buffer "*Message*")
```

## 打印到自己的缓冲区
当写一个批处理的elisp脚本时，最好是打印到自己的缓冲区中。

例如，假设你有一个批处理elisp脚本，查找并替换一个目录中的所有文件。为了遍历每一个文件，
它打印出文件的路径。如果你使用`message`，它打印到`*Message*`缓冲区中，如果多余一百行，
自动回到顶部。还有它可能把你脚本的输出和其他活动的输出搅和在一起。

这是一个打印到自己缓冲区的例子

``` lisp
(require 'find-lisp)
(with-output-to-temp-buffer "*my output*")
  (mapc 'my-process-file (find-lisp-find-files "~/" "\\.html$"))
  (princ "Done.\n")
  (switch-to-buffer "*my output*"))
```

在上面的例子中，任何需要打印的文件被输出到你的临时缓冲区。

## print和prin1函数
Elisp提供`print`函数，这是基本用法

``` lisp
(print OBJECT)
```

`OBJECT`是任何要打印的elisp对象。它可以是任何lisp数据类型，如 string、number、list、
buffer、frame等。

还有一个`prin1`函数，这个函数与`print`一样，除了不添加一个换行。

## format控制打印细节
一个lisp对象怎样转换为字符串由`format`函数完成。它带有一个输入字符串，和一些lisp对象的其他
参数，输出一个字符串。调用`describe-function`来查看它的在线文档。这是文档的部分摘录：


``` lisp
(format STRING &rest OBJECTS)

Format a string out of a format-string and arguments.
The first argument is a format control string.
The other arguments are substituted into it to make the result, a string.

The format control string may contain %-sequences meaning to substitute
the next available argument:

%s means print a string argument.  Actually, prints any object, with `princ'.
%d means print as number in decimal (%o octal, %x hex).
%X is like %x, but uses upper case.
%e means print a number in exponential notation.
%f means print a number in decimal-point notation.
%g means print a number in exponential notation
   or decimal-point notation, whichever uses fewer characters.
%c means print a number as a single character.
%S means print any object as an s-expression (using `prin1').

```

例如，如果你想打印yyy-mm-dd这样日期格式，并且带有前导**0**，你可以这样做

``` lisp
;; format yyyy-mm-dd, ISO 8601 format
(print (format "%40d-%2d-%02d" 2012 4 10))
```

## princ人类友好输出
`princ`和`prin1`很相似，不同之处在于它是人类友好的。例如，它不打印字符串的分割符。

``` lisp
(princ '("x" "y")) ; result display is (x y)
(prin1 '("x" "y")) ; result display is ("x" "y")
```
