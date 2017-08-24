###configparser  
configparser模块用于操作配置文件（parser解析)  
配置文件可以包含一个或多个节（section),每个节可以有多个参数（键值对)配置文件中键值对可以用冒号，也可以用等号。  
[Default]  
[wj]  
Password=123  
Quotation=100  
  
[zw]  
Password=321 
Quotation=100  
###configparser使用  
####读取
1. 引入configparser  
`import configparser`
2. 建立对象  
`config = configparser.ConfigParser()`  
3. 读取配置文件  
`config.read(u"配置文件路径")` 
4. 操作  
`config.sections()读取所有section，返回的是列表 `    
`config.get('section节点名','键名')返回的是键的值  `  
`config['section节点名','键名']返回的是键的值`    

####写入  
1. 引入configparser  
`import configparser`
2. 建立对象  
`config = configparser.ConfigParser()`  
3. 将信息存入config对象  
`config.add_section('节点名')添加节点`
`config.set('节点名','键','值')` 
4. 存入配置文件
`config.write('文件名')`  
###configparser方法  
1. config=configparser.ConfigParser()  
创建configparser实例  
  
2. config.sections()  
返回配置文件中节序列  
  
3. config.options(section)  
返回某个项目中的所有键的序列  
  
4. config.get(section,option)  
返回section节中，option的键值  
  
5. config.add_section(str)  
添加一个配置文件节点(str)  
  
6. config.set(section,option,val)  
设置section节点中，键名为option的值(val)  
  
7. config.read(filename)  
读取配置文件  
  
8. config.write(obj_file)  
写入配置文件  