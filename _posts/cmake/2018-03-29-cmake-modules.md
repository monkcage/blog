---
layout: post
title: CMake 函数、宏等检测
category: cmake
tags: [cmake]
---

## 检测函数是否存在
* 假如要检测printf()这个函数是否存在


    include(CheckFunctionExists)  
    check_function_exists(printf HAVE_PRINTF)

    检测结果会保存在HAVE_PRINTF中
    
## 检测头文件是否存在

    include(CheckIncludeFiles) 
    check_include_files(inttypes.h    HAVE_INTTYPES) 
    check_include_files("sys/time.h;sys/time.h" HAVE_SYS_TIME) 
    
## 检测宏是否存在
  
    include(CheckSymbolExists) 
    check_symbol_exists(ENAMETOOLONG errno.h HAVE_ENAMETOOLONG) 
    check_symbol_exists(__FUNCTION__ ""      HAVE_FUNCTION)
    
## 检测库是否存在

    include(CheckLibraryExists) 
    find_library(LIBICONV iconv) 
    if(LIBCONV)  
        check_library_exists("${LIBCONV}" iconv "" HAVE_ICONV) 
    else()  
        check_function_exists(iconv HAVE_ICONV) 
    endif() 

* OR
    
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
    
    
    
    
