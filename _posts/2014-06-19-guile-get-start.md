---
layout: post
title: Guile 入门
---

***

## 基本类型

* 字符(character)
* 字符串(string)
* 数字(number)
* 过程(procedure)

## 复合类型

* 列表(list)
* 点对(pair)
* 向量(vector)
* 多维数组(multi-dimensional array)
* 自定义类型

## 变量

变量的类型是不固定的，一个变量是一个位置的名称。

定义变量：

```scheme
(define variable-name value)
```

修改变量的值：

```scheme
(set! variable-name new-value)
```

## 定义过程

### 使用lambda

```scheme
(lambda (name address) expression ...)
```

例如：

```scheme
(lambda (name address)
  (string-append "Name=" name ":Address=" address))
```

过程调用：

```scheme
((lambda (name address)
   (string-append "Name=" name ":Address=" address))
 "FSF"
 "Cambridge"))
```

定义过程变量

```scheme
(define make-combined-string
  (lambda (name address)
    (string-append "Name:" name ":Address=" address)))
```

使用变量调用过程

```scheme
(make-combined-string "FSF" "Cambridge")
```

### 使用define

```scheme
(define (name [arg1 [arg2 ...]])
  expression ...)
```

上面对应的lambda格式

```scheme
(define name
  (lambda ([arg1 [arg2 ...]])
    expression ...))
```

其他几种过程的定义(用lambda表示)

```scheme
(lambda (arg1 ... . args) expression )
(lambda args expression ...)
```

对应的define表示

```scheme
(define (name arg1 ... . args) expression ...)
(define (name . args) expression ...)
```

**注意:**三个连续的点(...)表示省略，一个点(.)用于表示点对。

## 求值的结果有两种类型

* 表达式的值
* 副作用(side effect)

### 求值过程的描述

* scheme程序由表达式序列组成
* scheme解释器按顺序一个一个的对这些表达式求值
* 表达式有这几种
    * 字面数据(如：数字和字符串等)
    * 变量名
    * 过程调用
    * scheme的特殊语法

### 过程调用的格式

```scheme
(procedure [arg1 [arg2 ...]])
```

其中`procedure`表达式的求值结果值必须是一个过程。

### 对过程调用的求值处理

* 分别对表达式`procedure`,`arg1`,`arg2`求值。
* 以`procedure`表达式的值为过程，以`arg1`,`arg2`等表达式的值为参数，进行调用。

## 尾递归(tail recursive)

不消耗stack空间和其他资源，可以进大数据和长时间计算。

例子:

```scheme
(define (foo n)
  (diplay n)
  (newline)
  (foo (1+ n)))
```

另一个例子:

```scheme
(define (my-last lst)
  (if (null? (cdr lst))
      (car lst)
	  (my-last (cdr lst))))
```

实现正确尾递归的位置

* `and`的最后一个表达式
* `begin`的最后一个表达式
* `case`的最后一个表达式
* `cond`的最后一个表达式
* `do`的最后一个表达式
* `if`的then或else表达式
* `lambda`的最后一个表达式
* `let, let* letrec, let-syntax, letrec-syntax`body部分的最后一个表达式
* `or`的最后一个表达式

下面的核心函数是尾递归实现

* apply
* call-with-current-continuation
* call-with-values
* eval
* string-any, string-every

***
未完待续...
