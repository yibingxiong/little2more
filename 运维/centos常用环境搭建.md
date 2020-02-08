
# centos常用环境配置

## 修改软件源

[参考文章](https://developer.aliyun.com/mirror/centos?spm=a2c6h.13651102.0.0.3e221b11kAZnYc)

对于腾讯云的云服务器不必改， 默认是腾讯的源


## linux学习资料

- [Linux软件安装管理](https://www.imooc.com/learn/447）
- [Linux权限管理之基本权限](http://www.imooc.com/learn/481)
- [Linux达人养成计划 I](http://www.imooc.com/learn/175)
- [Linux服务管理](http://www.imooc.com/learn/537)
- [用iptables搭建一套强大的安全防护盾](http://www.imooc.com/learn/389)

## jdk安装

1. jdk下载

- [jdk历史版本](https://www.oracle.com/technetwork/java/javase/archive-139210.html)

- 下载

```
wget https://download.oracle.com/otn/java/jdk/8u231-b11/5b13a193868b4bf28bcb45c792fce896/jdk-8u231-linux-x64.rpm
```
不能这样下载， 因为有个什么破协议，需要先下载然后传上去

scp命令

```
scp root@107.172.27.254:/home/test.txt .   //下载文件

scp test.txt root@107.172.27.254:/home  //上传文件

scp -r root@107.172.27.254:/home/test .  //下载目录

scp -r test root@107.172.27.254:/home   //上传目录
```

1. 卸载默认jdk, 如果有的话

- 判断是否有jdk 
```
rpm -qa | grep jdk
```
- 删除
```
yum remove xxx
```

2. 对下载的jdk rpm文件赋予权限

```
chmod 777 jdk.xxx.rpm
```
3. 安装

```
rpm -ivh jdk.xxx.rpm
```

4. 默认安装路径 /usr/java

5. 配置环境变量

- 配置文件 /etc/profile
- 配置PATH ,CLASSPATH, JAVA_HOME
- source /etc/profile

```bash
# 这里 JAVA_HOME为java的安装目录
export JAVA_HOME=/usr/java/jdk1.8.0_202-amd64
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/tools.jar
```

6. 验证jdk
```
java -version
```

## tomcat安装

1. 下载（https://tomcat.apache.org/download-80.cgi）

- 地址： http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.50/bin/apache-tomcat-8.5.50.tar.gz

```
wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.50/bin/apache-tomcat-8.5.50.tar.gz
```

2. 解压缩

3. 配置环境变量

```
export CATALINA_HOME=/devlib/apache-tomcat-8.5.50
```

4. 配置utf-u字符集，支持中文，不是必须的

```bash
# ${CATALINA_HOME}/conf/server.xml URIEncoding="UTF-8"

<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIEncoding="UTF-8"/>

```

5. 验证

```bash
# 进入 ${CATALINA_HOME}/bin
./startup.sh
# 访问http://${ip}:8080/
```

6. 常用命令

- 启动 ${CATALINA_HOME}/bin/startup.sh
- 关闭 ${CATALINA_HOME}/bin/shutdown.sh

## maven安装

1. 下载

```
wget http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
```

2. 解压缩

3. 配置环境变量

- MAVEN_HOME=安装目录
- 将bin目录加到PATH中

4. 验证

```
mvn -version
```
