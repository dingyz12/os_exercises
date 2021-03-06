# 同步互斥(lec 18) spoc 思考题


- 有"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的ucore_code和os_exercises的git repo上。

## 个人思考题

### 基本理解
 - 什么是信号量？它与软件同步方法的区别在什么地方？
 - 什么是自旋锁？它为什么无法按先来先服务方式使用资源？
 - 下面是一种P操作的实现伪码。它能按FIFO顺序进行信号量申请吗？
```
 while (s.count == 0) {  //没有可用资源时，进入挂起状态；
        调用进程进入等待队列s.queue;
        阻塞调用进程;
}
s.count--;              //有可用资源，占用该资源； 
```

> 参考回答： 它的问题是，不能按FIFO进行信号量申请。
> 它的一种出错的情况
```
一个线程A调用P原语时，由于线程B正在使用该信号量而进入阻塞状态；注意，这时value的值为0。
线程B放弃信号量的使用，线程A被唤醒而进入就绪状态，但没有立即进入运行状态；注意，这里value为1。
在线程A处于就绪状态时，处理机正在执行线程C的代码；线程C这时也正好调用P原语访问同一个信号量，并得到使用权。注意，这时value又变回0。
线程A进入运行状态后，重新检查value的值，条件不成立，又一次进入阻塞状态。
至此，线程C比线程A后调用P原语，但线程C比线程A先得到信号量。
```

### 信号量使用

 - 什么是条件同步？如何使用信号量来实现条件同步？
 - 什么是生产者-消费者问题？
 - 为什么在生产者-消费者问题中先申请互斥信息量会导致死锁？

### 管程

 - 管程的组成包括哪几部分？入口队列和条件变量等待队列的作用是什么？
 - 为什么用管程实现的生产者-消费者问题中，可以在进入管程后才判断缓冲区的状态？
 - 请描述管程条件变量的两种释放处理方式的区别是什么？条件判断中while和if是如何影响释放处理中的顺序的？

### 哲学家就餐问题

 - 哲学家就餐问题的方案2和方案3的性能有什么区别？可以进一步提高效率吗？

### 读者-写者问题

 - 在读者-写者问题的读者优先和写者优先在行为上有什么不同？
 - 在读者-写者问题的读者优先实现中优先于读者到达的写者在什么地方等待？
 
## 小组思考题

1. （spoc） 每人用python threading机制用信号量和条件变量两种手段分别实现[47个同步问题](07-2-spoc-pv-problems.md)中的一题。向勇老师的班级从前往后，陈渝老师的班级从后往前。请先理解[]python threading 机制的介绍和实例](https://github.com/chyyuu/ucore_lab/tree/master/related_info/lab7/semaphore_condition)



丁延卓 计21 第十题
      
信号量：


    #!/usr/bin/env python
    import threading
	import random
	import time
	countMutex = threading.Semaphore(1)
	writeMutex = threading.Semaphore(1)
	canReadMutex = threading.Semaphore(1)
	rCount = 0
	class Reader(threading.Thread):
		def __init__(self):
			threading.Thread.__init__(self)
			
		def run(self):
			global countMutex, rCount, writeMutex,canReadMutex
			canReadMutex.acquire()
			canReadMutex.release()
			
			countMutex.acquire()
			if rCount == 0:
				writeMutex.acquire()
			rCount += 1
			countMutex.release()
			print("proc(%s) start  reading" %(self.name))
			time.sleep(random.randrange(1,2))
			print("proc(%s) end  reading" %(self.name))
			countMutex.acquire()
			rCount -= 1
			if rCount == 0:
				writeMutex.release()
			countMutex.release()
	class Writer(threading.Thread):
		def __init(self):
			threading.Thread.__init__(self)
			
		def run(self):
			global countMutex,rCount,writeMutex,canReadMutex
			canReadMutex.acquire()
			writeMutex.acquire()
			print("proc(%s) start writing " %(self.name))
			time.sleep(random.randrange(1,2))
			print("proc(%s) end writing " %(self.name))
			writeMutex.release()
			canReadMutex.release()
	if __name__ == "__main__":
		for p in range(1,15):
			i = random.randint(1,4)%2
			if i == 0:
				p = Reader()
				p.start()
			else:
				p = Writer()
				p.start()
	
			
条件变量：


    	#!/usr/bin/env python
	import threading
	import random
	import time
	condw = threading.Condition()
	condr = threading.Condition()
	cond = threading.Condition()
	wr = 0
	ar = 0
	ww = 0 
	aw = 0
	a_list = []
	a_num = 0
	class Reader(threading.Thread):
		def __init__(self):
			threading.Thread.__init__(self)
		def run(self):
			#print(self.name)
			global condw, condr,cond, wr, ar, ww, aw, a_list
			if cond.acquire():
				while ww + aw > 0: 
					wr +=1
					a_list.append(0)
					condr.acquire()
					cond.release()
					condr.wait()
					cond.acquire()
					condr.release()
					wr-=1
				ar = ar + 1
				#print("proc(%s) ar(++) is: %s" %(self.name,ar))
				cond.release()
			print("Reader(%s) is reading, active reader is %s" %(self.name, ar))
			time.sleep(1)
			#print(self.name)
			if cond.acquire():
				ar = ar - 1
				#print("proc(%s) ar(--) is: %s" %(self.name,ar))
				if ar == 0 and ww >0 :
					condw.acquire()
					a_list.pop()
					#print("condw notify")
					condw.notify()
					condw.release()
				cond.release()
	class Writer(threading.Thread):
		def __init(self):
			threading.Thread.__init__(self)
		def run(self):
			global condw, condr,cond, wr, ar, ww, aw, a_list
			if cond.acquire():
				while aw + ar > 0 :
					ww +=1
					a_list.append(1)
					condw.acquire()
					#print("W1%s" %self.name)  
					cond.release()              
					condw.wait()
					#cond.release()
					#print("W2%s" %self.name) 
					#print("W1%s" %self.name) 
					ww -=1
				aw +=1
				condw.release()
			print("Writer(%s) is writing, now wait write %s" %(self.name,ww))
			time.sleep(1)
			if cond.acquire():
				aw -=1
				while len(a_list) != 0 :
					#print(a_list)
					l = a_list.pop()
					#print(l)
					if l == 1 :
						condw.acquire()
						condw.notify()
						condw.release()
						#a_list.pop()
						break
					else:
						condr.acquire()
						condr.notify()
						condr.release()
						#a_list.pop()
				cond.release()
	if __name__ == "__main__":
		#0 reader 1 writer
		list_t = [0,0,0,0,1,0,1]
		for i in list_t:
			if i == 1:
				p = Writer()
				p.start()
			else:
				p = Reader()
				p.start()
