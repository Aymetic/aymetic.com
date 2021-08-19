---
title: 基于Qt开发的天气预报软件
tags:
  - Qt
  - C++
  - 自制软件
categories:
 - Qt
abbrlink: d34cf23a
date: 2021-03-08 07:54:43
description: 使用Qt库和C++开发的天气查询软件，用到了网络请求以及json解析
---

这个软件做出来很久了，以至于我已经忘记了我还做过这么一个软件，今天整理文件才发现，索性就现在为它记录一篇吧，毕竟是我自己做的第一个软件。

时间回到2019年暑假，那是我加入创明的第一个暑假，C&#43;&#43;组要求每个组员完成一个暑假作业，于是我拿起了刚刚学习的Qt框架。当时还构思了好久，想做的软件很多，但是刚学Qt，C&#43;&#43;也用的不太熟练。后来发现电脑上能看天气的软件很少，只有一个Windows自带的天气App，网页版还需要打开浏览器，不能够即开即用。所以决定开始自己用Qt搭建一个天气查询软件。

Qt是跟着B站的黑马程序员课程学的，特别喜欢那个老师的授课方式，现在还在我的收藏夹里，因为网络编程和绘图部分还没有系统学习😋，附上链接：[黑马程序员Qt（第一部分）_哔哩哔哩 (゜-゜)つロ 干杯~-bilibili](https://www.bilibili.com/video/BV1XW411x7NU)，我愿称之为经典！

## 界面

![主界面](https://gitee.com/yzeven/blog-pic/raw/master//image/1615187915-2e8d8e.png)

> 现在看以前做的软件感觉真的好蠢！界面留的空白太多，而且布局也非常的乱...

该有的功能都有，譬如当前温度（大大的显示）、天气详情以及未来五天的天气预报，图标是从阿里巴巴图标库搜到的，尽量保持了风格统一。

## 核心代码

### 网络请求

```cpp
connect(mNetManager,SIGNAL(finished(QNetworkReply*)),this,SLOT(onReplyFinished(QNetworkReply*)));
/**
 * 函数名：MainWindow::onGetWeather
 * 功能：访问天气Api并捕获返回的数据
 * 参数：无
 * 返回值：无
 */
void MainWindow::onGetWeather()
{
    qDebug() << QTime::currentTime().toString();
    //天气预报V2接口
    //http://t.weather.sojson.com/api/weather/city/v2/101180710
    QString localApi = "http://t.weather.itboy.net/api/weather/city/101180710";
    QString webApi = "http://t.weather.itboy.net/api/weather/city/";

    if(!cityID.isEmpty())
        mNetRequest->setUrl(QUrl(webApi + cityID));
    else
        mNetRequest->setUrl(QUrl(localApi));

    mNetRequest->setHeader(QNetworkRequest::UserAgentHeader, "RT-Thread ART");
    mNetManager->get(*mNetRequest);
}

/**
 * 函数名：MainWindow::onReplyFinished
 * 功能：将捕获的数据转换为字符串，便于调试
 * 参数：QNetworkReply *reply
 * 返回值：无
 */
void MainWindow::onReplyFinished(QNetworkReply* reply)
{
    qDebug() << QTime::currentTime().toString();

    QByteArray weather = reply->readAll();

    if(!weather.isEmpty())
        analyWeatherXML(weather); //解析天气信息的数据

    reply->deleteLater();
}
```

{% note info %}
2021年3月7日更新，原api已失效，已更换为http://t.weather.itboy.net/api/weather/city/+citycode
在此感谢[sojson](https://www.sojson.com/)提供的天气api接口，且新的api返回的数据还是原汁原味！
{% endnote %}

这里创建了一个`QNetworkAccessManager`类的`mNetManager`和`QNetworkRequest`类的`mNetRequest`，前者用来管理网络请求，后者用来发送请求，使用get的方法向api发出请求，当`mNetManager`请求完毕后，发出`SIGNAL(finished)`，然后调用槽函数`onReplyFinished`，从而获得api的reply，从reply中解析出来json。

{% note info %}
这里需要注意的是，根据Qt官方文档的说明中，使用完reply之后，一定要调用`deleteLater()`，否则会引起内存泄漏。
{% endnote %}

接下来就该解析api返回来的json

### 解析Json

```cpp
/**
 * 函数名：MainWindow::analyWeatherXML
 * 功能：解析Api返回的数据
 * 参数：QByteArray json
 * 返回值：无
 */
void MainWindow::analyWeatherXML(QByteArray json)
{
    if(json.isEmpty())
        return ;

    QString date[5] = {"NULL"}; //存储日期
    QJsonParseError err; 
    QJsonDocument jsonDoc = QJsonDocument::fromJson(json, &err);
    
    QJsonObject jsonObj = jsonDoc.object().value("data").toObject();
    QJsonArray forecast = jsonObj.value("forecast").toArray();

    QJsonObject cityInfo = jsonDoc.object().value("cityInfo").toObject();
    ui->labelCity->setText(cityInfo.value("parent").toString() +
                           cityInfo.value("city").toString());
    ui->labelTemperature->setText(jsonObj.value("wendu").toString());
    ui->labelTime->setText("最后更新于：" +
                           jsonDoc.object().value("time").toString());

    QJsonObject yesterday = jsonObj.value("yesterday").toObject(); //昨天
    ui->textBrowserYesterday->clear();
    ui->textBrowserYesterday->append(JsonObj2String(yesterday));
    date[0] = yesterday.value("ymd").toString();
    ui->groupBoxYesterday->setTitle(date[0]);

    QJsonObject today = forecast[0].toObject(); //今天
    ui->textBrowserToday->clear();
    ui->textBrowserToday->append(JsonObj2String(today));
    ui->labelFengsu->setText("风向：" + today.value("fx").toString() +
                             today.value("fl").toString());
    dealIcon(jsonObj);
    date[1] = today.value("ymd").toString();
    ui->groupBoxToday->setTitle(date[1]);

    QJsonObject tomorrow = forecast[1].toObject(); //明天
    ui->textBrowserTomorrow->clear();
    ui->textBrowserTomorrow->append(JsonObj2String(tomorrow));
    date[2] = tomorrow.value("ymd").toString();
    ui->groupBoxTomorrow->setTitle(date[2]);

    QJsonObject day_3 = forecast[2].toObject(); //后天
    ui->textBrowserDay_3->clear();
    ui->textBrowserDay_3->append(JsonObj2String(day_3));
    date[3] = day_3.value("ymd").toString();
    ui->groupBoxDay_3->setTitle(date[3]);

    QJsonObject day_4 = forecast[3].toObject(); //大后天
    ui->textBrowserDay_4->clear();
    ui->textBrowserDay_4->append(JsonObj2String(day_4));
    date[4] = day_4.value("ymd").toString();
    ui->groupBoxDay_4->setTitle(date[4]);
}
```

这里传入一个`QByteArray`类型的`json`，使用`QJsonDocument::fromJson`静态函数即可将`json`转化为`QJsonDocument`类型，这个时候`json`的每个对象可以转化为`QJsonObject`，数组可以转化为`QJsonArray`，转化之后就可以用`value(const QString &key)`函数将键值对的键传入，即可获得值。


```cpp
/**
 * 函数名：MainWindow::JsonObj2String
 * 功能：Json对象转换为String对象
 * 参数：const QJsonObject jsonObj
 * 返回值：QString对象
 */
QString MainWindow::JsonObj2String(const QJsonObject jsonObj)
{
    QString weather;

    if(!jsonObj.isEmpty())
    {
        weather += jsonObj.value("week").toString() + "\n";

        if(!jsonObj.value("fx").toString().isEmpty())
            weather += jsonObj.value("fx").toString() + "\n";
        else
            weather += jsonObj.value("fengxiang").toString() + "\n";

        if(!jsonObj.value("ganmao").toString().isEmpty())
            weather += jsonObj.value("ganmao").toString() + "\n";

        weather += jsonObj.value("high").toString() + "\n";
        weather += jsonObj.value("low").toString() + "\n";
        weather += jsonObj.value("type").toString() + "\n";
        weather += jsonObj.value("notice").toString();
    }

    return weather;
}
```

这个函数的作用在于将`json`里的预测数组里面的对象转化为`QString`，因为每天的预测内容都是一样的，这样避免代码重复。

{% note info %}
这里有一个更好的写法，将几个QGroupBox做成一个数组，和`json`里的预测数组相对于，这样就可以使用for循环完成解析，也可以减少代码量。不过这些是后话了，当时我咋没想到呢😂
{% endnote %}

### 搜索按钮

```cpp
/**
 * 函数名：MainWindow::serachCity
 * 功能：搜索输入框输入的城市
 * 参数：无
 * 返回值：无
 */
void MainWindow::searchCity()
{
    QFile loadFile("://data/cityData.json");//文件为城市代码数据库

    if(!loadFile.open(QIODevice::ReadOnly))
    {
        QMessageBox::warning(this, QString("警告"),
                             QString("无法加载城市数据库！\n请联系开发者"));
        return ;
    }

    QByteArray allData = loadFile.readAll();//读取数据
    loadFile.close();

    QJsonParseError json_error;
    QJsonDocument jsonDoc(QJsonDocument::fromJson(allData, &json_error));

    if(json_error.error != QJsonParseError::NoError)
    {
        qDebug() << "json error!";
        return ;
    }

    if(jsonDoc.isNull() || jsonDoc.isEmpty())
    {
        qInfo() << "parse json null or empty";
        return ;
    }

    QVariantList list = jsonDoc.toVariant().toList();//把数据库转换为数列
    QString searchData;

    for(int i = 0; i < list.count(); i++)
    {
        QVariantMap map = list[i].toMap();
        searchData = ui->searchBar->text();

        //这里用contains函数，而不是直接比较的原因是，数据库里市级城市名字不包含市，
        //所以用contains兼用性更好
        if(searchData.contains(map["city_name"].toString()))
            cityID = map["city_code"].toString();

        if(map["city_name"].toString().contains(searchData))
            cityID = map["city_code"].toString();
    }

    if(QString(cityID).isEmpty())
    {
        QMessageBox::warning(this, QString("警告"),
                             QString("没有找到" + searchData + "的城市ID" +
                                     "\n请检查重试"));
        return ;
    }

    QTimer::singleShot(1500, this, SLOT(onGetWeather()));
}
```

由于api参数里不允许直接搜索中文名城市，必须转化为对应的`citycode`，所以需要从文件读取`cityData.json`文件，将其转化为`QVariantMap`，便于搜索值。这里有两个`if`判断，是因为我们不知道用户输入的是`武汉市`还是`武汉`，所以需要两次判断。

天气图标处理的代码我就不贴啦，用的是`if`判断，其实可以优化成先计算字符串的hashcode值，再用switch即可。这就是后话了。

## 实际界面

![最终界面展示](https://gitee.com/yzeven/blog-pic/raw/master//image/1615381111-a08cd4.png)

界面上留的空白太多了，导致实际观感很一般，给人一种空空荡荡、很杂乱的感觉。

等哪天考完研有时间了再重做界面吧。

## 最后的感想

现在看以前的代码真的觉得写的很蠢，为什么要这样写，明明有更好的解决方案啊，但是自己当时做出来的时候明明很激动，毕竟经过了多天的努力，不断地搞定功能，不断地修Bug，真的是一件很快乐的事，每天睡觉前都会思考这个功能到底该如何实现等等。现在觉得写的不好，说明自己成长了，至少没有原地踏步，从某种意义来说还挺不错哈哈。好了，这篇算是完工了，接下来该开始肝另一篇咯~