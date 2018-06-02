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
元组是有序的，不可变的(不允许删除/修改/增加,并不是绝对的),当只有一个元素时需要加',':tuple = (1,);
可以拆包`name,age,id = mTuple('Jack',26,1212098)`,
比列表list好的地方: 是不可变对象(性能好,线程安全,可以用作字典的key,可拆包特性)
```
mTuple = ('Jack',24,'BeiJing','man','China');
name,age,*other = mTuple
print(name,age,other)
# 打印： Jack,24 ['BeiJing','man','China']

t = ('Jack',[12,13,14])
t[1].append(15)
# 元组不可变不是绝对的，将打印： Jack [12,13,14,15]
# 不建议这么做
```

3. dict = {'name':'java','age':21,'name':'javaScript','age':18}
查找和插入的速度极快，不会随着key的增加而变慢；需要占用大量的内存，内存浪费多
dict的key必须是不可变对象(可hash)

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

### namedtuple
nametup;e 是 tuple 的子类
```
from collections import namedtuple
User = namedtuple('User',['name','age','sex','address'])
user = User(name = 'Jack',age = 12,sex = 'man',address = 'TianJin China')
# 相当于创建了一个User对象,比自己创建对象要简单、方便

t = ('jack',34,'man')
user2 = User(*t,'TianJin China')
# 传入 tuple 初始化 namedtuple,其实就是参数为多个参数(类似：*args,**kwargs)

t1 = ('jack',34,'man','BeiJing')
user3 = User._make(t1)
# 使用 _make 方法构建,在构建多参数时可以使用，灵活性不是很高

d = user3._asdict()
# 转化为字典: {name:jack,age:34...}

name,age,*other = user3
# 使用了 tuple 的拆包特性
```

### defaultdict

`setdefault(key, default=Non)`:如果字典中包含有给定键，则返回该键对应的值，否则返回为该键设置的值
`defaultdict()`:在访问不存在的key时，会抛出keyError的错误，而使用该函数会根据设置的"类型名称"来
设置默认值(如 defaultdict(int) 会返回 0)
```
from collections import defaultdict

d = defaultdict(int)
# d['a'] 返回 0
d1 = defaultdict(list)
# d1['a'] 返回 []
def show() {
	return {
		'name': "",
		'age' : 23
	}
}
d2 = defaultdict(show)
# d2['a'] 返回 {'name':'','age':23},(默认值是 dict,通过设置函数返回)
```

### deque 双端队列
deque 是线程安全的,list 不是线程安全的;
一般容器中是存放相同类型的数据(list,deque),tuple则不同，它可以当作一个对象处理(里面有它的参数)；
注意它的copy是浅克隆


### Counter
继承与 dict 用于做大量数据的统计
```
from collections import Counter

numbers = ['a','a','b','c','b','c','a']
c = Counter(numbers)
print(c)
# 打印： Counter({'a': 3, 'b': 2, 'c': 2})
# c['a'] 打印 3

m = most_common(3)
# 这样做性能很好,返回最多前3个元素, [('a',3),('b',2),('c',2)]

c.elements()
# 返回一个迭代器
```

### OrderedDict
dict 的子类， dict 中的 key 是无序的,使用OrderedDict可以保证key的顺序
它的顺序是指插入的顺序并不是key自身的顺序,OrderedDict 可以实现一个先进先出的 dict,
当容量超出限制时，可以删除最早添加的key
```
from collections import OrderedDict

od = OrderedDict([('a', 1), ('b', 2), ('c', 3)])
print(od)
print(od.keys())
# 打印：OrderedDict([('a', 1), ('b', 2), ('c', 3)])
# odict_keys(['a', 'b', 'c'])

t = od.popitem()
# 删除最后一个元素，并返回它
# 打印 t = ('c', 3)

t2 = od.pop('a')
# 返回 a 所对应的值 1

odd = OrderedDict()
odd['a'] = 'jack'
odd['b'] = 'Lee'
odd['c'] = 'Wang'
odd.move_to_end('a')
# 将指定的key移动到末尾
# OrderedDict([('b', 'Lee'), ('c', 'Wang'), ('a', 'jack')])
```

### ChainMap
ChainMap类可把多个字典或者其它映射对象放在一起，组成一个单一的、可更新的映射对象,
所有传入来的映射对象保存在一个列表里
```
from collections import ChainMap

m1 = {'a':'1','b':'2','c':'3'}
m2 = {'wa':'M','wb':'Q','wc':'Y'}

c = ChainMap(m1,m2)
print(c.maps)
# maps 属性返回用户可更新的映射对象列表
# [{'a': '1', 'b': '2', 'c': '3'}, {'wa': 'M', 'wb': 'Q', 'wc': 'Y'}]

c.maps[0]['a'] = 'apple'
# 修改元素

m3 = {'a':1,'b':2}
m4 = {'c':3,'d':4}
c2 = ChainMap(m3,m4)
m5 = {'age':23}
print(c2.maps)
c2 = c2.new_child(m5)
print(c2.maps)
# new_child 创建一个新的ChainMap对象，在列表第一个元素里插入映射对象m，后面紧跟
# 原来所有映射对象。如果m是None，就插入一个空的字典对象
# 打印 [{'age': 23}, {'a': 1, 'b': 2}, {'c': 3, 'd': 4}]

x = {'a': 1, 'b': 2}
y = {'b': 10, 'c': 11}
z = ChainMap(y, x)
print(z.maps)
z = z.parents
print(z.maps)
# parents 返回除第一个映射对象之外的所有映射对象的 ChainMap对象
# [{'b': 10, 'c': 11}, {'a': 1, 'b': 2}]
# [{'a': 1, 'b': 2}]
```
