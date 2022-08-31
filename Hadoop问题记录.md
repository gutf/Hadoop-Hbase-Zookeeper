# 问题记录

## **解决 Hadoop 启动 ERROR: Attempting to operate on hdfs namenode as root 的方法**

```shell
[root@hadoop01 sbin]# cd /usr/local/hadoop-3.3.4/sbin/
[root@hadoop01 sbin]# ./start-all.sh 
Starting namenodes on [hadoop01]
ERROR: Attempting to operate on hdfs namenode as root
ERROR: but there is no HDFS_NAMENODE_USER defined. Aborting operation.
Starting datanodes
ERROR: Attempting to operate on hdfs datanode as root
ERROR: but there is no HDFS_DATANODE_USER defined. Aborting operation.
Starting secondary namenodes [hadoop01]
ERROR: Attempting to operate on hdfs secondarynamenode as root
ERROR: but there is no HDFS_SECONDARYNAMENODE_USER defined. Aborting operation.
Starting resourcemanager
ERROR: Attempting to operate on yarn resourcemanager as root
ERROR: but there is no YARN_RESOURCEMANAGER_USER defined. Aborting operation.
Starting nodemanagers
ERROR: Attempting to operate on yarn nodemanager as root
ERROR: but there is no YARN_NODEMANAGER_USER defined. Aborting operation.


```



解决方案

```shell
[root@hadoop01 sbin]# vim /etc/profile
export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root

[root@hadoop01 sbin]# source /etc/profile
```



## 无Datanode、Namenode进程

>如果是少namenode则是因为没有对HDFS格式化。执行命令：hadoop namenode -format 并按照相关提示进行格式化。
>
>如果是少datanode则可能是因为对HDFS进行了多次格式化，造成clusterID不一致。因为每次格式化，namenode的name->current->VALUE中的clusterID值都会发生变化，而datanode的clusterID值回一直不变。如果出现上诉原因，把namenode里面的clusterID复制到datanode里面的clusterID，使其保持一致。
>