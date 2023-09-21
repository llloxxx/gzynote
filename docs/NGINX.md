## 一、安装与配置
### （一）编译安装
#### 1、安装依赖环境
（1）安装gcc环境
```bash
yum install gcc-c++
```
（2）安装PERE库，用于解析正则表达式
```bash
yum install -y pcre pcre-devel
```
（3）zlib压缩和解压缩依赖
```bash
yum install -y zlib zlib-devel
```
（4）SSL安全的加密的套接字协议层，用于HTTP安全传输，也就是https
```bash
yum install -y openssl openssl-devel
```
#### 2、解压源码包
```bash
tar -zxvf nginx1.16.1.tar.gz
```
#### 3、编译前，先创建nginx临时目录，如果不创建，在启动nginx时会报错
```bash
mkdir /var/temp/nginx -p
```
#### 4、在nginx目录，输入如下命令进行配置，目的是为了创建makefile文件
```bash
./configure \\\\
--prefix=/usr/local/nginx \\\\
--pid-path=/var/run/nginx/nginx.pid \\\\
--lock-path=/var/lock/nginx.lock \\\\
--error-log-path=/var/log/nginx/error.log \\\\
--http-log-path=/var/log/nginx/access.log \\\\
--with-http_gzip_static_module \\\\
--http-client-body-temp-path=/var/temp/nginx/client \\\\
--http-proxy-temp-path=/var/temp/nginx/proxy \\\\
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \\\\
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \\\\
--http-scgi-temp-path=/var/temp/nginx/scgi
```
| 命令 | 解释 |
| --- | --- |
| --prefix | 指定Nginx安装目录 |
| --pid-path | 指向nginx的pid |
| --lock-path | 锁定安装文件，防止被恶意篡改或错误操作 |
| --error-log-path | 错误日志 |
| --http-log-path | 日志 |
| --with-http_gzip_static_module | 启用gzip模块，在线实时压缩输出数据流 |
| --http-client-body-temp-path | 设定客户端请求的临时目录 |
| --http-proxy-temp-path | 设定http代理临时目录 |
| --http-fastcgi-temp-path | 设定fastcgi临时目录 |
| --http-scgi-temp-path | 设定scgi临时目录 |

#### 5、make编译&安装
```bash
make
make install
```
#### 6、进入sbin目录启动nginx
```
启动：nginx
停止：./nginx -s stop
重新加载配置：./nginx -s reload
```
### （二）配置反向代理
反向代理：反向代理也叫reverse proxy，指的是代理外网用户的请求到内部的指定web服务器，并将数据返回给用户的一种方式，这是用的比较多的一种方式。 Nginx除了可以在企业提供高性能的web服务之外，另外还可以将本身不具备的请求通过某种预定义的协议转发至其它服务器处理，不同的协议就是Nginx服务器与其他服务器进行通信的一种规范，主要在不同的场景使用以下模块实现不同的功能：
```bash
ngx_http_proxy_module： 将客户端的请求以http协议转发至指定服务器进行处理。
ngx_stream_proxy_module：将客户端的请求以tcp协议转发至指定服务器处理。
ngx_http_fastcgi_module：将客户端对php的请求以fastcgi协议转发至指定服务器助理。
ngx_http_uwsgi_module：将客户端对Python的请求以uwsgi协议转发至指定服务器处理。
```

1. proxy_pass_request_body on | off; 是否向后端服务器发送HTTP包体部分,可以设置在http/server或location块，默认即为开启
2. proxy_pass_request_headers on | off; 是否将客户端的请求头部转发给后端服务器，可以设置在http/server或location块，默认即为开启
3. proxy_set_header; 可以更改或添加客户端的请求头部信息内容并转发至后端服务器，比如在后端服务器想要获取客户端的真实IP的时候，就要更改每一个报文的头部，如下： proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;_# proxy_set_header HOST $remote_addr;# 添加HOST到报文头部，如果客户端为NAT上网那么其值为客户端的共用的公网IP地址。_
4. proxy_hide_header field; 用于隐藏后端服务器特定的响应首部，默认nginx在响应报文中不传递后端服务器的首部字段Date, Server, XPad, X-Accel等
5. proxy_connect_timeout time; 配置nginx服务器与后端服务器尝试建立连接的超时时间，默认为60秒 proxy_connect_timeout 60s；_# 60s为自定义nginx与后端服务器建立连接的超时时间_
6. proxy_read_time time; 配置nginx服务器向后端服务器或服务器组发起read请求后，等待的超时时间，默认60s proxy_send_time time；_# 配置nginx项后端服务器或服务器组发起write请求后，等待的超时时间，默认60s_
7. proxy_http_version 1.0; 用于设置nginx提供代理服务的HTTP协议的版本，默认http 1.0
8. proxy_ignore_client_abort off; 当客户端网络中断请求时，nginx服务器中断其对后端服务器的请求。即如果此项设置为on开启，则服务器会忽略客户端中断并一直等着代理服务执行返回，如果设置为off，则客户端中断后Nginx也会中断客户端请求并立即记录499日志，默认为off。
9. proxy_headers_hash_bucket_size 64; 当配置了 proxy_hide_header和proxy_set_header的时候，用于设置nginx保存HTTP报文头的hash表的上限。
10. proxy_headers_hash_max_size 512; 设置proxy_headers_hash_bucket_size的最大可用空间
11. server_names_hash_bucket_size 512; server_name hash表申请空间大小
12. server_names_hash_max_szie 512; 设置服务器名称hash表的上限大小
#### 1、配置upstream
```bash
upstream [proxyName] {
server 192.168.1.173:8080;
server 192.168.1.174:8080;
server 192.168.1.175:8080;
}
```
#### 2、配置server
```bash
server {
listen 80;
server_name www.tomcats.com;

location / {
proxy_pass <http://tomcats>;
}
}
```
#### 3、正常情况下浏览器都是以 GET 方式向服务器发送请求，如果不想暴露服务器可以使用POST方式
```bash
location / {
proxy_pass <http://192.168.0.1:8080>;
proxy_method post;  #以post方式给浏览器返回信息
proxy_set_header Host $host;
proxy_set_header Z-Real-IP $remote_addr;    #获取客户端的ip地址设置到header中
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;    #获取所有转发请求的ip信息列表

}
```
示例1：
```bash
server {
listen  443 ssl;
server_name note.chenxide.space;

ssl_certificate /etc/nginx/cert/note.chenxide.space.pem; #change /etc/ssl/note/example.crt to your path of crt file.
ssl_certificate_key /etc/nginx/cert/note.chenxide.space.key;
ssl_prefer_server_ciphers on;
ssl_ciphers HIGH:!aNULL:!eNULL:!EXPORT:!CAMELLIA:!DES:!MD5:!PSK:!RC4;
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_session_cache builtin:1000 shared:SSL:10m;

location / {
proxy_pass  <http://172.17.0.7:8080>;
#proxy_method post;
proxy_set_header Host $http_host;    #获取客户端的ip地址设置到header中
proxy_set_header Z-Real-IP $remote_addr;    #获取所有转发请求的ip信息列表
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
}

server {
listen       80;
server_name  note.chenxide.space;
rewrite ^(.*)$ <https://note.chenxide.space>;  #重定向到HTTPS

}
```
实例2：
```bash
upstream trilium {
	server 172.17.0.6:8080;
}

server {
	listen	80;
	server_name	localhost;

	location / {
		proxy_pass	<http://trilium/>;
		index index.html index.htm;
	}
}

#HTTPS Server
server {
	listen	443 ssl;
	server_name	localhost;

	ssl_certificate	/your_path/ssl/example.crt;
	ssl_certificate_key	/your_path/ssl/example.key;
	ssl_session_cache	shared:SSL:1m;

	ssl_protocols	TLSv1 TLSv1.1 TLSv1.2;
	ssl_ciphers	ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
	ssl_prefer_server_ciphers	on;

	location / {
		proxy_pass	<http://trilium/>;
		index index.html index.htm;
	}
}
```
### （三）配置负载均衡
nginx默认采用轮询的方式进行负载均衡
#### 1、使用加权轮询
```bash
upstream [proxyName] {
server 192.168.1.173:8080 weight=1;
server 192.168.1.174:8080 weight=5;
server 192.168.1.175:8080 weight=2;
}
```
#### 2、hash负载均衡
```bash
upstream [proxyName] {
ip_hash

server 192.168.1.173:8080;
server 192.168.1.174:8080;
server 192.168.1.175:8080;
}
```
hash实际上只会计算192.168.1这段做哈希
使用ip_hash的注意点：

- 不能把后台服务器直接移除，只能标记down.
#### 3、url hash负载均衡
```bash
upstream [proxyName] {
hash $request_url;

server 192.168.1.173:8080;
server 192.168.1.174:8080;
server 192.168.1.175:8080;
}
```
#### 4、最小链接负载均衡
```bash
upstream [proxyName] {
least_conn;

server 192.168.1.173:8080;
server 192.168.1.174:8080;
server 192.168.1.175:8080;
}
```
#### 5、Nginx动静分离
```bash
upstream web {
server  192.168.36.1 weight=1 max_fails=2  fail_timeout=2;
server  192.168.36.2 weight=1 max_fails=2  fail_timeout=2;
}

upstream image  {
server  192.168.36.3 weight=1 max_fails=2  fail_timeout=2;
server  192.168.36.4 weight=1 max_fails=2  fail_timeout=2;
}

upstream php {
server  192.168.36.5 weight=1 max_fails=2  fail_timeout=2;
server  192.168.36.6 weight=1 max_fails=2  fail_timeout=2;
}

location  /{
root html/web;
index  index.php index.html;
}

location ~* \\\\.php$ {
fastcgi_proxy  <http://php>;
}

location ~* "\\\\.(.jpg|png|jpeg|gif)" {
proxy_pass <http://image>;
}
```
#### 6、Nginx Tcp负载均衡
Nginx在1.9.0版本开始支持tcp模式的负载均衡，在1.9.13版本开始支持udp协议的负载，udp主要用于DNS的域名解析，其配置方式和指令和http 代理类似，其基于ngx_stream_proxy_module模块实现tcp负载，另外基于模块ngx_stream_upstream_module实现后端服务器分组转发、权重分配、状态监测、调度算法等高级功能。
**Tcp负载均衡配置参数**
```bash
stream { #定义stream
upstream backend { #定义后端服务器
hash $remote_addr consistent; #定义调度算法
server backend1.example.com:12345 weight=5; #定义具体server
server 127.0.0.1:12345 max_fails=3 fail_timeout=30s;
server unix:/tmp/backend3;
}
upstream dns { #定义后端服务器
server 192.168.0.1:53535; #定义具体server
server dns.example.com:53;
}
server { #定义server
listen 12345; #监听IP:PORT
proxy_connect_timeout 1s; #连接超时时间
proxy_timeout 3s; #转发超时时间
proxy_pass backend; #转发到具体服务器组
}
server {
listen 127.0.0.1:53 udp reuseport;
proxy_timeout 20s;
proxy_pass dns;
}
server {
listen [::1]:12345;
proxy_pass unix:/tmp/stream.socket;
}
}
```
#### 7、基于Redis的负载均衡实例
**安装并配置Redis服务**
```bash
[root@CentOS7-1 ~]#yum install -y redis
[root@CentOS7-1 ~]#vim /etc/redis.conf
[root@CentOS7-1 ~]#egrep "^bind" /etc/redis.conf
bind 0.0.0.0
[root@CentOS7-1 ~]#systemctl start redis
[root@CentOS7-1 ~]#systemctl enable redis
Created symlink from /etc/systemd/system/multi-user.target.wants/redis.service to /usr/lib/systemd/system/redis.service.
[root@CentOS7-1 ~]#ss -ntl | grep 6379   # Redis基于6379端口进行工作
LISTEN     0      128          *:6379                     *:*
```
**Nginx配置**
```bash
[root@CentOS7 ~]#mkdir /apps/nginx/tcp
[root@CentOS7 ~]#cd /apps/nginx/tcp/
[root@CentOS7 tcp]#vim tcp.conf
stream {
upstream redis_server {
server 192.168.36.110:6379 max_fails=3 fail_timeout=30s;
}
server {
listen 192.168.36.104:6379;
proxy_connect_timeout 3s;
proxy_timeout 3s;
proxy_pass redis_server;
}
}

[root@CentOS7 tcp]#vim ../conf/nginx.conf
include /apps/nginx/tcp/tcp.conf;   # 注意此处的include与http模块平级，建议写在http模块上方

[root@CentOS7 tcp]#nginx -t
nginx: the configuration file /apps/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /apps/nginx/conf/nginx.conf test is successful
[root@CentOS7 tcp]#nginx -s reload

# 查看6379端口是否开启
[root@CentOS7 tcp]#ss -ntl | grep 6379
LISTEN     0      128    192.168.36.104:6379                     *:*

# 测试通过Nginx负载连接Redis
[root@CentOS7-1 ~]#redis-cli -h 192.168.36.104
192.168.36.104:6379> set name darius
OK
192.168.36.104:6379> get name
"darius"
192.168.36.104:6379>
```
#### 8、基于Mysql的负载均衡实例
**服务器安装Mariadb**
```bash
[root@CentOS7-1 ~]#yum install -y mariadb mariadb-server
[root@CentOS7-1 ~]#systemctl start mariadb   # 启动mariadb数据库服务
[root@CentOS7-1 ~]#systemctl enable mariadb   # 开机自启动数据库服务
[root@CentOS7-1 ~]#ss -ntl | grep 3306   # 检查端口是否启动
LISTEN     0      50           *:3306                     *:*

Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to /usr/lib/systemd/system/mariadb.service.
[root@CentOS7-1 ~]#mysql_secure_installation   # 对数据库进行安全加固

# 对数据库进行授权操作
MariaDB [(none)]> GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.36.%' IDENTIFIED BY 'centos';
Query OK, 0 rows affected (0.00 sec)
MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)
```
**Nginx配置**
```bash
[root@CentOS7 tcp]#vim tcp.conf
stream {
upstream mysql_server {
least_conn;
server 192.168.36.110:3306 max_fails=3 fail_timeout=30s;
}
server {
listen 192.168.36.104:3306;
proxy_connect_timeout 3s;
proxy_timeout 3s;
proxy_pass mysql_server;
}
}
[root@CentOS7 tcp]#nginx -t
nginx: the configuration file /apps/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /apps/nginx/conf/nginx.conf test is successful
[root@CentOS7 tcp]#nginx -s reload

# 对负载端口进行检查
[root@CentOS7 tcp]#ss -ntl | grep 3306
LISTEN     0      128    192.168.36.104:3306                     *:*
```
**测试通过nginx负载连接Mysql**
```bash
[root@CentOS7-1 ~]#mysql -uroot -pcentos -h 192.168.36.104
Welcome to the MariaDB monitor.  Commands end with ; or \\\\g.
Your MariaDB connection id is 16
Server version: 5.5.60-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\\\\h' for help. Type '\\\\c' to clear the current input statement.

MariaDB [(none)]> CREATE DATABASE Darius;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| Darius             |
| mysql              |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.00 sec)

MariaDB [(none)]>
```
### （四）upstream指令参数

- max_conns：限制最大同时连接数1.11.5之前只能用于商业版
- slow_start：单位秒，权重在指定时间内从1上升到指定值，不适用于hash负载均衡、随机负载均衡，如果在upstream中只有一台server，则该参数失效（商业版才有）
- down：禁止访问
- backup：备用机， 只有在其他服务器无法访问的时候才能访问到，不适用于hash负载均衡、随机负载均衡
- max_fails：表示失败几次，则标记server已宕机，剔除上游服务，默认值1
- fail_timeout：表示失败的重试时间，默认值10
#### 1、keepalived
```bash
upstream [proxyName] {
server 192.168.1.173:8080 weight=1;
server 192.168.1.174:8080 weight=5;
server 192.168.1.175:8080 weight=2;

keepalive 32;  #保持的连接数
}

server {
listen 80;
server_name www.tomcats.com;

location / {
proxy_pass <http://tomcats>;
proxy_http_version 1.1;   #连接的协议版本
proxy_set_header Connection "";   #清空连接请求头
}
}
```
#### 2、控制浏览器缓存
```bash
server {
listen 80;
server_name www.tomcats.com;

location / {
proxy_pass <http://tomcats>;
expires 10s;  #浏览器缓存10秒钟
#expires @22h30m  #在晚上10点30的时候过期
#expires -1h  #缓存在一小时前时效
#expires epoch  #不设置缓存
#expires off  #缓存关闭，浏览器自己控制缓存
#expires max  #最大过期时间
｝
｝
```
#### 3、反向代理缓存
```bash
upstream [proxyName] {
server 192.168.1.173:8080 weight=1;
server 192.168.1.174:8080 weight=5;
server 192.168.1.175:8080 weight=2;
}

#proxy_cache_path  #设置缓存保存的目录的位置
#keys_zone	#设置共享内以及占用的空间大小
#max_size 	#设置缓存最大空间
#inactive 	#缓存过期时间，错过此时间自动清理
#use_temp_path 	#关闭临时目录

proxy_cache_path /usr/local/nginx/upsteam_cache
keys_zone=mycache:5m
max_size=1g
inactive=8h
use_temp_path=off;

server {
listen 80;
server_name www.tomcats.com;

#开启并使用缓存
proxy_cache mycache;

#针对200和304响应码的缓存过期时间
proxy_cache)valid 200 304 8h;

location / {
proxy_pass <http://tomcats>;
}
}
```
### （五）配置ssl证书提供https访问
#### 1、安装ssl模块
要在nginx中配置https，就必须安装ssl模块，也就是：http_ssl_module。
进入到nginx的解压目录：/home/software/nginx-1.16.1
新增ssl模块（原来的那些模块需要保留）
```bash
./configure \\\\
--prefix=/usr/local/nginx \\\\
--pid-path=/var/run/nginx/nginx.pid \\\\
--lock-path=/var/lock/nginx.lock \\\\
--error-log-path=/var/log/nginx/error.log \\\\
--http-log-path=/var/log/nginx/access.log \\\\
--with-http_gzip_static_module \\\\
--http-client-body-temp-path=/var/temp/nginx/client \\\\
--http-proxy-temp-path=/var/temp/nginx/proxy \\\\
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \\\\
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \\\\
--http-scgi-temp-path=/var/temp/nginx/scgi  \\\\
--with-http_ssl_module
```
编译和安装
```bash
make
make install
```
#### 2、配置HTTPS
把ssl证书 *.crt 和私钥 *.key 拷贝到 /usr/local/nginx/conf 目录中。
新增server监听443端口：
```bash
server {
listen       443 ssl;
server_name  www.imoocdsp.com;
# 配置ssl证书
ssl_certificate      1_www.imoocdsp.com_bundle.crt;
# 配置证书秘钥
ssl_certificate_key  2_www.imoocdsp.com.key;
# ssl会话cache
ssl_session_cache    shared:SSL:1m;
# ssl会话超时时间
ssl_session_timeout  5m;
# 配置加密套件，写法遵循 openssl 标准
ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
ssl_prefer_server_ciphers on;

error_log logs/www_darius_com_error.log;
access_log logs/www_darius_com_access.log;

location / {
proxy_pass <http://tomcats/>;
index  index.html index.htm;
}
}
```
### （六）配置ha nginx
#### 1、安装keepalived
（1）下载：https://www.keepalived.org/download.html
（2）解压：tar -zxvf keepalived-2.0.18.tar.gz
（3）使用configure命令配置安装目录与核心配置文件所在位置：
./configure --prefix=/usr/local/keepalived --sysconf=/etc

- **prefix**：keepalived安装的位置sysconf：keepalived核心配置文件所在位置，固定位置，改成其他位置则keepalived启动不了，/var/log/messages中会报错
- **sysconf**：keepalived核心配置文件所在位置，固定位置，改成其他位置则keepalived启动不了，/var/log/messages中会报错

配置过程中可能会出现警告信息，如下所示：
```bash
*** WARNING - this build will not support IPVS with IPv6. Please install libnl/libnl-3 dev libraries to support IPv6 with IPVS.

# 安装libnl/libnl-3依赖
yum -y install libnl libnl-devel
```
（4）安装keepalived
```bash
make && make install
```
（5）配置文件在 /etc/keepalived/keepalived.conf
（6）忘记安装配置的目录，则通过如下命令找到：whereis keepalived
（7）启动keepalived
进入sbin目录 ./keepalived
#### 2、配置keepalived 主机
（1）通过命令 vim keepalived.conf 打开配置文件
```bash
global_defs {
# 路由id：当前安装keepalived的节点主机标识符，保证全局唯一
router_id keep_171
}

vrrp_instance VI_1 {
# 表示状态是MASTER主机还是备用机BACKUP
state MASTER
# 该实例绑定的网卡
interface ens33
# 保证主备节点一致即可
virtual_router_id 51
# 权重，master权重一般高于backup，如果有多个，那就是选举，谁的权重高，谁就当选
priority 100
# 主备之间同步检查时间间隔，单位秒
advert_int 2
# 认证权限密码，防止非法节点进入
authentication {
auth_type PASS
auth_pass 1111
}
# 虚拟出来的ip，可以有多个（vip）
virtual_ipaddress {
192.168.1.161
}
}
```
附：查看网卡信息命令 ip addr
（2）启动keepalived
（3）查看进程 ps -ef | grep keepalived
（4）查看vip（虚拟ip）
在网卡ens33下，多了一个 192.168.1.161 , 这个就是虚拟ip
#### 3、把keepalived注册为系统服务
1）拷贝配置文件

- 将keepalived目录下 etc/init.d/keepalived 拷贝到 /etc/init.d/ 下
- 将keepalived目录下etc/init.d/keepalived 拷贝到 /etc/sysconfig/ 下

（2）刷新systemctl  systemctl daemon-reload
（3）启动、停止、重启keepalived
```bash
#启动
systemctl start keepalived.service
#停止
systemctl stop keepalived.service
#重启
systemctl restart keepalived.service
```
#### 4、实现双机主备高可用
（1）修改备机配置
```bash
global_defs {
router_id keep_172
}
vrrp_instance VI_1 {
# 备用机设置为BACKUP
state BACKUP
interface ens33
virtual_router_id 51
# 权重低于MASTER
priority 80
advert_int 2
authentication {
auth_type PASS auth_pass 1111
}
virtual_ipaddress {
# 注意：主备两台的vip都是一样的，绑定到同一个vip
192.168.1.161
}
}
```
(2) 启动 Keepalived
(3) 访问vip即可访问主机，当主机失效时访问vip就会访问到备机
#### 5、keepalived配置nginx自动重启
(1)编写脚本
在 /etc/keepalived/ 下创建脚本 check_nginx_alive_or_not
```bash
#!/bin/bash

A=`ps -C nginx --no-header |wc -l`
# 判断nginx是否宕机，如果宕机了，尝试重启
if [ $A -eq 0 ];then
/usr/local/nginx/sbin/nginx
# 等待一小会再次检查nginx，如果没有启动成功，则停止keepalived，使其启动备用机
sleep 3
if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then
killall keepalived
fi
fi
```
(2)添加运行权限
```bash
chmod +x /etc/keepalived/check_nginx_alive_or_not.sh
```
(3)配置keepalived监听nginx脚本
```bash
vrrp_script check_nginx_alive {
script "/etc/keepalived/check_nginx_alive_or_not.sh"
interval 2 # 每隔两秒运行上一行脚本
weight 10 # 如果脚本运行失败，则升级权重+10
}
```
(4)重启Keepalived使得配置文件生效 systemctl restart keepalived
#### 6、keepalived双主热备
(1)配置DNS轮询
在同一个域名下配置两个ip，自行百度
(2)配置第一台主机
```bash
global_defs {
router_id keep_171
}
vrrp_instance VI_1 {
state MASTER i
nterface ens33
virtual_router_id 51
priority 100
advert_int 1
authentication {
auth_type PASS
auth_pass 1111
}
virtual_ipaddress {
192.168.1.161
}
}

vrrp_instance VI_2  {
state BACKUP
interface ens33
virtual_router_id 52
priority 80
advert_int 1
authentication {
auth_type PASS
auth_pass 1111
}
virtual_ipaddress {
192.168.1.162
}
}
```
(3)配置第二台主机
```bash
global_defs {
router_id keep_172
}
vrrp_instance VI_1 {
state BACKUP
interface ens33
virtual_router_id 51
priority 80
advert_int 1
authentication {
auth_type PASS
auth_pass 1111
}
virtual_ipaddress {
192.168.1.161
}
}

vrrp_instance VI_2 {
state MASTER
interface ens33
virtual_router_id 52
priority 100
advert_int 1
authentication {
auth_type PASS
auth_pass 1111
}
virtual_ipaddress {
192.168.1.162
}
}
```
(4)重启两台Keepalived systemctl restart keepalived
### （七）LVS(Linux Virtual Server)实现高可用负载均衡
#### 1、为什么要使用LVS+Nginx

- lvs基于四层负载均衡，工作效率较Nginx的七层负载更高，使用LVS搭建Nginx集群，可以提高性能
- 四层负载均衡无法对信息处理，只能通过ip+端口的形式转发，所以需要七成负载进行数据的处理
- Nginx接收请求来回，LVS可以只接受不响应
#### 2、LVS的三种模式
**(1)NAT模式**

- 客户端将请求发往LVS，LVS会选择一台服务器响应请求，服务器将结果返回给LVS，LVS再返回给客户端。
- 在NAT模式中，服务器的网关必须指向LVS，否则报文无法送达客户端
- NAT 技术将请求的报文和响应的报文都需要通过LVS进行地址改写，因此网站访问量比较大的时候负载均衡调度器有比较大的瓶颈，一般要求最多之能 10-20 台节点
- NAT 模式支持对 IP 地址和端口进行转换。即用户请求的端口和真实服务器的端口可以不一致

**(2)TUN模式**

- 客户端将请求发往LVS，LVS会选择一台服务器响应请求，在客户端与服务器之间建立隧道，返回结果的时候直接由服务器返回响应，不在经过LVS。
- TUN模式必须所有的服务器上都绑定VIP的IP地址，所有的服务器都必须有网卡。
- TUN模式走隧道运维难度大，并且会直接暴露服务器地址
- 服务器将应答包直接发给用户。所以，减少了负载均衡器的大量数据流动，负载均衡器不再是系统的瓶颈，就能处理很巨大的请求量，这种方式，一台负载均衡器能够为很多服务器进行分发。而且跑在公网上就能进行不同地域的分发

**(3)DR模式**

- 客户端将请求发往LVS，LVS会选择一台服务器响应请求，返回结果的时候通过统一的路由进行返回，不在经过LVS。
- 和TUN模式一样，LVS只是分发请求，应答包通过单独的路由返回给客户端，与TUN相比这种方式不需要隧道结构，可以兼容大多数的操作系统，同时统一路由可以隐藏真实的物理服务器。DR模式效率更高，但配置更复杂.
- 所有服务器节点和LVS只能在一个局域网里面。
#### 3、搭建LVS-DR模式
先关闭掉服务器上网络配置管理器，避免网络接口冲突
```bash
systemctl stop NetworkManager
systemctl disable NetworkManager
```
(1)创建子接口（创建LVS的虚拟ip）
进入网卡配置目录/etc/sysconfig/network-scripts/,找到网卡配置文件，这里以ifcfg-ens33为例，拷贝并创建子接口
```bash
cp ifcfg-ens33 ifcfg-ens33:1
```
修改子接口配置如下

- 配置中的 192.168.1.150 就是vip，是提供给外网用户访问的ip地址
```bash
DEVICE="ens33:1"
ONBOOT="yes"
IPADDR=192.168.1.150
NETMASK=255.255.255.0
BOOTPROTO=static
```

- 重启网络服务 service network restart

重启成功后，ip addr 查看一下，你会发现多了一个ip，也就是虚拟ip（vip）
注意：阿里云不支持配置网卡，需要购买相应的负载均衡服务，腾讯云支持配置网卡，但需要购买网卡支持，一个网卡支持10个虚拟ip配置
(2)安装ipvsadm
如今的centos都集成了LVS，所以ipvs是自带的，我们只需要安装ipvsadm即可（ipvsadm是管理集群的工具，通过ipvs可以管理集群，查看集群等操作）
```bash
yum install ipvsadm
```
(3)配置服务器（RS）的虚拟ip
进入网卡配置目录/etc/sysconfig/network-scripts/,找到ifcfg-lo，拷贝并创建子接口
```bash
cp ifcfg-lo ifcfg-lo:1
```
修改子接口配置如下
```bash
DEVICE="lo:1"
IPADDR=192.168.1.150
NETMASK=255.255.255.255
NETWORK=127.0.0.0
BROADCAST=127.255.255.255
ONBOOT="yes"
NAME=loopback
```
重启网络服务成功后，ip addr 查看一下，你会发现多了一个ip，也就是虚拟ip（vip）
(4)为服务器（RS）配置arp
ARP响应级别与通告行为参数说明
```bash
arp-ignore：ARP响应级别（处理请求）
0：只要本机配置了ip，就能响应请求
1：请求的目标地址到达对应的网络接口，才会响应请求
arp-announce：ARP通告行为（返回响应）
0：本机上任何网络接口都向外通告，所有的网卡都能接受到通告
1：尽可能避免本网卡与不匹配的目标进行通告2：只在本网卡通告
```
打开sysctl.conf:  vim /etc/sysctl.conf
配置所有网卡、默认网卡以及虚拟网卡的arp响应级别和通告行为，分别对应：all，default，lo
```bash
# configration for lvs
net.ipv4.conf.all.arp_ignore = 1
net.ipv4.conf.default.arp_ignore = 1
net.ipv4.conf.lo.arp_ignore = 1

net.ipv4.conf.all.arp_announce = 2
net.ipv4.conf.default.arp_announce = 2
net.ipv4.conf.lo.arp_announce = 2
```
刷新配置文件  sysctl -p
增加一个网关，用于接收数据报文，当有请求到本机后，会交给lo去处理
```bash
route add -host 192.168.1.150 dev lo:1
```
将网关添加至开机启动
```bash
echo "route add -host 192.168.1.150 dev lo:1" >> /etc/rc.local
```
(4)使用ipvsadm配置集群规则
创建LVS节点，用户访问的集群调度者
```bash
ipvsadm -A -t 192.168.1.150:80 -s rr -p 5
```

- A：添加集群
- t：tcp协议ip地址：设定集群的访问
- ip：也就是LVS的虚拟ip
- s：设置负载均衡的算法，
- rr：表示轮询
- p：设置连接持久化的时间,在指定时间内同一个用户的请求会访问到同一个服务器中

创建多台RS真实服务器
```bash
ipvsadm -a -t 192.168.1.150:80 -r 192.168.1.171:80 -g
ipvsadm -a -t 192.168.1.150:80 -r 192.168.1.172:80 -g
```

- a：添加真实服务器
- t：tcp协议
- r：真实服务器的ip地址
- g：设定DR模式

保存到规则库，否则重启失效  ipvsadm -S
检查集群
```bash
#查看集群列表
ipvsadm -Ln
#查看集群状态
ipvsadm -Ln --stats
```
一些其他命令
```bash
# 重启ipvsadm，重启后需要重新配置
service ipvsadm restart
# 查看持久化连接
ipvsadm -Ln --persistent-conn
# 查看连接请求过期时间以及请求源ip和目标ip
ipvsadm -Lnc
# 设置tcp tcpfin udp 的过期时间（一般保持默认）
ipvsadm --set 1 1 1
# 查看过期时间
ipvsadm -Ln --timeout
```
(5)访问虚拟ip，完成LVS搭建
### 附：LVS的负载均衡算法
#### (1)静态算法
静态：根据LVS本身自由的固定的算法分发用户请求。

- 轮询（Round Robin 简写’rr’）：轮询算法假设所有的服务器处理请求的能力都一样的，调度器会把所有的请求平均分配给每个真实服务器。（同Nginx的轮询）
- 加权轮询（Weight Round Robin 简写’wrr’）：安装权重比例分配用户请求。权重越高，被分配到处理的请求越多。（同Nginx的权重）
- 源地址散列（Source Hash 简写’sh’）：同一个用户ip的请求，会由同一个RS来处理。（同Nginx的ip_hash）
- 目标地址散列（Destination Hash 简写’dh’）：根据url的不同，请求到不同的RS。（同Nginx的url_hash）
#### (2)动态算法
动态：会根据流量的不同，或者服务器的压力不同来分配用户请求，这是动态计算的。

- 最小连接数（Least Connections 简写’lc’）：把新的连接请求分配到当前连接数最小的服务器。
- 加权最少连接数（Weight Least Connections 简写’wlc’）：服务器的处理性能用数值来代表，权重越大处理的请求越多。Real Server 有可能会存在性能上的差异，wlc动态获取不同服务器的负载状况，把请求分发到性能好并且比较空闲的服务器。
- 最短期望延迟（Shortest Expected Delay 简写’sed’）：特殊的wlc算法。举例阐述，假设有ABC三台服务器，权重分别为1、2、3 。如果使用wlc算法的话，当一个新请求进来，它可能会分给ABC中的任意一个。使用sed算法后会进行如下运算： 
   - A：（1+1）/1=2
   - B：（1+2）/2=3/2
   - C：（1+3）/3=4/3

最终结果，会把这个请求交给得出运算结果最小的服务器。最少队列调度（Never Queue 简写’nq’）：永不使用队列。如果有Real Server的连接数等于0，则直接把这个请求分配过去，不需要在排队等待运算了（sed运算）。
### （八）搭建Keepalived+Lvs+Nginx高可用集群负载均衡
如果原先服务器上配置了LVS+nginx需要清空ipvsadm中的配置
```bash
ipvsadm -C
```
如果配置了Keepalived+Nginx双主集群也需要去除掉Keepalived中原先的配置，按照的后文进行配置
#### (1)使用keepalived配置Master LVS
在LVS的机器上安装keepalived，安装过程参考上文
(1)修改keepalived的配置
```bash
global_defs {
router_id keep_151
}
vrrp_instance VI_1 {
state MASTER
interface ens33
virtual_router_id 41
priority 100
advert_int 1
authentication {
auth_type PASS
auth_pass 1111
}
virtual_ipaddress {
192.168.1.150
}
}

#配置集群访问的ip+端口，端口和nginx保持一致
virtual_server 192.168.1.150 80{
#健康检查的时间，单位：秒
delay_loop 6
#配置负载均衡的算法，默认的轮询
lb_algo rr
#设置LVS的模式 NAT|TUN|DR
lb-kind DR
#设置会话持久化的时间
persistence_timeout 5
#协议
protocol TCP

#配置负载均衡的真实服务器，也就是nginx节点的具体的ip地址
real_server 192.168.1.171 80{
#轮询权重配比
weight 1
#设置健康检查
TCP_CHECK {
#检查80端口
connect_port 80
#超时时间
connect_timeout 2
#重试次数
nb_get_retry 2
#重试间隔时间
delay_before_retry 3
}
}
real_server 192.168.1.171 80{
weight 1
TCP_CHECK {
connect_port 80
connect_timeout 2
nb_get_retry 2
delay_before_retry 3
}
}
}
```
```bash
global_defs {
router_id keep_151
}
vrrp_instance VI_1 {
state MASTER
interface ens33
virtual_router_id 41
priority 100
advert_int 1
authentication {
auth_type PASS
auth_pass 1111
}
virtual_ipaddress {
192.168.1.150
}
}

#配置集群访问的ip+端口，端口和nginx保持一致
virtual_server 192.168.1.150 80{
#健康检查的时间，单位：秒
delay_loop 6
#配置负载均衡的算法，默认的轮询
lb_algo rr
#设置LVS的模式 NAT|TUN|DR
lb-kind DR
#设置会话持久化的时间
persistence_timeout 5
#协议
protocol TCP

#配置负载均衡的真实服务器，也就是nginx节点的具体的ip地址
real_server 192.168.1.171 80{
#轮询权重配比
weight 1
#设置健康检查
TCP_CHECK {
#检查80端口
connect_port 80
#超时时间
connect_timeout 2
#重试次数
nb_get_retry 2
#重试间隔时间
delay_before_retry 3
}
}
real_server 192.168.1.171 80{
weight 1
TCP_CHECK {
connect_port 80
connect_timeout 2
nb_get_retry 2
delay_before_retry 3
}
}
}
```
(2)启动/重启keepalived
```bash
systemctl restart keepalived
```
#### (2)使用keepalived配置Backup LVS
配置在备用机上
```bash
global_defs {
router_id keep_152
}
vrrp_instance VI_1 {
state  BACKUP
interface ens33
virtual_router_id 41
priority 50
advert_int 1
authentication {
auth_type PASS
auth_pass 1111
}
virtual_ipaddress {
192.168.1.150
}
}

#配置集群访问的ip+端口，端口和nginx保持一致
virtual_server 192.168.1.150 80{
#健康检查的时间，单位：秒
delay_loop 6
#配置负载均衡的算法，默认的轮询
lb_algo rr
#设置LVS的模式 NAT|TUN|DR
lb-kind DR
#设置会话持久化的时间
persistence_timeout 5
#协议
protocol TCP

#配置负载均衡的真实服务器，也就是nginx节点的具体的ip地址
real_server 192.168.1.171 80{
#轮询权重配比
weight 1
#设置健康检查
TCP_CHECK {
#检查80端口
connect_port 80
#超时时间
connect_timeout 2
#重试次数
nb_get_retry 2
#重试间隔时间
delay_before_retry 3
}
}
real_server 192.168.1.171 80{
weight 1
TCP_CHECK {
connect_port 80
connect_timeout 2
nb_get_retry 2
delay_before_retry 3
}
}
}
```
### 其他配置
#### 1、修改Nginx Server版本信息
```bash
# 修改Nginx源码文件，此配置文件需要在nginx.conf的http中添加server_tokens  off;开启nginx版本隐藏才能实现预期效果
[root@CentOS7 nginx-1.14.2]#vim src/http/ngx_http_header_filter_module.c
49 static u_char ngx_http_server_string[] = "Server: Darius/10.0" CRLF;

# 停止Nginx服务，重新编译Nginx
[root@CentOS7 nginx-1.14.2]#/apps/nginx/sbin/nginx -s stop
[root@CentOS7 nginx-1.14.2]#./configure --prefix=/apps/nginx --user=nginx --group=nginx --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_stub_status_module --with-http_gzip_static_module --with-pcre --with-stream --with-stream_ssl_module --with-stream_realip_module --add-module=/root/echo-nginx-module

[root@CentOS7 nginx-1.14.2]#make && make ×××tall

启动服务
[root@CentOS7 nginx-1.14.2]#/apps/nginx/sbin/nginx

检测
[root@CentOS7-Test ~]#curl -I www.darius.com
HTTP/1.1 200 OK
Server: Darius/10.0

# 修改src/core/nginx.h文件无需开启隐藏功能，起到修改版本信息的效果
[root@CentOS7 nginx-1.14.2]# vim src/core/nginx.h
13 #define NGINX_VERSION      "10.0"
14 #define NGINX_VER          "Darius/" NGINX_VERSION
```
#### 2、Rewrite指令
Nginx服务器利用ngx_http_rewrite_module 模块解析和处理rewrite请求，此功能依靠 PCRE(perl compatibler egularexpression)，因此编译之前要安装PCRE库，rewrite是nginx服务器的重要功能之一，用于实现URL的重写，URL的重写是非常有用的功能，比如它可以在我们改变网站结构之后，不需要客户端修改原来的书签，也无需其他网站修改我们的链接，就可以设置为访问，另外还可以在一定程度上提高网站的安全性。
**通过正则表达式的匹配来改变URI，可以同时存在一个或多个指令，按照顺序依次对URI进行匹配，rewrite主要是针对用户请求的URL或者是URI做具体处理**
URI(universal resource identifier)：通用资源标识符，标识一个资源的路径，可以不带协议。 URL(uniform resource location):统一资源定位符，是用于在Internet中描述资源的字符串，是URI的子集，主要包括传输协议(scheme)、主机(IP、端口号或者域名)和资源具体地址(目录和文件名)等三部分，一般格式为 scheme://主机名[:端口号][/资源路径],如：[http://www.a.com:8080/path/file/index.html就是一个URL路径，URL必须带访问协议。](http://www.a.com:8080/path/file/index.html%E5%B0%B1%E6%98%AF%E4%B8%80%E4%B8%AAURL%E8%B7%AF%E5%BE%84%EF%BC%8CURL%E5%BF%85%E9%A1%BB%E5%B8%A6%E8%AE%BF%E9%97%AE%E5%8D%8F%E8%AE%AE%E3%80%82) 每个URL都是一个URI，但是URI不都是URL。 例如：[http://example.org/path/to/resource.txt](http://example.org/path/to/resource.txt) ##URI/URLftp://example.org/resource.txt ##URI/URL /absolute/path/to/resource.txt ##URI
**rewrite 四种flag使用介绍**

1. redirect； 临时重定向，重写完成后以临时重定向方式直接返回重写后生成的新URL给客户端，由客户端重新发起请求；使用相对路径,或者http://或https://开头，状态码：302
2. permanent； 永久重定向，重写完成后以永久重定向方式直接返回重写后生成的新URL给客户端，由客户端重新发起请求，状态码：301
3. last； 重写完成后停止对当前URI在当前location中后续的其它重写操作，而后对新的URL启动新一轮重写检查，不建议在location中使用
4. break； 重写完成后停止对当前URL在当前location中后续的其它重写操作，而后直接跳转至重写规则配置块之后的其它配置；结束循环，建议在location中使用

**注：其中前两种是跳转型的flag，后两种是代理型，跳转型是指有客户端浏览器重新对新地址进行请求，代理型是在WEB服务器内部实现跳转的。**
**rewrite域名永久重定向**
```bash
[root@CentOS7 conf.d]#vim ../conf/nginx.conf
location / {
root   html;
index  index.html index.htm;
rewrite / <http://www.darius.com> permanent;  # 永久重定向301
#rewrite / <http://www.darius.com> redirect;  # 临时重定向302
}

[root@CentOS7 conf.d]#nginx -t
nginx: the configuration file /apps/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /apps/nginx/conf/nginx.conf test is successful
[root@CentOS7 conf.d]#nginx -s reload

重定向检测
[root@CentOS7-Test ~]#curl 192.168.36.104
<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>
[root@CentOS7-Test ~]#curl -L 192.168.36.104
www.darius.com
[root@CentOS7-Test ~]#curl -I 192.168.36.104
HTTP/1.1 301 Moved Permanently
Server: Darius/10.0
Date: Sat, 01 Jun 2019 04:27:42 GMT
Content-Type: text/html
Content-Length: 178
Connection: keep-alive
Location: <http://www.darius.com>
```
**rewrite 临时重定向**
```bash
[root@CentOS7-Test ~]#curl -I 192.168.36.104
HTTP/1.1 302 Moved Temporarily
Server: Darius/10.0
Date: Sat, 01 Jun 2019 04:28:32 GMT
Content-Type: text/html
Content-Length: 154
Connection: keep-alive
Location: <http://www.darius.com>
```
**rewrite之URI重定向**
```bash
location /last {
rewrite ^/last/(.*) /test$1 last;
return 888 "last";
}
location /break {
rewrite ^/break/(.*) /test$1 break;
return 666 "break";
}
location /test {
return 999 "test";
}

[root@CentOS7 conf.d]#nginx -t
nginx: the configuration file /apps/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /apps/nginx/conf/nginx.conf test is successful
[root@CentOS7 conf.d]#nginx -s reload

# break不会跳转到其他location中
[root@CentOS7-Test ~]#curl -L -i <http://www.darius.com/break/index.html>
HTTP/1.1 404 Not Found
Server: Darius/10.0
Date: Sat, 01 Jun 2019 06:12:04 GMT
Content-Type: text/html
Content-Length: 162
Connection: keep-alive
Vary: Accept-Encoding

<html>
<head><title>404 Not Found</title></head>
<body bgcolor="white">
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>

# last会跳转到其他location中继续执行匹配操作
[root@CentOS7-Test ~]#curl -L -i <http://www.darius.com/last/index.html>
HTTP/1.1 999
Server: Darius/10.0
Date: Sat, 01 Jun 2019 06:12:11 GMT
Content-Type: text/html
Content-Length: 4
Connection: keep-alive

test
```
**rewrite实现页面自动跳转https**
```bash
server {
listen 80;
listen 443 ssl;
server_name www.darius.com;
error_log /apps/nginx/logs/www_darius_com_error.log;
access_log /apps/nginx/logs/www_darius_com_access.log access_json;
ssl_certificate /apps/nginx/certs/www.darius.com.crt;
ssl_certificate_key /apps/nginx/certs/www.darius.com.key;
ssl_session_cache shared:sslcache:20m;
ssl_session_timeout 10m;
location / {
root /data/nginx/html/pc;
index index.html;
if ( $scheme = http ){
rewrite (.*) <https://www.darius.com>;
}
}
}
[root@CentOS7 conf.d]#nginx -s reload

访问测试
[root@CentOS7-Test ~]#curl -L -i -k <http://www.darius.com>
HTTP/1.1 302 Moved Temporarily
Server: Darius/10.0
Date: Sat, 01 Jun 2019 06:29:34 GMT
Content-Type: text/html
Content-Length: 154
Connection: keep-alive
Location: <https://www.darius.com>

HTTP/1.1 200 OK
Server: Darius/10.0
Date: Sat, 01 Jun 2019 06:29:37 GMT
Content-Type: text/html
Content-Length: 7
Last-Modified: Thu, 30 May 2019 03:06:03 GMT
Connection: keep-alive
Vary: Accept-Encoding
ETag: "5cef489b-7"
Accept-Ranges: bytes

pc web
```
#### 3、if指令
用于条件匹配判断，并根据条件判断结果选择不同的Nginx配置，可以配置在server或location块中进行配置，Nginx的if语法仅能使用if做单次判断，不支持使用if else或者if elif这样的多重判断
```bash
location /main {
index index.html;
default_type text/html;
if ( $scheme = http ) {
echo "if --> $scheme";
}
}
[root@CentOS7 conf.d]#nginx -t
nginx: the configuration file /apps/nginx/conf/nginx.conf syntaxis ok
nginx: configuration file /apps/nginx/conf/nginx.conf test is successful
[root@CentOS7 conf.d]#nginx -s reload

检测
[root@CentOS7-Test ~]#curl www.darius.com/main
if --> http
```
使用正则表达式对变量进行匹配，匹配成功时if指令认为条件为true，否则认为false，变量与表达式之间使用以下符号链接
```bash
=：#比较变量和字符串是否相等，相等时if指令认为该条件为true，反之为false。
!=: #比较变量和字符串是否不相等，不相等时if指令认为条件为true，反之为false。
~：#表示在匹配过程中区分大小写字符，（可以通过正则表达式匹配），满足匹配条件为真，不满足为假。
~*: #表示在匹配过程中不区分大小写字符，（可以通过正则表达式匹配），满足匹配条件为真，不满足问假。
!~：#区分大小写不匹配，不满足为真，满足为假，不满足为真。
!~*:#为不区分大小写不匹配，满足为假，不满足为真。

-f 和 ! -f:判断请求的文件是否存在和是否不存在
-d 和 ! -d: #判断请求的目录是否存在和是否不存在。
-x 和 ! -x: #判断文件是否可执行和是否不可执行。
-e 和 ! -e: #判断请求的文件或目录是否存在和是否不存在(包括文件，目录，软链接)。

注：如果$变量的值为空字符串或是以0开头的任意字符串，则if指令认为该条件为false，其他条件为true。
```
**判断文件是否存在**
```bash
# 当用户访问到公司网站时，输入一个错误的URL，可以将用户访问的浏览页面重定向到公司官网首页上
location / {
root /data/nginx/html/pc;
index index.html;
if ( !-f $request_filename ){
rewrite (.*) <http://www.darius.com/index.html>;
}
}

浏览测试
[root@CentOS7-Test ~]#curl -L -i <http://www.darius.com/asdfg>
HTTP/1.1 302 Moved Temporarily
Server: Darius/10.0
Date: Sat, 01 Jun 2019 06:56:26 GMT
Content-Type: text/html
Content-Length: 154
Connection: keep-alive
Location: <http://www.darius.com/index.html>

HTTP/1.1 200 OK
Server: Darius/10.0
Date: Sat, 01 Jun 2019 06:56:26 GMT
Content-Type: text/html
Content-Length: 7
Last-Modified: Thu, 30 May 2019 03:06:03 GMT
Connection: keep-alive
Vary: Accept-Encoding
ETag: "5cef489b-7"
Accept-Ranges: bytes

pc web
```
#### 4、set指令
指定key并给其定义一个变量，变量可以调用Nginx内置变量赋值给key，另外set定义格式为set $key $value，及无论是key还是value都要加$符号。
```bash
[root@CentOS7 conf.d]#vim pc.conf
location /set {
root index.html;
default_type text/html;
set $name Darius;
echo $name;
set $my_port $server_port;
echo $my_port;
}

[root@CentOS7 conf.d]#nginx -t
nginx: the configuration file /apps/nginx/conf/nginx.conf syntaxis ok
nginx: configuration file /apps/nginx/conf/nginx.conf test is successful
[root@CentOS7 conf.d]#nginx -s reload

检测
[root@CentOS7-Test ~]#curl www.darius.com/set
Darius
80
```
#### 5、break指令
用于中断当前相同作用域(location)中的其他Nginx配置，与该指令处于同一作用域的Nginx配置中，位于它前面的配置生效，位于后面的指令配置就不再生效了，Nginx服务器在根据配置处理请求的过程中遇到该指令的时候，回到上一层作用域继续向下读取配置，该指令可以在server块和location块以及if块中使用，使用语法如下：
```bash
[root@CentOS7 conf.d]#vim pc.conf
location /set {
root index.html;
default_type text/html;
set $name Darius;
echo $name;
break;
set $my_port $server_port;
echo $my_port;
}

[root@CentOS7 conf.d]#nginx -s reload

检测
[root@CentOS7-Test ~]#curl www.darius.com/set
Darius
```
#### 6、return指令
从nginx版本0.8.2开始支持，return用于完成对请求的处理，并直接向客户端返回响应状态码，比如其可以指定重定向URL(对于特殊重定向状态码，301/302等) 或者是指定提示文本内容(对于特殊状态码403/500等)，处于此指令后的所有配置都将不被执行，return可以在server、if和location块进行配置
```bash
location /main {
index index.html;
default_type text/html;
if ( $scheme = http ) {
return 666 "not allow http"; # 可以是返回给客户端指定的HTTP状态码、也可以是返回给客户端的状态码及响应体内容（可以调用变量）、或者返回给客户端URL地址
# echo "if-----> $scheme";  # return后面的将不再执行
}

[root@CentOS7-Test ~]#curl www.darius.com/main
not allow http
[root@CentOS7-Test ~]#curl -I www.darius.com/main
HTTP/1.1 666
Server: Darius/10.0
Date: Sat, 01 Jun 2019 03:52:37 GMT
Content-Type: text/html
Content-Length: 14
Connection: keep-alive
```
#### 7、rewrite_log指令
设置是否开启记录ngx_http_rewrite_module模块日志记录到error_log日志文件当中，可以配置在http、server、location或if当中，需要日志级别为notice
```bash
[root@CentOS7 conf.d]#vim ../conf/nginx.conf
error_log  logs/error.log  notice;  # 开启错误日志notice级别

[root@CentOS7 conf.d]#vim pc.conf  # 启用rewrite_log指令
location /set {
root index.html;
default_type text/html;
set $name Darius;
echo $name;
rewrite_log on;
break;
set $my_port $server_port;
echo $my_port;
}

[root@CentOS7 conf.d]#nginx -t
nginx: the configuration file /apps/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /apps/nginx/conf/nginx.conf test is successful
[root@CentOS7 conf.d]#nginx -s reload

访问并验证
[root@CentOS7 conf.d]#tail -f /apps/nginx/logs/*.log
==> /apps/nginx/logs/error.log <==
2019/06/01 12:01:46 [warn] 11234#0: *40 using uninitialized "my_port" variable, client: 192.168.36.110, server: www.darius.com, request: "GET /set/aaa HTTP/1.1", host: "www.darius.com"
```
#### 8、Nginx防盗链
防盗链基于客户端携带的referer实现，referer是记录打开一个页面之前记录是从哪个页面跳转过来的标记信息，如果别人只链接了自己网站图片或某个单独的资源，而不是打开了网站的整个页面，这就是盗链，referer就是之前的那个网站域名，正常的referer信息有以下几种：
```bash
none：请求报文首部没有referer首部，比如用户直接在浏览器输入域名访问web网站，就没有referer信息。
blocked：请求报文有referer首部，但无有效值，比如为空。
server_names：referer首部中包含本主机名及即nginx 监听的server_name。
arbitrary_string：自定义指定字符串，但可使用*作通配符。
regular expression：被指定的正则表达式模式匹配到的字符串,要使用~开头，例如：
~.*\\\\.magedu\\\\.com。
```
```bash
[root@CentOS7 conf.d]#cat a.conf
server {
listen 80;
charset utf-8;
server_name www.a.com;
location / {
root /data;
index index.html;
}
}
[root@CentOS7 conf.d]#cat /data/index.html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>盗链页面</title>
</head>
<body>
<a href="<http://www.darius.com>">测试盗链</a>
<img src="<http://www.darius.com/logo.png>">
</body>
</html>
```
**被盗链日志查看**
```bash
[root@CentOS7 conf.d]#tail -f /apps/nginx/logs/*.log
==> /apps/nginx/logs/www_darius_com_access.log <==
{"@timestamp":"2019-06-01T15:21:30+08:00","host":"192.168.36.104","clientip":"192.168.36.1","size":0,"responsetime":0.000,"upstreamtime":"-","upstreamhost":"-","http_host":"www.darius.com","uri":"/logo.png","domain":"www.darius.com","xff":"-","referer":"<http://www.a.com/","tcp_xff":"","http_user_agent":"Mozilla/5.0> (Windows NT 10.0; WOW64; rv:67.0) Gecko/20100101 Firefox/67.0","status":"304"}
```
**开启防盗链机制** 基于访问安全考虑，nginx支持通过ungx_http_referer_module模块检查访问请求的referer信息是否有效实现防盗链功能.
### WebDAV配置
```bash
apt install nginx-full
vim /etc/nginx/sites-enabled/webdav.conf
server {
	listen	80;
  server_name localhost;
  # 设置使用utf-8编码，防止中文文件名乱码
  charset	utf-8;

  # 显示文件列表
  autoindex	on;

  # 默认存放文件的路径
  root	/home/dav;

  # 用户认证方式
  auth_basic	realm_name;
  # 用户密码文件存放位置
  auth_basic_user_file	/etc/nginx/.passwd.list;

  # dav允许的操作
  dav_methods	PUT DELETE MKCOL COPY MOVE;
  dav_ext_methods	PROFIND OPTIONS;

  # 创建文件的默认权限
  dav_access	user:rw group:rw all:r;

  # 临时文件位置
  client_body_temp_path	/tmp;

  # 最大上传文件限制，0表示无限制
  client_max_body_size	0;

  # 允许自动创建文件夹
  create_full_put_path	on;

}

$ echo -n 'sea:' | sudo tee /etc/nginx/.passwords.list
$ openssl passwd -apr1 | sudo tee -a /etc/nginx/.passwords.list
# 确认密码后重启nginx
```
另一种配置：
```bash
user nginx nginx; #用户及用户组
#user  nobody;
worker_processes  1;



events {
   worker_connections  1024;
}


http {
   # 定义的日志格式 
   log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
        '$status $body_bytes_sent "$http_referer" '
        '"$http_user_agent" "$http_x_forwarded_for"';
   
   #DAV文件锁内存共享区
   dav_ext_lock_zone zone=davlock:10m;
   
   #隐藏nginx版本号，避免被坏人利用
   server_tokens off;
   
   
   include       mime.types;
   default_type  application/octet-stream;
   
   sendfile        on;
   
   keepalive_timeout  65;
   
   #gzip  on;
   
   
   #HTTP server
   server {
      listen       13766;
      server_name  localhost;
      rewrite ^(.*)$  https://$host$1? permanent;    # 强制HTTP访问跳转为HTTPS访问

      charset utf-8;
      
      #access_log  logs/host.access.log  main;
      
      location / {
         root   html;
         index  index.html index.htm;
      }
      
      #error_page  404              /404.html;
      
      #
      error_page   500 502 503 504  /50x.html;
      location = /50x.html {
        root   html;
      }
 }


 # HTTPS server
 server {
 
  # 开启https和http2.0提升传输性能
  listen       13767 ssl http2;
  
  server_name  localhost;
  
  # 日志文件路径及日志格式类型
  access_log  /usr/local/nginx_webdav_personal/logs/webdav.access.log  main;

  charset  utf-8;
  
  # 网站证书文件
  ssl_certificate  ssl/nginx.crt;
  
  # 网站证书密钥文件
  ssl_certificate_key  ssl/nginx.key;
  
  # 网站证书密钥密码文件
  # ssl_password_file ssl/xxx;
  
  #开启对来自客户端发送的证书的验证
  #ssl_verify_client on;  # 启用客户端证书认证
  # 客户端证书信任链的CA中间证书或根证书
  #ssl_client_certificate /root/.local/share/mkcert/rootCA.pem; 
  
  
  ssl_dhparam  ssl/dhparam.pem; # DH参数文件
  ssl_ecdh_curve auto; # ECDH椭圆曲线算法为prime256v1
  
  #指定会话凭证密钥文件，用于在多台 Nginx 间实现会话凭证共享
  #否则 Nginx 会随机生成一个会话凭证密钥
  ssl_session_ticket_key  ssl/session_ticket.key;
  ssl_session_tickets on;  # 以会话凭证机制实现会话缓存
  
  # 会话缓存存储大小为10MB
  ssl_session_cache shared:SSL:10m;
  # 会话缓存超时时间为20分钟
  ssl_session_timeout  20m;    
  
  # 最大允许上传的文件大小
  client_max_body_size 20G;
  
  #设置使用的 SSL 协议
  ssl_protocols SSLv2 SSLv3 TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
  
  #设置 HTTPS 建立连接时用于协商使用的加密算法组合，也称密码套件
  #指令值内容为 openssl 的密码套件名称，多个套件名称间用“:”分隔
  ssl_ciphers  EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
  #是否启用在 SSLv3 和 TLSv1 协议的 HTTPS 连接时优先使用服务端设置的密码套件
  # ssl_prefer_server_ciphers on;
 
 
 # 启用HSTS(网站防护)
  add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";

  add_header X-Frame-Options DENY;               # 禁止被嵌入框架
  add_header X-XSS-Protection "1; mode=block";   # XSS跨站防护
  add_header X-Content-Type-Options nosniff;     # 防止在浏览器中的MIME类型混淆攻击


  location / {
   root html;
   index index.html;
  }

  location /dav {
  
   # 数据存放目录
   alias /srv/data1;
   
   index index.html index.htm;
   
   # 开启文件索引列表(看情况选择是否开启)
   autoindex on;
   
   #关闭情况下显示的文件时间为GMT时间，开启显示的是服务器中的时间
   autoindex_localtime on; 
    
   set $dest $http_destination;
    
   # 对目录请求、对URI自动添加"/"
  # if (-d $request_filename) {                   
  #  rewrite ^(.*[^/])$ $1/;
  #  set $dest $dest/;
  # }
   
   #对MOVE|COPY方法强制添加Destination请求头
   if ($request_method ~ (MOVE|COPY)) {
    more_set_input_headers 'Destination: $dest';
   }


   if ($request_method ~ MKCOL) {
    rewrite ^(.*[^/])$ $1/ break;
   }
   
   #webdav 配置
   client_body_temp_path /tmp;
   dav_methods PUT DELETE MKCOL COPY MOVE;  # DAV支持的请求方法
   dav_ext_methods PROPFIND OPTIONS LOCK UNLOCK; # DAV扩展支持的请求方法
   dav_ext_lock zone=davlock;     # DAV扩展锁绑定的内存区域
   create_full_put_path  on;      # 启用创建目录支持
   dav_access user:rw group:rw all:r;   #设置创建的文件及目录的访问权限
  
   #auth_basic "Authorized Users WebDAV";
   auth_basic "user login";
   auth_basic_user_file  dav/.davpasswd; #身份鉴权文件
        }
    }
}

```
