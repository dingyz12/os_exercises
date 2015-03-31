#lec9 虚存置换算法spoc练习

## 个人思考题
1. 置换算法的功能？
   - [x]  当出现缺页异常，需调入新页面而内存已满时，置换算法选择被置换的物理页面。

2. 全局和局部置换算法的不同？
   - [x] 局部置换算法分配给一个进程的物理页面数是确定的，在置换时仅限于当前进程；
         
        全局置换算法不关心当前的页面属于哪个进程，他讨论的是置换的范围，所有可能换出的物理页面。
   
3. 最优算法、先进先出算法和LRU算法的思路？
   - [x] 最优算法：依据未来来决定我换哪个页面；
         
         先进先出算法：按照被置换进来的顺序呢置换除去。
         
         LRU算法：统计过去的一些特征进行置换。

4. 时钟置换算法的思路？
   - [x]

5. LFU算法的思路？

6. 什么是Belady现象？

7. 几种局部置换算法的相关性：什么地方是相似的？什么地方是不同的？为什么有这种相似或不同？

8. 什么是工作集？

9. 什么是常驻集？

10. 工作集算法的思路？

11. 缺页率算法的思路？

12. 什么是虚拟内存管理的抖动现象？

13. 操作系统负载控制的最佳状态是什么状态？

## 小组思考题目

----
(1)（spoc）请证明为何LRU算法不会出现belady现象


(2)（spoc）根据你的`学号 mod 4`的结果值，确定选择四种替换算法（0：LRU置换算法，1:改进的clock 页置换算法，2：工作集页置换算法，3：缺页率置换算法）中的一种来设计一个应用程序（可基于python, ruby, C, C++，LISP等）模拟实现，并给出测试。请参考如python代码或独自实现。
 - [页置换算法实现的参考实例](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab3/page-replacement-policy.py)
 
## 扩展思考题
（1）了解LIRS页置换算法的设计思路，尝试用高级语言实现其基本思路。此算法是江松博士（导师：张晓东博士）设计完成的，非常不错！

参考信息：

 - [LIRS conf paper](http://www.ece.eng.wayne.edu/~sjiang/pubs/papers/jiang02_LIRS.pdf)
 - [LIRS journal paper](http://www.ece.eng.wayne.edu/~sjiang/pubs/papers/jiang05_LIRS.pdf)
 - [LIRS-replacement ppt1](http://dragonstar.ict.ac.cn/course_09/XD_Zhang/(6)-LIRS-replacement.pdf)
 - [LIRS-replacement ppt2](http://www.ece.eng.wayne.edu/~sjiang/Projects/LIRS/sig02.ppt)
