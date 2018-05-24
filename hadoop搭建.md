1. 配置网卡
```
[root@~]# vim /etc/sysconfig/network-scripts/ifcfg-eth0
```
2. 关闭防火墙

检查防火墙是否开机自启
```
[root@~]# chkconfig --list | grep iptables
```

关闭防火墙开机自启
```
[root@~]# chkconfig --del iptables
```

3. 配置hosts文件，路径在/etc/hosts

```
[root@~]# vim /etc/hosts
```
配置信息

** 192.168.2.110 master **

4. ping master

```
[root@~]# ping mastart
```

6. 查看虚拟机系统中是否安装lvm工具

```
[root@~]# rpm -qa | grep lvm
```

5. 创建物理卷

在虚拟机中添加3块硬盘(均为20G)，查看是否创建成功

```
[root@~]# fdisk -l
```

6. 创建物理卷

（pvcreate指令用于将物理硬盘分区初始化为物理卷，以便被LVM使用。）

a)使用sdb创建基于sdb的物理卷

```
[root@~]# pvcreate /dev/sdb
```

b)使用sdc创建基于sdc的物理卷

```
[root@~]# pvcreate /dev/sdc
```

c)使用sdd创建基于sdd的物理卷

```
[root@~]# pvcreate /dev/sdd
```

d)查看物理卷是否创建成功

```
[root@~]# pvdisplay
```

7. 激活卷组

```
[root@~]# vgchange -a y test_document
```

8. 创建逻辑卷

 (该命令是在卷组test_document上创建名字为lvhadoop，大小为5120M的逻  辑卷，并且设备入口为/dev/test_document/lvhadoop ,test_document为卷组名，lvhadoop为逻辑卷名）
```
[root@~]# lvcreate -L5120 -n lvhadoop test_document
```

(该命令是在卷组test_document上创建名字为lvdata，大小为51200M的逻  辑卷，并且设备入口为/dev/test_document/lvdata ,test_document为卷组名，lvdata为逻辑卷名）
```
[root@~]# lvcreate -L116736 -n lvdata test_document
```

9. 创建文件系统

```
[root@~]# mkfs -t ext4/dev/test_document/lvhadoop
[root@~]# mkfs -t ext4/dev/test_document/lvdata
```

10. 创建文件夹

在linux根目录下创建hadoop文件夹

在linux根目录下创建data文件夹

```
[root@~]# mkdir -p /hadoop
[root@~]# mkdir -p /data
```

11. 挂载

```
[root@~]# mount /dev/test_document/lvhadoop /hadoop/
[root@~]# mount /dev/test_document/lvdata /data/
```

12. 修改自动挂载的配置文件

```
tail -n 2 /etc/mtab >> /etc/fstab
```

13. 创建hadoop组和用户

密码改为：123456（与root用户的密码一致）
```
[root@~]# groupadd -g 3000 cloudadmin
[root@~]# useradd -u 3001 -g cloudadmin hadoop
[root@~]# passwd hadoop
```

14. 修改文件的系统权限

```
[root@~]# chown -R hadoop:cloudadmin /hadoop
[root@~]# chown -R hadoop:cloudadmin /data
```

15. 复制2个datanote节点

查看mac地址
```
[root@~]# cat /sys/class/net/eth0/address
```

修改ip和mac地址

16. 修改映射关系

用root登录第一台虚拟机，修改ip映射

```
[root@~]# vim /etc/hosts
```

进入/etc文件夹<br/>
scp hosts192.168.102.129://etc/<br/>
将hm etc文件夹下的hosts传到192.168.102.128 的etc下<br/>
scp hosts192.168.102.130://etc/<br/>
将hm etc文件夹下的hosts传到192.168.102.130 的etc下<br/>
有提示时，输入yes，密码是刚设置的123456

** 在每台虚拟机中进行ping通测试，这里每台虚拟机都需要测试与另外所有虚拟机是否通，且ping ip和ping 主机名都要测试（这里很容易漏测）**

### 在集群中配置SSH免密登录

1. 重启所有虚拟机，均用hadoop用户登录
2. 在所有节点里输入ssh-keygen -t rsa命令，然后一直按回车即可

```
[root@~]# ssh-keygen -t
[root@~]# cd ~
[root@~]# cd .ssh
[root@~]# catid_rsa.pub >> authorized_keys
[root@~]# scp authorized_keys192.168.2.111:/home/hadoop/.ssh/
[root@~]# scpauthorized_keys 192.168.2.112:/home/hadoop/.ssh/
[root@~]# ssh hd001
```

17. 在集群中的所有节点上创建相应的文件目录

```
[root@~]# mkdir -p /data/tmp
[root@~]# mkdir -p /data/name
[root@~]# mkdir -p /data/data
[root@~]# cd /data
[root@~]# ls
```

core-site.xml
``` xml
<property>
    <name>fs.defalut.name</name>
    <value>hdfs://master:9000</value>
</property>
<property>
    <name>hadoop.tmp.dir</name>
    <value>/data/tmp</value>
</property>
```

hdfs-site.xml
``` xml
<property>
    <name>hdfs.name.dir</name>
    <value>/data/name</value>
</property>
<property>
    <name>dfs.data.dir</name>
    <value>/data/data</value>
</property>
<property>
    <name>dfs.replication</name>
    <value>2</value>
</property>
<property>
    <name>dfs.namenode.secondary.http-address</name>
    <value>master:9001</value>
</property>
```

mapred-site.xml.template
``` xml
<property>
    <name>mapred.job.tracker</name>
    <value>master:9001</value>
</property>
```

slaves
```
hd001
hd002
```

18. 分发hadoop软件包到从节点上(hadoop用户)
```
[root@~]# scp -r hadoop-2.7.3hd001:/hadoop/
[root@~]# scp -rhadoop-2.7.3 hd002:/hadoop/
```

19. 格式化HDFS

在hadoop用户下进入主节点的/hadoop/hadoop2.7.3目录<br>
格式化hdfs

```
[root@~]# bin/hadoop namenode -format
```

** 格式化只能1次，如果后面再次格式化则会导致不成功，需要将所有节点上根目录下data目录下的data、name、tmp文件删除，再新建data、name、tmp空的文件夹。**

20. 启动hadoop 系统

  1. 用hadoop用户登录主节点，进入/hadoop/hadoop2.7.3目录
  2. 启动hadoop系统
__（关闭集群sbin/stop-all.sh）__

```
[root@~]# bin/start-all.sh或sbin/start-all.sh
```

21. 配置环境变量

```
[root@~]# export HADOOP_HOME=/hadoop/hadoop-2.7.6
[root@~]# export PATH=.:$HADOOP_HOME/bin:$JAVA_HOME/bin:$PATH
```
