+++
date = "2017-05-02T15:26:42+08:00"
description = ""
title = "使用OpenSSL生成证书"

+++

总的来说，使用证书包括三个步骤：

1. 产生一个强公钥、私钥对，私钥要保密存储
2. 产生一个证书签名请求（Certificate Signing Request），并发送给CA，让CA进行签名
3. 将CA颁发的证书放置在服务器中，配置网站使用HTTPS。


## 产生key

```shell
$ openssl genrsa -aes128 -out fd.key 2048
```