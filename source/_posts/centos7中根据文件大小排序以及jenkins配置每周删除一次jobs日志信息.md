---
layout: '[post]'
title: centos7中根据文件大小排序以及jenkins配置每周删除一次jobs日志信息
date: 2017-04-18 20:25
tags:
---
# 环境

操作系统：win7
虚拟机：centos7

# 效果

## 按照文件大小降序
```
du * -sh | sort -hr #h 表示以人可读的形式显示出来
```

## 按创建时间排序
```
ls -alc # 按创建时间排序
```

## 查看文件大小
```
du -sh # 查看文件大小

```
**根据文件的创建时间（修改时间）排序升序，取前100条，并且指定匹配名称为数字的，并删除**
```
rm -rf `ls -lc | head -n 100 | grep -w '[1-9]'`
```


## 查询时间段为2016年3月01号到2017年4月8号内的名称为数字的目录
```
find . -type d -newermt '01 Mar 2016' ! -newermt '08 Apr 2017' -print | grep -w '[1-9]*'
```
## 显示文件时，包含年月日
```
ls -l --time-style=full
```

--time-style参数：

```
full-iso 精确显示年月日时分秒纳秒及时区
long-iso 显示年月日时分
iso 默认就是iso，显示月日时分
locale 按当前系统环境区域设置显示
+Format 自定义格式显示
```

## full-iso显示效果：

```
ls -l --time-style=full-iso

drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02:00.912981382 +0800 公共
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02:00.911981382 +0800 模板
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02:00.912981382 +0800 视频
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02:00.912981382 +0800 图片
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02:00.912981382 +0800 文档
drwxr-xr-x. 2 yutao yutao 172 2017-04-05 18:51:58.592643038 +0800 下载
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02:00.912981382 +0800 音乐
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02:00.911981382 +0800 桌面
```

## long-iso显示效果

```
ls -l --time-style=long-iso

drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02 公共
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02 模板
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02 视频
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02 图片
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02 文档
drwxr-xr-x. 2 yutao yutao 172 2017-04-05 18:51 下载
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02 音乐
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:02 桌面
```

---
## 自定义

```
ls -l --time-style="+%Y-%m-%d %H:%l:%S"

drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:11:00 公共
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:11:00 模板
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:11:00 视频
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:11:00 图片
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:11:00 文档
drwxr-xr-x. 2 yutao yutao 172 2017-04-05 18: 6:58 下载
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:11:00 音乐
drwxr-xr-x. 2 yutao yutao   6 2017-03-21 11:11:00 桌面
```

---
## 查询7天前改动的文件夹，并列出详情、排序
```
[root@master01 builds]# find . -mtime +7 -type d -ls|sort

435016    4 drwxr-xr-x   2 jenkins  jenkins      4096 Apr 10 16:42 ./456
435045    4 drwxr-xr-x   2 jenkins  jenkins      4096 Apr 11 12:00 ./457
435051    4 drwxr-xr-x   2 jenkins  jenkins      4096 Apr 11 13:56 ./458
435062    4 drwxr-xr-x   2 jenkins  jenkins      4096 Apr 11 16:11 ./459
```
---

列举上面那些是为了以下需求做铺垫。
## 需求
最近`jenkins`出现没法构建的问题，点击`立即构建`没有反应。后来进入`系统设置`页面，看到提示，原来磁盘空间忙了。

1、首先我们需要知道构建的日志存在到哪里了？
2、公司要求，保留一周的记录。

## 查找jenkins的日志存放位置
由于当初我安装`jenkins`使用的是`rpm`进行安装。
所以查看安装路径的方法：

```bash
rpm -ql jenkins

/etc/init.d/jenkins
/etc/logrotate.d/jenkins
/etc/sysconfig/jenkins
/usr/lib/jenkins
/usr/lib/jenkins/jenkins.war
/usr/sbin/rcjenkins
/var/cache/jenkins
/var/lib/jenkins #存放的是构建时的日志
/var/log/jenkins #jenkins 运行时是日志
```
所以，我们需要进入`cd /var/lib/jenkins/jobs`

![这里写图片描述](http://img.blog.csdn.net/20170420193627039?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这里面的每个文件夹就是我们配置的job构建后的日志存储的地方(里面的`builds`)

我们在`jenkins`中先新建一个job，专门用来执行删除日志的脚本。

![这里写图片描述](http://img.blog.csdn.net/20170420194419387?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170420194432425?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170420194456254?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170420194515004?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

---
job配置好了后，我们就需要写脚本啦！
思路：
①先获取改动时间在7天前的文件。
②因为我们也需要知道，我们每次到底删除了哪些日志，所以我们还需要把删除的文件的时间显示到年月日。便于我们知道，删除的文件对不对。


**查询改动时间为7天前的目录并列出详细信息时间精确到精确显示年月日时分秒纳秒及时区并写入到文件名为yutao中**

以下命令执行的目录是：`/var/lib/jenkins/jobs`
```bash
# 因为我们要删除的是构建日志，所以需要查询每个`job`中`builds`路径中的日志
find */builds/* -mtime +7 -type d| xargs ls -dl --full-time>yutao
#find */builds/* -mtime +7 -type d| xargs ls -dl --full-time|sort

drwxr-xr-x 2 jenkins jenkins 4096 2016-10-18 10:31:47.305175960 +0800 ggblog-test/builds/38
drwxr-xr-x 2 jenkins jenkins 4096 2016-10-18 10:36:06.165175675 +0800 ggblog-test/builds/39
drwxr-xr-x 2 jenkins jenkins 4096 2016-10-18 10:40:07.867175411 +0800 ggblog-test/builds/40
drwxr-xr-x 2 jenkins jenkins 4096 2016-10-18 10:47:29.561174927 +0800 ggblog-test/builds/41
```

上面的方法有个问题：

```bash
find */builds/* -mtime +7 -type d| xargs ls -dl --full-time|sort
#即使find命令什么都没有查到，由于命令ls -d是显示当前目录的属性，所以它会显示为：

drwxr-xr-x 51 jenkins jenkins 4096 2017-04-20 14:13:46.495914200 +0800 .
```
也就是说即使什么都没有查到，依然会显示当前目录`.`，`.`表示的就是当前目录。

当然我们要进行删除操作，只需要这样写：

```bash
rm -rf `find */builds/* -mtime +7 -type d`
```
但是，作为优秀的程序员，我们需要知道我们删除的文件详情。所以我们需要打印删除的文件详细。
完整脚本：
```bash
#!/bin/bash
cd /var/lib/jenkins/jobs
echo 'delete log start'
files=`find */builds/* -mtime +7 -type d`
deletelog=`find */builds/* -mtime +7 -type d | xargs ls -dl --full-time|sort|xargs|sed "s/drwxr/\n\rdrwxr/g"
`
if [ ! "$files" = "" ]
then
echo '即将删除的文件：'
echo ${deletelog}
rm -rf ${files}
else
echo '没有文件要删除'
fi
echo 'delete log end'
```

效果：
![这里写图片描述](http://img.blog.csdn.net/20170420192039767?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170420192142516?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzA2NjI0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

http://www.9usb.net/201005/linux-ls.html
http://blog.csdn.net/u013008795/article/details/51135130
http://man.linuxde.net/ls