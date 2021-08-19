---
title: 使用MSVC2019静态编译生成Qt库
description: 
abbrlink: 2273ab9c
date: 2021-03-11 20:08:53
cover: https://gitee.com/yzeven/blog-pic/raw/master//image/1616050485-4a72bb.jpg
tags:
 - Qt
categories:
 - Qt
---





最近Qt发布了最新版的5.15.3版本，可惜只对商业用户发布，5.15再无社区版本的更新，而5.15.2的bug补丁将会合并进Qt6版本。这将是一个漫长的过程，且目前Qt6并不稳定（完善），且我并没有在5.15.2版本上遇到什么bug（~~可能是我太菜了~~），所以目前我们可以使用这个版本作为主力版本。

其次呢，Qt官方只提供了动态库版本，也就是说我们编译出来的可执行程序必须要和==dll==文件放在一起才能够运行。有没有一种办法可以将动态库合并进可执行程序？答案是我们自己编译静态库版本。

可供编译的工具有==MSVC==和==MinGW==

{% note warning %}
注意：MSVC和MinGW编译出来的库并不能混用，必报错，平常用啥编译器就用啥来编译源代码
{% endnote %}

## 前期准备

### 下载源码

Qt官方为我们提供了源码，我们需要把源码下载下来

[Qt官方地址](https://download.qt.io/official_releases/qt/5.15/5.15.2/single/)

[清华大学镜像地址](https://mirrors.tuna.tsinghua.edu.cn/qt/official_releases/qt/5.15/5.15.2/single/)

国内推荐清华大学镜像吧，基本跑满宽带

### 安装必备软件

- [点击下载Perl](https://platform.activestate.com/ActiveState/ActivePerl-5.28/distributions)（可能需要登录）
- [点击下载Ruby](https://rubyinstaller.org/downloads/)
![下载Ruby](https://gitee.com/yzeven/blog-pic/raw/master//image/1615553857-815bcd.png)
- [点击下载Jom](https://wiki.qt.io/Jom)（多核编译必备，若想龟速编译或者编译Qt6版本不需要下载）

![下载Jom](https://gitee.com/yzeven/blog-pic/raw/master//image/1615647250-069a65.png)

- 不需要安装Python2，因为python2是为了编译webengine模块，但是这个模块不支持静态库，故不需要
- 重点：必须安装==Visual Studio 2019==或==Visual Studio 2017==，这是编译工具

## 开始编译

首先把下载的源码压缩包解压到一个找得到的地方，我这里以Qt6.0.2版本为例，因为他的源码包比较小，编译的更快，其次我也想试试Qt6版本😋。我将其解压到这里：（路径不宜过长）

![源码路径](https://gitee.com/yzeven/blog-pic/raw/master//image/1615649649-c3803b.png)

### 修改配置文件

因为我们是使用MSVC编译器，所以进入`D:\Software\Qt\6.0.x\6.0.2\src\qtbase\mkspecs\common`这个路径，找到`msvc-desktop.conf`文件，打开，将所有的`-MD` 修改为 `-MT`，即表示编译为静态库。

### 配置编译参数

首先先了解一下编译时需要用到的参数

```bash
configure.bat 
-static //指明是静态编译
-prefix "D:\Software\Qt\6.0.x\6.0.2\msvc2019_64_static" //指明安装的目录，自行修改
-confirm-license -opensource  //指明是开源版本的qt
-debug-and-release //需要编译的版本，可选-debug和-relaease
-force-asserts //强制启用Q_ASSERT,即使在release构建中
-platform win32-msvc  //指明使用msvc编译
-nomake examples -nomake tests  //不编译样例
-plugin-sql-sqlite -plugin-sql-odbc -qt-zlib -qt-libpng -qt-libjpeg //可选插件
-opengl desktop //常规桌面版OpenGL
-mp //多核编译，编译速度会快一点，单核的话估计得一天...
-skip qtserialbus -skip qtspeech //跳过编译某些模块，有风险
```

修改好后的配置为

```bash
configure.bat -static -prefix "D:\Software\Qt\6.0.x\6.0.2\msvc2019_64_static" -confirm-license -opensource -debug-and-release -platform win32-msvc -nomake examples -nomake tests -plugin-sql-sqlite -plugin-sql-odbc -qt-zlib -qt-libpng -qt-libjpeg -opengl desktop -mp
```

主要是修改安装路径，其他基本不用管，然后打开==x64 Native Tools Command Prompt For VS2019==（根据自己系统位数来确定是x86还是x64，基本都选x64，注意别选错了）。

![](https://gitee.com/yzeven/blog-pic/raw/master//image/1615648127-e47ee9.png)

然后使用cd命令`cd `进入解压的源码文件夹，把上面的配置贴进去，回车！

![cd一下](https://gitee.com/yzeven/blog-pic/raw/master//image/1615649738-30ccc6.png)

应该会跑一阵子，没有错误即配置成功。警告可以忽略~

![配置完成](https://gitee.com/yzeven/blog-pic/raw/master//image/1615649959-aae40a.png)

### 开始编译

{% note danger %}

警告，如果你编译的是`Qt6`，请先查看本文下面的报错提示，提前修改错误，以防编译中断！

{% endnote %}

由于nmake不支持多核编译，单核过于龟速，所以我们改用Jom进行编译，可以指定核心数量（满核心，冲🚀）

```
jom 核心数 //如 jom 16
```

{% note info%}

使用Jom前记得添加环境变量，或者使用绝对路径如`X:Jom/bin/jom.exe`

{% endnote%}

#### Qt6设置

{%note warning%}

好家伙Qt6更改了编译和安装的命令，需要使用CMake命令进行编译和安装，具体如下：

{% endnote %}

```
cmake --build . ---parallel //编译本目录，parallel为并行，即多核心编译
cmake --install . //安装命令
```

![image-20210313235026874](https://gitee.com/yzeven/blog-pic/raw/master//image/1615650627-ea1667.png)

警告可以忽略

![编译失败，继续安装吧](https://gitee.com/yzeven/blog-pic/raw/master//image/1615652606-a83279.png)

{% note info%}

更新：这里的报错信息提示在`qtquick3d\src\3rdparty\assimp\src\code\PostProcessing\ComputeUVMappingProcess.cpp`第125行处，此处代码如下

```cpp
for (unsigned int fidx = 0; fidx < mesh->mNumFaces;++fidx)
    {
        const aiFace& face = mesh->mFaces[fidx];
        if (face.mNumIndices < 3) continue; // triangles and polygons only, please

        unsigned int small = face.mNumIndices, large = small;
        bool zero = false, one = false, round_to_zero = false;

        // Check whether this face lies on a UV seam. We can just guess,
        // but the assumption that a face with at least one very small
        // on the one side and one very large U coord on the other side
        // lies on a UV seam should work for most cases.
        for (unsigned int n = 0; n < face.mNumIndices;++n)
        {
        ...
```

根据编译报错提示，`int`后面的`char`非法，也就是说`small`被替换为了`char`，可能是宏定义报错，由于small是局部变量，那就好办了，直接改个名不就完事了。（记得把整个作用域的变量名都改一下）

```cpp
for (unsigned int fidx = 0; fidx < mesh->mNumFaces;++fidx)
    {
        const aiFace& face = mesh->mFaces[fidx];
        if (face.mNumIndices < 3) continue; // triangles and polygons only, please

        unsigned int l_small = face.mNumIndices, large = l_small;
        bool zero = false, one = false, round_to_zero = false;

        // Check whether this face lies on a UV seam. We can just guess,
        // but the assumption that a face with at least one very small
        // on the one side and one very large U coord on the other side
        // lies on a UV seam should work for most cases.
        for (unsigned int n = 0; n < face.mNumIndices;++n)
        {
        ...
```

🆗，接着编译~CMake会接着刚才中断的地方继续编译，很快就搞定了🚀

![编译完成，开始安装](https://gitee.com/yzeven/blog-pic/raw/master//image/1615700857-21985a.png)

{% endnote %}

然后安装测试（注意install后面的有个`.`)

安装完大概760MB

## 配置QtCreator

打开QtCreator的工具选项，找到`Qt version`

![](https://gitee.com/yzeven/blog-pic/raw/master//image/1615681791-ddacca.png)

添加一个新的路径，找到`安装路径/bin/qmake.exe`，添加后点击下方的`Apply`即可，否则不会刷新。

再点击上方的`Kits`，添加一个新的库，配置如下：

![Kits配置](https://gitee.com/yzeven/blog-pic/raw/master//image/1615682596-7028d8.png)

到此编译和配置已经完成咯，下面开始测试

## 测试编译

新建一个项目，命名随意，编译Kits选择刚才新加的：

![Kits选择](https://gitee.com/yzeven/blog-pic/raw/master//image/1615682716-f5684c.png)

选择release模式编译，等待编译完成后打开编译文件夹，即可看到编译后的文件大小只有12.7Mb，且只有一个文件即可运行，不需要在添加Qt的动态库文件

![编译结果](https://gitee.com/yzeven/blog-pic/raw/master//image/1615682847-2f7098.png)

教程到此就结束咯。静态编译真香哎🍔~
