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
> 说明：         
>     check_include_file(include variable)                         
>         @include : name of include file                      
>         @variable: variable to return result                       
>         
>     check_include_files(includes variable)         
>         @includes : list of files to include        
>         @variavle : variable to return result            
```
    include(CheckIncludeFiles) 
    check_include_files(inttypes.h    HAVE_INTTYPES) 
    check_include_files("sys/time.h;sys/time.h" HAVE_SYS_TIME) 
```
## 检测符号(标识符)是否存在
> 说明：  
>     check_symbol_exists(<symbol> <files> <variable>)                
>     检测标识符是否作为函数，变量或宏存在。                      
>     检测在给定的头文件*files*中是否存在标识符*symbol*,并把结果保存在*variable*中。指定的文件列表用半角分号隔开(“test1.h;test2.h”)
>     如果头文件*files*中把标识符*symbol*作为宏定义了，可以认为这个标识符是有效可用的；如果头文件*files*中把标识符*symbol*作为函数或变量声明了，那么链接的时候能够获取到，才会认为这个标识符是有效可用的；如果这个标识符是一个类型或枚举值，它是不能被识别的(可以考虑使用CheckTypeSize或CheckCSourceCompiles)。如果检测用C++完成，则可考虑使用check_cxx_symbol_exists()。
```
    include(CheckSymbolExists) 
    check_symbol_exists(ENAMETOOLONG errno.h HAVE_ENAMETOOLONG) 
    check_symbol_exists(__FUNCTION__ ""      HAVE_FUNCTION)
``` 
    
## 检测库是否存在
> 说明:
>     check_library_exists(library function location variable)            
>     @library  : the name of library you are looking for         
>     @function : the name of the function         
>     @location : location where the library should be found           
>     @variable : variable to store the result          
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
    
## AddFileDependencies
    add_file_dependencies(source_file depend_files...)
    为给定的源文件添加依赖文件
    
