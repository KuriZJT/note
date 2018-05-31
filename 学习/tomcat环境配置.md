# 环境配置

* 解压jdk
* 配置jdk环境变量

```
vi .bash_profile
export JAVA_HOME=/usr/local/jdk1.7.0_71
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$JAVA_HOME/bin:$PATH/home/stmm/jdk1.7.0_79

```

* 部署tomcat

setclasspath.sh
catalina.sh
如果jre版本不正确，修改这两个文件，在第一行加上

#### **export JRE\_HOME=/home/stmm/jdk1\.7\.0\_79/jre**

* 部署项目
* 导入数据