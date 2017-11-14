+++
date = "2017-05-03T15:31:23+08:00"
description = ""
title = "7z LZMA SDK源代码阅读"

+++

[下载地址](http://www.7-zip.org/sdk.html)

文档文件：

- `/DOC/7zC.txt`
- `/DOC/lzma-sdk.txt`

基础知识：

- 7z支持不同的压缩方法：LZMA、LZMA2等，暂时只关注LZMA
- 7z使用AES256加密

**编译`ANSI-C Decoder`**

```bash
$ cd C/Util/7z
$ make -f makefile.gcc
```

**`/C/Util/7z/7zMain.c`**

```c
main() {

	InFile_Open(&archiveStream.file, args[2]);
	FileInStream_CreateVTable(&archiveStream);
	LookToRead_CreateVTable(&lookStream, False);
  
	lookStream.realStream = &archiveStream.s;
	LookToRead_Init(&lookStream);

	CrcGenerateTable();

	SzArEx_Init(&db);
  
	res = SzArEx_Open(&db, &lookStream.s, &allocImp, &allocTempImp);
	
	...
	
	SzArEx_Free(&db, &allocImp);
	SzFree(NULL, temp);

	File_Close(&archiveStream.file);
}
```