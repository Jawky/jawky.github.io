---
title: Android基础知识
subtitle: "Android基础知识"
author: Jawky
layout: post
date: 2018/4/28 11:14:54     
tags:
    - 学习
---

**1、Android AIDL**

- AIDL (Android Interface Definition Language) 是一种IDL 语言，用于生成可以在Android设备上两个进程之间进行进程间通信(interprocess communication, IPC)的代码。如果在一个进程中（例如Activity）要调用另一个进程中（例如Service）对象的操作，就可以使用AIDL生成可序列化的参数。 AIDL IPC机制是面向接口的，像COM或Corba一样，但是更加轻量级。它是使用代理类在客户端和实现端传递数据。

**2、Activity、Window、View三者的差别**

- Activity像一个工匠（控制单元），Window像窗户（承载模型），View像窗花（显示视图） LayoutInflater像剪刀，Xml配置像窗花图纸。

- 在Activity中调用attach，创建了一个Window

- 创建的window是其子类PhoneWindow，在attach中创建PhoneWindow

- 在Activity中调用setContentView(R.layout.xxx)

- 其中实际上是调用的getWindow().setContentView()

- 调用PhoneWindow中的setContentView方法

- 创建ParentView： 作为ViewGroup的子类，实际是创建的DecorView(作为FramLayout的子类）

- 将指定的R.layout.xxx进行填充 通过布局填充器进行填充【其中的parent指的就是DecorView】

- 调用到ViewGroup

- 调用ViewGroup的removeAllView()，先将所有的view移除掉添加新的view：addView()

**3、Fragment 特点**

- Fragment可以作为Activity界面的一部分组成出现；

- 可以在一个Activity中同时出现多个Fragment，并且一个Fragment也可以在多个Activity中使用；

- 在Activity运行过程中，可以添加、移除或者替换Fragment；

- Fragment可以响应自己的输入事件，并且有自己的生命周期，它们的生命周期会受宿主Activity的生命周期影响。￼

**5、Launch mode应用场景**

- Standard，创建一个新的Activity。

- SingleTop，栈顶不是该类型的Activity，创建一个新的Activity。否则，onNewIntent。

- SingleTask，回退栈中没有该类型的Activity，创建Activity，否则，onNewIntent+ClearTop。

> 注意

- 设置了"singleTask"启动模式的Activity，它在启动的时候，会先在系统中查找属性值affinity等于它的属性值taskAffinity的Task存在； 如果存在这样的Task，它就会在这个Task中启动，否则就会在新的任务栈中启动。因此， 如果我们想要设置了"singleTask"启动模式的Activity在新的任务中启动，就要为它设置一个独立的taskAffinity属性值。

- 如果设置了"singleTask"启动模式的Activity不是在新的任务中启动时，它会在已有的任务中查看是否已经存在相应的Activity实例， 如果存在，就会把位于这个Activity实例上面的Activity全部结束掉，即最终这个Activity 实例会位于任务的Stack顶端中。

- 在一个任务栈中只有一个”singleTask”启动模式的Activity存在。他的上面可以有其他的Activity。这点与singleInstance是有区别的。singleInstance，回退栈中，只有这一个Activity，没有其他Activity。

- SingleTop适合接收通知启动的内容显示页面。

    例如，某个新闻客户端的新闻内容页面，如果收到10个新闻推送，每次都打开一个新闻内容页面是很烦人的。

- singleTask适合作为程序入口点。

    例如浏览器的主界面。不管从多少个应用启动浏览器，只会启动主界面一次，其余情况都会走onNewIntent，并且会清空主界面上面的其他页面。

- singleInstance应用场景：

    闹铃的响铃界面。 你以前设置了一个闹铃：上午6点。在上午5点58分，你启动了闹铃设置界面，并按 Home 键回桌面；在上午5点59分时，你在微信和朋友聊天；在6点时，闹铃响了，并且弹出了一个对话框形式的 Activity(名为 AlarmAlertActivity) 提示你到6点了(这个 Activity 就是以 SingleInstance 加载模式打开的)，你按返回键，回到的是微信的聊天界面，这是因为 AlarmAlertActivity 所在的 Task 的栈只有他一个元素， 因此退出之后这个 Task 的栈空了。如果是以 SingleTask 打开 AlarmAlertActivity，那么当闹铃响了的时候，按返回键应该进入闹铃设置界面。

**6、Android事件分发机制**