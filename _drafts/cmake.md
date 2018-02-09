* 目录结构
```
      ---example
          |---------hello_world.cxx
          |---------CMakeLists.txt
          |---------TutorialConfig.h.in
          |---------math
                     |-------mymath.cxx
                     |-------CMakeLists.txt
```
* hello_world.cxx
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
    int ret = mysqrt(2,2);
#else
    int ret = sqrt(4);
#endif
    printf("ret=%d\n", ret);
    printf("Version:%d.%d\n", Tutorial_VERSION_MAJOR, Tutorial_VERSION_MINOR);
    printf("Hello_world\n");
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

option(USER_MYMATH "Use tutorial provided math implementation" ON)

include_directories(${PROJECT_BINARY_DIR})


if(USE_MYMATH)
    add_subdirectory(math)
    set(EXTRA_LIBS ${EXTRA_LIBS} mymath)
endif(USE_MYMATH)

add_executable(Tutorial hello_world.cxx)

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
* CMakeLists.txt
```
add_library(mymath mymath.cxx)
```

https://www.cnblogs.com/chengxuyuancc/p/5347646.html
