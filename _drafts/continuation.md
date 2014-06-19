
```scheme
(let ((x (call/cc (lambda (k) k))))
  (x (lambda (ignore) "hi")))
```

上面的例子中，call/cc捕获的`continuation`可以之这样描述：
"对x赋值，然后对表达式`(x (lambda (ignore) "hi"))`求值。"

由于，（call/cc (lambda (k) k))的求值结果就是上面描述的`continuation`,
那么这时x的值就是这个`continuation`，根据`continuation`的求值规则，对于(x (lambda (igonre) "hi"))求值，即求值表达式`(lambda (ignore) "hi")`，这个的求值结果是一个过程，将这个过程
赋值给x，然后求值表达式`(x (lambda (igore) "hi"))`，即对`((lambda (ignore) "hi") (lambda (ignore) "hi"))`，所以结果是"hi"。
