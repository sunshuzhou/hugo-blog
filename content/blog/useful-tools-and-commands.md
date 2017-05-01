+++
date = "2017-04-14T16:51:57+08:00"
title = "USEFUL TOOLS AND COMMANDS"

+++

# 各种软件

## drozer
### 安装
- Webpage: https://github.com/mwrlabs/drozer
- Command:
	1. `easy_install twisted==10.2.0`
	2. `sudo easy_install drozer-2.x.x-py2.7.egg`
- Mac平台

### 开始使用
1. `adb forward tcp:31415 tcp:31415`
2. `drozer console connect`

### 命令
#### 帮助命令
- `help`
- `run -h`
- `run app.package.list -h`
- `-a package_name`
- `-f filter`

#### 常用命令
- 条件筛选某个应用： `run app.package.list -f sieve`
- 查看应用的信息： `run app.package.info -a package_name`
- 查看是否存在漏洞： `run app.package.attacksurface package_name`



### References
- [PDF](https://labs.mwrinfosecurity.com/assets/BlogFiles/mwri-drozer-user-guide-2015-03-23.pdf)
- Website

## OWASP-GoatDroid
##





## OpenSSL

- 查看DER格式的证书： `openssl x509 -in burp-suite-certificate -inform der -noout -text`

## Shell命令
### screen

- 列表：`screen -ls`
- 恢复：`screen -r [-d] name`

### tar

- 压缩成.tar.gz：`tar -czvf a.tar.gz abc`
- 解压缩.tar.gz：`tar -xzvf a.tar.gz`

## Android

### adb
- 列出所有设备： `adb devices`
