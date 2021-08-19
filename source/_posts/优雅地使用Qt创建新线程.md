---
title: 优雅地使用Qt创建新线程
katex: false
top_img: 页面顶图
highlight_shrink: true
tags:
  - Qt
categories:
  - Qt
abbrlink: 52aa6c97
date: 2021-04-14 10:49:00
---

Qt作为一个优秀的跨平台开发框架，对于多线程的支持必然要非常的优雅~本篇文章就来探讨如何利用Qt创建新的子线程。

关于多线程的有点我就不赘述了，如需了解，善用[百度](https://baidu.com)~

{% note info %}

本文根据B站黑马程序员的课程，再加上自己的一丢丢理解而写的。

{% endnote %}

## Qt4线程的创建与使用

在Qt4的时代（我莫得经历过，因为Qt4是十几年前发布的，其中的某些思想肯定跟不上目前的发展，但是Qt5依然兼容Qt4的多线程模式），如果我们想要创建一个线程，我们只需新建一个类`MyThread`并继承于`QThread`，并重写`QThread`类的虚函数`void run()`。当我们需要创建进程的时候，只需声明一个`MyThread`对象，然后调用`MyThread::start()`即开启了一个新线程。

例：

```c++
#include <QThread>

class MyThread: public QThread
{
public:
    MyThread();
protected:
    void run(); //重载父类虚函数
}
```

```c++
MyThread::MyThread()
{
 
}
void MyThread::run()
{
	int i = 0;
    while(true)
    {
        while(1)
        {
        	qDebug()<<i++<<endl;
        }
    }

}
```

```c++
MyThread* thread = new MyThread();
thraed->start();
```

如何停止线程在后面讲解。

## Qt5线程的创建与使用

来到Qt5时代，线程的调用不再是重新`run`函数，而是自定义一个继承于`QObject`的`MyThread`类，这个类需要一个线程**数据处理方法**和一个**线程结束信号**，具体定义流程如下：

1. 创建一个`MyThread`对象，且这个对象**不能**指定父对象
2. 创建一个`QThread`对象，即我们需要的子线程
3. 将自定义类放进我们创建的子线程对象，即调用`moveToThread(QThread *target)`
4. 启动子线程，即调用子线程对象的`start()`方法，这个时候子线程已经启动了，但是数据处理还没启动
5. 必须必须利用**信号槽**的方式启动自定义类的对象的方法，否则数据处理方法就在主线程中运行

上例子，比文字描述更加清晰

```c++
#include <QThread>

class MyWork: public QObject
{
public:
    MyWork();
    void work(); //线程工作函数
signals:
    void threadDone();//线程结束信号
}
```

```c++
MyWork::MyWork()
{
 
}
void MyWork::work()
{
	int i = 0;
    while(true)
    {
        while(1)
        {
        	qDebug()<<i++<<endl;
        }
    }

}
```

```c++
//主线程cpp文件
connect(this,&MainWindow::startThread,)
    
QThread* thread = new QThread(this);
MyWork* myWork = new MyWork();
thraed->start();
emit startThread();
```

待续....

## 后话

