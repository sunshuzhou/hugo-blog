+++
date = "2017-05-02T09:42:01+08:00"
description = ""
title = "shell编程基础"

+++

## 变量

```bash
myUrl="example.com"
echo ${myUrl}
myUrl="sunshuzhou.github.io"
echo $myUrl
# 只读变量，之后无法修改
readonly myUrl
```

```bash
# 删除变量，不能删除只读变量，变量删除后不能使用
unset variableName
```

### 特殊变量

- `$$`: 当前shell进程的ID
- `$0`: 当前脚本的文件名，从始至终不变
- `$n`: 传递给脚本或函数的第`n`个参数，第一个参数是`$1`，第二个参数是`$2`，以此类推
- `$#`: 传递给脚本或函数的参数个数

## 重定向

文件描述符：

- 0：标准输入
- 1：标准输出
- 2：标准错误输出

`1>&2`和`>&2`等价，将正常输出重定向到错误输出

```bash
if [[ ${BASH_VERSINFO[0] -lt 4 ]]; then
  echo "Bash version 4 is required to run this script." 1>&2
fi
```


## 判断

### 字符串判断

|语法|功能|
|---|---|
|`[[ -z $str ]]`|字符串为空返回`true`|
|`[[ -n $str ]]`|字符串不为空返回`true`|
|`[[ $str1 == $str2 ]]`|字符串相等返回`true`|

### 文件判断

|语法|功能|
|---|---|
|`[[ -e file ]]`|文件存在返回`True`|
|`[[ -r file ]]`|文件存在且可读返回`true`|
|`[[ -w file]]`|文件存在且可写`true`|


## 函数



## 参考

参考[此处](http://www.cnblogs.com/zhaobin/archive/2009/06/25/1511163.html)

