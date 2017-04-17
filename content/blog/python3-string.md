+++
date = "2017-04-15T16:18:51+08:00"
description = ""
title = "Python3 String"

+++


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
