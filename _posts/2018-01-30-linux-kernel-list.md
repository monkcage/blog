---
layout: post
title: 侵入式链表
author: monkcage
category: linux
tags: [linux, kernel, list]
---

```c
1:   #undef offset
2:   #define offset(TYPE, MEMBER) ((size_t) &((TYPE*)0)->MEMBER)
3:
4:   #define container_of(ptr, type, member) ({ \
5:       const typeof( ((type *)0)->member ) *__mptr = (ptr); \
6:       (type *)( (char *) __mptr - offsetof(type, member) );})
7:
8:   struct list_head{
9:      struct list_head *next, *prev;
10:  };
11:
12:  #define LIST_HEAD_INIT(name) { &(name), &(name) }
13:  #define LIST_HEAD(name) \
14:      struct list_head name = LIST_HEAD_INIT(name)
15：
16： static inline void INIT_LIST_HEAD(struct list_head *list)
17:  {
18:      list->next = list;
19:      list->prev = list;
20:  }
21: 
22:  static inline void __list_add(struct list_head *new,
23:                      struct list_head *prev,
24:                      struct list_head *next)
25:  {
26:      next->prev = new;
27:      new->next = next;
27:      new->prev = prev;
28:      prev->next = new;
29:  }
30: 
31:  static inline void list_add(struct list_head *new, struct list_head *head)
32:  {
33:      __list_add(new, head, head->next);
34:  }
35: 
36:  static inline void __list_del(struct list_head *prev, struct list_head *next)  
37:  {
38:     next->prev = prev;
39:     prev->next = next;
40:  }
41:  
42:  #define POISON_POINTER_DELTA 0
43:  #define LIST_POISON1 ((void *) 0x00100100 + POISON_POINTER_DELTA)
44:  #defone LIST_POISON2 ((void *) 0x00200200 + POISON_POINTER_DELTA)
45:
46:  static inline void __list_del_entry(struct list_head *entry)
47:  {
48:     __list_del(entry_prev, entry->next);
49:  }
50: 
51:  static inline void list_del(struct list_head *entry)
52:  {
53:      __list_del(entry->prev, entry->next);
54:      entry->next = LIST_POISON1;
55:      entry->prev = LIST_POISON2;
56:  }
57:
58:  #define list_entry(ptr, type, member) \
59:       container_of(ptr, type, member)
60: 
61:  #define list_for_each(pos, head) \
62:       for( pos = (head)->next; pos != (head); pos =  pos->next)
```

* 以上代码均摘自linux内核代码；
* 示例
```c
  #include <stdio.h>
  #include <stdlib.h>
  #include "list.h"
  
  typedef struct Stu_list{
     struct list_head next;
     char* name;
     int age;
  }
  
  Stu_list* init_node(char* name, int age)
  {
      struct Stu_list *node = (Stu_list*)malloc(sizeof(Stu_list));
      node->name = (char*)malloc(strlen(name)+1);
      strcpy(node->name, name);
      node->age = age;
      return node;
  }
  
  int main(int argc, char *argv[])
  {
      LIST_HEAD(head);
      INIT_LIST_HEAD(&head);
      struct Stu_list *stu1 = init_node("AAA", 23);
      struct Stu_list *stu2 = init_node("BBB", 24);
      struct Stu_list *stu3 = init_node("CCC", 25);
      
      list_add(&(stu1->next), &head);
      list_add(&(stu2->next), &head);
      list_add(&(stu3->next), &head);
      
      struct list_head* pos;
      list_for_each(pos, &head){
          struct Stu_list* node = list_entry(pos, struct Stu_list, next);
          printf("%s, %d\n", node->name, node->age);
      }
      return 0;
 }
  
```
