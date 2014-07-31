---
layout: post
title: CMake 教程
---


## 基本出发点(步骤1)

最基本的项目是从源代码文件生成一个可执行文件。
对于简单的项目，两行CMakeLists文件是必需的全部。
这将是我们教程的起点。该CMakeLists文件看起来像这样：

```cmake
cmake_minimum_required (VERSION 2.6)
project (Tutorial)
add_executable(Tutorial tutorial.cxx)
```

**注意**: 该例子的CMakeLists文件中使用的是小写的命令，CMake支持大写，小写以及大小写混合命令。
源代码tutorial.cxx将计算一个数的平方根，它的第一版非常的简单，如下：

```c++
// A simple program that computes the square root of a number
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
int main (int argc, char *argv[])
{
  if (argc < 2)
  {
    fprintf(stdout,"Usage: %s number\n",argv[0]);
    return 1;
  }
  double inputValue = atof(argv[1]);
  double outputValue = sqrt(inputValue);
  fprintf(stdout,"The square root of %g is %g\n",
  inputValue, outputValue);
  return 0;
}
```
