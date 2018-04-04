---
layout: post
title: Nginx信号处理
category: Nginx
tags: [os, nginx, signal]
---

**在main函数中ngx_init_signal()进行信号初始化操作**
```c
ngx_int_t 
ngx_init_signals(ngx_log_t *log)
{
    ngx_signal_t         *sig;
    struct sigaction      sa;
    for (sig = signals; sig->signo != 0; sig++){
        ngx_memzero(&sa, sizeof(struct sigaction));
        sa.sa_handler = sig->handler;
        sigemptyset(&sa.sa_mask);
        if(sigaction(sig->signo, &sa, NULL) == -1){
#if (NGX_VALGRIND)
            ngx_log_error();
#else 
            ngx_log_error();
#endif
        }
    }
    return NGX_OK;
}
```

其中signals为全局变量，在ngx_process.c中定义了，定义如下：

```c
typedef struct{
    int    signo;
    char   *signame;
    char   *name;
    void   (*handler)(int signo);
}ngx_signal_t;

ngx_signal_t signals[] = {
      { ngx_signal_value(NGX_RECONFIGURE_SIGNAL),
        "SIG" ngx_value(NGX_RECONFIGURE_SIGNAL),
        "reload",
        ngx_signal_handler},
      { ...... },
      { SIGALRM, "SIGALRM", "", ngx_signal_handler},
      { ...... },
      { SIGSYS, "SIGSYS, SIG_IGN", "", SIG_IGN},
      {0, NULL, "", NULL}
};

```

同样是在ngx_process.c文件中定义了ngx_signal_handler()信号处理函数，定义如下：
```c
void
ngx_signal_handler(int, signo)
{
    char         *action;
    ngx_signal_t *sig;
    ngx_int_t    ignore = 0;
    ngx_err_t    err = ngx_error;
    
    for(sig = signals; sig->signo != 0; sig++){
        if(sig->signo == signo){
            break;
        }
    }
    
    ngx_time_sigsafe_update();
    
    action = "";
    
    switch(ngx_process) {
        case NGX_PROCESS_MASTER:
        case NGX_PROCESS_SINGLE:
            switch (signo) {
            case ngx_signal_value(NGX_SHUTDOWN_SIGNAL):
                ngx_quit = 1;
                action = ", shutting down";
                break;
            case ngx_signal_value(NGX_TERMINATE_SIGNAL):
            case SIGINT:
                ngx_terminate = 1;
                action = ", exiting";
                break;
            ...
            ...
            }
            break;
        
        case NGX_PROCESS_WORKER:
        case NGX_PROCESS_HELPER:
            switch(signo) {
            case ngx_signal_value(NGX_NOACCEPT_SIGNAL):
                if(!ngx_daemonized){
                    break;
                }
                ngx_debug_quit = 1;
            case ngx_singal_value(NGX_SHUTDOWN_SIGNAL):
                ngx_quit = 1
                action = ", shutting down";
                break;
             ...
             ...
            }
            break;
    }
    
    if(ignore){
        ngx_log_error();
    }
    
    if(signo == SIGCHLD){
        ngx_process_get_status();
    }
    
    ngx_set_errno(err);
    
}
```

首先根据*ngx_process*判断进程是master还是slave，在根据*signo*设置不同的*全局信号变量*，这些变量的定义如下：
```c
sig_atomic_t ngx_reap;
sig_atomic_t ngx_sigio;
sig_atomic_t ngx_sigalrm;
sig_atomic_t ngx_terminate;
...
...
```
然后各个进程会检查这些全局的信号变量，再进行相应的操作。
