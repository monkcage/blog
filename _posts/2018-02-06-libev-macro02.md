---
title: libev 宏解释
layout: post
author: MonkCage
category: libev
tags: [libev,macro]
---

> ev_vars.h
>> VAR(name, type name)以及VARx(type, name)
>> 
>> 在ev.c文件中有 "VAR(name,decl) decl;";
>>
>> 所以 VAR(name, type name)展开就是 type name; 即定义一个变量；
>>
>> VARx(type, name)展开就是 type name; 也是定义一个变量；

> ev.h
>> EV_P, EV_P_, EV_A, EV_A_, EV_WATCHER*等
>>
>> "#define EV_P_ EV_P,"展开就是 "struct ev_loop *loop,",
>>
>> "#define EV_A_ EV_A,"展开就是 "loop,",
>>
>> 而在libev中可以看见很多这样的函数 func(EV_P_ type arg1, type arg2), 把宏展开就是func(struct ev_loop *loop, arg1, arg2), 所以EV_P_的最大作用是作为函数的形参；
>>
>> 那么在调用函数时可以使用 func(EV_A_ arg1, arg2), 宏展开就是func(loop, arg1, arg2), 所以EV_A_的最大作用是作为函数的实参；
>>
>>
>> EV_WATCHER(type)展开就是一系列的变量定义，在ev_watcher, ev_watcher_list等结构体中，就成了结构体成员变量了；
>>
>> 所以以上提到的宏，个人认为仅仅是为了方便，少写点代码！
