###OptionParser  
OptionParser用于处理命令行参数  
使用OptionPrser步骤：  
1. 导入模块  
``` from optparse import OptionParser```      
2. 创建对象  
```parse = OptionParser()```  
3.增加命令行描述，每个命令行参数就是由参数名字符串和参数属性组成的。如 -f 或者 –file 分别是长短参数名：  
``` parse.add_option("-s","--host",dest="host",help="ip地址")  ```  
`parse.add_option("-p","--port",dest="port",action='store',help="端口号")`  
`parse.add_option("-U","--username",dest="username",help="登陆用户名") ` 
`parse.add_option("-P","--password",dest="password",help="登陆密码")`  
4. 一旦你已经定义好了所有的命令行参数，调用 parse_args() 来解析程序的命令行,解析后返回两个值，option是一个对象，当需要调用命令行接受的参数是，可以使用option.host,option.port等方式去出命令行里传进来的值，args接收的是没有在add_option里声明的参数，用列表接收.  
`(option,args)=parse.parse_args()`  
