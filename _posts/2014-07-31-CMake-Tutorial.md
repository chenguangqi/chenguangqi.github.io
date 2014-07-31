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

### 添加版本号和配置的头文件

我们添加的第一个功能是为我们的可执行文件和项目提供一个版本号。
虽然我们在源码中就可以做到，但是CMake提供了更灵活的方法。
添加版本号可以如下修改CMakeLists文件：

```cmake
cmake_minimum_required (VERSION 2.6)
project (Tutorial)
# The version number. 
set (Tutorial_VERSION_MAJOR 1)
set (Tutorial_VERSION_MINOR 0)
 
# configure a header file to pass some of the CMake settings
# to the source code
configure_file (
  "${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"
  "${PROJECT_BINARY_DIR}/TutorialConfig.h"
  )
 
# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
include_directories("${PROJECT_BINARY_DIR}")
 
# add the executable
add_executable(Tutorial tutorial.cxx)
```

**注意**： 以#开始的行是注释

因为配置文件将被保存到编译目录中，我们必须把它添加到include文件的
搜索路径中。然后在源码中添加TutorialConfig.h文件，内容如下：

```c++
// the configured options and settings for Tutorial
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
```

参考文件：

* [原文](http://www.cmake.org/cmake/help/cmake_tutorial.html)



