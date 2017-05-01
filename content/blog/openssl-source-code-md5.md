+++
date = "2017-04-27T09:03:58+08:00"
description = ""
title = "OpenSSL中MD5算法源码阅读"
tags = ["OpenSSL", "MD5"]

+++

## 基础

- [RFC 1321](https://www.ietf.org/rfc/rfc1321.txt)
- [OpenSSL MD5 Man Page](https://www.openssl.org/docs/manmaster/man3/MD5.html)
## 涉及的文件

- `include/openssl/md5.h`
- `include/openssl/crypto.h`
- `crypto/md5/md5_locl.h`
- `crypto/md5/md5_dgst.c`
- `crypto/include/internal/md32_common.h`

### include/openssl/md5.h

- 定义了MD5的基础数据类型：MD5_LONG，MD5_CTX

```c
#ifndef HEADER_MD5_H
# define HEADER_MD5_H

# include <openssl/opensslconf.h>

// 判断是否定义了没有MD5算法这个宏
# ifndef OPENSSL_NO_MD5
# include <openssl/e_os2.h>
# include <stddef.h>
# ifdef  __cplusplus
extern "C" {
# endif

// 基础数据类型，当做32位无符号类型使用
# define MD5_LONG unsigned int

// 块长度 64 * 8 = 512位，64个字节
# define MD5_CBLOCK      64
// 16 * 32 = 512，存储一个块需要16个MD5_LONG
# define MD5_LBLOCK      (MD5_CBLOCK/4)
// 哈希值长度，128位，16字节
# define MD5_DIGEST_LENGTH 16

// MD5的迭代状态
typedef struct MD5state_st {
    // MD5的中间状态
    MD5_LONG A, B, C, D;
    // MD5处理的字节数，最长支持2^64，大于2^64则取低64位
    MD5_LONG Nl, Nh;
    // 存储一个块
    MD5_LONG data[MD5_LBLOCK];
    // 当前
    unsigned int num;
} MD5_CTX;

// 初始化MD5_CTX
int MD5_Init(MD5_CTX *c);
// 加入MD5串
int MD5_Update(MD5_CTX *c, const void *data, size_t len);
// 计算最终MD5串，并放入md中
int MD5_Final(unsigned char *md, MD5_CTX *c);
// 计算一次MD5，定义在md5_one.c中
unsigned char *MD5(const unsigned char *d, size_t n, unsigned char *md);
void MD5_Transform(MD5_CTX *c, const unsigned char *b);
# ifdef  __cplusplus
}
# endif
# endif

#endif
```

### crypto/md5/md5_locl.h

- 定义了MD5的轮函数

```c
/*
 * Copyright 1995-2016 The OpenSSL Project Authors. All Rights Reserved.
 *
 * Licensed under the OpenSSL license (the "License").  You may not use
 * this file except in compliance with the License.  You can obtain a copy
 * in the file LICENSE in the source distribution or at
 * https://www.openssl.org/source/license.html
 */

#include <stdlib.h>
#include <string.h>
#include <openssl/e_os2.h>
#include <openssl/md5.h>

#ifdef MD5_ASM
# if defined(__i386) || defined(__i386__) || defined(_M_IX86) || \
     defined(__x86_64) || defined(__x86_64__) || defined(_M_AMD64) || defined(_M_X64)
#  define md5_block_data_order md5_block_asm_data_order
# elif defined(__ia64) || defined(__ia64__) || defined(_M_IA64)
#  define md5_block_data_order md5_block_asm_data_order
# elif defined(__sparc) || defined(__sparc__)
#  define md5_block_data_order md5_block_asm_data_order
# endif
#endif

void md5_block_data_order(MD5_CTX *c, const void *p, size_t num);

#define DATA_ORDER_IS_LITTLE_ENDIAN

#define HASH_LONG               MD5_LONG
#define HASH_CTX                MD5_CTX
#define HASH_CBLOCK             MD5_CBLOCK
#define HASH_UPDATE             MD5_Update
#define HASH_TRANSFORM          MD5_Transform
#define HASH_FINAL              MD5_Final
#define HASH_MAKE_STRING(c,s)   do {    \
        unsigned long ll;               \
        ll=(c)->A; (void)HOST_l2c(ll,(s));      \
        ll=(c)->B; (void)HOST_l2c(ll,(s));      \
        ll=(c)->C; (void)HOST_l2c(ll,(s));      \
        ll=(c)->D; (void)HOST_l2c(ll,(s));      \
        } while (0)
#define HASH_BLOCK_DATA_ORDER   md5_block_data_order

#include "internal/md32_common.h"

#define F(b,c,d)        ((((c) ^ (d)) & (b)) ^ (d))
#define G(b,c,d)        ((((b) ^ (c)) & (d)) ^ (c))
#define H(b,c,d)        ((b) ^ (c) ^ (d))
#define I(b,c,d)        (((~(d)) | (b)) ^ (c))

#define R0(a,b,c,d,k,s,t) { \
        a+=((k)+(t)+F((b),(c),(d))); \
        a=ROTATE(a,s); \
        a+=b; };\

#define R1(a,b,c,d,k,s,t) { \
        a+=((k)+(t)+G((b),(c),(d))); \
        a=ROTATE(a,s); \
        a+=b; };

#define R2(a,b,c,d,k,s,t) { \
        a+=((k)+(t)+H((b),(c),(d))); \
        a=ROTATE(a,s); \
        a+=b; };

#define R3(a,b,c,d,k,s,t) { \
        a+=((k)+(t)+I((b),(c),(d))); \
        a=ROTATE(a,s); \
        a+=b; };

```

### md5_one.c

- 定义了简单的MD5函数，能进行一次MD5运算


```c
#include <stdio.h>
#include <string.h>
#include <openssl/md5.h>
#include <openssl/crypto.h>

#ifdef CHARSET_EBCDIC
# include <openssl/ebcdic.h>
#endif

unsigned char *MD5(const unsigned char *d, size_t n, unsigned char *md)
{
    MD5_CTX c;
    static unsigned char m[MD5_DIGEST_LENGTH];

    if (md == NULL)
        md = m;
    if (!MD5_Init(&c))
        return NULL;
#ifndef CHARSET_EBCDIC
    MD5_Update(&c, d, n);
#else
    {
        char temp[1024];
        unsigned long chunk;

        while (n > 0) {
            chunk = (n > sizeof(temp)) ? sizeof(temp) : n;
            ebcdic2ascii(temp, d, chunk);
            MD5_Update(&c, temp, chunk);
            n -= chunk;
            d += chunk;
        }
    }
#endif
    MD5_Final(md, &c);
    OPENSSL_cleanse(&c, sizeof(c)); /* security consideration */
    return (md);
}
```

### `crypto/internal/include/md32_common.h`

- 定义了MD系列哈希算法需要使用的宏
- 例如HASH_Update在MD5中包含后被定义为MD5_Update
