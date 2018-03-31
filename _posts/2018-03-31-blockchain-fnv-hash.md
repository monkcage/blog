---
layout: post
title: FNV hash算法
category: algo
tags: [fnv,sha,algo]
---

**FNV哈希算法全名为Fowler-Noll-Vo算法， 是以三位发明者Glenn Fowler, Landon Curt Noll, Phong Vo的名字组合命名，最早在1991年提出。**
* 算法优点
  - FNV能快速hash大量数据并保持较小的冲突率，它的高度分散使它适用于hash一些非常相近的字符串，比如URL,hostname等。

* 算法描述         
    相关变量：        
        @hash          : 返回值，一个n位无符号整数            
        @offset_basis  : 初始的哈希值         
        @FNV_prime     : FNV用于散列的质数          
        @octet_of_data : 8位数据            
    - FNV-1          
        hash = offset_basis       
        foreach octet_of_data in hash_data{         
            hash = hash * FNV_prime       
            hash = has xor octet_of_data}        
        return hash           
        
    - FNV-1a         
        hash = offset_basis          
        foreach octet_of_data in hash_data{             
            hash = hash xor octet_of_data            
            hash = hash * FNV_prime  }             
        return hash             
        
* FNV_PRIME取值
  32bit : FNV_PRIME = 2^24 + 2^8 + 0x93 = 16777619
  64bit : FNV_PRIME = 2^40 + 2^8 + 0xb3 = 1099511628211
  128bit: FNV_PRIME = 2^88 + 2^8 + 0x3b = 309485009821345068724781371
