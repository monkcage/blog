    一直想找个比较全面的例子，来阐述cmake的使用，但是哪有"放之四海皆准"的例子呢！所以现在只能由浅到深的举例，来尽可能多的包cmake的要点都包含进来。

### 工程目录结构
```
      ---example
          |---------hello_world.cxx
          |---------CMakeLists.txt
          |---------TutorialConfig.h.in
          |---------math
                     |-------mymath.cxx
                     |-------CMakeLists.txt
```


### Hello world
* hello_world.cxx
```
# include <stdio.h>
# include <stdlib.h>

int main(int argc, char *argv[])
{
    printf("Hello world!\n");
    return 0;
}
```
* CMakeLists.txt
```
cmake_minimum_required(VERSION 3.0)
project(Tutorial)

add_executable(Tutorial hello_world.cxx)
```
> 以上就是最简单的一个CMake工程！

### 引入外部宏
* hello_world.cxx
```
# include <stdio.h>
# include <stdlib.h>
# include "TutorialConfig.h"

int main(int argc, char *argv[])
{
    printf("Version: %d.%d\n", Tutorial_VERSION_MAJOR, Tutorial_VERSION_MINOR);
    printf("Hello world!\n");
    return 0;
}
```
* CMakeLists.txt
```
cmake_minimum_required(VERSION 3.0)
project(Tutorial)

set(Tutorial_VERSION_MAJOR 1)
set(Tutorial_VERSION_MINOR 0)

configure_file(
     ${PROJECT_SOURCE_DIR}/TutorialConfig.h.in
     ${PROJECT_BINARY_DIR}/TutorialConfig.h
)

include_directories(${PROJECT_BINARY_DIR})

add_executable(Toturial hello_world.cxx)
```
* TutorialConfig.h.in
```
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
```
> 在实际应用中，有些工程可能需要给某些宏指定不同的值，来完成一些特殊的需求。那就可以把这些红定义在CMake中，在构建时，直接修改CMake文件，而不去修改源文件。

### 添加自定义库
* hello world.cxx
```
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "TutorialConfig.h"

#ifdef USE_MYMATH
extern int mysqrt(int, int);
#endif


int main(int argc, char *argv[])
{
#ifdef USE_MYMATH
    int ret = mysqrt(1, 2);
#else
    int ret = sqrt(4);
#endif
    printf("mymath=%d\n", ret);
    printf("VERSION: %d.%d\n", Tutorial_VERSION_MAJOR, Tutorial_VERSION_MINOR);
    printf("Hello world!\n");
    return 0;
}
```
* CMakeLists.txt
```
cmake_minimum_required(VERSION 3.0)
project(Tutorial)

option(USER_MYMATH "Use tutorial provided math implementation" ON)

set(Tutorial_VERSION_MAJOR 1)
set(Tutorial_VERSION_MINOR 0)

configure_file(
     ${PROJECT_SOURCE_DIR}/TutorialConfig.h.in
     ${PROJECT_BINARY_DIR}/TutorialConfig.h
)

if (USE_MYMATH)
    add_subdirectory(math)
    set(EXTRA_LIBS ${EXTRA_LIBS} mymath)
endif (USE_MYMATH)

include_directories(${PROJECT_BINARY_DIR})

add_executable(Toturial hello_world.cxx)

target_link_libraries(Tutorial ${EXTRA_LIBS})
```
* TutorialConfig.h.in
```
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@

#cmakedefine USE_MYMATH
```
* mymath.cxx
```
int mysqrt(int a, int b)
{ return a+b; }
```
* math中CMakeLists.txt
```
add_library(mymath mymath.cxx)
```

### 跨平台相关
* hello world.cxx
```
#include <stdio.h>
#include <stdlib.h>
#include "TutorialConfig.h"

#ifdef USE_MYMATH
extern int mysqrt(int, int);
#endif


int main(int argc, char *argv[])
{
#ifdef HAVE_PRINTF
    printf("Have found printf\n");
#else
    printf("found no printf\n");
#endif
    return 0;
}
```
* CMakeLists.txt
```
cmake_minimum_required(VERSION 3.0)
project(Tutorial)

include(CheckFunctionExists)
check_function_exists(printf HAVE_PRINTF)

configure_file(
     ${PROJECT_SOURCE_DIR}/TutorialConfig.h.in
     ${PROJECT_BINARY_DIR}/TutorialConfig.h
)



include_directories(${PROJECT_BINARY_DIR})

add_executable(Toturial hello_world.cxx)
```
* TutorialConfig.h.in
```
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@

#cmakedefine HAVE_PRINTF
```
> cmake中需要包含CheckFunctionExists库，然后使用check_function_exists.








https://www.cnblogs.com/chengxuyuancc/p/5347646.html
https://cmake.org/cmake-tutorial/
