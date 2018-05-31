## 查看linux是32位还是64位

getconf LONG_BIT
uname -a

## 安装db2

``` shell
groupadd -g 901 db2iadm1
groupadd -g 902 db2fadm1
groupadd -g 903 db2sadm1

```

```
useradd -m -g db2iadm1 -d /home/db2inst1 db2inst1 --实例用户
useradd -m -g db2fadm1 -d /home/db2fenc1 db2fenc1 --受防护用户
useradd -m -g db2sadm1 -d /home/db2dasusr1 db2dasusr1 --DAS用户

```

```
passwd db2inst1

```

db2@2018

```
passwd db2fenc1

```

db2@2018

```
passwd db2dasusr1

```

db2@2018y

## 检查用户组和用户是否创建成功

用户组: more /etc/group \| grep db2
用户: more /etc/passwd \| grep db2

```
./db2prereqcheck

```

```
yum install libstdc++.so.6

```

```
./db2_install

```

```
cd /opt/ibm/db2/V9.7/instance

```

./dascrt -u db2dasusr1 ###创建DB2管理服务器
./db2icrt -u db2fenc1 db2inst1 ###创建DB2实例

vi  /ect/services    #修改services文件，在该文件的最后增加如下内容
db2c_db2inst1   50000/tcp   #TCP/IP services for db2inst1

su - db2inst1

#设置DB2的通信方式为tcpip
db2set DB2COMM=tcpip

#设置dbm参数SVCENAME为db2c_db2inst1
db2 update dbm cfg using SVCENAME db2c_db2inst1

#设置数据库自动启动
db2set DB2AUTOSTART=YES

#来查看当前的实例名
env \| grep DB2INSTANCE

#启动当前实例
db2start

#建立数据库databasename ,并指定字符集类型为GBK和区域为CN。
db2 create database databasename using codeset gbk territory cn
#也可以执行命令db2samp来建立DB2自带的范例数据库sample

#连接该数据库：
db2  connect  to  databasename

db2stop

[https://blog.csdn.net/m0_37373806/article/details/56843471](https://blog.csdn.net/m0_37373806/article/details/56843471)
经查证，属于DB2表空间不足导致的，连接到目标数据库下执行下列语句
CREATE BUFFERPOOL buf1 IMMEDIATE  SIZE 250 NUMBLOCKPAGES 108 BLOCKSIZE 32 PAGESIZE 32 K ;
CREATE  LARGE  TABLESPACE ts2 PAGESIZE 32 K  MANAGED BY AUTOMATIC STORAGE EXTENTSIZE 32 OVERHEAD 10.5 PREFETCHSIZE 32 TRANSFERRATE 0.14 BUFFERPOOL  buf1 ;

update db cfg using LOGFILSIZ 10240 --日志文件大小
update db cfg using LOGPRIMARY 100 --主日志文件个数
update db cfg using LOGSECOND 100 --辅助日志文件的个数

重启服务

CREATE BUFFERPOOL MYPOOL SIZE 500 PAGESIZE 32K;
CREATE TEMPORARY TABLESPACE TEMPSPACE2 PAGESIZE 32K MANAGED BY DATABASE USING(FILE
'/tmp/tbstmp32k' 128000) EXTENTSIZE 80 bufferpool MYPOOL;

建立DUAL视图：
create view dual as select IBMREQD as DUMMY from SYSIBM.SYSDUMMY1
这样查询就可以直接从DUAL中取系统数据了

db2递归函数
with n(字段) as
(
--递归种子
nuion all

)

db2的substr函数的第二个参数不能为0

配置环境变量
export JAVA\_HOME=/usr/local/java/jdk1\.7\.0\_45
export JRE\_HOME=/usr/local/java/jdk1\.7\.0\_45/jre
export CLASSPATH=\.:$JAVA\_HOME/lib/dt\.jar:$JAVA\_HOME/lib/tools\.jar:$JRE\_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin: $PATH