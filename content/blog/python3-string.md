+++
date = "2017-04-15T16:18:51+08:00"
description = ""
title = "Python3 List, Tuple, Dict & Set"
slug = 'python3-containers'

+++


# String
- Python3默认字符串是Unicode编码的

# List

```python
classmates = ['Michael', 'Bob'];
print(len(classmates));
# first element
print(classmates[0]);
# last element
print(classmates[-1]);

# 追加到末尾
classmates.append('Adam')

# 插入到指定位置
classmates.insert(1, "Jack")

# 删除末尾
classmates.pop()
# 删除指定位置
classmates.pop(i)

# 元素类型不必相同
s = ['python', 'java', ['asp', 'php'], 1234]
```


# Tuple

一旦初始化就不能改变
```python
t = (1, 2)

# 单个元素的tuple
t = (10, )
t = ('a', )
```

```python
# -*- coding: utf-8 -*-
L = ["Bart", 'Lisa', 'Adam']
for name in L:
  print "Hello", name
```

# Dict
```python
# 定义字典
d = ['Michael': 95, 'Bob': 75, 'Tracy': 85]
# 取元素
print d['Bob']
# 访问不存在的元素会抛出异常
d['Thomas']
#判断是否存在某个key
'Thomas' in d
d.get('Thomas') # 将返回None，建议使用这种方式获取
d.get('Thomas', -1) # 自定义默认返回值
```

# Set
- 集合类型，无序，不可重复

```python
s = set([1, 2, 3])
# Add element
s.add(4)
# Delete element
s.remove(2)
# 交集
s1 & s2
# 并集
s1 | s2
```
