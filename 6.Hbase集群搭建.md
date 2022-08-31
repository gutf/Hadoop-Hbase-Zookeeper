# 解压

```shell
[root@hadoop02 hbase]# pwd
/home/hbase
[root@hadoop02 hbase]# ll
总用量 278464
-rw-r--r--. 1 root root 285145442 8月  29 21:14 hbase-2.4.13-bin.tar.gz
[root@hadoop02 hbase]# 
[root@hadoop02 hbase]# tar -zxvf hbase-2.4.13-bin.tar.gz -C /usr/local/
[root@hadoop01 hbase]# cd /usr/local/hbase-2.4.13/conf/
[root@hadoop03 conf]# ll
总用量 44
-rw-r--r--. 1 root root 1811 1月  22 2020 hadoop-metrics2-hbase.properties
-rw-r--r--. 1 root root 4284 1月  22 2020 hbase-env.cmd
-rw-r--r--. 1 root root 7669 1月  22 2020 hbase-env.sh
-rw-r--r--. 1 root root 2249 1月  22 2020 hbase-policy.xml
-rw-r--r--. 1 root root 2301 1月  22 2020 hbase-site.xml
-rw-r--r--. 1 root root 1169 1月  22 2020 log4j-hbtop.properties
-rw-r--r--. 1 root root 5735 1月  22 2020 log4j.properties
-rw-r--r--. 1 root root   10 1月  22 2020 regionservers

```



# 配置环境变量

```shell
[root@hadoop01 conf]# vim /etc/profile
[root@hadoop02 conf]# vim /etc/profile
[root@hadoop03 conf]# vim /etc/profile

# 添加以下内容
export HBASE_HOME=/usr/local/hbase-2.4.13
export PATH=$HBASE_HOME/bin:$PATH


# 使配置的环境变量立即生效
[root@hadoop01 conf]# source /etc/profile
[root@hadoop02 conf]# source /etc/profile
[root@hadoop03 conf]# source /etc/profile

```



# 集群配置

## hbase-env.sh

```shell
# 配置JDK安装位置
export JAVA_HOME=/usr/java/jdk1.8.0_201
# 不使用内置的zookeeper服务
export HBASE_MANAGES_ZK=false
# Hadoop配置
export HBASE_CLASSPATH=/usr/local/hadoop-3.3.4/etc/hadoop

```



## hbase-site.xml

```xml
<configuration>
    <property>
        <!-- 指定 hbase 以分布式集群的方式运行 -->
        <name>hbase.cluster.distributed</name>
        <value>true</value>
    </property>
    <property>
        <!-- 指定 hbase 在 HDFS 上的存储位置 需和hadoop中的core-site.xml中的fs.defaultFS一致-->
        <name>hbase.rootdir</name>
        <value>hdfs://hadoop01:9000/hbase</value>
    </property>
    <property>
        <!-- 指定 zookeeper 的地址-->
        <name>hbase.zookeeper.quorum</name>
        <value>hadoop01,hadoop02,hadoop03</value>
    </property>
    <property>
    	<name>hbase.tmp.dir</name>
    	<value>./tmp</value>
  	</property>
  	<property>
    	<name>hbase.unsafe.stream.capability.enforce</name>
    	<value>false</value>
  	</property>
    <property>
  		<name>hbase.wal.provider</name>
  		<value>filesystem</value>
	</property>

</configuration>

```



##  regionservers

```shell
hadoop001
hadoop002
hadoop003
```



## backup-masters

```shell
hadoop02
```

> `backup-masters` 这个文件是不存在的，需要新建，主要用来指明备用的 master 节点，可以是多个，这里我们以 1 个为例。



# 启动集群

## Zookeeper集群

```shell
# 在每个节点执行 zkServer.sh命令
[root@hadoop01 bin]# zkServer.sh
[root@hadoop01 bin]# zkServer.sh
[root@hadoop01 bin]# zkServer.sh
```



## Hadoop集群

```shell
# 在namenode节点执行start-all.sh命令
[root@hadoop01 sbin]# start-all.sh
```



## Hbase集群

```shell
# 在HDFS的namenode节点执行start-hbase.sh命令
[root@hadoop01 bin]# start-hbase.sh 
```



集群状态查看

访问**http://hbase主节点IP:16010**查看集群状态



















