# python IO-File
------

### 文件读写方式

| 方式 | 说明 |
|:-:|:-:|
| r | 只读方式打开;文件必须存在,文件指针放在文件开头 |
| w | 只写方式打开;文件不存在则创建,存在则覆盖 |
| a | 追加方式打开;文件存在,指针放在文件末尾;不存在则创建新的 |
| r+ | 读写方式打开;同 r |
| w+ | 读写方式打开;同 w |
| a+ | 读写方式打开;同 a |
|  | rb/wb/ab/rb+/wb+/ab+ 二进制方式打开 |


### 读取/写文件
| 方法 | 描述 | 利 | 弊 |
| :-: | :-: | :-:| :-: |
| read() | 一次性读取文件到内存中,返回字符串 | 简单,速度快 |文件大,占用内存大|
| readline() | 逐行读取,返回list | 占用内存小 | 速度慢 |
| readlines() | 一次性读取所有内容,返回list | 速度快 | 文件大,占用内存大 |
```
# errors 忽略编码错误
try:
    f = open('/file/user.json', 'r',encoding='UTF-8',errors='ignore')
    print(f.read())
finally:
    if f:
        f.close()
# 简化(自动调用 close)
with open('/file/user.json', 'r',encoding='UTF-8') as f:
    print(f.read())
# 更好的写法(strip:去掉末尾的'\n')
with open('/file/user.text','r') as file:
    for lineStr in file:
        print(lineStr.strip())
		
# 写文件
with open('file/test.text','r+',encoding='UTF-8') as file:
	file.write('Hello, world!')
```

## StringIO/BytesIO 内存中读写str/bytes
StringIO 与 cStringIO 用法相同,不过cString比StringIO执行效率更高,通常引入的做法是
```
try:  
    import cStringIO as StringIO  
except ImportError:  
    import StringIO 
```
* 常用方法:
`read()` `readline()` `readlines()` `close() 释放缓冲区` `flush() 刷新缓冲区` 
`tell() 返回当前读写位置` `getvalue() 返回StingIO中所有数据` `write()` `writeline()`
> seek(pos[,mode])：移动当前读写位置至pos处，可选参数mode为0时将读写位置移动到 pos处,
为1时将读写位置从当前位置移动pos个长度，为2时读写位置置于末尾处再向后 移动pos个长度。默认为0

* StringIO 要么用来读，要么用来写，不能同时用 ？
因为牵扯到读写位置指针的问题,如下代码所示,默认读写开始位置为 0,当调用 write() 方法时,
读写位置指针为 写入的字符串长度 + 1,而调用read()/readline()/readlines()是从当前位置开始读取的。
```
try:  
    import cStringIO as StringIO  
except ImportError:
    import StringIO 
    
f = StringIO();
f.write('Hello World');
print("当前位置",f.tell())
s = f.readline();
print("str = ",s)
# 输出 ：
# 当前位置 11
# str = 

f2 = StringIO('Hello World');
print("当前位置",f2.tell())
s2 = f2.readline();
print("str = ",s2);
# 输出 ：
# 当前位置 0
# str = Hello World
```

## 操作文件和目录
```
print(os.name)
print(os.environ)

print("当前目录的绝对路径 = ",os.path.abspath('.'))
# join 可以正确处理不同操作系统的路径分隔符
print("合并路径",os.path.join('/Users/michael', 'testdir'))
# 创建一个目录
# os.mkdir('/Users/michael/testdir')
# 删掉一个目录:
# os.rmdir('/Users/michael/testdir')
# 拆分路径
print(os.path.split('/Users/michael/testdir/file.txt'))
# 得到文件扩展名
print(os.path.splitext('/path/to/file.txt'))
# 文件重命名
os.rename('test.txt', 'test.py')
# 删掉文件:
os.remove('test.py')
```

## 序列化
* dumps() 序列化对象为 bytes
```
import pickle
# 返回一个 bytes
pickle.dumps(obj)
```
 
* dump() 序列化对象后写入文件
```
import pickle
file = open('/uesr.text','wb')
pickle.dump(obj,file)
file.close()
```
* load() 从文件中读取序列化的对象
```
file = open('/uesr.text','wb')
p = pickle.load(file)
file.close()
```

## JSON
* object to JSON
```
import json
class Book(object):
	def __int__(self,name):
		self.name = name
def book2Json(obj):
	return {
		'name':obj.name
	}
book = Book('<My Book>')
# dumps() 返回一个 str
# dump() 将 str 写入文件
j = json.dumps(book,default=book2Json)

# 简单的方法
j2 = json.dumps(book1, default=lambda obj: obj.__dict__)
```

* JSON to Object
```
import json
def json2Book(str):
	return Book(str['name'])
obj = json.loads(j2,object_hook = json2Book)
```


