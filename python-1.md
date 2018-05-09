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