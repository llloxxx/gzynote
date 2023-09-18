## 1、Rsync简介
Rsync是一款开源的数据备份工具，可以用于不同主机之间同步传输数据，可以实现全量备份和增量备份。
**Rsync监听端口：**
```
Rsync工具默认运行在TCP的873端口。
```
**Rsync工作模式：**
Rsync三种工作模式：

1. 本地模式：不常用，类似cp命令；
2. 远程模式：常用，在不同主机之间传输数据；
3. 后台服务模式：常用，用于实时同步数据，安全性高。
## 2、数据备份简介
### 2.1 数据备份方式
从备份方式来说，数据备份有两种备份方式：
1.全量备份
2.增量备份
**全量备份：**
![](https://cdn.nlark.com/yuque/0/2023/png/35951150/1694012391394-c492c75b-c6e7-41d0-ade5-3a00df694ea2.png#averageHue=%23e8c186&clientId=u54edd68f-de4e-4&from=paste&id=u798dc416&originHeight=330&originWidth=573&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u7cc5f926-7494-4ce9-aa40-099cbc82703&title=)
[Full_backup.png](https://images.hanzz.red/hanzz.red_image/image-4b50f96a42dd44449e8f080afe274521.png)
_完全备份，将客户端所有数据内容全部备份至服务端（效率低下，占用空间）。_
**增量备份：**
![](https://cdn.nlark.com/yuque/0/2023/png/35951150/1694012391367-5435f852-681b-46b4-b153-4fac329476ac.png#averageHue=%23e7bf84&clientId=u54edd68f-de4e-4&from=paste&id=u6715f77b&originHeight=341&originWidth=585&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u8276e636-cc97-4ad3-bb9d-01f176c9637&title=)
[Incremental_backup.png](https://images.hanzz.red/hanzz.red_image/image-a389014a6feb4ad68158bdb9ee19b36b.png)
_增量备份，将客户端数据和服务端数据对比，只备份客户端新增加或者改变的数据至服务端（提高备份效率，节省空间，适合异地备份）。_
### 2.2 数据备份架构

1. 数据推送（上传）：所有主机推送本地数据至Rsync备份服务器，会导致数据同步缓慢（适合少量数据备份）。

![](https://cdn.nlark.com/yuque/0/2023/png/35951150/1694012391373-02ef083c-3885-41b0-a715-b04a71c21d79.png#averageHue=%23f8f8f8&clientId=u54edd68f-de4e-4&from=paste&id=u675fee81&originHeight=428&originWidth=575&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u2dc4f64d-be84-41b9-a8c2-267938ab596&title=)
[Data_upload.png](https://images.hanzz.red/hanzz.red_image/image-47c0d4b9a2504f949836965ac329a2dc.png)

1. 数据拉取（下载）：所有主机从Rsync备份服务器，拉取备份数据到本地。

![](https://cdn.nlark.com/yuque/0/2023/png/35951150/1694012391370-4a8bea1c-a9ab-45ac-a551-3b3d36de7c18.png#averageHue=%23f7f7f7&clientId=u54edd68f-de4e-4&from=paste&id=u570d918b&originHeight=418&originWidth=584&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u2372419a-5dd3-4a26-bfd4-91e3488d916&title=)
[Data_download.png](https://images.hanzz.red/hanzz.red_image/image-46b6568d35ff40d5b287ce3feaf193ce.png)

1. 大量服务器数据备份场景。

![](https://cdn.nlark.com/yuque/0/2023/png/35951150/1694012391360-da9dcb85-39f7-47ca-9da7-bbc7be5140a3.png#averageHue=%23f9f9f9&clientId=u54edd68f-de4e-4&from=paste&id=ud51eb7d3&originHeight=597&originWidth=848&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u71934e69-a26c-4c29-a067-d0f89b1a5a5&title=)
[Multi_server_data backup](https://images.hanzz.red/hanzz.red_image/image-8b5f856c97984132b3d1b3d636b29db7.png)

1. 异地备份场景。

![](https://cdn.nlark.com/yuque/0/2023/png/35951150/1694012392004-6728a156-b605-44e3-9997-0ed624c764ca.png#averageHue=%23f8f8f8&clientId=u54edd68f-de4e-4&from=paste&id=uc422b658&originHeight=409&originWidth=805&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u849c650e-870b-4eb6-8c3c-37ea5f06d95&title=)
[Remote_data_backup](https://images.hanzz.red/hanzz.red_image/image-fe8011aefb2f4389ad669437152c0226.png)
## 3、Rsync本地模式和远程模式
### 3.1 安装Rsync工具
安装命令：
```
//环境准备
client服务器：10.0.0.15
server服务器：10.0.0.16
 
//客户端和服务端都需要安装Rsync工具
yum -y install rsync
```
### 3.2 rsync命令选项
```bash
-a	//归档模式传输，等于-tropglD
-v	//详细模式输出，显示速率，文件数量等
-z	//传输时进行压缩，提高效率
-r	//递归传输，传输目录，传输目录时目录名称后加"/"表示传输目录下的所有文件
-t	//保持文件时间信息
-o	//保持文件属主信息
-g	//保持文件属组信息
-p	//保持文件权限
-l	//保留软链接
-D	//保持设备文件信息
-P	//显示同步的过程及传输时的进度等信息
-L	//保留软连接指向的目标文件
--delete		//让目标目录和源目录数据保持一致
--bwlimit		//限速传输
--exclude=PATTERN	//指定排除不需要传输的文件模式
--exclude-from=FILE	//排除FILE中指定模式的文件
```
### 3.3 本地模式用法
```bash
	rsync [OPTION...] SRC... [DEST]
 
示例：
//备份本地文件至其他位置
[root@client ~]# touch file1
[root@client ~]# rsync -avz file1 /tmp/
sending incremental file list
file1
 
sent 83 bytes  received 35 bytes  236.00 bytes/sec
total size is 0  speedup is 0.00
 
[root@client ~]# ls /tmp/
crontab.gjp7fs
file1
ks-script-nc4wzV
systemd-private-a5efc68639ea4045a00fd333479c44be-vgauthd.service-QyLl51
systemd-private-a5efc68639ea4045a00fd333479c44be-vmtoolsd.service-KUA16O
systemd-private-bdfee1d0d79e4d4c83528124b59af5ac-vgauthd.service-yWorJ2
systemd-private-bdfee1d0d79e4d4c83528124b59af5ac-vmtoolsd.service-SBKSbM
yum.log
 
//备份目录时，使目标目录与源目录数据保持一致；若目标目录有多余的文件则会被删除
[root@client ~]# mkdir hzz
[root@client ~]# touch hzz/hzz{1..2}
[root@client ~]# rsync -avz /root/hzz /tmp/
sending incremental file list
hzz/
hzz/hzz1
hzz/hzz2
 
sent 163 bytes  received 58 bytes  442.00 bytes/sec
total size is 0  speedup is 0.00
 
[root@client ~]# ls /tmp/hzz/
hzz1  hzz2
[root@client ~]# touch /tmp/hzz/hzz3
[root@client ~]# ls /tmp/hzz/
hzz1  hzz2  hzz3
 
[root@client ~]# rsync -avz --delete /root/hzz /tmp/
sending incremental file list
deleting hzz/hzz3
 
sent 88 bytes  received 29 bytes  234.00 bytes/sec
total size is 0  speedup is 0.00
[root@client ~]# ls /tmp/hzz/
hzz1  hzz2
```
### 3.4 远程模式用法
```bash
	rsync [OPTION...] [USER@]HOST:SRC... [DEST]
	rsync [OPTION...] SRC... [USER@]HOST:DEST
 
示例：
//将本地文件备份至其他主机
[root@client ~]# rsync -avz file1 root@10.0.0.16:/root/
The authenticity of host '10.0.0.16 (10.0.0.16)' can't be established.
ECDSA key fingerprint is SHA256:rTf+awJf/okAhV+47f4pbkueWtE//bqfsTNJmzaw0Rg.
ECDSA key fingerprint is MD5:7a:3e:15:3a:6f:d3:9f:5c:25:d2:73:ae:a2:4c:0b:da.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.0.0.16' (ECDSA) to the list of known hosts.
root@10.0.0.16's password: 
sending incremental file list
file1
 
sent 83 bytes  received 35 bytes  15.73 bytes/sec
total size is 0  speedup is 0.00
 
[root@server ~]# ls
anaconda-ks.cfg  file1
 
//将其他主机文件备份至本地
[root@client ~]# rm -rf file1 
[root@client ~]# rsync -avz root@10.0.0.16:/root/file1 /root/
root@10.0.0.16's password: 
receiving incremental file list
file1
sent 43 bytes  received 84 bytes  84.67 bytes/sec
total size is 0  speedup is 0.00
[root@client ~]# ls
anaconda-ks.cfg  file1  hzz
//传输时限制传输速度
[root@client ~]# dd if=/dev/zero of=/root/1G.txt bs=1024M count=1
记录了1+0 的读入
记录了1+0 的写出
1073741824字节(1.1 GB)已复制，11.0137 秒，97.5 MB/秒
[root@client ~]# rsync -avzP --bwlimit=10 1G.txt root@10.0.0.16:/root/
root@10.0.0.16's password: 
sending incremental file list
1G.txt
  1,073,741,824 100%   10.66MB/s    0:01:36 (xfr#1, to-chk=0/1)
 
sent 1,044,300 bytes  received 35 bytes  9,993.64 bytes/sec
total size is 1,073,741,824  speedup is 1,028.16
 
[root@server ~]# ll -h
总用量 1.1G
-rw-r--r--  1 root root 1.0G 11月 24 10:30 1G.txt
-rw-------. 1 root root 1.3K 11月 23 04:13 anaconda-ks.cfg
-rw-r--r--  1 root root    0 11月 24 10:24 file1
 
注意事项：
//备份数据时，若不限速会导致带宽被占满
rsync -avzP --bwlimit=10 1G.txt root@10.0.0.16:/root/
 
//备份目录时，目录名称后不加"/"表示备份整个目录包含目录本身
rsync -avz dir root@10.0.0.16:/root/
 
//备份目录时，目录名称后加"/"表示只备份目录中的文件，不备份目录本身
rsync -avz dir/ root@10.0.0.16:/root/
 
//使用"--delete"选项时，请注意千万不要写错目标目录的名称
//下面命令会导致目标系统"/"目录被清空
rsync -avz --delete dir root@10.0.0.16:/ root/
```
## 4、Rsync服务模式
远程模式在传输数据时使用ssh远程登录，使用的是系统用户（不安全）；服务模式以守护进程的方式运行，使用的是虚拟用户，用户不存在操作系统中。
### 4.1 服务端配置

- 确保rsync服务已经安装
- 修改服务配置文件/etc/rsyncd.conf
- 创建用户以及数据存放目录
- 创建虚拟用户密码文件并设置权限
- 关闭防火墙、selinux
- 重启服务并将服务设置开机自启
- 检查服务端口是否开启
```bash
//安装rsync服务
[root@server ~]# yum -y install rsync
 
//编辑服务配置文件/etc/rsyncd.conf
[root@server ~]# vim /etc/rsyncd.conf
uid = rsync				//运行服务的用户
gid = rsync				//运行服务的组
address = 10.0.0.16			//服务监听地址
port = 873				//服务监听端口
fake super = yes			//服务无需使用root用户身份，即可接收文件的完整属性
use chroot = yes			//禁锢数据目录，不允许跳出
max connections = 4			//最大连接数
timeout = 900				//超时时间
ignore errors				//忽略某些错误信息
read only = false			//客户是否只读
list = false				//不允许查看模块信息
auth users = rsync_hzz			//定义虚拟用户，用户数据传输
secrets file = /etc/rsync.passwd	//定义虚拟用户密码认证文件
 
[backup]				//定义模块名称
path = /backup				//数据存放目录
comment = backup			//模块注释信息
 
//创建用户以及数据存放目录
[root@server ~]# useradd -r rsync
[root@server ~]# mkdir /backup
[root@server ~]# chown -R rsync.rsync /backup
 
//创建虚拟用户密码文件并设置权限
[root@server ~]# vim /etc/rsync.passwd
rsync_hzz:passwd
[root@server ~]# chmod 600 /etc/rsync.passwd
 
//关闭防火墙、selinux
[root@server ~]# systemctl status firewalld.service 
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:firewalld(1)
 
[root@server ~]# getenforce 
Disabled
 
//重启服务并设置服务开机自启
[root@server ~]# systemctl restart rsyncd
[root@server ~]# systemctl enable rsyncd
Created symlink from /etc/systemd/system/multi-user.target.wants/rsyncd.service to /usr/lib/systemd/system/rsyncd.service.
 
//检查服务端口是否开启
[root@server ~]# ss -anltup | grep "rsync"
tcp    LISTEN     0      5      10.0.0.16:873                   *:*                   users:(("rsync",pid=31344,fd=4))
```
### 4.2 客户端配置

- 客户端也须安装rsync
- 配置密码文件并设置权限
- 测试收发数据
```bash
//客户端安装rsync
[root@client ~]# yum -y install rsync
 
//配置传输密码
//方法1：将密码写入文件
[root@client ~]# echo 'passwd' > /etc/rsync.pass
[root@client ~]# cat /etc/rsync.pass
passwd
[root@client ~]# chmod 600 /etc/rsync.pass
 
测试收发数据：
[root@client ~]# rsync -avz --password-file=/etc/rsync.pass /root/file1 rsync_hzz@10.0.0.16::backup
sending incremental file list
file1
 
sent 88 bytes  received 43 bytes  262.00 bytes/sec
total size is 0  speedup is 0.00
 
[root@server ~]# ls /backup/
file1
 
//方法2：使用密码环境变量RSYNC_PASSWORD
[root@client ~]# export RSYNC_PASSWORD='passwd'
 
测试收发数据
[root@client ~]# touch file2
[root@client ~]# rsync -avz /root/file2 rsync_hzz@10.0.0.16::backup
sending incremental file list
file2
 
sent 87 bytes  received 43 bytes  260.00 bytes/sec
total size is 0  speedup is 0.00
 
[root@server ~]# ls /backup/
file1 file2
```
## 5、Rsync服务模式实战

1. 客户端每天凌晨3点备份/etc目录至/backup下以"主机名_IP地址_当前时间命名"的目录中
2. 客户端推送/backup目录下数据备份目录至Rsync备份服务器
3. 客户端只保留最近七天的备份数据，避免浪费磁盘空间
4. 服务端部署rsync服务，用于接收用户的备份数据
5. 服务端每天校验客户端推送过来的数据是否完整，并将结果以邮件的方式发送给管理员
6. 服务端仅保留6个月的备份数据

注意：所有服务器的备份目录均为/backup，所有脚本存放目录均为/scripts。
**服务端部署rsync服务：**
```bash
[root@server ~]# mkdir /scripts
[root@server ~]# vim /scripts/rsync_server.sh
#!/bin/bash
#安装rsync服务
yum -y install rsync
#配置rsync服务
cat > /etc/rsyncd.conf << EOF
uid = rsync
gid = rsync
port = 873
fake super = yes
use chroot = yes
max connections = 4
timeout = 900
ignore errors
read only = false
list = false
auth users = rsync_backup
secrets file = /etc/rsync.passwd
[backup]
path = /backup
comment = backup
EOF
#创建服务用户，创建数据备份目录并设置权限
useradd -r rsync
mkdir /backup
chown -R rsync.rsync /backup
#生成数据传输用户密码文件并设置权限
echo "rsync_backup:passwd" > /etc/rsync.passwd
chmod 600 /etc/rsync.passwd
#关闭防火墙和selinux
systemctl stop firewalld
setenforce 0
#设置服务开机自启，并重启服务
systemctl enable rsyncd
systemctl restart rsyncd
 
[root@server ~]# chmod +x /scripts/rsync_server.sh 
 
[root@server ~]# /scripts/rsync_server.sh 
```
**客户端备份数据并推送至rsync服务器：**
```bash
[root@client ~]# mkdir /scripts
[root@client ~]# vim /scripts/etc_backup.sh
#!/bin/bash
#客户端安装rsync
yum -y install rsync
#配置rsync客户端虚拟用户密码
export RSYNC_PASSWORD='passwd'
#创建备份目录
mkdir -p /backup/$(hostname)_$(ip addr show ens36 | awk -F '[ /]+' 'NR==3 {print $3}')_$(date "+%F")
#打包备份/etc目录下的数据至备份目录中
tar -zcf /backup/$(hostname)_$(ip addr show ens36 | awk -F '[ /]+' 'NR==3 {print $3}')_$(date "+%F")/etc_backup.tar.gz /etc/
#计算备份文件的校验值
md5sum /backup/$(hostname)_$(ip addr show ens36 | awk -F '[ /]+' 'NR==3 {print $3}')_$(date "+%F")/etc_backup.tar.gz > /backup/$(hostname)_$(ip addr show ens36 | awk -F '[ /]+' 'NR==3 {print $3}')_$(date "+%F")/checksum.txt
#将打包备份好的数据推送到rsync备份服务器
rsync -az /backup/$(hostname)_$(ip addr show ens36 | awk -F '[ /]+' 'NR==3 {print $3}')_$(date "+%F") rsync_backup@10.0.0.16::backup
#删除七天前的备份文件
find /backup/ -mtime +7 | xargs rm -rf
 
[root@client ~]# chmod +x /scripts/etc_backup.sh
 
[root@client ~]# crontab -e
0 3 * * * /scripts/etc_backup.sh
```
**服务端校验数据并将结果以邮件发送给管理员：**
```bash
//配置邮件服务
[root@server ~]# yum -y install mailx
[root@server ~]# cat > /etc/mail.rc << EOF
set from=2207118234@qq.com
set smtp=smtps://smtp.qq.com:465
set smtp-auth-user=2207118234@qq.com
set smtp-auth-password=xxxxxxxxx
set smtp-auth=login
set ssl-verify=ignore
set nss-config-dir=/etc/pki/nssdb/
EOF
 
//配置脚本校验数据并将结果发送给管理员
[root@server ~]# vim /scripts/checksum.sh
#!/bin/bash
#校验备份数据并将结果发送给管理员
md5sum -c /backup/client_10.0.0.15_$(date "+%F")/checksum.txt | mail -s "/backup/client_10.0.0.15_$(date "+%F")" 2207118234@qq.com
#删除6个月以前的备份数据
find /backup/ -mtime +180 | xargs rm -rf
 
[root@server ~]# chmod +x /scripts/checksum.sh
 
[root@server ~]# crontab -e
0 6 * * * /scripts/checksum.sh
```

