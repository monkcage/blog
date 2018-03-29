---
layout: post
title: CMake 函数、宏等检测
category: cmake
tags: [cmake]
---

## 检测函数是否存在
* 假如要检测printf()这个函数是否存在
> 说明：   
>     check_library_exists(<function> <variable>)    
>     检测系统库是否提供函数*function*，检测结果保存在*variable*中。但是这个函数并不能判定任何系统同文件中声明的函数， 仅能判断在链接是能找到的函数。如果想要判断任意头文件中声明的函数，可以考虑使用check_symbol_exists()

```
    include(CheckFunctionExists)  
    check_function_exists(printf HAVE_PRINTF)
```
    检测结果会保存在HAVE_PRINTF中
    
## 检测头文件是否存在
```
    include(CheckIncludeFiles) 
    check_include_files(inttypes.h    HAVE_INTTYPES) 
    check_include_files("sys/time.h;sys/time.h" HAVE_SYS_TIME) 
```
## 检测宏是否存在
```
    include(CheckSymbolExists) 
    check_symbol_exists(ENAMETOOLONG errno.h HAVE_ENAMETOOLONG) 
    check_symbol_exists(__FUNCTION__ ""      HAVE_FUNCTION)
``` 
## 检测库是否存在
```
    include(CheckLibraryExists) 
    find_library(LIBICONV iconv) 
    if(LIBCONV)  
        check_library_exists("${LIBCONV}" iconv "" HAVE_ICONV) 
    else()  
        check_function_exists(iconv HAVE_ICONV) 
    endif() 
```

```
    include(CheckLibraryExists)  
    include(CheckCSourceCompiles)  
    #find_library(LIBATOMIC_OPS atomic_ops)  
    check_s_source_compiles( 
    "#include <stdlib.h>     
    #include <stdio.h>    
    #include <atomic_ops.h>   
    int main(void){   
    AO_t i=0;        
    AO_compare_and_swap(&i, 0, 1);}"   
    HAVE_LIBATOMIC) 
````
    
    
    
