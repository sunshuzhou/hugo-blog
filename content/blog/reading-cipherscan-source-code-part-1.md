+++
date = "2017-05-02T09:57:48+08:00"
description = ""
title = "cipherscan代码阅读——第一部分"
tags = ["cipherscan源码阅读"]
+++

# 介绍

[cipherscan](https://github.com/mozilla/cipherscan/blob/master/cipherscan)是mozilla编写的用于测试服务器TLS配置的工具。
这篇文章是第一部分，主要将`cipherscan`这个shell脚本的功能和其中的主要函数。
[第二部分](/blog/cipherscan代码阅读第二部分/)讲解了analyze.py文件的功能其中的函数。


cipherscan主要完成了以下功能：

- 测试服务器对`sslv2`、`sslv3`、`tlsv1`、`tlsv1_1`和`tlsv1_2`的支持程度，主要包括以下功能：
	- OCSP Stapling
	- Cipher Suite
- 测试服务器对椭圆曲线的支持程度

这些功能都是借助`openssl`的`s_client`完成的。关于`s_client`，可以参看[这篇文章](/blog/openssl常用命令之s_client/)。

# 函数列表

这里列出其中的比较重要的函数。

- `join_array_by_char`

- `test_cipher_on_target`

代码结构
```bash
for i in ()
```
使用各个ssl/tls版本测试指定的cipher suite和椭圆曲线

- `test_curve`

测试每一种椭圆曲线

- 构造ssl命令：`gtimeout 30 openssl s_client -status -connect $TARGET -cipher $current_cipher -tursted_first -no_ssl2 -no_ssl3 -curves $test_curves`，如果有CA则还要加上CA参数`-CApath`或`-CAfile`
- 测试：首先使用所有椭圆曲线作为参数，然后逐个去除，直到协商到某个非椭圆曲线的Cipher Suite

- `parse_openssl_output`

