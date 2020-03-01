> 每次搭各种环境都很痛苦，遇到各种问题，因此写下此篇记录下centos7下一些环境的搭建过程、遇到的问题及解决方法，有其他环境的搭建也会进行更新。本文不值得看，但可以收藏下，已备不时之需。本文主要参考了[从0开始 独立完成企业级Java电商网站开发服务端](https://coding.imooc.com/class/421.html)

## 修改软件源

修改为国内的yum源可以加快下载速度

[参考文章](https://developer.aliyun.com/mirror/centos?spm=a2c6h.13651102.0.0.3e221b11kAZnYc)

对于腾讯云的云服务器不必改， 默认是腾讯的源


## linux学习资料

- [Linux软件安装管理](https://www.imooc.com/learn/447)
- [Linux权限管理之基本权限](http://www.imooc.com/learn/481)
- [Linux达人养成计划 I](http://www.imooc.com/learn/175)
- [Linux服务管理](http://www.imooc.com/learn/537)
- [用iptables搭建一套强大的安全防护盾](http://www.imooc.com/learn/389)



## jdk安装

1. jdk下载

- [jdk历史版本地址](https://www.oracle.com/technetwork/java/javase/archive-139210.html)

- 下载

```
wget https://download.oracle.com/otn/java/jdk/8u231-b11/5b13a193868b4bf28bcb45c792fce896/jdk-8u231-linux-x64.rpm
```
**（不能这样下载， 因为有个什么破协议，需要先下载然后传上去）**

上传到服务器可以用scp命令

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
# 这里直接给了最高权限，大家慎重(这里必须要给，否则会报错)
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

在/etc/profile加的变量如下

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

1. 下载（历史版本地址https://tomcat.apache.org/download-80.cgi）


```
wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.50/bin/apache-tomcat-8.5.50.tar.gz
```

2. 解压缩

3. 配置环境变量

```
# 这个我之前没配过，貌似用处不大
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
5. 常用命令

- 清除： mvn clean
- 编译： mvn compile
- 打包： mvn package
- 跳过单元测试： mvn clean package -Daven.test.skip=true

## ftp服务搭建（vsftpd)

1. 安装

```
yum -y install vsftpd
```

2. 配置文件位于/etc/vsftpd/vsftpd.conf

2. 必须将/sbin/nologin 加到/etc/shells中 (这个卡了很久，请一定注意，否则nologin的用户连不上)

3. 创建虚拟用户

- 创建一个目录作为ftp目录， 如/ftpfile
- 添加匿名用户 useradd -M -s /sbin/nologin ftpuser  -M表示不要家目录
- 修改权限： chown -R ftpuser.ftpuser /ftpfile
- 重设ftpuser密码 passwd ftpuser


4. 配置

```
cd /etc/vsftpd
vim chroot_list
```

将新增的ftpuser加到配置文件中

5. vim /etc/selinux/config 修改SELINUX=disable

如果遇到550则执行 setsebool -P ftp_home_dir 1

6. vsftpd.conf配置

配置说明参考

http://learning.happymmall.com/vsftpdconfig/vsftpd.conf.readme.html

注意几个点:
- local_root
- local_enable
- anonymous_enable
- pasv_min_port
- pasv_max_port


- 出现vsftpd：500 OOPS: vsftpd: refusing to run with writable root inside chroot ()错误的解决方法

加一行这个配置就可以

```
allow_writeable_chroot=YES
```


7. 防火墙配置



```
# 这是centos7, 6是不一样的
启动： systemctl start firewalld
关闭： systemctl stop firewalld
查看状态： systemctl status firewalld 
开机禁用  ： systemctl disable firewalld
开机启用  ： systemctl enable firewalld

开放端口 firewall-cmd --zone=public --add-port=80/tcp --permanent    （--permanent永久生效，没有此参数重启后失效）
关闭端口 firewall-cmd --zone=public --remove-port=80/tcp --permanent
查看开放的端口 firewall-cmd --zone=public --list-ports
更新防火墙规则： firewall-cmd --reload

```

8. 验证

- 启动
·
```
service vsftpd start 
```
- 尝试用客户端去连

9. 常用命令

- 启动： service vsftpd start
- 停止： service vsftpd restart
- 关闭： service vsftpd stop


```
#设置开机启动
systemctl enable vsftpd.service

#启动
systemctl start vsftpd.service

#停止
systemctl stop vsftpd.service

#查看状态
systemctl status vsftpd.service

#重启
systemctl restart vsftpd.service
```

## nginx

1. 安装gcc

```
yum install -y gcc
gcc -v
```

2. 安装pcre

```
yum install -y pcre-devel
```

3. 安装zlib

```
yum install -y zlib zlib-devel
```

4. 安装openssl(支持ssl)

```
yum install -y openssl openssl-devel
```

5. 以上可以一个命令安装

```
yum install -y gcc pcre-devel zlib zlib-devel openssl openssl-devel
```

6. 下载nginx

历史版本http://nginx.org/download/

```
wget http://nginx.org/download/nginx-1.13.6.tar.gz
```

7. 解压

8. 进入解压后的目录执行

```
./configure
# 加--prefix=/usr/nginx可以指定安装目录
whereis nginx可以查询安装目录， 默认在/usr/local/
```
9. 执行make 然后make install

10. 常用命令

- 测试配置文件正确性
```
/nginx/sbin/nginx -t
```
- 启动
```
/nginx/sbin/nginx
```
- 停止
```
/nginx/sbin/nginx -s stop
或
nginx -s quit
```
- 重启

```
/nginx -s reload
```
- 进程查看

```
ps -ef|grep nginx
```
- 平滑重启
```
kill -HUP 进程号
```

11. 配置

- 分解配置文件

在/usr/local/nginx/conf/nginx.conf增加 include vhost/*.conf  这样方便每个域名配搞个配置文件

### 指向端口

```
server {
    default_type 'text/html';
    charset utf-8;
    listen 80;
    # 开索引
    autoindex on; 
    server_name learning.happymmall.com;
    access_log /usr/local/nginx/logs/access.log combined;
    index index.html index.htm index.jsp index.php;
    #error_page 404 /404.html;
    if ( $query_string ~* ".*[\;'\<\>].*" ){
        return 404;
    }

    # 注意这个
    location / {
        proxy_pass   http://127.0.0.1:81/aa;
        # 真实环境别这么写
        add_header Access-Control-Allow-Origin *;
    }
}

```

### 指向目录

```
server {
    listen 80;
    # 注意这里禁止索引了
    autoindex off;
    server_name img.happymmall.com;
    access_log /usr/local/nginx/logs/access.log combined;
    index index.html index.htm index.jsp index.php;
    #error_page 404 /404.html;
    if ( $query_string ~* ".*[\;'\<\>].*" ){
        return 404;
    }

    location ~ /(mmall_fe|mmall_admin_fe)/dist/view/* {
        deny all;
    }

    location / {
        root /product/ftpfile/img/;
        add_header Access-Control-Allow-Origin *;
    }
}

```

## mariaDB安装


https://www.cnblogs.com/yhongji/p/9783065.html


## mysql

1. 安装

```
yum install -y mysql-server
```
(centos7下这样不行， 软件源里边没有这个，需要下载源再装，如下)

参考文章https://www.cnblogs.com/mcoding-one/p/11698271.html


2. 配置文件位置/etc/my.cnf

3. 配置mysql自启动

用上面配置vsftpd自启动的方法也可以

```
chkconfig mysqld on
# 2-5需要时on
chkconfig --list mysqld
```
4. 防火墙开放3306
5. 启动验证

```
启动：systemctl start mysqld.service
停止：systemctl stop mysqld.service
重启：systemctl restart mysqld.service
查看服务状态：systemctl status mysqld.service
```

- 出现Job for mysqld.service failed because the control process exited with error code问题

删除/var/lib/mysql 再启动

- 登录(能登录进去就证明可以了)

```
mysql -u root
```
- 登录出错
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)

修改/etc/my.cnf 在[mysqld]下面加一行skip-grant-tables
登录后改root密码， 然后去掉这一行

改密码的方法

```
alter user'root'@'localhost' IDENTIFIED BY '你的密码';
```


5. mysql用户和权限相关配置

https://www.cnblogs.com/gychomie/p/11013442.html

```
创建用户

create user 'test1'@'localhost' identified by '‘密码';

flush privileges;刷新权限

 

其中localhost指本地才可连接

可以将其换成%指任意ip都能连接

也可以指定ip连接


修改密码

Alter user 'test1'@'localhost' identified by '新密码';

flush privileges;

 

授权

grant all privileges on *.* to 'test1'@'localhost' with grant option;

 

with gran option表示该用户可给其它用户赋予权限，但不可能超过该用户已有的权限

比如a用户有select,insert权限，也可给其它用户赋权，但它不可能给其它用户赋delete权限，除了select,insert以外的都不能

这句话可加可不加，视情况而定。

 

all privileges 可换成select,update,insert,delete,drop,create等操作

如：grant select,insert,update,delete on *.* to 'test1'@'localhost';

 

第一个*表示通配数据库，可指定新建用户只可操作的数据库

如：grant all privileges on 数据库.* to 'test1'@'localhost';

 

第二个*表示通配表，可指定新建用户只可操作的数据库下的某个表

如：grant all privileges on 数据库.指定表名 to 'test1'@'localhost';


查看用户授权信息

show grants for 'test1'@'localhost';

 
撤销权限

revoke all privileges on *.* from 'test1'@'localhost';

用户有什么权限就撤什么权限

删除用户

drop user 'test1'@'localhost';
```



## 参考资料

- [从0开始 独立完成企业级Java电商网站开发(服务端)](https://coding.imooc.com/class/421.html)
- [CentOS 镜像](https://developer.aliyun.com/mirror/centos?spm=a2c6h.13651102.0.0.3e221b11kAZnYc)
- [CentOS7 防火墙（firewall）的操作命令](https://www.cnblogs.com/leoxuan/p/8275343.html)
- [CentOS 7防火墙快速开放端口配置方法](https://www.linuxidc.com/Linux/2019-06/159104.htm)
- [MYSQL8创建、删除用户和授权、消权操作](https://www.cnblogs.com/gychomie/p/11013442.html)


(持续更新...)