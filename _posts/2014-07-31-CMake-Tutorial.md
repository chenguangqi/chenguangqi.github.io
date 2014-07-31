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

当CMake配置头文件的时候，用CMakeLists文件中值替换`@Tutorial_VERSION_MAJOR@`和`@Tutorial_VERSION_MINOR@`的值。
接着我们修改tutorial.cxx文件，包含配置头文件并使用版本号。修改后的代码如下：

```c++
// A simple program that computes the square root of a number
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "TutorialConfig.h"
 
int main (int argc, char *argv[])
{
  if (argc < 2)
    {
    fprintf(stdout,"%s Version %d.%d\n",
            argv[0],
            Tutorial_VERSION_MAJOR,
            Tutorial_VERSION_MINOR);
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

主要的变换是包含TutorialConfig.h文件并打印包含版本号的使用方法消息

## 添加库(步骤2)

现在我们为项目添加一个库。这个库包含一个我们自己实现的计算一个数的平方根。
可执行文件使用这个库代替编译器提供的标准计算平方根的函数。在该教程中，我们
将这个库放在MathFunctions子目录中。该子目录中有一个只有一行内容的CMakeLists文件：

```cmake
add_library(MathFunctions mysqrt.cxx)
```

源文件mysqrt.cxx有的mysqrt函数提供与编译器的sqrt函数相似的功能。要使用这个新库，我们
在顶层的CMakeLists文件添加`add_subdirectory`调用，这样这个库就会被编译。我们也要添加
另一个include目录，这样就可以在MathFunctions/mysqrt.h文件中找到mysqrt函数的原型。
最后为可执行文件添加新库。顶层CMakeLists文件的最后几行如下：

```cmake
include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
add_subdirectory (MathFunctions) 
 
# add the executable
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Tutorial MathFunctions)
```

现在我们考虑MathFunctions库是可选的，在这个教程中其实我们不需要这么做。但是，你可能
想要用很多的库或一些第三方提供的库。首先在顶层的CMakeLists文件中添加一个`option`:

```cmake
# should we use our own math functions?
option (USE_MYMATH 
        "Use tutorial provided math implementation" ON)
```

在CMake GUI中，显示默认值为ON，当然你可以修改为你想要。这个设置将被保存在缓存中，
在该项目中运行CMake时，用户不需要每次都设置该值。为了编译和链接MathFunctions库的候选项
可以下次改变它。 修改顶层CMakeLists文件的最后，如下：

```cmake
# add the MathFunctions library?
#
if (USE_MYMATH)
  include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
  add_subdirectory (MathFunctions)
  set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif (USE_MYMATH)
 
# add the executable
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Tutorial  ${EXTRA_LIBS})
```

参考文件：

* [原文](http://www.cmake.org/cmake/help/cmake_tutorial.html)



