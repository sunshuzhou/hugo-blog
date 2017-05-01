+++
date = "2017-04-17T15:16:34+08:00"
description = ""
title = "Python 高级特性"

+++

# 切片
```python
# 取前3个元素，L[0]，L[1]，L[2]
L[0:3]
# 取最后三个元素
L[-3:]
L = range(100)
# 100以内的偶数
L[::2]
# 100以内的奇数
L[1::2]
```

# 迭代

```python
# d is a dict
for key in d:
  pass
for value in d.values():
  pass
for k, v in d.items():
  pass

# index + value
for i, value in enumerate(['A', 'B', 'C']):
  print(i, value)
```

# 列表生成式
```python
[x * x for x in range(1, 11)] # [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

[x * x for x in range(1, 11) if x % 2 == 0] # [4, 16, 36, 64, 100]

[m + n for m in 'ABC' for n in 'XYZ']
# ['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']

# d is a dict
[k + '=' + v for k, v in d.items()]
```

# 生成器
- 生成器类型不会立刻产生list，仅在需要时进行运算，节省内存
```python
(x * x for x in range(10))
# <generator object <genexpr> at 0x1098fac80>
```
