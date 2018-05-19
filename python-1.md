### python 进制转换

1. int(x, base=10) 函数：将一个数字或字符转化为整形(默认十进制)
```
int(12.34) # 12
int('10100111110',2) # 二进制转十进制 1342
int('17',8) # 八进制转十进制 15
int('0xf',16) # 十六进制转十进制 15
```

2. 十进制转二进制、八进制、十六进制
```
bin(5) # 转二进制 0b101
oct(5) # 转八进制 0o5
hex(5) # 转十六进制 0x5
```

3. float(),bool(),str()

### 迭代 list 加下标
```
# zip 可以合并两个 list
list = zip([1,2,3],['a','b','c'])
for i, value in enumerate(['A', 'B', 'C']):
	print(i+","+value)
```

### 迭代 dict
```
d = {'a': 1, 'b': 2, 'c': 3}
# 遍历 key(默认)
for key in d:
	print(key)
	
# 遍历 value
for value ind.values():
	print(value)
	
# 遍历 key value
for k, v in d.items():
	print(k+"-"+v)
```

### 列表生成器
```
# 生成 [1x2, 3x4, 5x6, 7x8, ..., 99x100]
print([x*(x+1) for x in range(1,100,2)])

# for 后加 if判断
print(x * x for x in range(1, 11) if x % 2 == 0])

# 两层循环
print([m + n for m in 'ABC' for n in 'XYZ'])

# 字符转化为大写(非字符忽略)
L = ["java","python",176]
print([x.upper() for x in L if isinstance(x, str) ])
```

### list、Tuple、Dict、Set
1. list = [1,2,3,4]
列表是可变的，有序的。可以随意增加/删除的数据集合

2. tuple = (1,2,3,4)
元组是有序的，不可变的(不允许删除/修改/增加),当只有一个元素时需要加',':tuple = (1,)

3. dict = {'name':'java','age':21,'name':'javaScript','age':18}
查找和插入的速度极快，不会随着key的增加而变慢；需要占用大量的内存，内存浪费多
dict的key必须是不可变对象

4. set = ([1,2,3,4])
无序不重复元素集,要创建一个set，需要提供一个list作为输入集合

### python 闭包
闭包：在外部函数内定义了内部函数，并且内部函数使用了外部函数的变量，最后外部函数返回了外部函数;
闭包的作用： 可以实现装饰器模式

匿名函数：lambda 表示匿名函数，冒号前面的 x 表示函数参数。
匿名函数有个限制，就是只能有一个表达式，不写return，返回值就是该表达式的结果。

decorator：decorator 本质上就是一个高阶函数，它接收一个函数作为参数，然后，返回一个新函数。
```
def log(f):
    def fn(x):
        print('call ' + f.__name__ + '()...')
        return f(x)
    return fn
def show(s):
	print(s+" call")
	
t = show('12')

n = log(show)
n('34')
# 输出
# 12 call
# call show()...
# 34 call

```
python 中引入 @ 标识符，简化了代码的编写，调用时只要在函数的定义前加 @ 和 装饰器名称即可
*args : 标识一个可迭代tuple
*kw : 表示一个参数dict
```
def you(*args):
	print(args)
you(1,2,3,4)
# 输出：(1, 2, 3, 4)

def my(**kw):
	print(kw)
my(user='jack',age=26)
#输出：{'user': 'jack', 'age': 26}
```

functools：把原函数的所有必要属性都一个一个复制到新函数上
```
import functools
def log(f):
    @functools.wraps(f)
    def wrapper(*args, **kw):
        print 'call...'
        return f(*args, **kw)
    return wrapper
```

偏函数 functools.partial：把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单
```
import functools

show = functools.partial(sorted, cmp=lambda s1, s2: cmp(s1.upper(), s2.upper()))
print show(['bob', 'about', 'Zoo', 'Credit'])
# 输出：['about', 'bob', 'Credit', 'Zoo']

int2 = functools.partial(int, base=2)
int2('1000000')
# 输出：64
```

Python是动态语言，解释执行，因此Python代码运行速度慢。
如果要提高Python代码的运行速度，最简单的方法是把某些关键函数用 C 语言重写，这样就能大大提高执行速度

### python 对象
> setattr(self,k,v): 用于设置属性值
> types.MethodType() :把一个函数变为一个方法
```
import types
def show(self):
    print('show..',)

class Person(object):
    def __init__(self, name):
        self.name = name

p1 = Person('Bob')
p1.get_grade = types.MethodType(show, p1, Person)
print(p1.get_grade())
```
继承
```
class Person(object):
	def __init__(self,name):
		self.name = name
class Student(Person):
	def __init__(self,name,age):
		super(Student,self).__init__(name)
		self.age = age
```

### 函数
> dir() 获取变量的所有属性
> type() 获取变量的类型，它返回一个 Type 对象
> isinstance() 判断它是否是某种类型的实例
> cmp(x,y) 函数用于比较2个对象
> @property 把一个getter方法变成属性,@property本身又创建了另一个装饰器@score.setter，
负责把一个setter方法变成属性赋值
```
class Student(object):
    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
```
>  __slots__ 限制当前类所能拥有的属性，如果不需要添加任意动态的属性，使用__slots__也能节省内存
```
class Student(obj):
	__slots__ = ('name','age')
	def __init__(self, name, age):
		self.name = name
		self.age = age
```