+++
date = "2017-04-24T21:13:18+08:00"
description = ""
title = "Learning Android (2nd edition) - Part II"

+++

# Learning Android (2nd edition) - Part II

## Chapter 6. Yamba Project Overview

类似Twitter的样例程序，将在接下来的八章中实现。

开发原则：
- 轻度增量开发
- 总是完整
- 重构代码

## Chapter 7. Android User Interface

- Views: Button, Label, Text Box
- Layouts
	- LinearLayout：线性布局，垂直或者水平
	- TableLayout：表格布局
	- FrameLayout：帧布局
	- RelativeLayout：相对布局

### AsyncTask

- [API Reference](https://developer.android.com/reference/android/os/AsyncTask.html)

## Chapter 8. Fragments
- [Settings](https://developer.android.com/guide/topics/ui/settings.html)

## Chapter 9. Intents, Action Bar, and More
- `onCreateOptionsMenu()`
- `onOptionsItemSelected()`

## Android FileSystem

### 分区
- /system：系统分区，只读
- /mnt/sdcard：sd卡存储，读写需要申请权限：`WRITE_TO_EXTERNAL_STORAGE`，`READ_EXTERNAL_STORAGE`
- /data：用户数据
	- /data/app
	- /data/data 存放各个应用的数据

## Chapter 10. Services

- 创建时调用`onCreate()`，启动时调用`onStartCommand()`，结束时调用`onDestory()`
- 绑定时调用`onBind()`
- Service运行在UI线程中，要执行耗时或网络操作都要另外新建线程

### IntentService

- `onHandleIntent()`方法会在另外的线程中运行，执行完后，Service结束

## Chapter 11. Content Providers

	$ cd /data/data/com.example.sunshuzhou.yamba/databases
	$ sqlite3 timeline.db
	sqlite> .schema
	sqlite> .dump

- vnd.android.cursor.item
- vnd.android.cursor.dir

[Content Provider Guide](https://developer.android.com/guide/topics/providers/content-providers.html)

## Chapter 12. Lists and Adapters

## Chapter 13. Broadcast Receivers

- publish/subscribe模式
	- 系统事件
	- 用户自定义事件
	- 定义Receiver（继承自BroadcastReceiver）
- 添加通知：

		NotificationManager notificationManager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
        Notification notification = new Notification.Builder(this)
                .setContentTitle("Title")
                .setContentText("message")
                .setSmallIcon(android.R.drawable.sym_action_email)
                .getNotification();
        notificationManager.notify(42, notification);

## Chapter 14. App Widgets

讲了如何加入Widget。

## Chapter 15. Networking and Web Overview

- [Apache HTTP Client](https://hc.apache.org/httpcomponents-client-ga/)
- HttpURLConnection

## Chapter 16. Interaction and Animation: Live Wallpaper and Handlers

不太适合，不用看
