+++
date = "2017-05-03T20:30:28+08:00"
description = ""
title = "数字证书的各种格式"

+++

# 数字证书的各种格式

## 证书

X.509证书实际上是一个数字文档，其中包含了实体的公钥和信息，并且根据[RFC5280](https://www.rfc-editor.org/rfc/rfc5280.txt)进行了编码和数字签名。

实际上，X.509证书通常指的是RFC5280中的X.509v3。

## 编码格式

- DER（Distinguished Encoding Rules）：ASN.1的二进制编码格式
- PEM（Privacy Enhanced Mail）：是DER证书的Base64编码格式。

这两种编码格式是可以互相转换的。

### 转换

```bash
# PEM to DER
$ openssl x509 -in cert.crt -outform der -out cert.der
```

```bash
# DER to PEM
$ openssl x509 -in cert.crt -inform der -outform pem -out cert.pem
```

### 查看

```bash
# 查看PEM格式，openss默认就是pem格式
$ openssl x509 -in cert.pem -text -noout
```

```bash
# 查看DER格式，需要指定inform
$ openssl x509 -in certificate.der -inform der -text -noout
```

## 文件后缀

- `.CRT`：数字证书格式，DER或PEM编码都可以，通常用于*nix系统。
- `.CER`：与`.CRT`一样，也是数字证书，DER或PEM编码都可以，通常用于Windows系统。
- `.KEY`：PKCS#8格式的公钥或私钥，DER或PEM编码都可以。
