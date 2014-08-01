---
layout: post
title: CMake 教程
comments: true
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

USE\_MYMATH的设定值决定是否编译和使用MathFunctions库，注意使用变量\(如这里的EXTRA\_LIBS\)收集所有的可选库，最后链接到
可执行文件中。这是一种方法可以保持大项目和可选组件之间的清洁。对源码的修改相当的直接和独立：

```c++
// A simple program that computes the square root of a number
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "TutorialConfig.h"
#ifdef USE_MYMATH
#include "MathFunctions.h"
#endif
 
int main (int argc, char *argv[])
{
  if (argc < 2)
    {
    fprintf(stdout,"%s Version %d.%d\n", argv[0],
            Tutorial_VERSION_MAJOR,
            Tutorial_VERSION_MINOR);
    fprintf(stdout,"Usage: %s number\n",argv[0]);
    return 1;
    }
 
  double inputValue = atof(argv[1]);
 
#ifdef USE_MYMATH
  double outputValue = mysqrt(inputValue);
#else
  double outputValue = sqrt(inputValue);
#endif
 
  fprintf(stdout,"The square root of %g is %g\n",
          inputValue, outputValue);
  return 0;
}
```

在源码中我们也使用了USE\_MYATH, 这个变量可以通过在配置文件TutorialConfig.h.in中添加下面的内容，
使CMake传递它到源码中。

```c++
#cmakedefine USE_MYMATH
```

## 安装与测试(步骤3)

下一步我们将为项目添加安装规则和测试支持。安装规则是相当直接的。为安装MathFunctions的库和头文件，
在MathFunctions的CMakeLists文件中添加下面两行：

```cmake
install (TARGETS MathFunctions DESTINATION bin)
install (FILES MathFunctions.h DESTINATION include)
```

为应用程序安装可执行文件和配置头文件，在顶层CMakeLists文件中添加下面行：

```cmake
# add the install targets
install (TARGETS Tutorial DESTINATION bin)
install (FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"        
         DESTINATION include)
```

就这些，这时你可以编译这个教程，然后键入`make install`(或者从IDE中编译INSTALL目标),它将安装相应的头文件，库文件和可执行文件。CMake的CMAKE\_INSTALL\_PREFIX变量决定安装的根目录。添加测试的过程也是相当直接的。在顶层CMakeLists文件的最后，
我们添加几个基本测试来验证这个应用程序可以正常工作。

```cmake
# does the application run
add_test (TutorialRuns Tutorial 25)
 
# does it sqrt of 25
add_test (TutorialComp25 Tutorial 25)
 
set_tests_properties (TutorialComp25 
  PROPERTIES PASS_REGULAR_EXPRESSION "25 is 5")
 
# does it handle negative numbers
add_test (TutorialNegative Tutorial -25)
set_tests_properties (TutorialNegative
  PROPERTIES PASS_REGULAR_EXPRESSION "-25 is 0")
 
# does it handle small numbers
add_test (TutorialSmall Tutorial 0.0001)
set_tests_properties (TutorialSmall
  PROPERTIES PASS_REGULAR_EXPRESSION "0.0001 is 0.01")
 
# does the usage message work?
add_test (TutorialUsage Tutorial)
set_tests_properties (TutorialUsage
  PROPERTIES 
  PASS_REGULAR_EXPRESSION "Usage:.*number")
```

首先简单的验证应用程序可以运行，没有段错误或崩溃，返回值是0。
这是CTest测试项的基本格式。接着使用PASS\_REGULAR\_EXPRESSION测试属性
验证测试项的输出包含某些字符串。我们现在的情况验证计算的平方根，以及当提供的参数的数量不正确
时，输出使用方法信息。如果想添加大量的测试项来测试不同的输入值，你可能需要考虑创建一个宏，如下：

```cmake
#define a macro to simplify adding tests, then use it
macro (do_test arg result)
  add_test (TutorialComp${arg} Tutorial ${arg})
  set_tests_properties (TutorialComp${arg}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result})
endmacro (do_test)
 
# do a bunch of result based tests
do_test (25 "25 is 5")
do_test (-25 "-25 is 0")
```
**注意**：在测试的前面添加`enable_testing ()`命令

每次调用do_test，就为项目添加一个测试项，带有测试名称，输入和结果等参数。

## 添加系统自检(步骤4)

接下来让我们考虑将一些依赖于目标平台可能没有的特性的代码添加到项目,
在这个例子中,我们将添加一些依赖于是否在目标平台有log和exp函数的代码。
当然,几乎每个平台都有这些功能,但是对于本教程假设他们不是常见的。
如该平台有`log`，然后我们在`mysqrt`函数中使用它来计算平方根。
首先在顶层的CMakelists文件中使用CheckFunctionExists.cmake宏来测试这些
函数是否有效:

```cmake
# does this system provide the log and exp functions?
include (CheckFunctionExists.cmake)
check_function_exists (log HAVE_LOG)
check_function_exists (exp HAVE_EXP)
```

接着我们修改TutorialConfig.h.in文件，如果在这个平台上有这些函数，就定义这些宏:

```c++
// does the platform provide exp and log functions?
#cmakedefine HAVE_LOG
#cmakedefine HAVE_EXP
```

重要的是对`log`和`exp`的测试要在`configure_file`命令之前完成。在CMake中,`configure_file`命令
使用当前的设置立即生成配置文件。最在我们的mysqrt函数中，我们可以提供一个基于`log`和`exp`的实现，
如果他们在系统中是有效的，使用的代码如下：

```c++
// if we have both log and exp then use them
#if defined (HAVE_LOG) && defined (HAVE_EXP)
  result = exp(log(x)*0.5);
#else // otherwise use an iterative approach
  . . .
```

**注意**：CheckFunctionExists.cmake文件可能需要指定全路径。

## 添加生成的文件和生成器(步骤5)

这一节我们将演示如何向应用程序的编译过程中添加生成的文件。在这个例子中，我们
将添加预计算平方根的表作为编译过程的一部分，然后将个表编译进我们的程序中。
为了完成这个，我们首先需要一个生成这个表的程序。仅需要在MathFunctions子目录中
新建一个`MakeTable.ccx`文件。

```c++
// A simple program that builds a sqrt table 
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
 
int main (int argc, char *argv[])
{
  int i;
  double result;
 
  // make sure we have enough arguments
  if (argc < 2)
    {
    return 1;
    }
  
  // open the output file
  FILE *fout = fopen(argv[1],"w");
  if (!fout)
    {
    return 1;
    }
  
  // create a source file with a table of square roots
  fprintf(fout,"double sqrtTable[] = {\n");
  for (i = 0; i < 10; ++i)
    {
    result = sqrt(static_cast<double>(i));
    fprintf(fout,"%g,\n",result);
    }
 
  // close the table with a zero
  fprintf(fout,"0};\n");
  fclose(fout);
  return 0;
}
```
注意生成的这表是以有效的C++代码，它被保存到在参数中指定的文件中。接着在MathFunctions的CMakeLists文件中
添加相应的命令来编译MakeTable可执行文件，然后运行它作为编译过程的一个部分。完成这些需要一些命令，如下：

```cmake
# first we add the executable that generates the table
add_executable(MakeTable MakeTable.cxx)
 
# add the command to generate the source code
add_custom_command (
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h
  COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h
  DEPENDS MakeTable
  )
 
# add the binary tree directory to the search path for 
# include files
include_directories( ${CMAKE_CURRENT_BINARY_DIR} )
 
# add the main library
add_library(MathFunctions mysqrt.cxx ${CMAKE_CURRENT_BINARY_DIR}/Table.h)
```

首先像其他添加的生成可执行文件命令一样添加生成MakeTable可执行文件的命令。
然后通过执行`MakeTable`，我们添加一个怎样生成`Table.h`文件的定制命令。
接着我们让CMake知道`mysqrt.cxx`依赖生成的文件`Table.h`。要完成这个需要为
MathFunctions库的源码列表添加生成的`Table.h`文件。我们也必须添加当前的bin
目录到include目录列表中，这样mysqrt.cxx文件中的include命令就可以找到Table.h文件。

当编译这个项目的时候，它首先编译MakeTable可执行文件，然后运行MakeTable生成Table.h文件。
最后它编译包含Table.h文件的mysqrt.cxx文件生成MathFunctions库。

这时候顶层的CMakeLists文件的带有我们已经添加的所有功能，如下：

```cmake
cmake_minimum_required (VERSION 2.6)
project (Tutorial)
 
# The version number.
set (Tutorial_VERSION_MAJOR 1)
set (Tutorial_VERSION_MINOR 0)
 
# does this system provide the log and exp functions?
include (${CMAKE_ROOT}/Modules/CheckFunctionExists.cmake)
 
check_function_exists (log HAVE_LOG)
check_function_exists (exp HAVE_EXP)
 
# should we use our own math functions
option(USE_MYMATH 
  "Use tutorial provided math implementation" ON)
 
# configure a header file to pass some of the CMake settings
# to the source code
configure_file (
  "${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"
  "${PROJECT_BINARY_DIR}/TutorialConfig.h"
  )
 
# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
include_directories ("${PROJECT_BINARY_DIR}")
 
# add the MathFunctions library?
if (USE_MYMATH)
  include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
  add_subdirectory (MathFunctions)
  set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif (USE_MYMATH)
 
# add the executable
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Tutorial  ${EXTRA_LIBS})
 
# add the install targets
install (TARGETS Tutorial DESTINATION bin)
install (FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"        
         DESTINATION include)

enable_testing () 
 
# does the application run
add_test (TutorialRuns Tutorial 25)
 
# does the usage message work?
add_test (TutorialUsage Tutorial)
set_tests_properties (TutorialUsage
  PROPERTIES 
  PASS_REGULAR_EXPRESSION "Usage:.*number"
  )
 
 
#define a macro to simplify adding tests
macro (do_test arg result)
  add_test (TutorialComp${arg} Tutorial ${arg})
  set_tests_properties (TutorialComp${arg}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result}
    )
endmacro (do_test)
 
# do a bunch of result based tests
do_test (4 "4 is 2")
do_test (9 "9 is 3")
do_test (5 "5 is 2.236")
do_test (7 "7 is 2.645")
do_test (25 "25 is 5")
do_test (-25 "-25 is 0")
do_test (0.0001 "0.0001 is 0.01")
```

`TutorialConfig.h.in`文件如下：

```c++
/ the configured options and settings for Tutorial
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
#cmakedefine USE_MYMATH
 
// does the platform provide exp and log functions?
#cmakedefine HAVE_LOG
#cmakedefine HAVE_EXP
```

MathFunctions的CMakeLists文件如下：

```cmake
# first we add the executable that generates the table
add_executable(MakeTable MakeTable.cxx)
# add the command to generate the source code
add_custom_command (
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h
  DEPENDS MakeTable
  COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h
  )
# add the binary tree directory to the search path 
# for include files
include_directories( ${CMAKE_CURRENT_BINARY_DIR} )
 
# add the main library
add_library(MathFunctions mysqrt.cxx ${CMAKE_CURRENT_BINARY_DIR}/Table.h)
 
install (TARGETS MathFunctions DESTINATION bin)
install (FILES MathFunctions.h DESTINATION include)
```

## 构建一个安装程序(步骤6)

下面假设我们想发布我们的项目给其他的人使用。我们想要为多种平台提供二进制和源码发布。
这有点不同于我们在设置部分安装和测试(步骤3)之前所做，之前我们是从编译后的源码安装二进制文件。
在这个例子中我们将构建以安装包，支持的二进制安装和包管理功能有cygwin，debian，RPMs等。
为了实现这一点,我们将使用CPack创建特定于平台的安包装，更详细的在"用CPack打包"一章中。
具体的我们需要在顶层的CMakeLists.txt文件的底部添加下面行：

```cmake
# build a CPack driven installer package
include (InstallRequiredSystemLibraries)
set (CPACK_RESOURCE_FILE_LICENSE  
     "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
set (CPACK_PACKAGE_VERSION_MAJOR "${Tutorial_VERSION_MAJOR}")
set (CPACK_PACKAGE_VERSION_MINOR "${Tutorial_VERSION_MINOR}")
include (CPack)
```

就这些。在开始出我们包含InstallRequiredSystemLibraries。这个模块将为项目当前平台包含任何需要的
运行时库。然后我们设置Cpack变量，指定license文件的位置和该项目的版本信息。版本的信息使用我们在
本教程前面定义的变量。最后我们包含CPack模块，使用这些变量和系统的其他属性设置安装包。

接着手动构建这个项目，然后运行CPack。为构建二进制发布版，执行下面的命令：

```bash
cpack -C CPackConfig.cmake
```

构建源码安装包，执行下面的命令：

```bash
cpack -C CPackSourceConfig.cmake
```

## 添加仪表板支持(步骤7)

添加将我们的测试结果提到仪表盘的支持是非常简单的。在本教程的前面我们已经为该项目定义了很多的测试项。
我们只需要运行测试，然后将结果提交到仪表盘。为了包含仪表盘支持，我们包含Ctest模块到我们的顶层CMakeLists文件
中。

```cmake
# enable dashboard scripting
include (CTest)
```

为仪表盘指定这个项目的名称，我们创建CTestConfig.cmake，内容如下：

```cmake
set (CTEST_PROJECT_NAME "Tutorial")
```

Ctest将会在运行的时候读取这个文件。为了创建一个简单的仪表盘你可以在你的项目中运行CMake，将目录切换到
bin目录，然后运行`ctest -D Experimental`。你的仪表盘的结果将会上传到[Kitware的公共仪表盘](http://www.cdash.org/CDash/index.php?project=PublicDashboard)。


参考文件：

* [原文](http://www.cmake.org/cmake/help/cmake_tutorial.html)
