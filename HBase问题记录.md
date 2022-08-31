# Hadoop与Hbase版本兼容问题

>首先搭建集群使用的是hadoop-3.3.4、hbase-2.4.13、zookeeper-3.7.1，搭建完毕后发现hadoop与hbase不兼容，故将hbase版本降级为hbase-2.3.0

# 执行./hbase shell命令出现的问题

>执行./hbase shell出现以下内容：

```shell
[root@hadoop01 bin]# ./hbase shell
  This file has been superceded by packaging our ruby files into a jar and using jruby's bootstrapping to invoke them. If you need to source this file fo some reason it is now named 'jar-bootstrap.rb' and is located in the root of the file hbase-shell.jar and in the source tree at 'hbase-shell/src/main/ruby'.
```

解决方案

```shell
[root@hadoop01 bin]# hbase shell
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/hbase-2.4.13/lib/client-facing-thirdparty/slf4j-reload4j-1.7.33.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hadoop-3.3.4/share/hadoop/common/lib/slf4j-reload4j-1.7.36.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Reload4jLoggerFactory]
HBase Shell
Use "help" to get list of supported commands.
Use "exit" to quit this interactive shell.
For Reference, please visit: http://hbase.apache.org/2.0/book.html#shell
Version 2.4.13, r90fb1ddc1df9b345f26687d5d24cedfb19621d63, Wed Jun 22 20:16:39 PDT 2022
Took 0.0014 seconds                                                                                                                                                                         
hbase:001:0> status
1 active master, 1 backup masters, 3 servers, 0 dead, 0.6667 average load
Took 1.1545 seconds                                                                                                                                                                         
hbase:002:0> exit

```



# slf4j-reload4j-1.7.33.jar

```shell
[root@hadoop01 bin]# hbase shell
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/hbase-2.4.13/lib/client-facing-thirdparty/slf4j-reload4j-1.7.33.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hadoop-3.3.4/share/hadoop/common/lib/slf4j-reload4j-1.7.36.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Reload4jLoggerFactory]

```

>hadoop与hbase均存在该jar，如果冲突可以删除其中一个jar即可（**未验证！！！**）



# HBase执行关闭命令等待时间过长的解决方法

执行stop-hbase.sh时，等待很长时间都没结束（出来很多“...”），有2个解决方法：

 

**方法1**

```
hbase-daemons.sh stop master
stop-hbase.sh
```

 

**方法2**

```
hbase-daemons.sh stop regionserver
stop-hbase.sh
```

 

> - 同时有hbase-daemon.sh和hbasee-daemons.sh两个文件，前者是操作本机的，后者是操作整个集群的，注意不要打漏了s
> - 尽量别使用kill命令来关闭，否则会有一定的损坏风险。



# org.apache.hadoop.hbase.ipc.ServerNotRunningYetException: Server is not running yet

修改hbase-site.xml文件，添加内容如下

```xml
<property>
  <name>hbase.wal.provider</name>
  <value>filesystem</value>
</property>
```























