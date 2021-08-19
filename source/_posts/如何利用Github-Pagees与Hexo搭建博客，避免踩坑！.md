---
title: 如何利用Github Pagees与Hexo搭建博客，避免踩坑！
tags:
  - Hexo
  - Github
abbrlink: 4d550080
date: 2019-05-27 14:48:02
---
>未接触过前端的童鞋也可以做出自己的博客，按着教程一步一步来，还是比较简单的，就是。。。坑比较多

# 一 、搭建GitHub Pages
>为什么选择GitHub？
></br>Github有一个很有爱的项目，叫做github pages，这个项目是给开发者建立一个私人页面，上面用来分享新颖的想法和自己写的代码.(其实是免费，嘿嘿嘿)

## 1.注册自己的账号
[注册GitHub](https://github.com/join?source=header-home)
## 2.创建仓库
![图片](https://s2.ax1x.com/2019/07/14/ZIpsI0.png)
## 3.进入你刚才建立的仓库
进入设置

![图片](https://s2.ax1x.com/2019/07/14/ZIpraq.png)

随便选择一个主题

![图片](https://s2.ax1x.com/2019/07/14/ZIp0qs.png)

现在你可以用 username.github.io访问你的博客了！
# 二 、安装Hexo
>要使用Hexo,需要安装Nodejs以及Git

## 安装Node.js
[下载Node.js](https://nodejs.org/en/download/)

>基本默认配置就可以安装，把安装路径改一下即可


## 安装Git
[下载Git](https://git-scm.com/download/)

注意安装的时候把安装路径设置一下，且其中有个选项选择 `use vim`，其余的一直next即可。
## Hexo
>配置好上面的环境，就可以安装Hexo了

在你需要安装Hexo的目录下(新建一个文件夹)右键选择 Git Bash Here

```bash
npm install hexo-cli -g   
hexo init #初始化网站   
npm install   
hexo g #生成或 hexo generate,这里是简写，下同  
hexo s #启动本地服务器 或者 hexo server,这一步之后就可以通过http://localhost:4000  查看了
```
接下来是怎么创建新文章
```bash
hexo new "文章名" #新建文章
hexo new page "页面名" #新建页面
```
常用简写
```bash
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server 
hexo d == hexo deploy #用于部署到GitHub
```
新建一篇文章后就可以预览了,在hexo new之后执行一次生成hexo g再执行hexo s启动本地服务器,如果之前还在hexo s 按Ctrl + C 结束.

## 添加主题
### 安装主题(NexT主题):

在博客根目录下执行以下命令：
```bash
hexo clean
git clone https://github.com/theme-next/hexo-theme-next themes/next
```
### 启动主题
找到 <font color="red">博客根目录</font> 下的_config.yml 文件,打开找到 theme：属性并设置为next

### 更新主题
```bash
cd themes/next 
git pull
hexo g
hexo s
```
此时刷新 http://localhost:4000/ 页面就能看到新的主题了.
## 使用Hexo deploy部署到github
编辑博客根目录下_config.yml文件
```javascript
deploy:
    type: git
    repo: git@github.com:bigouo/bigouo.github.io.git  #这里的昵称填你自己的
    branch: master
```
看清楚冒号后面有空格！！！

提前安装一个插件

`npm install hexo-deployer-git --save`

接下来就是将Hexo部署到自己的Github仓库上
# 三、部署到Github
## 1.检查SSH keys的设置
>以下命令均是在Git bash里输入
```bash
cd ~/.ssh
ls
#此时会显示一些文件
mkdir key_backup
cp id_rsa* key_backup
rm id_rsa*  
#以上三步为备份和移除原来的SSH key设置，若以前没用过可以省略
ssh-keygen -t rsa -C "邮件地址@youremail.com" #生成新的key文件,邮箱地址填你的Github地址
#Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):<回车就好>
#接下来会让你输入密码
```
之后就可以看到成功的界面。
## 2.添加SSH Key到Github
进入[Github首页](https://github.com/)

*添加SSH Key

点击GitHub用户头像，进入设置，找到`SSH and GPG keys`，进入即可开始下面步骤

![图片](https://s2.ax1x.com/2019/07/14/ZIpDZn.png)

找到 系统当前用户目录下(开启查看隐藏文件) C:\Users\用户名\ .ssh\ id_rsa.pub文件以文本方式打开。打开之后全部复制到key中

到了这就可以测试一下是否成功了:
```bash
ssh -T git@github.com
#之后会要你输入yes/no,输入yes就好了。
```
若返回Hi username ！You've successfully ......说明你已经成功了！

设置你的账号信息:
```bash
git config --global user.name "你的名字"     #随便填。。。
git config --global user.email "邮箱@邮箱.com"    #github邮箱
```
## 3.部署到github
    hexo d
这时再刷新 username.github.io 就可以看到你的博客了

如果此时不成功，则依次执行以下代码：
```bash
$ cd d:/hexo/blog

$ git clone https://github.com/your/your.github.io.git .deploy/your.github.io
```
把your替换成你的仓库名即可

接着创建一个deploy脚本文件
```bash
hexo generate
cp -R public/* .deploy/jiji262.github.io
cd .deploy/jiji262.github.io
git add .
git commit -m “update”
git push origin master
```
接下来可能会让你输入GitHub的账号以及密码

具体原理我也不懂。。。以下为解释
>简单解释一下，hexo generate生成public文件夹下的新内容，然后将其拷贝至jiji262.github.io的git目录下，然后使用git commit命令提交代码到jiji262.github.io这个repo的master branch上。

<font size=5 color="#FF0000"> Enjoy it!</font>

>最后编辑于2019年5月27日15点23分