+++
date = "2017-04-15T15:29:38+08:00"
description = ""
title = "Python3 Basic"

+++

# 基本类型

- 布尔型
  - `True`
  - `False`
- 整数，浮点数
  - 十六进制：`0xff00`
  - `100`，`-10.234`
  - 整数没有大小限制，浮点数超出一定范围表示为`inf`
- 字符串
  - 'abc'，"AHJK"
  - 转义字符：`\n\t'
  - 不转义字符：r'\n\t'
- 空值
  - None

# 变量
- 声明不需要加变量类型：`a = False`
- 常量用全大写：`PI = 3.1415926`，但是还是可以修改，Python无常量

# 运算

- 逻辑运算：`and`，`or`，`not`
- 加减乘除：`+ - * /`
- 取余：`%`
- 向下取整除法：
  - `10 // 3`，输出3
  - `11 // 4`，输出2

---

# Demo Code

```python
# This is comment
a = 100
if a > 0:
  print(a)
else:
  print(-a)
```

```python
# 多行输出
print('''line1
line2
line3''')
```
