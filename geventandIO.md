###协程  
微线程  
遇到IO就切换  
    
	import gevent
	def foo():
	    print('hello bar')
	    gevent.sleep(2)
	    print('hello bar too')
	
	def bar():
	    print('hello foo')
	    gevent.sleep(1)
	    print('hello foo too')
	
	gevent.joinall([gevent.spawn(foo),gevent.spawn(bar)])

协程gevent并发爬取网页  
  
	__author__ = 'Mr.Bool'
	import gevent
	import time
	from urllib import request
	from gevent import monkey
	monkey.patch_all()
	def f(url):
	    print("GET:",url)
	    resp=request.urlopen(url)
	    data=resp.read()
	    print("%d bytes is received from %s "%(len(data),url))
	
	urls=[
	    "https://www.python.org",
	    'https://github.com/',
	    'https://www.hao123.com'
	]
	tims_start=time.time()
	for url in urls:
	    f(url)
	print("同步cost",time.time()-tims_start)
	
	async_time_start=time.time()
	gevent.joinall(
	    [
	        gevent.spawn(f,'https://www.python.org'),
	        gevent.spawn(f,'https://github.com/'),
	        gevent.spawn(f,'https://www.hao123.com'),
	     ]
	)
	print("异步cost",time.time()-async_time_start)  
通过gevent实现单线程下的多socket并发  
	
	import sys
	import socket
	import time
	import gevent
	 
	from gevent import socket,monkey
	monkey.patch_all()
	 
	 
	def server(port):
	    s = socket.socket()
	    s.bind(('0.0.0.0', port))
	    s.listen(500)
	    while True:
	        cli, addr = s.accept()
	        gevent.spawn(handle_request, cli)
	 
	 
	 
	def handle_request(conn):
	    try:
	        while True:
	            data = conn.recv(1024)
	            print("recv:", data)
	            conn.send(data)
	            if not data:
	                conn.shutdown(socket.SHUT_WR)
	 
	    except Exception as  ex:
	        print(ex)
	    finally:
	        conn.close()
	if __name__ == '__main__':
	    server(8001)
  
	import socket
client:
 
	HOST = 'localhost'    # The remote host
	PORT = 8001           # The same port as used by the server
	s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect((HOST, PORT))
	while True:
	    msg = bytes(input(">>:"),encoding="utf8")
	    s.sendall(msg)
	    data = s.recv(1024)
	    #print(data)
	 
	    print('Received', repr(data))
	s.close()  
100个:  
  
	import socket
	import threading
	
	def sock_conn():
	
	    client = socket.socket()
	
	    client.connect(("localhost",8001))
	    count = 0
	    while True:
	        #msg = input(">>:").strip()
	        #if len(msg) == 0:continue
	        client.send( ("hello %s" %count).encode("utf-8"))
	
	        data = client.recv(1024)
	
	        print("[%s]recv from server:" % threading.get_ident(),data.decode()) #结果
	        count +=1
	    client.close()
	
	
	for i in range(100):
	    t = threading.Thread(target=sock_conn)
	    t.start()

事件驱动：  
根据事件做出反应，先将事件装进队列，然后再一个一个取出。   
  
	__author__ = 'Mr.Bool'
	import gevent
	def foo():
	    print('hello bar')
	    gevent.sleep(2)
	    print('hello bar too')
	
	def bar():
	    print('hello foo')
	    gevent.sleep(1)
	    print('hello foo too')
	
	gevent.joinall([gevent.spawn(foo),gevent.spawn(bar)])  
网页  
  
	__author__ = 'Mr.Bool'
	import gevent
	import time
	from urllib import request
	from gevent import monkey
	monkey.patch_all()
	def f(url):
	    print("GET:",url)
	    resp=request.urlopen(url)
	    data=resp.read()
	    print("%d bytes is received from %s "%(len(data),url))
	
	urls=[
	    "https://www.python.org",
	    'https://github.com/',
	    'https://www.hao123.com'
	]
	tims_start=time.time()
	for url in urls:
	    f(url)
	print("同步cost",time.time()-tims_start)
	
	async_time_start=time.time()
	gevent.joinall(
	    [
	        gevent.spawn(f,'https://www.python.org'),
	        gevent.spawn(f,'https://github.com/'),
	        gevent.spawn(f,'https://www.hao123.com'),
	     ]
	)
	print("异步cost",time.time()-async_time_start)  
select  
  
	__author__ = 'Mr.Bool'
	import select
	import socket
	import queue
	
	server=socket.socket()
	server.bind(('localhsot',9999))
	server.listen(1000)
	server.setblocking(False)
	intputs=[server,]
	outputs=[]
	msg_dic={}
	while True:
	    readable,writeable,excepational=select.select(intputs,outputs,intputs)
	    for r in readable:
	        if r is server:
	            print("有新的链接")
	            conn,addr=server.accept()
	            intputs.append(conn)
	            msg_dic[conn]=queue.Queue()
	        else:
	            data=r.recv(1024)
	            print(data)
	            msg_dic[r].put(data)
	            outputs.append(r)
	    for w in writeable:
	        data_to_client=msg_dic[w].get()
	        w.send(data_to_client)
	        outputs.remove(w)
	    for e in excepational:
	        if e in outputs:
	            outputs.remove(e)
	        intputs.remove(e)
	        del msg_dic[e]

