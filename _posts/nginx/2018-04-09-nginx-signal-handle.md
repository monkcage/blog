---
layout: post
title: Nginx 信号处理流程
category: nginx
tags: [nginx, signal]
---

**Nginx进程间通信使用了三种方式：信号，共享内存以及socket pair**

#### 本节主要讲Nginx信号通信流程

**首先简化Nginx流程，去掉所有其他无关的操作，留下信号相关和一些必须的函数，main函数看起来就简单了**
```c
int ngx_cdecl
main(int argc, char *const *argv)
{
    if(ngx_get_options(argc, argv) != NGX_OK){
        return 1;
    }
    
    if(ngx_signal){
        return ngx_signal_process(cycle, ngx_signal);
    }
    
    if(ngx_init_signals() != NGX_OK){
        return 1;
    }
    
    if(ngx_create_pidfile(&ccf->pid, cycle->log) != NGX_OK){
       return 1;
    }
    
    ngx_master_process_cycle(cycle);
}
```
* 说明
   - ngx_get_options()获取命令行参数，赋值给相应的全局变量。与信号相关的就是ngx_signal变量。假如输入命令*nginx -s SIGHUB*,那个ngx_signal就是
   "SIGHUB"字符串。(在nginx以及开启的情况下)；
   - ngx_signal_process()在nginx已近开启的情况下才会调用；
   - ngx_init_signals()初始化信号，绑定信号处理函数；
   - ngx_create_pidfile()创建pid文件，在运行nginx后可以发现有个nginx.pid文件，里面记录了当前master进程的pid；
   - ngx_master_process_cycle()创建子进程
   
**ngx_init_signals()**
```c
typedef struct{
    int      signo;
    char     *signame;
    char     *name;
    void     (*handler)(int signo);
}ngx_signal_t;

ngx_signal_t signals[] = {
    { ngx_signal_value(NGX_RECONFIGURE_SIGNAL),
      "SIG" ngx_value(NGX_RECONFIGUTE_SIGNAL),
      "reload",
       ngx_signal_handler},
    { ... ...},
    { 0, NULL, "", NULL}
    };
    
ngx_int_t 
ngx_init_signals()
{
   ngx_signal_t     *sig;
   struct sigaction sa;
   for(sig = signals; sig->signo != 0; sig++){
       sa.sa_handler = sig->handler;
       sigempty(&sa.sa_mask);
       if(sigaction(sig->signo, &sa, NULL) == -1){
          ngx_log_error();
          return NGX_ERROR;
       }
   }
   return NGX_OK;
}
```
    ngx_init_signal()主要是绑定信号对应的处理函数，但是使用的都是同一个处理函数ngx_singal_handler(),此函数在内部有做了分类处理。
    ngx_singal_handler先根据进程类型(master/slave等)分类，然后再进行分类。处理方式为给全局的临界变量赋值。待进程进入事件循环，会检查这些变
    量的值做出相应的操作。
    
```
```
  
  
