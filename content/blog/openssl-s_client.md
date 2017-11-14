+++
date = "2017-05-02T16:49:15+08:00"
description = ""
title = "OpenSSL常用命令之s_client"
tags = ["OpenSSL"]
+++

OpenSSL的`s_client`程序实现了一个通用的SSL/TLS客户端，可以用来对服务器端进行测试。

本文使用的SSL版本如下：
```bash
$ openssl version
OpenSSL 1.0.2k  26 Jan 2017
```

使用`man`查看手册进行学习：

```bash
$ man s_client
```

下面列出一些常用的参数和实际例子。

## 连接某个网站

语法：`-connect host:port`

其他命令都是在这个命令的基础上去添加限制来达到不同的测试目的，所以这个命令一定要知道。这里给出一个连接百度的例子：

```bash
$ openssl s_client -connect www.baidu.com:443
CONNECTED(00000003)
depth=3 C = US, O = "VeriSign, Inc.", OU = Class 3 Public Primary Certification Authority
verify return:1
depth=2 C = US, O = "VeriSign, Inc.", OU = VeriSign Trust Network, OU = "(c) 2006 VeriSign, Inc. - For authorized use only", CN = VeriSign Class 3 Public Primary Certification Authority - G5
verify return:1
depth=1 C = US, O = Symantec Corporation, OU = Symantec Trust Network, CN = Symantec Class 3 Secure Server CA - G4
verify return:1
depth=0 C = CN, ST = beijing, L = beijing, O = "BeiJing Baidu Netcom Science Technology Co., Ltd", OU = service operation department., CN = baidu.com
verify return:1
---
Certificate chain
# 下面省略了证书信息、连接状态，会话信息等
```

## 使用自定义CA列表进行验证

## OCSP stapling

语法：`-status`

Sends a certificate status request to the server (OCSP stapling). The server response (if any) is printed out.

```bash
$ openssl s_client -connect www.baidu.com:443 -status
CONNECTED(00000003)
depth=3 C = US, O = "VeriSign, Inc.", OU = Class 3 Public Primary Certification Authority
verify return:1
depth=2 C = US, O = "VeriSign, Inc.", OU = VeriSign Trust Network, OU = "(c) 2006 VeriSign, Inc. - For authorized use only", CN = VeriSign Class 3 Public Primary Certification Authority - G5
verify return:1
depth=1 C = US, O = Symantec Corporation, OU = Symantec Trust Network, CN = Symantec Class 3 Secure Server CA - G4
verify return:1
depth=0 C = CN, ST = beijing, L = beijing, O = "BeiJing Baidu Netcom Science Technology Co., Ltd", OU = service operation department., CN = baidu.com
verify return:1
OCSP response:
======================================
OCSP Response Data:
    OCSP Response Status: successful (0x0)
    Response Type: Basic OCSP Response
    Version: 1 (0x0)
    Responder Id: B1A06212090B2C833135F92F8B3A8E849DE471FE
    Produced At: Apr 29 08:47:23 2017 GMT
    Responses:
    Certificate ID:
      Hash Algorithm: sha1
      Issuer Name Hash: D1B1648B8C9F0DD16BA38ACD2B5017D5F9CFC064
      Issuer Key Hash: 5F60CF619055DF8443148A602AB2F57AF44318EF
      Serial Number: 01A00C5992A0A1426F751433BD5CBF
    Cert Status: good
    This Update: Apr 29 08:47:23 2017 GMT
    Next Update: May  6 08:47:23 2017 GMT

    Signature Algorithm: sha1WithRSAEncryption
         a5:d0:ae:04:70:8d:2f:12:c2:f4:3f:1e:6c:87:df:73:9b:a9:
# 更多输出省略
```

## 使用指定协议

语法：`-nextprotoneg protocols`

```bash
$ openssl s_client -connect www.baidu.com:443 -nextprotoneg http/1.1
```

## 设置信任链

语法：`-trusted_first`




## 参考

- [OpenSSL s_client man page](https://www.openssl.org/docs/manmaster/man1/s_client.html)