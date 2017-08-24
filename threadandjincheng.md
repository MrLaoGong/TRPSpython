###python线程与进程（process)  
进程：资源的集合，本身不执行  
线程：一串指令  

线程共享内存，进程独立内存，进程启动慢，线程启动快，线程之间数据可以交流，进程之间数据不可以交流。  
###守护进程   
正常情况主线程与子线程没有依赖关系，程序执行，你走我也走。子子线程里使用join方法，使主线程等待子线程执行完毕后才执行。子线程设置setDaemon(True)把当前线程设置成守护线程，该线程就不重要了，一定在start之前设置。  python解释器同一时段只放行一个线程
###GIL锁(全局解释器锁)  
解释器出口控制，同一时间只有一个线程可以工作。其余线程等着。启动的线程实实在在在CPU上了，但同一时间只有一个线程可以拿到数据。全局解释器锁。  
###线程锁（互斥锁）  
threading.Lock()  lock.acquire()  lock.release()  
###递归锁  
threading.RLock()  多个门被锁，钥匙识别不出哪个对应的门  
##semaphore(信号量  
semaphore=threading.BoundedSemaphore(5)#最多允许5个线程同时运行  
semaphore.acquire() lock.release()  控制同一时刻允许有多少个线程可以执行，实例，线程池，进程池
###Event  
event=threading.Event()  
event.wait()  
event.set()  
event.clear()
	__author__ = 'Mr.Bool'
	import time
	import threading
	event=threading.Event()
	def lighter():
	    count=0
	    event.set()
	    while True:
	        if count>5 and count<10:
	            event.clear()#变红灯
	            print("红灯")
	        elif count>10:
	            event.set()#变绿灯
	            print("变绿灯")
	            count=0
	        else:
	            print("现在是绿灯")
	        time.sleep(1)
	        count+=1
	
	def car(name):
	    while True:
	        if event.is_set():
	            print("%s 可以通行"%name)
	        else:
	            print("%s 不可通行"%name)
	            event.wait()
	        time.sleep(1)
	
	t1=threading.Thread(target=lighter)
	t1.start()
	t2=threading.Thread(target=car,args=('Benz',))
	t2.start()  
###队列  
优点：解耦高效  
queue.Queue()先进先出  
queue.LifoQueue()后进先出买水果  
queue.PriorityQueue()存储数据时可以设置优先级  
put(位置，数据)#put(1，'wj')放置数据在第一位
	__author__ = 'Mr.Bool'
	import queue
	import time
	import threading
	q=queue.Queue()
	def Proceder():
	    count =0
	    while True:
	        q.put("骨头%s"%count)
	        print("生产了骨头%s"%count)
	        count+=1
	        time.sleep(1)
	def eat(name):
	    while True:
	        g=q.get()
	        print("%s ear %s"%(name,g))
	        time.sleep(0.3)
	
	p=threading.Thread(target=Proceder)
	p.start()
	d1=threading.Thread(target=eat,args=('wangwang',))
	d1.start()
	d2=threading.Thread(target=eat,args=('wangcai',))
	d2.start()  
###多进程  
import multiprocessing  
在multiprocessing里存在Process，Pool,Queue,Pipe,Manager  
Process启动进程  
Pool创建进程池使用Pool时一定先close()，后join()  
Queue数据交互  
Pipe管道数据交互  
Manager数据交互共享，可新建dict，list等进行共享
使用实例：  
1. process  

	import multiprocessing
	import time,threading
	import os
	def threadingrun(name):
	    # print("%s is a big man"%name)
	    pass
	
	def run(name):
	    print("now pid:%s"%os.getpid())
	    print("parent pid:%s"%os.getppid())
	    t=threading.Thread(target=threadingrun,args=(name,))
	    t.start()
	if __name__ == '__main__':
	    for i in range(10):
	        p1=multiprocessing.Process(target=run,args=("wj",))
	        p1.start()  
2. poll
	from multiprocessing import Pool  
	import os  
	import time  
	def processingtest(s):  
	    time.sleep(2)  
	    print("in process",os.getpid())  
	    return s+100  
	def Bar(arg):  
	    print('-->exec done:',arg,os.getpid())  
	    pass  
	if __name__=="__main__":  
	    pool=Pool(3)  
	    print("主进程")  
	    for i in range(10):  
	        pool.apply_async(func=processingtest,args=(i,),callback=Bar)  
	    print('end')  
	    pool.close()  
	    pool.join()  
3. Queue:   
	from multiprocessing import Process,Queue,Pipe,Manager
	def processingtest(s):
	    s.put([32,'hello'])
	    print('子进程',s.get())
	
	if __name__=="__main__":
	    parent_pipe,child_pipe=Pipe()
	    q=Queue()
	    p=Process(target=processingtest,args=(q,))
	    p.start()
	    print('父进程',q.get())  
4. Pipe:  
	from multiprocessing import Process,Queue,Pipe,Manager  
	def processingtest(child_pipe):  
	    child_pipe.send("hello parent")  
	    print(child_pipe.recv())  
	if __name__=="__main__":  
	    parent_pipe,child_pipe=Pipe()  
	    p=Process(target=processingtest,args=(child_pipe,))  
	    p.start()  
	    print(parent_pipe.recv())  
	    parent_pipe.send("hello son")    
5. Manager:  
	from multiprocessing import Process,Manager
	import os
	def processingtest(d,l):
	    d[os.getpid()]=os.getpid()
	    l.append(os.getpid())
	    pass
	if __name__=="__main__":
	    with Manager() as manager:
	        d=manager.dict()
	        l=manager.list()
	        obj_list=[]
	        for i in range(10):
	            p=Process(target=processingtest,args=(d,l))
	            p.start()
	            obj_list.append(p)
	        for i in range(10):
	            obj_list[i].join()
	        print(d)
	        print(l)  

