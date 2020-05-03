# 部署失败 - no spaces left


原因： `postdrop 耗尽资源`

症状描述：

```
ps aux 
ps aux | grep postdrop
```
满屏幕的postdrop进程，系统资源耗尽
PS：再次出现硬盘100%被写满的状况

日志被刷爆了:
```
[root@centos ~]# tail -f /var/log/maillog
Sep 29 15:50:41 VM_26_233_centos postfix/postdrop[11290]: warning: mail_queue_enter: create file maildrop/98512.11290: No space left on device
Sep 29 15:50:41 VM_26_233_centos postfix/postdrop[28639]: warning: mail_queue_enter: create file maildrop/98551.28639: No space left on device
Sep 29 15:50:41 VM_26_233_centos postfix/postdrop[13193]: warning: mail_queue_enter: create file maildrop/98611.13193: No space left on device
Sep 29 15:50:41 VM_26_233_centos postfix/postdrop[6030]: warning: mail_queue_enter: create file maildrop/98512.6030: No space left on device
```

磁盘被maildrop占满了空间，刷满了各种簇文件
/var/spool/postfix/maildrop/

解决
```
#先kill掉满屏的postdrop
ps -ef|grep postdrop |grep -v grep|cut -c 9-15|xargs kill -9
 
#删除巨量的文件
#直接rm -fr /var/spool/postfix/maildrop/* 会导致失败 /bin/rm: argument list too long 
#所以要换个删除的方式
ls /var/spool/postfix/maildrop | xargs -n 50 rm -fr ls
```
～释放出磁盘的空间

谷歌了下，postdrop是由sendmail因为邮件发送不成功而启动的，而postdrop总是执行失败，导致持续写入日志到日志文件。日志文件增大的速率超过了logrotate的删除频率，所以占据了100%的磁盘空间。并且线程数也不断的增加。 sendmail是由crontab 定时任务启动的。

1. 检查下crontab -l 里面的计划任务是否有问题，（也可以`less /var/spool/postfix/maildrop/****` 里面的文件，可以看到是哪个crontab的问题）。
2. 也可以停掉posfix /etc/init.d/posfix stop
3. 当然也可以设置crond的MAILTO为空，即不发邮件。


# postdrog进程产生解决

`解决crond引发大量sendmail、postdrop进程问题`


***问题***：

服务器内存接近耗尽报警，ps -ef查看有3000多个进程，大部分都是crond、sendmail、postdrop
而 ***postdrop是由sendmail启动的，而sendmail又是由crond启动的。***

 

***问题成因***：

crond在执行脚本时会将脚本输出信息以邮件的形式发送给系统用户，所以必然要调用sendmail，而sendmail又会调用postdrop发送邮件，但是如果系统的postfix服务没有正常运行，那么邮件就会发送不成功，造成sendmail、postdrop、crond进程就无法正常退出，形成大量的僵尸进程



***解决办法***：

先把僵尸进程都干掉ps -ef | egrep "sendmail|postdrop" | grep -v grep |xargs kill，让内存降下来，其实我一开始将postfix服务重启了一下，问题就解决了，观察了一段时间，僵尸进程并没有再次出现。

为防以后postfix挂了再出现类似问题，可以进行如下配置，将crond的邮件通知关闭：
将/etc/crontab和/etc/cron.d/0hourly里的MAILTO=root修改为MAILTO=""
crontab -e第一行增加一段MAILTO=""
