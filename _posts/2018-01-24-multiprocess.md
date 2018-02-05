---
layout: post
title: Python multiprocess中Queue问题
category: python
tags: [multiprocess, ipc, pool, queue]
---

首先直接上代码
```c
from multiprocessing import Pool, Manager, Queue
from time import sleep

def fun(num, queue) :
    print "hello %d" % num
    return
    
def main() :
    pool = Pool(processes=4)
    q = Queue()
    for k in range(5) :
        pool.apply_async(fun, args=(k, q))
    pool.close()
    pool.join()
    return
    
if __name__ == '__main__' :
    main()

```

* 本想把Queue实例作为参数传到进程中，可是没有得到想要的结果
  - 在笔者的环境中没有报错，但是结果不是预期的
  - 也可能报错 RuntimeError: Queue objects should only be shared between process through inheritance.
  
## 解决
   * 把multiprocessing.Queue()换成multiprocessing.Manager().Queue()
