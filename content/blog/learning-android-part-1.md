+++
date = "2017-04-24T21:13:15+08:00"
description = ""
title = "Learning Android (2nd edition) -- Part I"

+++

# Learning Android (2nd edition) -- Part I

## Chapter 1. Android Overview

安卓历史介绍，没必要看

## Chapter 2. Java Review

提供了Java语言的快速参考手册，以后需要时可以查看。

## Chapter 3. The Stack

- 安卓底层基于Linux，使用了Linux的很多特性，如权限管理，网络，内存和电池管理等。
- 增强了一些功能：
	- 针对移动设备的电池管理
	- 基于Binder的快速IPC
	- 应用沙盒化执行
- HAL (Hardware Abstraction Layer)
	- Linux支持很多硬件，但是并没有标准化。
	- HAL统一了设备对应用的接口，开发者看到的都是接口一致的设备，如摄像头。
	- 厂商将设备的驱动作为内核模块放入Linux中。
- Native Libraries
	- `Bionic`替换了`libc`
		- 是个更小的版本
		- 使得移植Dalvik到Linux上更困难
		- 版权问题
	- Binder
	- Webkit
	- ...
- Native Daemons (守护进程)
	- Service Manager (servicemanager)
	- Android Debug Bridge (adbd)
	- ...
- Android使用`toolbox`提供对`ls`，`ps`等命令的支持，不支持`grep`，`vi`等命令；**可以尝试替换成`busybox`**。

### Dalvik
- 基于寄存器的虚拟机系统，标准的Java虚拟机是基于栈的；主要是为了能运行在ARM芯片上，节省耗电
- 标准JVM初始化对象时，将相应类从硬盘中读取出来加载到RAM中；移动设备使用的是solid state memory，Dalvik仅记录相应类的位置。
- 每个安卓应用存在于一个Dalvik虚拟机中（沙盒化），Android系统中可能同时存在多个Dalvik虚拟机；因此Dalvik被优化成使用很小的内存，同时使用共享的系统库，而不用为每个实例拷贝一份。

## Chapter 4. Installing and Beginning use of Android Tools

讲Eclipse下的Android开发配置，过时，基础，不需要看。

## Chapter 5. Main Building Block

### Activity
- Activity Manager负责管理Activity的生命周期
- 启动Activity是极为费时的操作，包括解析xml对象等操作；所以暂时不使用的Activity会保留一段时间，以便用户再次使用时快速加载进来
- 一个设备上只有一个Activity能处于Running状态（包括分屏的？）
- 各个状态名称只是说明其是否处于可见状态，与其是否在执行操作无关：处于Running状态的Activity可能只是在等待用户输入，处于Stopped状态的可能在下载文件（通过后台线程或Service）

### Intent
- Intent用于Activity之间通信，包括同个App内和不同App之间；也可用于接受广播
- 显示Intent指定某个Activity，隐式Intent指定某种类型

### Services
- Service和Activities都运行在主线程中（UI线程），因此Service中要单独开线程处理费时操作，否则会影响用户界面的流畅

### Content Providers
- 数据库操作
- `CRUD`：`insert()`，`query()`，`update()`，`delete()`
- Android内置的Providers：
	- The Contacts Provider：联系人数据
	- The Settings Provider：系统设置数据
	- The Media Store：媒体数据，图片和视频等

### Broadcast Receivers

系统事件会发出特定广播：如收到短信，电池电量低，系统启动完毕等。注册了相应事件App会收到该事件广播，从而能完成特定功能：如筛选恶意短信，降低App访问网络的频率，启动Activity，启动Service等。
