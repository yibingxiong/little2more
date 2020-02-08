
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
（不能这样下载， 因为有个什么破协议，需要先下载然后传上去）

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
- 添加匿名用户 useradd -M -s /sbin/nologin ftpuser  -M表示不要家
- 修改权限： chown -R ftpuser.ftpuser /ftpfile
- 重设ftpuser密码 passwd xxxx


4. 配置

```
cd /etc/vsftpd
vim chroot_list
```

将新增的ftpuser加到配置文件中

5. vim /etc/selinux/config 修改SELINUX=disable

如果遇到550则执行 setsebool -P ftp_home_dir 1

6. vsftpd.conf配置

```bash
# 配置说明,来源http://learning.happymmall.com/vsftpdconfig/vsftpd.conf.readme.html
本项目要用到的配置项：
1）local_root=/ftpfile(当本地用户登入时，将被更换到定义的目录下，默认值为各用户的家目录)
2）anon_root=/ftpfile(使用匿名登入时，所登入的目录)
3）use_localtime=YES(默认是GMT时间，改成使用本机系统时间)
4）anonymous_enable=NO(不允许匿名用户登录)
5）local_enable=YES(允许本地用户登录)
6）write_enable=YES(本地用户可以在自己家目录中进行读写操作)
7）local_umask=022(本地用户新增档案时的umask值)
8）dirmessage_enable=YES(如果启动这个选项，那么使用者第一次进入一个目录时，会检查该目录下是否有.message这个档案，如果有，则会出现此档案的内容，通常这个档案会放置欢迎话语，或是对该目录的说明。默认值为开启)
9）xferlog_enable=YES(是否启用上传/下载日志记录。如果启用，则上传与下载的信息将被完整纪录在xferlog_file 所定义的档案中。预设为开启。)
10）connect_from_port_20=YES(指定FTP使用20端口进行数据传输，默认值为YES)
11）xferlog_std_format=YES(如果启用，则日志文件将会写成xferlog的标准格式)
12）ftpd_banner=Welcome to mmall FTP Server(这里用来定义欢迎话语的字符串)
13）chroot_local_user=NO(用于指定用户列表文件中的用户是否允许切换到上级目录)
14）chroot_list_enable=YES(设置是否启用chroot_list_file配置项指定的用户列表文件)
15）chroot_list_file=/etc/vsftpd/chroot_list(用于指定用户列表文件)
16）listen=YES(设置vsftpd服务器是否以standalone模式运行，以standalone模式运行是一种较好的方式，此时listen必须设置为YES，此为默认值。建议不要更改，有很多与服务器运行相关的配置命令，需要在此模式下才有效，若设置为NO，则vsftpd不是以独立的服务运行，要受到xinetd服务的管控，功能上会受到限制)
17）pam_service_name=vsftpd(虚拟用户使用PAM认证方式，这里是设置PAM使用的名称，默认即可，与/etc/pam.d/vsftpd对应) userlist_enable=YES(是否启用vsftpd.user_list文件，黑名单,白名单都可以
18)pasv_min_port=61001(被动模式使用端口范围最小值)
19)pasv_max_port=62000(被动模式使用端口范围最大值)
20)pasv_enable=YES(pasv_enable=YES/NO（YES）
若设置为YES，则使用PASV工作模式；若设置为NO，则使用PORT模式。默认值为YES，即使用PASV工作模式。
   FTP协议有两种工作方式：PORT方式和PASV方式，中文意思为主动式和被动式。
   一、PORT（主动）方式的连接过程是：客户端向服务器的FTP端口（默认是21）发送连接请求，服务器接受连接，建立一条命令链路。
  当需要传送数据时，客户端在命令链路上用 PORT命令告诉服务器：“我打开了****端口，你过来连接我”。于是服务器从20端口向客户端的****端口发送连接请求，建立一条数据链路来传送数据。
   二、PASV（被动）方式的连接过程是：客户端向服务器的FTP端口（默认是21）发送连接请求，服务器接受连接，建立一条命令链路。
  当需要传送数据时，服务器在命令链路上用 PASV命令告诉客户端：“我打开了****端口，你过来连接我”。于是客户端向服务器的****端口发送连接请求，建立一条数据链路来传送数据。
  从上面可以看出，两种方式的命令链路连接方法是一样的，而数据链路的建立方法就完全不同。而FTP的复杂性就在于此。
)
```

出现vsftpd：500 OOPS: vsftpd: refusing to run with writable root inside chroot ()错误的解决方法

加一行这个配置就可以

```
allow_writeable_chroot=YES
```


7. 防火墙配置

参考文档 https://www.cnblogs.com/leoxuan/p/8275343.html

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
参考文档

https://www.linuxidc.com/Linux/2019-06/159104.htm


8. 验证

- 启动

service vsftpd restart 

9. 常用命令

- 启动： service vsftpd start
- 停止： service vsftpd restart
- 关闭： service vsftpd stop

也可以

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

### 执行目录

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