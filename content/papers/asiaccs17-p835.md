+++
date = "2017-04-26T09:20:43+08:00"
description = ""
title = "SECRET: On the Feasibility of a Secure, Efficient, and Collaborative Real-Time Web Editor"
tags = ["AsiaCCS17"]
+++

## ABSTRACT

在线实时协同编辑网站存在问题：

  1. 传输明文
  2. 传输密文，但是效率低

贡献：提出[SECRET](https://github.com/RUB-NDS/SECRET/)

  1. 允许全部加密文档或局部加密文档
  2. tree-based OT，保持结构的加密
  3. 只需要现代浏览器，无需另外安装软件

## Security Model

- Honest-But-Curious Cloud Server.
- Passive Man-In-The-Middle
- Web Attacker Model


**structure-preserving encryption**

- XML encryption
- JSON Web Encryption (JWE)



## References to Read

- Operational Transform [11]
- [12]
- [28]
- [37]
- incremental encryption schemes[7]
