

## 查看帮助
* man grep
* info grep
info比man信息更全，示例更全

shell语法可查看：man sh


## grep ack ag
简介：
* grep：linux默认搜索工具
* ack：ubuntu下为ack-grep，可替代grep，更快
* ag：ubuntu13.04以上，软件包silversearcher-ag，可以多线程，在多核cpu上比ack更快
* pt：https://github.com/monochromegane/the_platinum_searcher

目前先用ag，ubuntu12.04从源码安装ag：[github](https://github.com/ggreer/the_silver_searcher)


网上的总结：
* ubuntu下ack为ack-grep
* 在搜索的总数据量较小的情况下， 使用grep， ack甚至ag在感官上区别不大
* 搜索的总数据量较大时， grep效率下滑的很多， 完全不要选
* ack在某些场景下没有grep效果高(比如使用-v搜索中文的时候)
* 在不使用ag没有实现的选项功能的前提下， ag完全可以替代ack/grep



## 任务相关
* & ：在命令后加&，将任务在后台运行，如在后台打开一个vim编辑器
* jobs ：查看后台所有的任务，我们可以看到后台打开了一个vim编辑器
* fg：将一个后台命令调至前台继续运行
* bg：将一个后台命令在后台继续运行
* ctrl + z：将一个正在前台运行的任务调至后台

```bash
jty@jty-vm:~$ vim 11 &  vim 22 &
[1] 40894
[2] 40895
jty@jty-vm:~$ jobs
[1]+  Stopped                 vim 11
[2]-  Stopped                 vim 22
jty@jty-vm:~$ fg 2
vim 22

[2]+  Stopped                 vim 22
jty@jty-vm:~$ jobs
[1]-  Stopped                 vim 11
[2]+  Stopped                 vim 22
jty@jty-vm:~$ bg 2
[2]+ vim 22 &
jty@jty-vm:~$ jobs
[1]-  Stopped                 vim 11
[2]+  Stopped                 vim 22
```


