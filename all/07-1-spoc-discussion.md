# 同步互斥(lec 17) spoc 思考题


- 有"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的ucore_code和os_exercises的git repo上。

## 个人思考题

### 背景
 - 请给出程序正确性的定义或解释。
  
   >并发进程的正确性：（1）执行过程中是不确定和不可重现的（2）程序的错误可能是间歇发生的。
 - 在一个新运行环境中程序行为与原来的预期不一致，是错误吗？
    
    >不是，由并发程序的正确性可知。
 - 程序并发执行有什么好处和障碍？
  
   > 好处：（1） 共享资源（2）可以加速 （3）模块化

   >障碍：并发时可能会出现错误。
 - 什么是原子操作？
   
  >原子操作是指一次不存在任何中断或者失败的操作。


### 现实生活中的同步问题

 - 家庭采购中的同步问题与操作系统中进程同步有什么区别？
  
  >家庭采购的过程中不会产生两个人同时都放上标签的错误。
 - 如何通过枚举和分类方法检查同步算法的正确性？
   
  >
 - 尝试描述方案四的正确性。
 - 互斥、死锁和饥饿的定义是什么？
   
  >一个进程占用资源，其他的进程不能使用

  >多个进程各占用部分资源，形成循环等待
  
  >其他进程可能轮流占优资源，一个进程一直得不到资源
  
  
  
### 临界区和禁用硬件中断同步方法

 - 什么是临界区？
    
   >进程中访问临界资源的一段需要互斥执行的代码。
 - 临界区的访问规则是什么？
 
  >空闲则入，忙则等待，有限等待，让权等待。
 - 禁用中断是如何实现对临界区的访问控制的？有什么优缺点？
    
   >禁用中断之后，没有了上下文的切换，因此没有并发。

   >缺点：禁用中断后，进程无法被停止
   
   >临界区可能很长，对硬件的中断响应时间产生影响。
### 基于软件的同步方法

 - 尝试通过枚举和分类方法检查Peterson算法的正确性。
 - 尝试准确描述Eisenberg同步算法，并通过枚举和分类方法检查其正确性。

### 高级抽象的同步方法

 - 如何证明TS指令和交换指令的等价性？
 - 为什么硬件原子操作指令能简化同步算法的实现？
 
## 小组思考题

1. （spoc）阅读[简化x86计算机模拟器的使用说明](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab7/lab7-spoc-exercise.md)，理解基于简化x86计算机的汇编代码。

2. （spoc)了解race condition. 进入[race-condition代码目录](https://github.com/chyyuu/ucore_lab/tree/master/related_info/lab7/race-condition)。

 - 执行 `./x86.py -p loop.s -t 1 -i 100 -R dx`， 请问`dx`的值是什么？
 
    > -1
 - 执行 `./x86.py -p loop.s -t 2 -i 100 -a dx=3,dx=3 -R dx` ， 请问`dx`的值是什么？
    > -1 -1
 - 执行 `./x86.py -p loop.s -t 2 -i 3 -r -a dx=3,dx=3 -R dx`， 请问`dx`的值是什么？

    > -1 -1
 - 变量x的内存地址为2000, `./x86.py -p looping-race-nolock.s -t 1 -M 2000`, 请问变量x的值是什么？
  
   > 2
 - 变量x的内存地址为2000, `./x86.py -p looping-race-nolock.s -t 2 -a bx=3 -M 2000`, 请问变量x的值是什么？为何每个线程要循环3次？
  
    > 2
 - 变量x的内存地址为2000, `./x86.py -p looping-race-nolock.s -t 2 -M 2000 -i 4 -r -s 0`， 请问变量x的值是什么？
   
    > 2
 - 变量x的内存地址为2000, `./x86.py -p looping-race-nolock.s -t 2 -M 2000 -i 4 -r -s 1`， 请问变量x的值是什么？
   
    > 2
 - 变量x的内存地址为2000, `./x86.py -p looping-race-nolock.s -t 2 -M 2000 -i 4 -r -s 2`， 请问变量x的值是什么？ 
   
    > 2
 - 变量x的内存地址为2000, `./x86.py -p looping-race-nolock.s -a bx=1 -t 2 -M 2000 -i 1`， 请问变量x的值是什么？ 
    
    >1

3. （spoc） 了解software-based lock, hardware-based lock, [software-hardware-lock代码目录](https://github.com/chyyuu/ucore_lab/tree/master/related_info/lab7/software-hardware-locks)

  - 理解flag.s,peterson.s,test-and-set.s,ticket.s,test-and-test-and-set.s 请通过x86.py分析这些代码是否实现了锁机制？请给出你的实验过程和结论说明。能否设计新的硬件原子操作指令Compare-And-Swap,Fetch-And-Add？
 
      > flag.s并不能实现锁机制。
        
      >peterson.s实现了锁机制。
        
      > test-and-set.s实现了锁机制。
        
      > ticket.s实现了锁机制.
        
      > test-and-test-and-set.s实现了锁机制
```
Compare-And-Swap

int CompareAndSwap(int *ptr, int expected, int new) {
  int actual = *ptr;
  if (actual == expected)
    *ptr = new;
  return actual;
}
```

```
Fetch-And-Add

int FetchAndAdd(int *ptr) {
  int old = *ptr;
  *ptr = old + 1;
  return old;
}
```
