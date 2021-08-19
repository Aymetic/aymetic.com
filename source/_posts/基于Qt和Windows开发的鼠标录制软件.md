---
title: 基于Qt和Windows开发的鼠标录制软件
abbrlink: 1f91202
date: 2021-03-08 07:54:10
tags:
  - Qt
  - C++
  - 自制软件
categories: Qt
description: 本程序基于Qt和Windows钩子开发的鼠标录制软件，现在已有连击模式以及录制脚本模式，且使用了多线程模式，所有操作均在子线程完成。
---

前不久在公司实习的时候，有一次需要执行大量的重复操作，单靠人手挪动鼠标会极其麻烦，所以就想到了鼠标录制软件。遂在网上搜了一番，还真找到一个，是一个个人开发者用C#语言编写的，使用起来挺顺手，但是总觉得少点什么。

![别人的软件](https://gitee.com/yzeven/blog-pic/raw/master//image/1615719995-a96d86.png)

过了几天才发现，虽然它可以很完美地录制鼠标脚本，但是却没有最简单的鼠标连击功能。于是乎，自己动手做一个吧~

## 开发前准备

其实需求挺简单，在别人软件已有的功能上添加一个鼠标连点功能。

- 开发平台：Windows 10 20H2

- 开发软件：Qt 5.15.2

- 开发语言：C++

## 动手开发

### 出师不利

刚开始做准备的时候，我就想到Qt自带的`MouseEvent`和`KeyEvent`能否实现相关功能。后来在实际测试时发现，只有窗口获得焦点且鼠标在窗口内部的时候，`event`才可以监听鼠标和键盘，这肯定不符合鼠标连点器的需求。然后这个项目就被搁置了一段时间。

接下来就是每天不断地在网上搜相关的文章、博客。诺大的互联网总会有我们想要的答案。终于，我在知乎搜到了一个基于C++和Windows实现的鼠标连点器，没有界面，几十行代码就搞定了。链接：[C++自制鼠标连点器](https://zhuanlan.zhihu.com/p/144930696)。

通读知乎这个代码，发现作者用到了Windows平台的鼠标钩子。这就有点难受啊，Qt跨平台的特性就无法在本程序中体现出来！也罢，先把Windows平台搞出来先。我们先来看一下知乎作者的代码：



#### Windows鼠标事件

这里先解释一下Windows系统的鼠标事件函数

```c++
VOID mouse_event(
  DWORD     dwFlags,     // 见下👇
  DWORD     dx,          // 根据MOUSEEVENTF_ABSOLUTE标志，指定x方向的绝对位置或相对位置
  DWORD     dy,          // 根据MOUSEEVENTF_ABSOLUTE标志，指定y方向的绝对位置或相对位置
  DWORD     dwData,      // 滚轮，暂不知何用
  ULONG_PTR dwExtraInfo  // 应用程序定义的信息
);
//dwFlags常数 意义
const int MOUSEEVENTF_MOVE = 0x0001;      　//移动鼠标
const int MOUSEEVENTF_LEFTDOWN = 0x0002; 　//模拟鼠标左键按下
const int MOUSEEVENTF_LEFTUP = 0x0004; 　　//模拟鼠标左键抬起
const int MOUSEEVENTF_RIGHTDOWN = 0x0008; //模拟鼠标右键按下
const int MOUSEEVENTF_RIGHTUP = 0x0010; 　//模拟鼠标右键抬起
const int MOUSEEVENTF_MIDDLEDOWN = 0x0020; //模拟鼠标中键按下
const int MOUSEEVENTF_MIDDLEUP = 0x0040; 　//模拟鼠标中键抬起
const int MOUSEEVENTF_ABSOLUTE = 0x8000; 　//标示是否采用绝对坐标
```

例子：

```c++
POINT pt;//定义坐标变量
GetCursorPos(&pt);//获取当前鼠标指针绝对坐标
mouse_event(MOUSEEVENTF_LEFTDOWN, 0, 0, 0, 0);//模拟左键按下
mouse_event(MOUSEEVENTF_LEFTUP, 0, 0, 0, 0);　//模拟左键弹起，即完成一次鼠标点击
```



有了这个基础，我们就可以动手完成软件的连点功能。思路是主界面选择连击模式，然后点击开始后程序抛出一个子线程，用来执行一个鼠标连击的死循环。开始动手~（关于Qt如何创建新线程，请点击[优雅地使用Qt创建新线程（待施工）]()）

```c++
void ConThread::work()
{
    while ( !finished )
    {
        if ( pause == true )
        {
            qDebug() << "连击已暂停";
            return;
        }
        POINT pos;
        GetCursorPos( &pos );
        emit sendMouseLocationData( pos );
        mouse_event( buttonDown, 0, 0, 0, 0 );
        mouse_event( buttonUp, 0, 0, 0, 0 );
        QThread::msleep( timeInterval ); //延时函数
    }
    qDebug() << "连击线程结束！";
    emit workDone(); //发送完成信号给主线程
}
```

美滋滋~连点器就这么诞生了，还是比较简单的。（殊不知，后面还有更大的坑要填（录制模式））

先挖坑🌌这可能是我最长的一篇博客了，慢慢来🤭。