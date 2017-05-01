+++
date = "2017-04-29T13:54:30+08:00"
description = ""
title = "Netscape Portable Runtime Library 基础"

+++

NSPR最初由Netscape开发，现在由Mozilla负责，主要提供兼容各个操作系统的底层功能，包括多线程、文件读取、网络和内存管理等。Mozilla的很多产品，如Filefox、NSS都依赖于该库。

## 安装

在Mac下安装直接使用`brew`即可，其他平台的安装也十分方便，具体参考[NSPR官网](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSPR)。

	brew install nspr


## 基础

### 命名约定

- 类型以`PR`开始，大写CamelCase，如`PRInt`、`PRFileDesc`
- 函数以`PR_`开始，大写CamelCase，如`PR_Read`、`PR_JoinThread`
- 预定义宏以`PR`开始，全大写字母，以`_`分隔，如`PR_BYTES_PER_SHORT`、`PR_EXTERN`

### 基础类型

#### 整数

- `PRInt8` `PRUint8`
- `PRInt16` `PRUint16`
- `PRInt32` `PRUint32`
- `PRInt64` `PRUint64`
- 最大最小值：`PR_INT16_MIN` `PR_INT16_MAX`
- 无符号整数只定义了最大值，最小值为0：`PR_UINT16_MAX`

需要注意的是64位的值，是以结构体的类型进行定义的，不能像其他整型一样直接使用。

```c
typedef struct {
  #ifdef IS_LITTLE_ENDIAN
    PRUint32 lo, hi;
  #else
    PRUint32 hi, lo;
  #endif
} PRInt64;
```
要进行相关操作需要使用NSPR定义的函数，如逻辑与操作`LL_AND`，加法操作`LL_ADD`。

### 线程

NSPR提供

## 参考

- [Introduction to NSPR](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSPR/Reference/Introduction_to_NSPR)
- Cross-platform development in C++
