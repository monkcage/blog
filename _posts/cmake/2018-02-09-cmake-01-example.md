---
layout: post
title: CMAKE例子
author: MonkCage
category: cmake
tags : camke
---

## hello world

*   最基本CMakeLists文件看起来像是这样:

```
cmake_minimum_required(VERSION 3.0)
project(Tutorial)
add_executable(Tutorial tutorial.cxx)
```

    tutorial.cxx可以是最简单的hello world程序，如下：
    
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
   printf("Hello world!\n");
   return 0;
}
```

*  添加版本号

```
cmake_minimum_required(VERSION 3.0)
project(Tutorial)

# version
set (Tutorial_VERSION_MAJOR 1)
set (Totorial_VERSION_MINOR 0)

# configure file, pass the version number to source file
configure_file (
       ${PROJECT_SOURCE_DIR}/TutorialConfig.h.in
       ${PROJECT_BINARY_DIR}/TutorialConfig.h
)

include_directories(${PROJECT_BINARY_DIR})

add_executable(Tutorial tutorial.cxx)
```

    TutorialConfig.h.in
    
```
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
```

   tutorial.cxx
   
```
#include <stdio.h>
#include <stdlib.h>
#include "TutorialConfig.h"

int main(int argc, char *argv[])
{
   printf("Version:%d.%d\n", Tutorial_VERSION_MAJOR, Tutorial_VERSION_MANOR);
   printf("Hello world!\n");
   return 0;
}
```

## 引入自定义库
    目录结构如下
    
 ```
        ---example
           |---------hello_world.cxx
           |---------CMakeLists.txt
           |---------TutorialConfig.h.in
           |---------math
                      |--------mymath.cxx
                      |--------CMakeLists.txt
 ```
 
 * 主CMakeLists.txt
 
 ```
cmake_minimum_required(VERSION 3.0)
project(Tutorial)

# version
set (Tutorial_VERSION_MAJOR 1)
set (Totorial_VERSION_MINOR 0)

# configure file, pass the version number to source file
configure_file (
       ${PROJECT_SOURCE_DIR}/TutorialConfig.h.in
       ${PROJECT_BINARY_DIR}/TutorialConfig.h
)

include_directories(${PROJECT_BINARY_DIR})

add_subdirectory(math)

add_executable(Tutorial tutorial.cxx)

target_link_libraries(Tutorial mymath)
 ```
 
 * math中CMakeList.txt
 
 ```
 add_library(mymath mymath.cxx)
 ```
 
 * mymath.cxx
 
 ```
 int myadd(int a, int b)
 { return a+b; }
 ```
 
 * hello_world.cxx
 
 ```
#include <stdio.h>
#include <stdlib.h>
#include "TutorialConfig.h"

extern int myadd(int, int);

int main(int argc, char *argv[])
{
   printf("myadd:%d\n",  myadd(1, 2));
   printf("Version:%d.%d\n", Tutorial_VERSION_MAJOR, Tutorial_VERSION_MANOR);
   printf("Hello world!\n");
   return 0;
}
 ```
 

