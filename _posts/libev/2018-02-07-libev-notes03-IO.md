---
title: libev IO复用
author: MonkCage
category: libev
tags: [libev,io,epoll]
---

### ev_epoll.c/ev_kqueue.c/ev_poll.c/ev_port.c/ev_select.c都是IO复用的封装，现在以epoll IO复用来讲解；**

> ev_epoll.c包含如下函数
>> void epoll_modify(EV_P_ int fd, int oev, int nev)
>>
>> void epoll_poll(EV_P_ ev_tstamp timeout)
>>
>> int inline_size epoll_init(EV_P_ int flags)
>>
>> void inline_size epoll_destroy(EV_P)
>>
>> void inline_size epoll_fork(EV_P)

### epoll_init

```
int inline_size
epoll_init (EV_P_ int flags)
{
#ifdef EPOLL_CLOEXEC
  backend_fd = epoll_create1 (EPOLL_CLOEXEC);

  if (backend_fd < 0 && (errno == EINVAL || errno == ENOSYS))
#endif
    backend_fd = epoll_create (256);

  if (backend_fd < 0)
    return 0;

  fcntl (backend_fd, F_SETFD, FD_CLOEXEC);

  backend_mintime = 1e-3; /* epoll does sometimes return early, this is just to avoid the worst */
  backend_modify  = epoll_modify;
  backend_poll    = epoll_poll;

  epoll_eventmax = 64; /* initial number of events receivable per poll */
  epoll_events = (struct epoll_event *)ev_malloc (sizeof (struct epoll_event) * epoll_eventmax);

  return EVBACKEND_EPOLL;
}
```

* 其中EV_P_宏是在ev.h文件中定义的："#define EV_P struct ev_loop *loop", "#define EV_P_ EV_P,"；
* epoll_init
  + backend_fd, backend_modify, backend_poll等宏是在ev_wrap.h中定义；
  + 函数中调用epoll_create来创建epoll的文件描述符，并把epoll_modify,epoll_poll赋值为事件主循环结构体的相应成员；
  + epoll_modify调用了epoll_ctl(),因此epoll_modify的主要作用是向epoll事件循环中添加/删除/修改相关的事件；
  + epoll_poll中调用了epoll_wait(),中epoll的事件队列中去除就绪的事件；
