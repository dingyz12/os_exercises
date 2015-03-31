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
   - [x] 通过一个循环链表将所有的页连接起来，每次缺页时通过指针查找没有被访问过的页面并替换之。

5. LFU算法的思路？
   - [x] 对每个页面的访问次数进行统计，缺页时替换访问次数最少的页面。
6. 什么是Belady现象？
   - [x] 物理页面存储增加时，缺页率反而会增加。
7. 几种局部置换算法的相关性：什么地方是相似的？什么地方是不同的？为什么有这种相似或不同？
   - [x] LRU 性能好，开销大；FIFO开销小。
         
         LRU存储的是最近访问的时间；FIFO存储的是第一次被访问（装入）的时间。
8. 什么是工作集？
   - [x] w(t,deta)即在某个时间段内，被访问过的页面的集合。
9. 什么是常驻集？
   - [x] 当前状态下缓存的页面的集合。
10. 工作集算法的思路？
   - [x] 每次访问正常时，去掉不在工作集中的页面；每次缺页时直接添加。
11. 缺页率算法的思路？
   - [x] 在缺页时，查找上次缺页时的时间，如果两次的时间间隔<=T,则将该页加入，常驻集变大；
         
         如果两次的时间间隔>T,去掉所有不在工作集的页。
12. 什么是虚拟内存管理的抖动现象？
   - [x] 因为进程太多而导致每个进程的分配的物理页面太小，缺页增加。
13. 操作系统负载控制的最佳状态是什么状态？
   - [x] 当产生缺页的平均间隔时间 等于 处理缺页异常的时间。
## 小组思考题目

----
(1)（spoc）请证明为何LRU算法不会出现belady现象
   - [x] 因为小的物理页帧的栈包含于大的数目的物理页帧的栈。
   假设1<= i <= t-1时，s(i)包含于s'(i),现在要证S(t)包含于S'(t)
   - （1）b(t)同时属于s(t)和S'(t):此时满足包含关系；
   - （2）b(t)不属于S(t)但是属于S'(t)：满足包含关系；
   - （3） (3)  (1)和(2)很容易证明，
       对于b(t)同时不属于s(t-1)和s'(t-1)的情况，我们依然按照视频里栈的方式对s(t-1)和s'(t-1)排序

                由于s(t-1)包含于s'(t-1),所以s(t-1)内每一个元素都存在于s'(t-1)中。
                
                现在两个栈都是按最后一次访问的时间的顺序来排列的，由于s(t)在进行替换时会替换s(t-1)里面最长时间没被访问的元素
                
                (栈底)，设为a,那么a显然也存在于s'(t-1)里面，并且它不一定是s'(t-1)的栈底。
                
                    A. 当a是s'(t-1)的栈底时，s(t)和s'(t)替换的都是a, s(t) = s(t-1) - {a} + {b(t)} , s'(t) = s'(t-1) - {a} +
                    
                    {b(t)}
                    
                    B. 当a不是s'(t-1)的栈底是，则s'(t-1)的栈底c必然不属于s(t-1)，否则就会与a是s(t-1)的栈底矛盾（即c比a有更长
                    
                    的时间未被访问），此种情况下s(t)和s'(t)依然满足包含关系
   -   (4) 由归纳假设可以得知此种情况不存在

(2)（spoc）根据你的`学号 mod 4`的结果值，确定选择四种替换算法（0：LRU置换算法，1:改进的clock 页置换算法，2：工作集页置换算法，3：缺页率置换算法）中的一种来设计一个应用程序（可基于python, ruby, C, C++，LISP等）模拟实现，并给出测试。请参考如python代码或独自实现。
 - [页置换算法实现的参考实例](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab3/page-replacement-policy.py)
 [x] 缺页率置换算法：
         
        #include <iostream>
	##include <stdlib.h>
	##include <stdio.h>
 	using namespace std;
	struct node 
	{
		int page_number;
		node * next;
		node * prev;
	};
	node * root;
	int T = 2;
	int n = 10;
	int page_visit[10] = {2,2,3,1,2,4,2,4,0,3};
	int t_last = -1 ;
	int t_current;
	int cache[5] = {0,3,4,-1,-1};
	int workset[2] = {0,3};
	bool page_miss(int x)
	{
		node * p = root;
		while(p != NULL)
		{
			if( p -> page_number == x)
			return 0;
			p = p -> next;
		}
		return 1;
	}
	bool not_in_workset(int x)
	{
		if(x != workset[0] && x != workset[1])
			return 1;
		return 0;
	}
	void func()
	{
		int size = 3;
		for(int i = 0;i<n;i++)
		{
		
			int x = page_visit[i];
			cout << "visiting: "<<char(x+97)<<endl;
			if(page_miss(x))  //page missing
			{
				t_current = i;
				if(t_current - t_last > T) //standing set is too big;
				{
				    	cout <<"missing and the standing set is too big\n";
						node * p = root;
						node * p2 ;
						while(p!=NULL)
						{
						//cout << workset[0]<< " "<<workset[1] <<endl;
						//cout << not_in_workset(p -> page_number)<<endl;
							if( not_in_workset(p -> page_number))
							{
								if( p != root)
								{
									if(p -> next == NULL)
									{
										cout << "delete: "<< char(p -> page_number  + 97) <<endl;
										size --;
										p -> prev -> next = NULL;
										p -> prev = NULL;
									}
								
									else
									{
										p -> prev -> next = p -> next;
										p -> next -> prev = p -> prev;
										cout << "delete: "<< char(p -> page_number  + 97) <<endl;
										size --;
									}
								}
								if(p == root)
								{
									cout << "delete: "<< char(p -> page_number  + 97) <<endl;
									size --;
									root = p -> next;
									root -> prev = NULL;
								}
							}
							p2 = p;
							p = p->next;
						
						}
					
					node * q = new node();
					q -> page_number = x;
					cout <<"add: "<<char(x + 97)<<endl;
					p2 -> next = q;
					q -> prev = p2;
					q -> next = NULL;
				
				}
				else  //standing set is too small
				{
				 	cout <<"missing and the standing set is too small\n";
					 cout <<"add: "<<char(x + 97)<<endl;
					node * q = new node();
					q -> page_number = x;
					node * p = root;
					while( p-> next != NULL)
						p = p -> next;
				
					p -> next = q;
					q -> prev = p;
					q -> next = NULL;
			
				}
		
			}
	
		workset[0] = workset[1];
		workset[1] = x;
	
	
	
		}
	
	



	}
	int main()
	{
   
		root = new node();
		root -> prev = NULL;
		root -> page_number = 0;
		root -> next = new node();
		root -> next -> page_number = 3;
		node * p = root -> next;
		p -> prev = root;
		p -> next = new node();
		p -> next -> page_number = 4;
		p -> next -> prev = p;
		p -> next -> next = NULL;
		func();

	}
 
## 扩展思考题
（1）了解LIRS页置换算法的设计思路，尝试用高级语言实现其基本思路。此算法是江松博士（导师：张晓东博士）设计完成的，非常不错！

参考信息：

 - [LIRS conf paper](http://www.ece.eng.wayne.edu/~sjiang/pubs/papers/jiang02_LIRS.pdf)
 - [LIRS journal paper](http://www.ece.eng.wayne.edu/~sjiang/pubs/papers/jiang05_LIRS.pdf)
 - [LIRS-replacement ppt1](http://dragonstar.ict.ac.cn/course_09/XD_Zhang/(6)-LIRS-replacement.pdf)
 - [LIRS-replacement ppt2](http://www.ece.eng.wayne.edu/~sjiang/Projects/LIRS/sig02.ppt)
