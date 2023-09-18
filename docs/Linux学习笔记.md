# 一、Samba
1.Samba优化配置
修改samba服务配置文件smb.conf，在global节添加以下配置
```bash
read raw = yes #允许samba以64Kb块大小进行读写
write raw = yes
socket options = TCP_NODELAY #不等确认收到就继续发送数据
socket options IPTOS_LOWDELAY #用吞吐量换取低延迟，影响路由器
```
2.samba 安装依赖
```bash
 apt -y install acl apt-utils attr autoconf bind9utils binutils bison build-essential ccache chrpath curl debhelper dnsutils docbook-xml docbook-xsl flex gcc gdb git glusterfs-common gzip heimdal-multidev hostname htop krb5-config krb5-kdc krb5-user lcov libacl1-dev libarchive-dev libattr1-dev libavahi-common-dev libblkid-dev libbsd-dev libcap-dev libcephfs-dev libcups2-dev libdbus-1-dev libglib2.0-dev libgnutls28-dev libgpgme-dev libicu-dev libjansson-dev libjs-jquery libjson-perl libkrb5-dev libldap2-dev liblmdb-dev libncurses5-dev libpam0g-dev libparse-yapp-perl libpcap-dev libpopt-dev libreadline-dev libsystemd-dev libtasn1-bin libtasn1-6-dev libtracker-sparql-2.0-dev libunwind-dev lmdb-utils locales lsb-release make mawk mingw-w64 patch perl perl-modules-5.32 pkg-config procps psmisc python3 python3-cryptography python3-dbg python3-dev python3-dnspython python3-gpg python3-iso8601 python3-markdown python3-matplotlib python3-pexpect python3-pyasn1 python3-setproctitle rng-tools rsync sed sudo tar tree uuid-dev wget xfslibs-dev xsltproc zlib1g-dev  
```

# 二、打印机配置
安装cups
```bash
apt install cups
apt install printer-driver-gutenprint
apt install foomatic-db-engine
```
允许cups远程访问：
```bash
cupsctl --remote-any
```
 允许局域网搜索打印机，配置文件中  
```bash
Browser  Off;改为On
```
# 三、通配符与正则表达式
### 1、常见通配符
| 符号 | 作用 |
| --- | --- |
| * | 匹配任意0或多个字符、字符串 |
| ? | 匹配任意一个字符，有且只有一个字符 |
| 符号合集 | 匹配一堆字符或文本 |
| [abcd] | 匹配abcd中任意一个字符，abcd也可以是不连续任意字符 |
| [a-z] | 匹配a到z之间任意一个字符，要求连续字符，也可以连续数字，匹配[1-9] |
| [!abcd] | 不匹配括号中任意一个字符，也可写[!a-d] |
| [^abcd] | 同上，!可以换成^ |


### 2、特殊通配符
| 符号 | 作用 |
| --- | --- |
| [[:upper:]] | 所有大写字母 |
| [[:lower:]] | 所有小写字母 |
| [[:alphar:]] | 所有字母 |
| [[:digit:]] | 所有数字 |
| [[:alnum:]] | 所有的字母和数字 |
| [[:space:]] | 所有空白字符 |
| [:punct:]] | 所有标点符号 |


### 3、特殊引号
| 名称 | 解释 |
| --- | --- |
| 单引号' ' | 所见即所得，强引用，单引号中内容会原样输出 |
| 双引号" " | 弱引用，能够识别各种特殊符号、变量、转义符等，解析后再输出结果 |
| 没有引号 | 一般连续字符串、数字、路径可以省略双引号，遇见特殊字符，空格、变量等，必须加上双引号 |
| 反引号  `` | 常用于引用命令结果，同于$ |


### 4、重定向
文件描述符
在Linux系统中，一切设备都是文件，而每打开一个文件，就有一个代表该文件的文件描述符
程序启动时默认打开三个I/O设备文件：

- 标准输入文件stdin，文件描述符0
- 标准输出文件stdout，文件描述符1
- 标准错误输出文件stderr，文件描述符2
| 符号 | 特殊符号 | 简介 |
| --- | --- | --- |
| 标准输入stdin | 代码为0，配合< 或 << | 数据流从右向左 |
| 标准输出stdout | 代码1，配合 > 或 >> | 数据从左向右 |
| 标准错误输出stderr | 代码2，配合 > 或 >> | 数据从左向右 |
| 重定向符号 | 数据流是箭头方向 |  |
| 标准输入重定向 | 0< 或 < | 数据一般从文件流向处理命令 |
| 追加输入重定向 | 0<< 或 << | 同上 |
| 标准输出重定向 | 1> 或 > | 正常输出重定向给文件，默认覆盖 |
| 标准输出追加重定向 | 1>> 或 >> | 内容追加重定向到文件底部 |
| 标准错误输出重定向 | 2> | 将标准错误内容重定向到文件，默认覆盖 |
| 标准错误输出追加重定向 | 2>> | 追加到文件底部 |

| 符号 | 解释 |
| --- | --- |
| ; | 分号，命令分隔符或是结束符，某些文件中的注释符。例：#pwd;ls   ##执行两条命令 |
| # | 1.文件中注释的内容；2.shell中root身份提示符 |
|  |  |
| $ | 1.$变量，取出变量的值，2.shell中普通用户身份提示符 |
| \\ | 转义符，将特殊含义的字符还原成普通字符 |
| {}[[1]](#fn1) | 1.生成序列，2.引用变量作为变量与普通字符的分割 |


**逻辑操作符**
逻辑符既可以在linux系统中直接用，也可以在Bash脚本中用

1. `&&` 前一个命令成功，再执行下一个命令，如：ls /tmp && cd /home
2. `||` 前一个命令失败了，再执行下一个命令
3. `!` ①在bash中取反；②在vim中强制做某项操作；③历史命令中 `!ls` 找出最近一次以`ls`开头的命令

### **5、正则表达式**

---


1. 用{}快速备份文件: `cp /etc/nginx/nginx.conf{,.bak}` [↩︎](#fnref1) 

# 四、在Linux中使用访问控制列表（ACL）保护文件目录
作为系统管理员，我们的首要任务就是切实保护数据的安全，以免被未授权的人访问。我们都很清楚使用一些大有帮助的Linux命令设置的权限，比如chmod、chown和chgrp等命令，可是这些默认的权限集有一定的局限性，有时可能还满足不了我们的要求。比如说，我们就无法针对同一目录或文件为不同用户设置不同的权限集。因而，访问控制列表(ACL)应运而生。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684059815950-59a80cb0-f130-4f2d-abb5-27f8c5504ac3.png#averageHue=%23899079&clientId=u62c829e8-bcc7-4&from=paste&id=u06eb72e2&originHeight=229&originWidth=327&originalType=url&ratio=1&rotation=0&showTitle=false&size=103430&status=done&style=none&taskId=u84dd9616-7a8f-459f-8ccc-84d4b93baca&title=)
**Linux访问控制列表**
比如说，你有三个用户，分别是“tecmint1”、“tecmint2”和“tecmint3”。每个用户都有一个共同的用户组，比如说“acl”。用户“tecmint1”希望，只有“tecmint2”用户才能读取和访问归“tecmint1”用户所有的文件，其他人都无权访问该文件。
访问控制列表(ACL)让我们可以做到同样这点。这些ACL让我们可以为某个用户、用户组或不在用户组列表中的任何用户的任何组授予权限。
注意：按照红帽产品说明文档，它为ext3文件系统和NFS导出文件系统提供了ACL支持。
如何检查Linux系统中的ACL支持?
在继续下一步之前，你应确保ACL在现有的内核和已挂载的文件系统上得到支持。
## 1. 检查内核是否支持ACL。
运行下面这个命令，检查是否为文件系统提供ACL支持，是否有POSIX_ACL=Y选项(如果出现的是N，而不是Y，那么这意味着内核并不支持ACL，需要重新加以编译)。
```bash
[root@linux ~]# grep -i acl /boot/config*CONFIG_EXT4_FS_POSIX_ACL=y
CONFIG_REISERFS_FS_POSIX_ACL=y
CONFIG_JFS_POSIX_ACL=y
CONFIG_XFS_POSIX_ACL=y
CONFIG_BTRFS_FS_POSIX_ACL=y
CONFIG_FS_POSIX_ACL=y
CONFIG_GENERIC_ACL=y
CONFIG_TMPFS_POSIX_ACL=y
CONFIG_NFS_V3_ACL=y
CONFIG_NFSD_V2_ACL=y
CONFIG_NFSD_V3_ACL=y
CONFIG_NFS_ACL_SUPPORT=m
CONFIG_CIFS_ACL=y
CONFIG_9P_FS_POSIX_ACL=y
```
## 2. 检查所需的程序包。
在开始处理ACL之前，要确保你已安装了所需的程序包。下面是所需的程序包，需要使用yum或apt-get命令加以安装。
```bash
[root@linux ~]# yum install nfs4-acl-tools acl libacl  [on RedHat based systems]
[tecmint@linux ~]$ sudo apt-get install nfs4-acl-tools acl [on Debian based systems]
```
## 3. 检查已挂载的文件系统是否支持ACL。
现在，检查已挂载的文件系统是否使用ACL选项挂载的。我们可以使用“mount”命令来进行同样的检查，如下所示。
```bash
[root@linux ~]# mount  | grep -i root
/dev/mapper/fedora-root on / type ext4 (rw,relatime,data=ordered)
```
但在本例中，它在默认情况下没有显示acl。所以，接下来我们可以再次使用acl选项，重新挂载已挂载的分区。但在继续下一步之前，我们还有另一个选项：确保有没有使用acl选项来挂载，因为对较新系统而言，它可能整合了默认的挂载选项。
```bash
[root@linux ~]# tune2fs -l /dev/mapper/fedora-root | grep acl
Default mount options:    user_xattr acl
```
在上面的输出结果中，你能看到默认的挂载选项已经支持acl。另一个选项是，重新挂载分区，如下所示。
```bash
[root@linux ~]# mount -o remount,acl /
```
下一步，将下面这一项条目添加到/etc/fstab文件，让其具有永久性。
```
/dev/mapper/fedora-root / ext4 defaults,acl 1 1
```
再次重新挂载分区。
```
[root@linux ~]# mount -o remount /
```
## 4. 针对NFS服务器。
在NFS服务器上，如果NFS服务器导出的文件系统支持ACL，ACL又可以被NFS客户机读取，那么客户机系统就能使用ACL。
想禁用NFS共享区上的ACL，你就得在NFS服务器的“/etc/exportfs”文件中添加选项“no_acl”。想在NSF客户端禁用它，那么在挂载期间再次使用“no_acl“选项。
如何在Linux系统中实现ACL支持?
ACL有两种类型：
◦访问ACL：访问ACL用于授予针对任何文件或目录的权限。
◦默认ACL：默认ACL用于授予/设置只针对特定目录的访问控制列表。
访问ACL与默认ACL之间的区别如下：
◦默认ACL只能用在目录级别。
◦在该目录里面创建的任何子目录或文件将从父目录那里继承ACL。另一方面，文件继承默认ACL作为其访问ACL。
◦我们使用“–d”来设置默认ACL，默认ACL是可选的。
在设置默认ACL之前
想为特定的文件或目录设置默认ACL，可以使用“getfacl”命令。在下面这个例子中，getfacl用于为文件夹“Music”获得默认ACL。
```bash
[root@linux ~]# getfacl Music/
# 文件：Music/
# 所有者：root
# 用户组：root
user::rwx
group::r-x
other::r-x
default:user::rwx
default:group::r-x
default:other::rw-
```
**设置默认ACL之后**
想为特定的文件或目录设置默认ACL，使用“setfacl”命令。在下面这个例子中，setfacl命令将为文件夹“Music”设置新的ACL(读取和执行)。
```bash
[root@linux ~]# setfacl -m d:o:rx Music/
[root@linux ~]# getfacl Music/
# 文件：Music/
# 所有者：root
# 用户组：root
user::rwx
group::r-x
other::r-x
default:user::rwx
default:group::r-x
default:other::r-x
```
**如何设置新的ACL**
使用“setfacl”命令用于设置或修改任何文件或目录。比如说，想为用户“tecmint1”授予读取和写入权限。
```bash
# setfacl -m u:tecmint1:rw /tecmint1/example
```
如何查看ACL
使用“getfacl”命令用于查看任何文件或目录的ACL。比如说，想查看“/tecmint1/example”上的ACL，使用下面这个命令。
```bash
# getfacl /tecmint1/example# 文件：tecmint1/example/
# 所有者：tecmint1
# 用户组：tecmint1
user::rwx
user:tecmint1:rwx
user:tecmint2:r--
group::rwx
mask::rwx
other::---
```
**如何删除ACL**
想删除任何文件/目录的ACL，我们可以使用x和b这两个选项，如下所示。
```bash
# setfacl -x ACL file/directory # 只删除文件/目录的指定ACL
```
```bash
# setfacl -b file/directory # 删除文件/目录的所有ACL
```
不妨将ACL实现到下列场景。
两个用户(tecmint1和tecmint2)都有共同的辅助组，名为“acl”。我们将创建归“tecmint1”用户所有的一个目录，并为用户“tecmint2”提供针对该目录的读取和执行权限。
第1步：创建两个用户，并清除两个用户的密码。
```bash
[root@linux ~]# for user in tecmint1 tecmint2> do
> useradd $user
> passwd -d $user
> done
Removing password for user tecmint1.
passwd: Success
Removing password for user tecmint2.
passwd: Success
```
第2步：为辅助组创建用户组和用户。
```bash
[root@linux ~]# groupadd acl
[root@linux ~]# usermod -G acl tecmint1
[root@linux ~]# usermod -G acl tecmint2
```
第3步：创建目录/tecmint，并将所有权改为tecmint1。
```bash
[root@linux ~]# mkdir /tecmint1
[root@linux ~]# chown tecmint1 /tecmint1/[root@linux ~]# ls -ld /tecmint1/
drwxr-xr-x 2 tecmint1 root 4096 Apr 17 14:46 /tecmint1/[root@linux ~]# getfacl /tecmint1
getfacl: Removing leading '/' from absolute path names
# 文件：tecmint1
# 所有者：tecmint1
# 用户组：root
user::rwx
group::r-x
other::r-x
```
第4步：以tecmint1身份登录，在/tecmint文件夹下创建一个目录。
```bash
[tecmint@linux ~]$ su - tecmint1
Last login: Thu Apr 17 14:49:16 IST 2014 on pts/4[tecmint1@linux ~]$ cd /tecmint1/
[tecmint1@linux tecmint1]$ mkdir example[tecmint1@linux tecmint1]$ ll
total 4
drwxrwxr-x 2 tecmint1 tecmint1 4096 Apr 17 14:50 example[tecmint1@linux tecmint1]$ whoami
tecmint1
```
第5步：现在使用“setfacl”设置ACL，那样“tecmint1”用户会拥有所有的rwx(读取、写入和执行)权限，“tecmint2”用户只有“example”文件夹上的读取权限，其他用户没有任何权限。
```bash
$ setfacl -m u:tecmint1:rwx example/
$ setfacl -m u:tecmint2:r-- example/
$ setfacl -m  other:--- example/
$ getfacl example/# 文件：example
# 所有者：tecmint1
# 用户组：tecmint1
user::rwx
user:tecmint1:rwx
user:tecmint2:r--
group::r-x
mask::rwx
other::---
```
第6步：现在以另一用户(即“tecmint2”)的身份在另一个终端上登录，将目录改为“/tecmint1”。现在试着使用“ls”命令，查看内容，然后试着更改目录，看看有什么区别，如下所示。
```bash
[tecmint@linux ~]$ su - tecmint2
Last login: Thu Apr 17 15:03:31 IST 2014 on pts/5[tecmint2@linux ~]$ cd /tecmint1/
[tecmint2@linux tecmint1]$ ls -lR example/
example/:
total 0[tecmint2@linux tecmint1]$ cd example/
-bash: cd: example/: Permission denied[tecmint2@linux tecmint1]$ getfacl example/
# 文件：example
# 所有者：tecmint1
# 用户组：tecmint1
user::rwx
user:tecmint1:rwx
user:tecmint2:r--
group::rwx
mask::rwx
other::---
```
第7步：现在为“tecmint2”用户授予针对“example”文件夹的“执行”权限，然后使用“cd”命令看看有什么效果。现在，“tecmint2”用户拥有查看和更改目录的权限，但没有写入任何内容的权限。
```bash
[tecmint1@linux tecmint1]$ setfacl -m u:tecmint2:r-x example/
[tecmint1@linux tecmint1]$ getfacl example/# 文件：example
# 所有者：tecmint1
# 用户组：tecmint1
user::rwx
user:tecmint1:rwx
user:tecmint2:r-x
group::rwx
mask::rwx
other::---[tecmint@linux ~]$ su - tecmint2
Last login: Thu Apr 17 15:09:49 IST 2014 on pts/5[tecmint2@linux ~]$ cd /tecmint1/
[tecmint2@linux tecmint1]$ cd example/
[tecmint2@linux example]$ getfacl .[tecmint2@linux example]$ mkdir test
mkdir: cannot create directory 'test': Permission denied[tecmint2@linux example]$ touch test
touch: cannot touch 'test': Permission denied
```
注意：实现ACL后，你会看到“ls –l”输出有一个额外的“+”符号，如下所示。
```bash
[root@linux tecmint1]# lltotal 4
drwxrwx---+ 2 tecmint1 tecmint1 4096 Apr 17 17:01 example
```
参考链接：
ACL的说明文档：
[https://access.redhat.com/site/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Storage_Administration_Guide/ch-acls.html](https://access.redhat.com/site/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Storage_Administration_Guide/ch-acls.html)
原文地址：[http://www.tecmint.com/secure-files-using-acls-in-linux/](http://www.tecmint.com/secure-files-using-acls-in-linux/)

---

## Secure Files/Directories using ACLs (Access Control Lists) in Linux
As a **System Admin**, our first priority will be to protect and secure data from unauthorized access. We all are aware of the permissions that we set using some helpful Linux commands like **chmod**, **chown**, **chgrp**… etc. However, these default permission sets have some limitation and sometimes may not work as per our needs. For example, we cannot set up different permission sets for different users on same directory or file. Thus, **Access Control Lists** (**ACLs**) were implemented.
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684059858777-9abfaec6-3782-4d33-bb1a-54330e14772f.png#averageHue=%2317362a&clientId=u62c829e8-bcc7-4&from=paste&id=uf9a86388&originHeight=321&originWidth=435&originalType=url&ratio=1&rotation=0&showTitle=false&size=37357&status=done&style=none&taskId=ud6603596-5213-425c-9643-fce5c19e17f&title=)
Linux Access Control Lists
Let’s say, you have three users, ‘**tecmint1**‘, ‘**tecmint2**‘ and ‘**tecmint3**‘. Each having common group say ‘**acl’**. User ‘**tecmint1**‘ want that only ‘**tecmint2**‘ user can **read** and **access** files owned by ‘**tecmint1**‘ and no one else should have any access on that.
**ACL**s (**Access Control Lists**) allows us doing the same trick. These ACLs allow us to grant permissions for a **user**, **group** and any group of any users which are not in the group list of a user.
**Note**: As per Redhat Product Documentation, it provides ACL support for ext3 file system and NFS exported file systems.
### How to Check ACL Support in Linux Systems
Before moving ahead you should have support for ACLs on current Kernel and mounted file systems.
### 1. Check Kernel for ACL Support
Run the following command to check ACL Support for file system and **POSIX_ACL=Y** option (if there is **N** instead of **Y**, then it means Kernel doesn’t support ACL and need to be recompiled).
```
[root@linux ~]# grep -i acl /boot/config*

CONFIG_**EXT4_FS_POSIX_ACL=y**
CONFIG_**REISERFS_FS_POSIX_ACL=y**
CONFIG_JFS_POSIX_ACL=y
CONFIG_XFS_POSIX_ACL=y
CONFIG_BTRFS_FS_POSIX_ACL=y
CONFIG_FS_POSIX_ACL=y
CONFIG_GENERIC_ACL=y
CONFIG_TMPFS_POSIX_ACL=y
CONFIG_NFS_V3_ACL=y
CONFIG_NFSD_V2_ACL=y
CONFIG_NFSD_V3_ACL=y
CONFIG_NFS_ACL_SUPPORT=m
CONFIG_CIFS_ACL=y
CONFIG_9P_FS_POSIX_ACL=y
```
### 2. Check Required Packages
Before starting playing with ACLs make sure that you have required packages installed. Below are the required packages that needs to be installed using **yum** or **apt-get**.
```
[root@linux ~]# yum install nfs4-acl-tools acl libacl		[on **RedHat** based systems]

[tecmint@linux ~]$ sudo apt-get install nfs4-acl-tools acl	[on **Debian** based systems]
```
### 3. Check Mounted File System for ACLs Support
Now, check the mounted file system that whether it is mounted with ACL option or not. We can use **‘mount**‘ command for checking the the same as shown below.
```
[root@linux ~]# mount  | grep -i root

/dev/mapper/fedora-root on / type ext4 (rw,relatime,data=ordered)
```
But in our case its not showing acl by default. So, next we have option to remount the mounted partition again using acl option. But, before moving ahead, we have another option to make sure that partition is mounted with acl option or not, because for recent system it may be integrated with default mount option.
```
[root@linux ~]# tune2fs -l /dev/mapper/fedora-root | grep acl

Default mount options:    user_xattr acl
```
In the above output, you can see that default mount option already have support for acl. Another option is to remount the partition as shown below.
```
[root@linux ~]# mount -o remount,acl /
```
Next, add the below entry to ‘/etc/fstab’ file to make it permanent.
```
/dev/mapper/fedora-root /	ext4    defaults,acl 1 1
```
Again, remount the partition.
```
[root@linux ~]# mount -o remount  /
```
### 4. For NFS Server
On NFS server, if file system which is exported by NSF server supports ACL and ACLs can be read by NFS Clients, then ACLs are utilized by client System.
For disabling ACLs on NFS share, you have to add option “**no_acl**” in ‘**/etc/exportfs**‘ file on NFS Server. To disable it on NSF client side again use “**no_acl**” option during mount time.
### How to Implement ACL Support in Linux Systems
There are two types of **ACLs**:

1. **Access ACLs**: Access ACLs are used for granting permissions on any file or directory.
2. **Default ACLs**: Default ACLs are used for granting/setting access control list on a specific directory only.

Difference between Access ACL and Default ACL:

1. Default ACL can be used on directory level only.
2. Any sub directory or file created within that directory will inherit the ACLs from its parent directory. On the other hand a file inherits the default ACLs as its access ACLs.
3. We make use of “**–d**” for setting default ACLs and Default ACLs are optionals.
### Before Setting Default ACLs
To determine the default ACLs for a specific file or directory, use the ‘**getfacl**‘ command. In the example below, the **getfacl** is used to get the default ACLs for a folder ‘**Music**‘.
```
[root@linux ~]# getfacl Music/

# file: Music/
# owner: root
# group: root
user::rwx
group::r-x
other::r-x
default:user::rwx
default:group::r-x
default:other::rw-
```
### After Setting Default ACLs
To set the default ACLs for a specific file or directory, use the ‘**setfacl**‘ command. In the example below, the **setfacl** command will set a new ACLs (**read** and **execute**) on a folder ‘**Music’**.
```
[root@linux ~]# setfacl -m d:o:rx Music/
[root@linux ~]# getfacl Music/
# file: Music/
# owner: root
# group: root
user::rwx
group::r-x
other::r-x
default:user::rwx
default:group::r-x
default:other::r-x
```
### How to Set New ACLs
Use the ‘**setfacl’** command for setting or modifying on any file or directory. For example, to give **read** and **write** permissions to user ‘**tecmint1**‘.
```
# setfacl -m u:tecmint1:rw /tecmint1/example
```
### How to View ACLs
Use the ‘**getfacl**‘ command for viewing ACL on any file or directory. For example, to view ACL on ‘**/tecmint1/example**‘ use below command.
```
# getfacl /tecmint1/example

# file: tecmint1/example/
# owner: tecmint1
# group: tecmint1
user::rwx
user:tecmint1:rwx
user:tecmint2:r--
group::rwx
mask::rwx
other::---
```
### How to Remove ACLs
For removing ACL from any file/directory, we use **x** and **b** options as shown below.
```
# setfacl -x ACL file/directory  	# remove only specified ACL from file/directory.

# setfacl -b  file/directory   		#removing all ACL from file/direcoty
```
Let’s implement ACL’s on following scenario’s.
Two Users (**tecmint1** and **tecmint2**), both having common secondary group named ‘**acl**‘. We will create one directory owned by ‘**tecmint1**‘ and will provide the **read** and **execute** permission on that directory to user ‘**tecmint2**‘.
**Step 1:** Create two users and remove password from both
```
[root@linux ~]# for user in tecmint1 tecmint2

> do
> useradd $user
> passwd -d $user
> done
Removing password for user tecmint1.
passwd: Success
Removing password for user tecmint2.
passwd: Success
```
**Step 2:** Create a Group and Users to Secondary Group.
```
[root@linux ~]# groupadd acl
[root@linux ~]# usermod -G acl tecmint1
[root@linux ~]# usermod -G acl tecmint2
```
**Step 3:** Create a Directory **/tecmint** and change ownership to **tecmint1**.
```
[root@linux ~]# mkdir /tecmint1
[root@linux ~]# chown tecmint1 /tecmint1/
```
```
[root@linux ~]# ls -ld /tecmint1/

drwxr-xr-x 2 tecmint1 root 4096 Apr 17 14:46 /tecmint1/
```
```
[root@linux ~]# getfacl /tecmint1

**getfacl: Removing leading '/' from absolute path names**
# file: tecmint1
# owner: tecmint1
# group: root
user::rwx
group::r-x
other::r-x
```
**Step 4:** Login with **tecmint1** and create a Directory in **/tecmint** folder.
```
[tecmint@linux ~]$ su - tecmint1

Last login: Thu Apr 17 14:49:16 IST 2014 on pts/4
```
```
[tecmint1@linux ~]$ cd /tecmint1/
[tecmint1@linux tecmint1]$ mkdir example
```
```
[tecmint1@linux tecmint1]$ ll

total 4
drwxrwxr-x 2 tecmint1 tecmint1 4096 Apr 17 14:50 example
```
```
[tecmint1@linux tecmint1]$ whoami
tecmint1
```
**Step 5:** Now set ACL using ‘**setfacl**‘, so that ‘**tecmint1**‘ will have all **rwx** permissions, ‘**tecmint2**‘ will have only **read** permission on ‘**example**‘ folder and other will have no permissions.
```
$ setfacl -m u:tecmint1:rwx example/
$ setfacl -m u:tecmint2:r-- example/
$ setfacl -m  other:--- example/
$ getfacl example/

# file: example
# owner: tecmint1
# group: tecmint1
user::rwx
user:tecmint1:rwx
user:tecmint2:r--
group::r-x
mask::rwx
other::---
```
**Step 6:** Now login with other user i.e. ‘**tecmint2**‘ on another terminal and change directory to ‘**/tecmint1**‘. Now try to view the contents using ‘**ls**‘ command and then try to change directory and see the difference as below.
```
[tecmint@linux ~]$ su - tecmint2

Last login: Thu Apr 17 15:03:31 IST 2014 on pts/5
```
```
[tecmint2@linux ~]$ cd /tecmint1/
[tecmint2@linux tecmint1]$ ls -lR example/
example/:
total 0
```
```
[tecmint2@linux tecmint1]$ cd example/

-bash: cd: example/: Permission denied
```
```
[tecmint2@linux tecmint1]$ getfacl example/

# file: example
# owner: tecmint1
# group: tecmint1
user::rwx
user:tecmint1:rwx
user:tecmint2:r--
group::rwx
mask::rwx
other::---
```
**Step 7:** Now give ‘**execute**‘ permission to ‘**tecmint2**‘ on ‘**example**‘ folder and then use **‘cd**‘ command to see the effect. Now ‘**tecmint2**‘ have the permissions to view and change directory, but don’t have permissions for writing anything.
```
[tecmint1@linux tecmint1]$ setfacl -m u:tecmint2:r-x example/
[tecmint1@linux tecmint1]$ getfacl example/

# file: example
# owner: tecmint1
# group: tecmint1
user::rwx
user:tecmint1:rwx
user:tecmint2:r-x
group::rwx
mask::rwx
other::---
```
```
[tecmint@linux ~]$ su - tecmint2

Last login: Thu Apr 17 15:09:49 IST 2014 on pts/5
```
```
[tecmint2@linux ~]$ cd /tecmint1/
[tecmint2@linux tecmint1]$ cd example/
[tecmint2@linux example]$ getfacl .
```
```
[tecmint2@linux example]$ mkdir test

mkdir: cannot create directory ‘test’: Permission denied
```
```
[tecmint2@linux example]$ touch test

touch: cannot touch ‘test’: Permission denied
```
**Note**: After implementing ACL, you will see a extra ‘**+**‘ sign for ‘ls –l’ output as below.
```
[root@linux tecmint1]# ll

total 4
drwxrwx---+ 2 tecmint1 tecmint1 4096 Apr 17 17:01 example
```
# 
# 五、Linux firewalld防火墙的使用配置

防火墙指的是一个由软件和硬件设备组合而成，内部和外部网络的环境间产生一种保护的屏障，是一种获取安全性方法的形象说法，它是一种计算机硬件和软件的结合，从而实现对计算机不安全网络因素的阻断。本文主要介绍Linux firewalld防火墙的使用配置。
### 1、 Linux 防火墙简介
Linux 防火墙是一种位于内部网络与外部网络之间的网络安全系统，是一项信息安全的防护系统，依照特定的规则，允许或是限制传输的数据通过。
防火墙主要通过Netfilter与TCPwrappers两个机制来管理的。 管理防火墙的两种方式：
1）iptables 使用较为复杂，但功能强大。iptables 是交由内核层面的netfilter网络过滤器来处理。
2）firewalld 使用相对简单，firewalld是交由内核层面的nftables包过滤框架来处理。相较于传统的防火墙管理配置工具，firewalld支持动态更新技术并加入了区域（zone）的概念。简单来说，区域就是firewalld预先准备了几套防火墙策略集合（策略模板），用户可以根据生产场景的不同而选择合适的策略集合，从而实现防火墙策略之间的快速切换。
### 2、firewalld常用的区域
firewalld中将过滤规则集合称之为zone。一个zone就是一套过滤规则，数据包必须要经过某个zone才能入站或出站。
firewalld将网卡对应到不同的区域（zone），zone 默认共有9个，分别为block、dmz、drop external、home、internal、public、trusted、work。
不同的区域之间的差异是其对待数据包的默认行为不同，根据区域名字我们可以很直观的知道该区域的特征，在CentOS7系统中，默认区域被设置为public。firewalld 中最上层的组织是区域。如果一个包匹配区域相关联的网络接口或源 IP/掩码 ，它就是区域的一部分。
**常用区域如下表**

| 区域 | 说明 |
| --- | --- |
| trusted (信任区域) | 可接收所有的网络连接 |
| public (公共区域) | 除非与传出流量相关。或与ssh或dhcpv6- client预定义服务匹配,否则拒绝流量传入， 在公共区域内， 不能相信网络内的其他计算机不会对计算机造成危害， 只能接收经过选择的连接。 该区域是新添加网络接口的默认区域。 |
| work (工作区域) | 除非与传出流量相关，或与ssh、 ipelieat、dhepv-client 预定义服务匹配， 否则拒绝 流量传入，用于工作区。 相信网络内的其他计算机不会危害计算机， 仅接收经过选择的连接。 |
| home (家庭区域) | 除非与传出流量相关， 或与ssh、ip-client. mdns、 samba-client、dhepv6-clicnt 预定 义服务匹配，否则拒绝流量传入，用于家庭网络。 信任网络内的其他计算机不会危害 计算机， 仅接收经过选择的连接。 |
| internal (内部区域) | 除非与传出流量相关， 或与ssh、ipelient、mdns、 samba-client. dbepv6-client 预定 义服务匹配，否则拒绝流量传入，用于内部网络。 信任网络内的其他计算机不会危害 计算机， 仅接收经过选择的连接。 |
| extemal (外部区域) | 除非与传出流量相关，或与ssh预定义服务匹配， 否则拒绝流量传入。通过此区域转 发的IPv4传出流量将进行地址伪装， 可用于为路由器启用了伪装功能的外部网络。 |
| dmz (隔离区域也称为非军事区域) | 除非 与传出的流量相关，或与ssh预定义服务匹配， 否则拒绝流量传入 block (限制区城) 除非与传出流量相关，否则拒绝所有传入流量 drop (丢弃区域) 除非与传出流量相关，否则丢弃所有传入流量， 并且不产生包含ICMP(Intermet Control Message Protocol,互联网控制报文协议》的错误响应 |

### 3、firewalld-cmd终端管理工具
可以使用 firewalld-cmd 命令来管理我们的防火墙规则，安装 firewalld 这个软件包，系统就会提供该命令工具。
firewalld-cmd有两种模式，Runtime模式是当前立即生效，重启后失效。Permanent模式是当前不生效，重启后生效。
firewalld配置的防火墙策略默认为运行时（Runtime）模式，又称为当前生效模式，而且会随着系统的重启而失效。如需让配置策略一直存在，可以使用永久（Permanent）模式了，只需用firewall-cmd命令正常设置防火墙策略时添加--permanent参数，此时配置的防火墙策略就可以永久生效。但永久生效模式设置的策略只有在系统重启之后才能自动生效。如让配置的策略立即生效，需要手动执行firewall-cmd --reload命令。 用法如下：
**firewalld-cmd [OPTIONS…] 参数选项：**

| 参数 | 作用 |
| --- | --- |
| --get-default-zone | 查询默认的区域名称 |
| --set-default-zone=<区域名称> | 设置默认的区域，使其永久生效 |
| --get-zones | 显示可用的区域 |
| --get-services | 显示预先定义的服务 |
| --get-active-zones | 显示当前正在使用的区域与网卡名称 |
| --add-source= | 将源自此 IP 或子网的流量导向指定的区域 |
| --remove-source= | 不再将源自此 IP 或子网的流量导向某个指定区域 |
| --add-interface=<网卡名称> | 将源自该网卡的所有流量都导向某个指定区域 |
| --change-interface=<网卡名称> | 将某个网卡与区域进行关联 |
| --list-all | 显示当前区域的网卡配置参数、资源、端口， 以及服务等信息 |
| --add-service=<服务名> | 设置默认区域允许该服务的流量 |
| --add-port=<端口号/协议> | 设置默认区域允许该端口的流量 |
| --remove-service=<服务名> | 设置默认区域不再允许该服务的流量 |
| --remove-port=<端口号/协议> | 设置默认区域不再允许该端口的流量 |
| --reload | 让“永久生效”的配置规则立即生效， 并覆盖当前的配置规则 |
| --panic-on | 开启应急状况模式 |
| --panic-off | 关闭应急状况模式 |

**使用示例：**

1. 查看firewalld服务当前所使用的区域 $ firewall-cmd --get-default-zone public $
2. 查询指定网卡在firewalld服务中绑定的区域 $ firewall-cmd --get-zone-of-interface=ens160 public $
3. firewalld服务的默认区域设置为public
```bash
$ firewall-cmd --set-default-zone=public
Warning: ZONE_ALREADY_SET: public success
```

1. 启动和关闭firewalld防火墙服务的应急状况模式 如果想在1s内或其它情况下阻断一切网络连接，可以使用此模式。该模式会切断一切网络连接，使用时需慎重。
```bash
$ firewall-cmd --panic-on
success
$ firewall-cmd --panic-off
success
```

1. 查询SSH和HTTPS协议的流量是否允许放行
```bash
$ firewall-cmd --zone=public --query-service=ssh yes
$ firewall-cmd --zone=public --query-service=https no
```

1. 将HTTPS协议的流量设置为永久允许放行
```bash
$ firewall-cmd --permanent --zone=public --add-service=https
success
$ firewall-cmd --reload
success
$ firewall-cmd --zone=public --query-service=https yes
$ 注意：执行firewall-cmd --reload命令后设置才会生效
```

1. 将HTTP协议的流量设置为永久拒绝 默认情况下HTTP协议的流量就没有被允许，则会提示“Warning: NOT_ENABLED: http”
```bash
$ firewall-cmd --permanent --zone=public --remove-service=http
Warning: NOT_ENABLED: http success
$ firewall-cmd --reload
success
$ 注意：执行firewall-cmd --reload命令后设置才会生效
```

1. 将运行时配置保存为永久配置
```bash
firewall-cmd --runtime-to-permanent
```
### 4、常用命令
#### 1）基本操作
查看状态 `systemctl status firewalld` 或者 `firewall-cmd --state`
启动 `systemctl start firewalld`
停止 `systemctl stop firewalld`
开机启动 `systemctl enable firewalld`
禁用启动 `systemctl disable firewalld`
#### 2）配置firewalld
查看版本 `firewall-cmd --version`
查看帮助 `firewall-cmd --help`
查看设置 显示状态： `firewall-cmd --state`
查看区域信息: `firewall-cmd --get-active-zones`
查看指定接口所属区域： `firewall-cmd --get-zone-of-interface=eth0`
拒绝所有包 `firewall-cmd --panic-on`
取消拒绝状态 `firewall-cmd --panic-off`
查看是否拒绝 `firewall-cmd --query-panic`
更新防火墙规则 `firewall-cmd --reload firewall-cmd --complete-reload` 注意：firewall-cmd --reload和firewall-cmd --complete-reload区别是第一个无需断开连接，也是firewalld特性之一动态添加规则，第二个需要断开连接，类似重启服务 将接口添加到区域，默认接口都在public。
`firewall-cmd --zone=public --add-interface=eth0` 注意：永久生效需加上 --permanent 然后reload防火墙
设置默认接口区域 `firewall-cmd --set-default-zone=public`
查看所有打开的端口 `firewall-cmd --zone=dmz --list-ports`
添加一个端口到区域 `firewall-cmd --zone=dmz --add-port=8080/tcp` 注意：若要永久生效参考上述方法
添加服务 服务需要在配置文件中添加，/etc/firewalld 目录下有services文件夹 
`firewall-cmd --zone=work --add-service=smtp`
移除服务 `firewall-cmd --zone=work --remove-service=smtp`
例如，
```bash
#拒绝172.27.10.0/22网络用户访问ssh
firewall-cmd --permanent --zone=classroom --add-rich-rule='rule family=ipv4 source address=172.27.10.0/22 service name=ssh reject'
#端口转发 ：将来自172.25.0.0/24子网5432的端口流量转发到本地的80端口
firewall-cmd --permanent --add-rich-rule='rule family=ipv4 source address=172.25.0.0/24 forward-port port=5423 protocol=tcp to-port=80'
#在默认区域中，允许每分钟对ftp有两次新连接,临时生效
firewall-cmd --add-rich-rule='rule service name=ftp limit value=2/m accept'
#丢弃来自默认区域中任何位置的所有传入IPsec esp协议包。
firewall-cmd --permanet --add-rich-rule='rule protocol value=esp drop'
#在192.168.10/24子网中的vnc区域，接受端口7900~7905上的所有TCP包。
firewall-cmd --permanent --zone=vnc --add-rich-rule='family-ipv4 source address=192.168.1.0/24 port prt=7900-7905 protocol=tcp accept'
#允许172.25.1.0/32子网访问http，并记录此流量，但限制每秒3个新连接
firewall-cmd --permanet --add-rich-rle='rule familiy-ipv4 source address=172.25.0.0/32 service name="http" log level=notice prefix="NEW HTTP" limit value="3/s" accept'
```
### 5、端口流量转发
端口流量转发就是把一个端口的数据转发到另外一个端口。可以实现用户通过其他端口号也能访问ssh服务，不需要修改ssh的端口。
```bash
命令写法：
firewall-cmd --permanent --zone=<区域> --add-forward-port=port=<源端口号>:proto=<协议>:toport=<目标端口号>:toaddr=<目标IP地址>
例如，
$ firewall-cmd --permanent --zone=public --add-forward-port=port=28974:proto=tcp:toport=22:toaddr=192.168.10.10
success
$ firewall-cmd --reload
success

```

# 六、Linux目录结构与权限
### **一、目录结构**
`/`    根目录，所有目录的起点
`/boot`  开机所需文件，内核、开机菜单以及所需配置文件等
`/dev`  以文件形式存放任何设备与接口
`/lib`   存放系统库文件，开机时用到的函数库，以及/bin与/sbin下面的命令要调用的函数
`/sbin`    开机过程中需要的命令，存放特权级二进制文件
`/bin`    存放二进制文件，可执行命令
`/usr`    存放安装程序，软件默认安装目录
`/var`    存放经常变化的文件，如日志等
`/mnt`    文件挂载目录
`/home`    普通用户目录
`/etc`    存放配置文件目录
`/opt`    大型软件存放目录非强制，第三方软件
`/root`    特权用户目录
`/media`   用于挂载设备文件的目录
`/srv`   一些网络服务的数据文件目录
`/tmp`   任何人均可使用的“共享”临时目录
`/proc`   虚拟文件系统，例如系统内核、进程、外部设备及网络状态等
`/usr/local`   用户自行安装的软件
`/usr/sbin`   Linux系统开机时不会使用到的软件/命令/脚本
`/usr/share`   帮助与说明文件，也可放置共享文件
`/lost+found`   当文件系统发生错误时，将一些丢失的文件片段存放在这里
#### 文件属性
**ls -lhi**
![[linux_file_info.png]]

- 第1列是Inode号码（Index node索引节点）身份证号码
- 第2列是文件的类型和权限，开头d代表是文件夹，-代表是普通文件，后面共9个字符，每三个为一组，分别为所属用户的权限，所属用户组的权限和其他用户的权限，r=read，w=write，x=执行/execute
| 格式 | 类型 |
| --- | --- |
| - | 普通文件regular  file，二进制、图片、日志、txt等 |
| d | 文件夹directory |
| b | 块设备文件，/dev/sda1，硬盘，光驱 |
| c | 设备文件，终端/dev/tty1，网络串口文件 |
| s | 套接字文件，进程间通信（socket）文件 |
| p | 管道文件pipe |
| l | 链接文件，link类型，快捷方式 |

#### Linux文件常见类型

- 第3列是硬链接的数量
- 第4、5列是所属用户和所属用户组
- 第6列是文件大小
- 第7-9列是文件的修改时间
- 第10列是文件名字

用 `file  文件名`可以查看更详细的文件属性
### **二、权限**
| 权限分配 | 文件所有者 |  |  | 文件所属组 |  |  | 其他用户 |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 权限项 | 读 | 写 | 执行 | 读 | 写 | 执行 | 读 | 写 | 执行 |
| 字符表示 | r | w | x | r | w | x | r | w | x |
| 数字表示 | 4 | 2 | 1 | 4 | 2 | 1 | 4 | 2 | 1 |

因此，在使用chmod更改权限的时候还可以以数字代替字符
例：
`chmod 777 ./test.txt`   ##更改该文件的权限为最高
```bash
chmod参数：

u	文件属主权限
g	组用户权限
o	其他用户权限
a	所有用户，包括以上三种

权限设定
+	增加权限，如：chmod u+rwx test.txt
-	取消权限
=	唯一设定权限

权限类别
r	读
w	写
x	执行
X	表示只有该档案是个子目录或者该档案已经被设定过为可执行
s	文件属主和组id
i	给文件加锁，使其他用户无法访问
```
chown    更改所属用户或所属组
`chown user:group test.txt`

- R 参数：更改目录以及其子目录的所属用户或组
#### umask
umask命令用来限制新文件权限的掩码
也称之为遮罩码，防止文件、文件夹创建的时候，权限过大
当新文件被创建时，其最初的权限由文件创建掩码决定
用户每次注册进入系统时，umask命令都被执行，并自动设置掩码改变默认值，新的权限将会把旧的覆盖
umask默认配置在/etc/profile  61-64行
> umask值就是指Linux文件的默认属性需要减掉的值
>  
> 比如Linux的创建普通文件的最大默认属性是666，目录的最大属性是777

#### chattr
chattr命令用于更改文件的扩展属性，比chmod更改的权限更底层
#### lsattr 查看
```bash
参数：
a：只能向文件中添加数据，不得删除
-R：递归更改目录属性
-V：显示命令执行过程

模式：
+ 增加参数
- 移除参数
= 更新为指定参数
A 不让系统修改文件最后访问时间
a 只能追加文件数据，不得删除
i 文件不能被删除、改名、修改内容

```
# 七、**Linux常用命令**
### 1、cd
```bash
change directory的缩写
更改目录的意思

语法：cd	可选参数	文件夹

几个特殊的目录
. 当前目录
.. 上一级目录
-  上一次的工作目录
~  当前系统登录用户的家目录
```
### 2、ls
```bash
list  列出文件夹中的内容
语法： ls	可选参数	可选文件夹对象
-a	列出所有文件，包括隐藏文件
-l	显示详细信息
-h	以人类可阅读的方式列出信息,需要同-l配合使用
--full-time	以完整的时间格式输出
-t	根据文件最后修改的时间排序
-F  列出文件类型
以/结尾是文件夹
以*结果是可执行文件
以@结尾是软链接
没有符号的是普通文件类型
ls不显示颜色，vim .bashrc
alias ls='ls --color'
```
### 3、pwd
```bash
print work directory列出当前目录路径
```
### 4、su
```bash
su - zhangsan
切换用户到zhangsan
```
### 5、logout: 脱出当前用户
### 6、mkdir
```bash
创建文件夹命令
make directory
语法：mkdir 可选参数 文件夹1 文件夹2 文件夹3
-p 创建递归文件夹，如：mkdir -p /home/dir1/dir11/dir111
同时创建多个文件夹：
mkdir dir{1..100}
```
### 7、绝对相对路径与touch命令
从根目录开始的就是绝对路径，反之则是相对路径
```bash
touch 选项 文件
作用：1.创建普通文件，在linux下文件后缀名不代表格式touch创建的都是普通文件；2.修改文件的时间
不存在的文件将会被创建为空文件，除非使用-c 或 -h 参数。
touch {连续数字或字母｝ 创建多个文件序列
touch {1..10}
touch {a..z}	//例：touch file{a..z}，就创建了filea、fileb ...直到filez
-c, --no-create 不创建任何文件
-t STAMP	使用[[CC]YY]MMDDhhmm[.ss] 格式的时间替代当前时间
-r, --reference=文件	使用指定文件的时间属性替代当前文件时间
```
### 8、cp
```bash
用法：cp 选项 源文件 目标文件
-r 复制文件夹
-p 复制时保持属性不变
-d 复制时保持软链接不变
-i 复制前提示是否覆盖
```
### 9、mv
```bash
move，移动,重命名
mv 源文件 目标文件
-f,--force 覆盖前不提示
-i, --interactive 覆盖前询问
```
### 10、rm
```bash
remove，删除
-f, --force 强制删除
-i, 删除前确认
-I, 删除超过三个文件或者递归删除前要求确认
-r, -R, --recursive 递归删除目录及其内容
-v, --verbose 详细显示进行的步骤
--help 显示此帮助信息
--version 显示版本信息
-d 只能用于删除空文件夹
```
### 11、帮助命令
man
### 12、开关机命令
```bash
shutdown
-h now	立刻关机
-h 1	1分钟后关机
-r now	立刻重启
-r 1	1分钟后重启
halt	立刻关闭系统，需手工切断电源
init 0	切换运行级别为0,0表示关机
init 6	切换运行级别为6，6表示重启
poweroff	立刻关闭系统，且关闭电源
logout	注销退出当前用户
exit	同上，快捷键ctrl+d
```
### 13、常用快捷键
```bash
ctrl+c	取消当前操作
ctrl+l	清空屏幕
ctrl+d	退出当前用户
ctrl+a	光标移到行首
ctrl+e	光标移到行尾
ctrl+u	删除光标到行首的内容
```
### 14、Linux环境变量
```bash
查看环境变量
echo $PATH
例：
root@GZY:~# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
冒号是分隔符
```
### 15、重定向符号 >
```bash
重定向的意思是“将数据传到其他地方”
>	输出覆盖
>>	输出追加
<或<<	标准输入
以上都需要配合其他命令如cat使用

直接使用 > file1 是清空文件内容

例：
cat file1 > file2    //把文件file1中的内容覆盖写入文件file2中
cat file1 >> file2    //把文件file1中的内容追加写入文件file2的内容末尾

cat >> file3 <<EOF    //按回车后可以输入内容，结尾再输入EOF结束

xargs -n 3 < file1    //把文件file1中的内容分为3个为一行输出在屏幕上
```
### 16、cat
```bash
用法：cat 选项 文件
-A, --show-all	等价于 -vET
-b, --number-nonblank	对非空输出行编号
-e	等价于 -vE
-E, --show-ends	在每行结束处显示 $
-n, --number	对输出的所有行编号
-s, --squeeze-blank	不输出多行空行
-t	等价于 -vT
-T, --show-tabs	将跳格字符显示为 ^I
-u
-v, --show-nonprinting	使用 ^ 和 M- 引用，除了 LFD 和 TAB 之外
--help	显示帮助
--version	输出版本信息

例：
cat file1	查看文件内容
cat file1 file2 > file3	将多个文件内容合并写入新文件中
cat /dev/null > file1	清空文件内容，/dev/null是linux系统的黑洞文件
```
### 17、管道符
```bash
|	需配合 grep 使用
用法：1.过滤  cat file1 | grep "xxx"
```
### 18、more less
```bash
分屏查看文本内容
more 文件名    #从头开始显示文件内容
按空格显示下一屏
按回车显示下一行
=显示当前行号
q退出
```
### 19、head tail
```bash
head -5 文件名	#显示文件从头开始的5行内容，不加参数默认显示头10行
-c	指定字符数量
head -c 5 文件名	#输出这个文件中的5个字符

tail -5 文件名	#显示文件从末尾开始的5行内容，不加参数默认显示最后10行
-f	实时刷新文件内容变化，可用来查看日志等
-F	不断打开文件，配合-f参数使用
```
### 20、cut
```bash
用法：cut 参数 数值区间 文件
-b		以字节为单位分割
-n		取消分割多字节字符，与-b一起用
-c		以字符为单位
-d		自定义分隔符，默认以tab为分隔符
-f		与-d一起使用，指定显示哪个区域
N		第N个字节、字符或字段，从 1 计数起
N-		从第N个字节、字符或字段直至行尾
N-M		从第N到第M（包括M）个字节、字符或字段
-M		从第1到第M（包括M）个字节、字符或字段

例：
1.截取每一行的第4个字符
cut -c 4 abc.txt

2.截取4到6个字符
cut -c 4-6 abc.txt

3.截取第5和7的字符
cut -c 5,7 abc.txt

4.截取一个范围的字符，如第四个到结尾
cut -c 4- abc.txt

5.截取一个范围的字符，如开头到第六个字符
cut -c -6 abc.txt

6.指定分隔符号进行截取
cut -d ":" -f 区域范围 abc.txt	#以冒号为分隔符
cut -d ":" -f -4 abc.txt	#以冒号为分隔符，找出前4列的内容
cut -d ":" -f -4 abc.txt | head -5	#二次过滤，在上一个基础上找出前5行
```
### 21、sort
sort命令将输入的文件内容按照规则排序，然后输出结果
```bash
语法：sort 选项 文件
-b, --ignore-leading-blanks	忽略前导的空白区域
-n, --numeric-sort	根据字符串数值比较
-r, --reverse	逆序输出排序结果
-u, --unique	去重排序，配合-c，严格校验排序；不配合-c，则只输出一次排序结果
-t, --field-separator=分隔符		使用指定的分隔符代替非空格到空格的转换
-k, --key=位置1[,位置2]		在位置1开始一个key，在位置2终止（默认为行尾）

例：
sort -n file.txt	对文件第一个字符进行排序，默认从小到大
sort -nr file.txt	从大到小排序
sort -n -t "." -k 4 file.txt	根据”.“作为分隔符并以第四个区域进行从小到大排序
```
### 22、uniq
uniq命令可以输出或者忽略文件中的重复行，常与sort排序结合使用
```bash
用法：uniq 选项 文件
从输入文件或者标准输入中筛选相邻的匹配行并写入到输出文件或标准输出。
不附加任何选项时匹配行将在首次出现处被合并。

-c, --count		在每行前加上表示相应行目出现次数的前缀编号
-d, --repeated	只输出重复的行
-u, --unique	只显示出现过一次的行，注意uniq的只出现过一次，是针对-c统计之后的结果

例：
sort -n file.txt | uniq   #去重
sort -n file.txt | uniq -c  #统计每一行重复次数
sort -n file.txt | uniq -d -c  #只找出文件中的重复行，且统计出现次数
sort -n file.txt | uniq -c -u  #找出只出现过一次的行
```
### 23、wc
用于统计文件的行数、单词、字节数
```bash
用法：wc 选项 文件
-c, --bytes  #打印字节数
-m, --chars  #打印字符数，因为字符串结尾会有一个$符号，所以打印的字符数要比看到的多一个
-l, --lines  #打印行数
-L, --max-line-length  #打印最长行的长度
-w, --words  #打印单词数
```
### 24、tr
tr命令从标准输入中替换、缩减或删除字符，将结果写入到标准输出
```bash
用法：tr 选项 字符集1 [字符集2]

字符集1：指定要转换或删除的原字符集
当执行转换操作时，必须使用参数“字符集2”指定转换的目标字符集。
但执行删除操作时，不需要参数“字符集2”；

-c, --complement   #取代所有不属于第一字符集的字符；
-d, --delete   #删除所有属于第一字符集的字符；
-s, --squeeze-repeats   #把连续重复的字符以单独一个字符表示；
-t, --truncate-set1   #先删除第一字符集较第二字符集多出的字符。
例：
echo "my name is alex" | tr "[a-z]" "[A-Z]" #转换为大写
```
### 25、find
```bash
语法：find 路径 -命令参数 [输出形式]
```
| 参数 | 解释 |
| --- | --- |
| -maxdepth | <目录层级>：设置最大目录层级 |
| -mindepth | <目录层级>：设置最小目录层级 |
| -atime | 按照文件访问access的时间查找，单位是天 |
| -ctime | 按照文件的改变change状态来查找文件，单位是天 |
| -mtime | 根据文件修改modify时间查找文件 |
| -name | 按照文件名字查找，支持*?[]通配符 |
| -group | 按照文件的所属组查找 |
| -perm | 按照文件的权限查找 |
| -size n[cwbkMG] | 按照文件的大小为n个由后缀决定的数据块。
其中后缀为：
b：代表512位元组的区块（如果用户没有指定后缀，则默认为b)
c：代表字节数
k：代表kilo bytes
w：字（2字节）
M：兆字节
G：千兆字节 |
| type | 查找某一类型的文件
b -  块设备文件
d -  目录
c -  字符设备文件
p -  管道文件
l -  符号链接文件
f -  普通文件
s - socket文件 |
| -user | 按照文件属主来查找文件 |
| -path | 配合-prune参数排除指定目录 |
| Actions模块 |  |
| -prune | 使find命令不在指定的目录寻找 |
| -delete | 删除找出的文件 |
| -exec或-ok | 对匹配的文件执行相应shell命令 |
| -print | 将匹配的结果标准输出 |
| OPERATORS |  |
| ! | 取反 |
| -a -o | 去交集、并集，作用类似&&和 |


```bash
例：
find / -name "test.txt"

find /opt -name "*.xxd.txt"

find /opt -maxdepth 1 -name "*ssx.txt"

find /opt -type f -name "[0-9]"   #在opt目录下寻找开头为0-9的类型为普通文件的文件

find /opt -type f -name "[0-9]" -delete   #删除上述文件

find /opt -atime -2    #2天内被访问过的的文件
find /opt -atime 2    #2天前的
find /opt -atime +2    #超过2天的

find /opt ! -type d    #找出非文件夹类型的文件，感叹号取反

find /opt -type f -name "*.txt" -ok rm {} \\;    #删除opt目录下所有以txt结尾的文件
```
### 26、xargs
又称管道命令
### 27、which、whereis
`which  命令`
从环境变量`$PATH`中寻找某个命令
`whereis  文件名`
寻找文件在哪里
### 28、tar、gzip、zip
```bash
语法：
tar 选项 参数

-A或--catenate：新增文件到已存在的备份文件
-B：设置区块大小
-c或--create：建立新的备份文件
-C<目录>：这个选项用在解压缩，若要在特定目录解压缩，可以使用这个选项
-d：记录文件的差别
-x或--extract或--get：从备份文件中还原文件
-t或--list：列出备份文件的内容
-z或--gzip或--ungzip：通过gzip指令处理备份文件
-Z或--compress或--uncompress：通过compress指令处理备份文件
-f<备份文件>或--file=<备份文件>：指定备份文件，必须写在结尾
-v或--verbose：显示指令执行过程
-r：添加文件到已经压缩的文件
-u：添加改变了和现有的文件到已经存在的压缩文件
-j：支持bzip2解压文件
-l：文件系统边界设置
-k：保留原有文件不覆盖
-m：保留文件不被覆盖
-w：确认压缩文件的正确性
-p或--same-permissions：用原来的文件权限还原文件
-P或--absolute-names：文件名使用绝对名称，不移除文件名称前的/号，不建议使用
-N<日期格式>或--newer=<日期时间>：只将较指定日期更新的文件保存到备份文件里
--exclude=<范本样式>：排除符号范本样式的文件
-h, --dereference跟踪符号链接，将它们所指向的文件归档并输出
```
例：
```bash
仅打包，不压缩
tar -cvf alltmp.tar ./*
打包后且用gzip命令压缩，节省磁盘空间
tar -zcvf alltmp.tar ./*
```
注意：

- f参数必须写在最后，后面紧跟压缩文件名
- tar命令仅打包，习惯用.tar作为后缀
- tar命令加上z参数，文件以.tar.gz或tgz表示
### 29、sed
```bash
sed -i "s/haodao/HAODAO/g" test.txt    #将文件中的"haodao"全部替换为"HAODAO"

sed -i "s:/etc/dhcp:/home:g" text.txt    #将文件中的/etc/dhcp目录替换为/home
```

# 八、Linux磁盘管理
**章节简述：**
Linux系统中颇具特色的文件存储结构常常搞得新手头昏脑涨，本章将从Linux系统中的文件存储结构开始，讲述文件系统层次标准（Filesystem Hierarchy Standard，FHS）、udev硬件命名规则以及硬盘设备的原理。
为了让读者更好地理解文件系统的作用，刘遄老师将在本章详细地分析Linux系统中最常见的Ext3、Ext4与XFS文件系统的不同之处，并带领各位读者着重练习硬盘设备分区、格式化以及挂载等常用的硬盘管理操作，以便熟练掌握文件系统的使用方法。
在打下坚实的理论基础并完成一些相关的实践练习后，我们将进一步完整地部署交换（SWAP）分区、配置quota磁盘配额服务、使用VDO（虚拟数据优化）技术，以及掌握ln命令带来的软硬链接。相信各位读者在学习完本章后，会对Linux系统以及Windows系统中的磁盘存储以及文件系统有深入的理解。
本章目录结构

- 6.1 一切从“/”开始
- 6.2 物理设备的命名规则
- 6.3 文件系统与数据资料
- 6.4 挂载硬件设备
- 6.5 添加硬盘设备
- 6.6 添加交换分区
- 6.7 磁盘容量配额
- 6.8 VDO虚拟数据优化
- 6.9 软硬方式链接
### **6.1 一切从“/”开始**
在Linux系统中，目录、字符设备、套接字、硬盘、光驱、打印机等都被抽象成文件形式，即刘遄老师一直强调的“Linux系统中一切都是文件”。既然平时我们打交道的都是文件，那么又应该如何找到它们呢？在Windows操作系统中，想要找到一个文件，要依次进入该文件所在的磁盘分区（也叫盘符），然后再进入该分区下的具体目录，最终找到这个文件。但是在Linux系统中并不存在C、D、E、F等盘符，Linux系统中的一切文件都是从“根”目录（/）开始的，并按照文件系统层次标准（FHS）采用倒树状结构来存放文件，以及定义了常见目录的用途。
另外，Linux系统中的文件和目录名称是严格区分大小写的。例如，root、rOOt、Root、rooT均代表不同的目录，并且文件名称中不得包含斜杠（/）。Linux系统中的文件存储结构如图6-1所示。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684061588058-1ce9abe4-044d-48cb-848e-d28ceaaf23f8.png#averageHue=%23faf9f9&clientId=u62c829e8-bcc7-4&from=paste&id=ud3706cf3&originHeight=258&originWidth=618&originalType=url&ratio=1&rotation=0&showTitle=false&size=9892&status=done&style=none&taskId=ubfdc7082-35c6-464b-b41b-c472e529d04&title=)
图6-1  Linux系统中的文件存储结构
前文提到的FHS是根据以往无数Linux系统用户和开发者的经验而总结出来的，是用户在Linux系统中存储文件时需要遵守的规则，用于指导用户应该把文件保存到什么位置，以及告诉用户应该在何处找到所需的文件。但是，FHS对于用户来讲只能算是一种道德上的约束，有些用户就是懒得遵守，依然会把文件到处乱放，有些甚至从来没有听说过它。这里并不是号召各位读者去谴责他们，而是建议大家要灵活运用所学的知识，千万不要认准这个FHS协定只讲死道理，不然吃亏的可就是自己了。在Linux系统中，最常见的目录以及所对应的存放内容如表6-1所示。

| 表6-1 | Linux系统中常见的目录名称以及相应内容 |
| --- | --- |
| 目录名称 | 应放置文件的内容 |
| /boot | 开机所需文件—内核、开机菜单以及所需配置文件等 |
| /dev | 以文件形式存放任何设备与接口 |
| /etc | 配置文件 |
| /home | 用户主目录 |
| /bin | 存放单用户模式下还可以操作的notion://www.notion.so/Linux%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4 |
| /lib | 开机时用到的函数库，以及/bin与/sbin下面的命令要调用的函数 |
| /sbin | 开机过程中需要的命令 |
| /media | 用于挂载设备文件的目录 |
| /opt | 放置第三方的软件 |
| /root | 系统管理员的家目录 |
| /srv | 一些网络服务的数据文件目录 |
| /tmp | 任何人均可使用的“共享”临时目录 |
| /proc | 虚拟文件系统，例如系统内核、进程、外部设备及网络状态等 |
| /usr/local | 用户自行安装的软件 |
| /usr/sbin | Linux系统开机时不会使用到的软件/命令/脚本 |
| /usr/share | 帮助与说明文件，也可放置共享文件 |
| /var | 主要存放经常变化的文件，如日志 |
| /lost+found | 当文件系统发生错误时，将一些丢失的文件片段存放在这里 |

在Linux系统中另外还有一个重要的概念—路径。路径指的是如何定位到某个文件，分为绝对路径与相对路径。绝对路径指的是从根目录（/）开始写起的文件或目录名称，而相对路径则指的是相对于当前路径的写法。
### **6.2 物理设备的命名规则**
在Linux系统中一切都是文件，硬件设备也不例外。既然是文件，就必须有文件名称。系统内核中的udev设备管理器会自动把硬件名称规范起来，目的是让用户通过设备文件的名字可以猜出设备大致的属性以及分区信息等；这对于陌生的设备来说特别方便。另外，udev设备管理器的服务会一直以守护进程的形式运行并侦听内核发出的信号来管理/dev目录下的设备文件。Linux系统中常见的硬件设备及其文件名称如表6-2所示。

| 表6-2 | 常见的硬件设备及其文件名称 |
| --- | --- |
| 硬件设备 | 文件名称 |
| IDE设备 | /dev/hd[a-d] |
| SCSI/SATA/U盘 | /dev/sd[a-z] |
| virtio设备 | /dev/vd[a-z] |
| 软驱 | /dev/fd[0-1] |
| 打印机 | /dev/lp[0-15] |
| 光驱 | /dev/cdrom |
| 鼠标 | /dev/mouse |
| 磁带机 | /dev/st0或/dev/ht0 |

由于现在的IDE设备已经很少见了，所以一般的硬盘设备都是以“/dev/sd”开头。而一台主机上可以有多块硬盘，因此系统采用a～z来代表26块不同的硬盘（默认从a开始分配），而且硬盘的分区编号也很有讲究：
主分区或扩展分区的编号从1开始，到4结束；
逻辑分区从编号5开始。
国内很多Linux培训讲师以及很多知名Linux图书在讲到设备和分区名称时，总会讲错两个知识点。第一个知识点是设备名称的理解错误。很多培训讲师和Linux技术图书中会提到，比如/dev/sda表示主板上第一个插槽上的存储设备，学员或读者在实践操作的时候会发现果然如此，因此也就对这条理论知识更加深信不疑。但真相不是这样的，/dev目录中sda设备之所以是a，并不是由插槽决定的，而是由系统内核的识别顺序来决定的，而恰巧很多主板的插槽顺序就是系统内核的识别顺序，因此才会被命名为/dev/sda。大家以后在使用iSCSI网络存储设备时就会发现，明明主板上第二个插槽是空着的，但系统却能识别到/dev/sdb这个设备—就是这个道理。
第二个知识点是对分区名称的理解错误。很多Linux培训讲师会告诉学员，分区的编号代表分区的个数。比如sda3表示这是设备上的第3个分区，而学员在做实验的时候确实也会得出这样的结果。但是这个理论知识是错误的，因为分区的数字编码不一定是强制顺延下来的，也有可能是手工指定的。因此sda3只能表示是编号为3的分区，而不能判断sda设备上已经存在了3个分区。
在填了这两个“坑”之后，再来分析一下/dev/sda5这个设备文件名称包含哪些信息，如图6-2所示。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684061588193-6e133f57-ddcf-4d6d-8bf8-1ddca448d207.png#averageHue=%23ede8d0&clientId=u62c829e8-bcc7-4&from=paste&id=u6b8c99d8&originHeight=185&originWidth=467&originalType=url&ratio=1&rotation=0&showTitle=false&size=25410&status=done&style=none&taskId=uc1d9b011-3c06-4858-b9f3-d7ad45fed63&title=)
图6-2 设备文件名称
首先，/dev/目录中保存的应当是硬件设备文件；其次，sd表示的是存储设备；然后，a表示系统中同类接口中第一个被识别到的设备；最后，5表示这个设备是一个逻辑分区。一言以蔽之，“/dev/sda5”表示的就是“这是系统中第一块被识别到的硬件设备中分区编号为5的逻辑分区的设备文件”。考虑到很多读者完全没有Linux基础，不太容易理解前面所说的主分区、扩展分区和逻辑分区的概念，因此接下来简单科普一下硬盘相关的知识。
正是因为计算机有了硬盘设备，我们才能够在玩游戏的过程中或游戏通关之后随时存档，而不用每次重头开始。硬盘设备是由大量的扇区组成的，每个扇区的容量为512字节。其中第一个扇区最重要，它里面保存着主引导记录与分区表信息。就第一个扇区来讲，主引导记录需要占用446字节，分区表占用64字节，结束符占用2字节；其中分区表中每记录一个分区信息就需要16字节，这样一来最多只有4个分区信息可以写到第一个扇区中，这4个分区就是4个主分区。第一个扇区中的数据信息如图6-3所示。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684061588333-5524e844-d826-437d-8065-356520e1f0ef.png#averageHue=%23f8f8f7&clientId=u62c829e8-bcc7-4&from=paste&id=u966c9b17&originHeight=436&originWidth=764&originalType=url&ratio=1&rotation=0&showTitle=false&size=77866&status=done&style=none&taskId=u4ece2f59-3b57-4b99-8c4f-61387a85fff&title=)
图6-3  第一个扇区中的数据信息
现在，问题来了—每块硬盘最多只能创建出4个分区？这明显不合情理也不够用。
于是为了解决分区个数不够的问题，可以将第一个扇区的分区表中16字节（原本要写入主分区信息）的空间（称之为扩展分区）拿出来指向另外一个分区。也就是说，扩展分区其实并不是一个真正的分区，而更像是一个占用16字节分区表空间的指针—一个指向另外一个分区的指针。这样一来，用户一般会选择使用3个主分区加1个扩展分区的方法，然后在扩展分区中创建出数个逻辑分区，从而来满足多分区（大于4个）的需求。当然，就目前来讲大家只要明白为什么主分区不能超过4个就足够了。主分区、扩展分区、逻辑分区可以像图6-4那样来规划。
**Tips**
所谓扩展分区，严格地讲它不是一个实际意义的分区，而仅仅是一个指向其他分区的指针，这种指针结构将形成一个单向链表。因此扩展分区自身不能存储数据，用户需要在其指向的对应分区（称之为逻辑分区）上进行操作。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684061588263-ce6f5e05-dc96-4bfb-b91d-465fa7203e23.png#averageHue=%23d6e29b&clientId=u62c829e8-bcc7-4&from=paste&id=uf4553768&originHeight=196&originWidth=524&originalType=url&ratio=1&rotation=0&showTitle=false&size=23804&status=done&style=none&taskId=uf726cbb6-ad5f-43d8-938d-f3dff1a363f&title=)
图6-4 硬盘分区的规划
**大家可以试着解读一下/dev/hdc8代表着什么？** （**答案模式**）

- *答案：**这是第3块IDE设备（现在比较少见）中编号为8的逻辑分区。

对了！如果大家参加红帽RHCE考试或者购买了一台云主机，还会看到类似于/dev/vda、/dev/vdb这样的设备。这种以vd开头的设备叫作Virtio设备，简单来说就是一种虚拟化设备。像KVM、Xen这种虚拟机监控器（Hypervisor）默认就都是这种设备。
### **6.3 文件系统与数据资料**
文件系统的作用是合理规划硬盘，以保证用户正常的使用需求。
Linux系统支持数十种文件系统，而最常见的文件系统如下所示。
**Ext2**：最早可追溯到1993年，是Linux系统的第一个商业级文件系统，它基本沿袭了UNIX文件系统的设计标准。但由于不包含日志读写功能，数据丢失的可能性很大，因此大家能不用就不用，或者顶多建议用于SD存储卡或U盘。
**Ext3**：是一款日志文件系统，它会把整个硬盘的每个写入动作的细节都预先记录下来，然后再进行实际操作，以便在发生异常宕机后能回溯追踪到被中断的部分。Ext3能够在系统异常宕机时避免文件系统资料丢失，并能自动修复数据的不一致与错误。然而，当硬盘容量较大时，所需的修复时间也会很长，而且也不能100%地保证资料不会丢失。
**Ext4**：Ext3的改进版本，作为RHEL 6系统中默认的文件管理系统，它支持的存储容量高达1EB（1EB=1,073,741,824GB），且能够有无限多的子目录。另外，Ext4文件系统能够批量分配block（块），从而极大地提高了读写效率。现在很多主流服务器也会使用Ext4文件系统。
**XFS**：是一种高性能的日志文件系统，而且是RHEL 7/8中默认的文件管理系统。它的优势在发生意外宕机后尤其明显，即可以快速地恢复可能被破坏的文件，而且强大的日志功能只需花费极低的计算和存储性能。它支持的最大存储容量为18EB，这几乎满足了所有需求。
RHEL 7/8系统中一个比较大的变化就是使用了XFS作为文件系统，这不同于RHEL 6使用的Ext4。从红帽公司官方发布的说明来看，这确实是一个不小的进步，但是刘遄老师在实测中发现并不完全属实。因为单纯就测试一款文件系统的“读取”性能来说，到底要读取多少个文件，每个文件的大小是多少，读取文件时的CPU、内存等系统资源的占用率如何，以及不同的硬件配置是否会有不同的影响，这些因素都是不确定的，因此实在不敢直接照抄红帽官方的介绍。我个人认为XFS虽然在性能方面比Ext4有所提升，但绝不是压倒性的，因此XFS文件系统最卓越的亮点应该当属可支持高达18EB的存储容量吧。
18EB等于18,874,368TB。假设每块硬盘的容量是100TB，那么大概需要19万块硬盘才能把18EB的数据都装下。总之，当用了XFS之后，文件的存储上限就不再取决于技术层面，而是钱包了。过去常常跟同学们开玩笑，“如果有18EB的数据在上海机房，想以最快的方式传送到北京，我们有什么好办法呢？”答案是“乘坐京沪高铁”。
在拿到一块新的硬盘存储设备后，先需要分区，然后再格式化文件系统，最后才能挂载并正常使用。硬盘的分区操作取决于您的需求和硬盘大小；也可以选择不进行分区，但是必须对硬盘进行格式化处理。

---

**Tips**
就像拿到了一张未裁切的完整纸张那样，首先要进行裁切以方便使用（**分区**），接下来在裁切后的纸张上画格以便能书写工整（格式化），最后是正式的使用（**挂载**）。
接下来向大家简单地科普一下硬盘在格式化后发生的事情。再次强调，大家不用刻意去记住，只要能看懂就行了。
日常需要保存在硬盘中的数据实在太多了，因此Linux系统中有一个名为super block的“硬盘地图”。Linux并不是把文件内容直接写入到这个“硬盘地图”里面，而是在里面记录着整个文件系统的信息。因为如果把所有的文件内容都写入到这里面，它的体积将变得非常大，而且文件内容的查询与写入速度也会变得很慢。Linux只是把每个文件的权限与属性记录在inode中，而且每个文件占用一个独立的inode表格，该表格的大小默认为128字节，里面记录着如下信息：
该文件的访问权限（read、write、execute）；
该文件的所有者与所属组（owner、group）；
该文件的大小（size）；
该文件的创建或内容修改时间（Ctime）；
该文件的最后一次访问时间（Atime）；
该文件的修改时间（Mtime）；
文件的特殊权限（SUID、SGID、SBIT）；
该文件的真实数据地址（point）。
而文件的实际内容则保存在block块中（大小一般是1KB、2KB或4KB），一个inode的默认大小仅为128字节，记录一个block则消耗4字节。当文件的inode被写满后，Linux系统会自动分配出一个block，专门用于像inode那样记录其他block块的信息，这样把各个block块的内容串到一起，就能够让用户读到完整的文件内容了。对于存储文件内容的block块，有下面两种常见的情况（以4KB大小的block为例进行说明）。
情况1：文件很小（1KB），但依然会占用一个block，因此会潜在地浪费3KB。
情况2：文件很大（5KB），那么会占用两个block（5KB−4KB后剩下的1KB也要占用一个block）。
大家看到这里，是不是觉得Linux系统好浪费啊？为什么最后一个block块容量总不能被完全使用呢？其实每个系统都是一样的，只不过大家此前没有留意过罢了。同学们可以随手查看一个电脑中已有的文件，看看文件的实际大小与占用空间是否一致，如图6-5所示。
计算机系统在发展过程中产生了众多的文件系统，为了使用户在读取或写入文件时不用关心底层的硬盘结构，Linux内核中的软件层为用户程序提供了一个虚拟文件系统（Virtual File System，VFS）接口，这样用户实际上在操作文件时就是统一对这个虚拟文件系统进行操作了。图6-6所示为VFS的架构示意图。从中可见，实际文件系统在VFS下隐藏了自己的特性和细节，这样用户在日常使用时会觉得“文件系统都是一样的”，也就可以随意使用各种命令在任何文件系统中进行各种操作了（比如使用cp命令来复制文件）。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684061588282-3b22c7b7-157e-4fa3-b35b-c35cb9d09e71.png#averageHue=%23f6f5f4&clientId=u62c829e8-bcc7-4&from=paste&id=ua38841a9&originHeight=646&originWidth=412&originalType=url&ratio=1&rotation=0&showTitle=false&size=32556&status=done&style=none&taskId=u587df049-e2ca-40fc-93e2-08b34c32d78&title=)
图6-5 文件的实际大小与占用空间
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684061589363-8640abe8-668b-4221-bd90-615311b7776f.png#averageHue=%23fefefd&clientId=u62c829e8-bcc7-4&from=paste&id=u02739dbf&originHeight=701&originWidth=806&originalType=url&ratio=1&rotation=0&showTitle=false&size=156109&status=done&style=none&taskId=u3a1796fe-5b41-4e2e-97c9-0539e3bf438&title=)
图6-6  VFS的架构示意图
VFS也有点像一个翻译官。我们不需要知道对方的情况，只要告诉VFS想进行的操作是什么，它就会自动判断对方能够听得懂什么指令，然后翻译并交代下去。这可以让用户不用操心这些“小事情”，专注于自己的操作。
**Tips**
在医学圈里有句这样一句话，“当您开始关注身体某个器官的时候，大概率是它最近不舒服了”。由于VFS真的太好用了，而且几乎不会出现任何问题，所以如果不在这里讲一下它的理论，相信很多同学很可能在多年后都不知道自己用过它。
### **6.4 挂载硬件设备**
我们在用惯了Windows系统后总觉得一切都是理所当然的，平时把U盘插入到电脑后也从来没有考虑过Windows系统做了哪些事情，才使得我们可以访问这个U盘的。接下来我们会逐一学习在Linux系统中挂载和卸载存储设备的方法，以便大家更好地了解Linux系统添加硬件设备的工作原理和流程。前面讲到，在拿到一块全新的硬盘存储设备后要先分区，然后格式化，最后才能挂载并正常使用。“分区”和“格式化”大家以前经常听到，但“挂载”又是什么呢？
刘遄老师在这里给您一个最简单、最贴切的解释—当用户需要使用硬盘设备或分区中的数据时，需要先将其与一个已存在的目录文件进行关联，而这个关联动作就是“挂载”。下文将向读者逐步讲解如何使用硬盘设备，但是鉴于与挂载相关的理论知识比较复杂，而且很重要，因此决定再拿出一个小节单独讲解，这次希望大家不仅要看懂，而且还要记住。
mount命令用于挂载文件系统，格式为“mount文件系统 挂载目录”。mount命令中可用的参数及作用如表6-3所示。挂载是在使用硬件设备前所执行的最后一步操作。只需使用mount命令把硬盘设备或分区与一个目录文件进行关联，然后就能在这个目录中看到硬件设备中的数据了。对于比较新的Linux系统来讲，一般不需要使用-t参数来指定文件系统的类型，Linux系统会自动进行判断。而mount中的-a参数则厉害了，它会在执行后自动检查/etc/fstab文件中有无被疏漏挂载的设备文件，如果有，则进行自动挂载操作。

| 表6-3 | mount命令中的参数以及作用 |
| --- | --- |
| 参数 | 作用 |
| -a | 挂载所有在/etc/fstab中定义的文件系统 |
| -t | 指定文件系统的类型 |

例如，要把设备/dev/sdb2挂载到/backup目录，只需要在mount命令中填写设备与挂载目录参数就行，系统会自动判断要挂载文件的类型，命令如下：
[root@linuxprobe ~]# mount /dev/sdb2 /backup
如果在工作中要挂载一块网络存储设备，该设备的名字可能会变来变去，这样再写为sdb就不太合适了。这时推荐用UUID（Universally Unique Identifier，通用唯一识别码）进行挂载操作。UUID是一串用于标识每块独立硬盘的字符串，具有唯一性及稳定性，特别适合用来挂载网络设备。那么，怎么才能得知独立硬盘的UUID呢？答案是使用blkid命令。
blkid命令用于显示设备的属性信息，英文全称为“block id”，语法格式为“blkid [设备名]”。使用blkid命令来查询设备UUID的示例如下：
```bash
[root@linuxprobe ~]# blkid
/dev/sdb1: UUID="2db66eb4-d9c1-4522-8fab-ac074cd3ea0b" TYPE="xfs" PARTUUID="eb23857a-01"
/dev/sdb2: UUID="**478fRb-1pOc-oPXv-fJOS-tTvH-KyBz-VaKwZG**" TYPE="ext4" PARTUUID="eb23857a-02"
```
有了设备的UUID值之后，就可以用它挂载网络设备了：
[root@linuxprobe ~]# mount UUID=478fRb-1pOc-oPXv-fJOS-tTvH-KyBz-VaKwZG /backup
上面的方法执行mount命令后就能立即使用文件系统了，但系统在重启后挂载就会失效，也就是说需要每次开机后都手动挂载一下。如果想让硬件设备和目录永久地进行自动关联，就必须把挂载信息按照指定的填写格式“设备文件 挂载目录 格式类型 权限选项 是否备份 是否自检”（各字段的意义见表6-4）写入到/etc/fstab文件中。这个文件中包含着挂载所需的诸多信息项目，一旦配置好之后就能一劳永逸了。

| 表6-4 | 用于挂载信息的指定填写格式中，各字段所表示的意义 |
| --- | --- |
| 字段 | 意义 |
| 设备文件 | 一般为设备的路径+设备名称，也可以写唯一识别码（UUID，Universally Unique Identifier） |
| 挂载目录 | 指定要挂载到的目录，需在挂载前创建好 |
| 格式类型 | 指定文件系统的格式，比如Ext3、Ext4、XFS、SWAP、iso9660（此为光盘设备）等 |
| 权限选项 | 若设置为defaults，则默认权限为：rw, suid, dev, exec, auto, nouser, async |
| 是否备份 | 若为1则开机后使用dump进行磁盘备份，为0则不备份 |
| 是否自检 | 若为1则开机后自动进行磁盘自检，为0则不自检 |

如果想将文件系统为Ext4的硬件设备/dev/sdb2在开机后自动挂载到/backup目录上，并保持默认权限且无须开机自检，就需要在/etc/fstab文件中写入下面的信息，这样在系统重启后也会成功挂载。
```bash
[root@linuxprobe ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Tue Jul 21 05:03:40 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
/dev/mapper/rhel-root                     /        xfs     defaults    0 0
UUID=812b1f7c-8b5b-43da-8c06-b9999e0fe48b /boot    xfs     defaults    0 0
/dev/mapper/rhel-swap                     swap     swap    defaults    0 0
**/dev/sdb2                                 /backup  ext4    defaults    0 0**
```
由于后面需要使用系统镜像制作Yum/DNF软件仓库，我们提前把光盘设备挂载到/media/cdrom目录中。光盘设备的文件系统格式是iso9660：
```bash
[root@linuxprobe ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Tue Jul 21 05:03:40 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
/dev/mapper/rhel-root                     /              xfs        defaults       0 0
UUID=812b1f7c-8b5b-43da-8c06-b9999e0fe48b /boot          xfs        defaults       0 0
/dev/mapper/rhel-swap                     swap           swap       defaults       0 0
/dev/sdb2                                 /backup        ext4       defaults       0 0
**/dev/cdrom                                /media/cdrom   iso9660    defaults       0 0**
```
写入到/etc/fstab文件中的设备信息并不会立即生效，需要使用mount -a参数进行自动挂载：
[root@linuxprobe ~]# mount -a
df命令用于查看已挂载的磁盘空间使用情况，英文全称为“disk free”，语法格式为“df -h”。
-h参数便捷地对存储容量进行“进位”操作。例如，在遇到10240K的时候会自动进位写成10M。
```bash
[root@linuxprobe~]# df -h
Filesystem             Size  Used Avail Use% Mounted on
devtmpfs               969M     0  969M   0% /dev
tmpfs                  984M     0  984M   0% /dev/shm
tmpfs                  984M   18M  966M   2% /run
tmpfs                  984M     0  984M   0% /sys/fs/cgroup
/dev/mapper/rhel-root   17G  3.9G   14G  23% /
/dev/sda1             1014M  152M  863M  15% /boot
**/dev/sdb2              480M   20M  460M   4% /backup**
tmpfs                  197M   16K  197M   1% /run/user/42
tmpfs                  197M  3.5M  194M   2% /run/user/0
**/dev/sr0               6.7G  6.7G     0 100% /media/cdrom**
```
对了！说到网络存储设备，建议您在fstab文件挂载信息中加上_netdev参数。加上后系统会等联网成功后再尝试挂载这块网络存储设备，从而避免了开机时间过长或失败的情况（第17章学习iSCSI技术时可以用上）。
```bash
[root@linuxprobe ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Tue Jul 21 05:03:40 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
/dev/mapper/rhel-root                       /              xfs       defaults            0 0
UUID=812b1f7c-8b5b-43da-8c06-b9999e0fe48b   /boot          xfs       defaults            0 0
/dev/mapper/rhel-swap                       swap           swap      defaults            0 0
/dev/sdb2                                   /backup        ext4      defaults**,_netdev   ** 0 0
/dev/cdrom                                  /media/cdrom   iso9660   defaults            0 0
```
挂载文件系统的目的是为了使用硬件资源，而卸载文件系统则意味不再使用硬件的设备资源。既然挂载操作就是把硬件设备与目录两者进行关联的动作，那么卸载操作只需要说明想要取消关联的设备文件或挂载目录的其中一项即可，一般不需要加其他额外的参数。
umount命令用于卸载设备或文件系统，英文全称为“un mount”，语法格式为“umount [设备文件/挂载目录]”。
```
[root@linuxprobe ~]# umount /dev/sdb2
```
如果我们当前就处于设备所挂载的目录，系统会提示该设备繁忙，此时只需要退出到其他目录后再尝试一次就行了。轻松搞定。
```bash
[root@linuxprobe ~]# cd /media/cdrom/
[root@linuxprobe cdrom]# umount /dev/cdrom
umount: /media/cdrom: target is busy.
[root@linuxprobe  cdrom]# cd ~
[root@linuxprobe ~]# umount /dev/cdrom
[root@linuxprobe ~]#
```
**Tips**
lsblk命令用于查看已挂载的磁盘的空间使用情况，英文全称为“list block id”，输入该命令后按回车键执行即可。
```bash
[root@linuxprobe ~]# lsblk
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda             8:0    0   20G  0 disk
├─sda1          8:1    0    1G  0 part /boot
└─sda2          8:2    0   19G  0 part
├─rhel-root 253:0    0   17G  0 lvm  /
└─rhel-swap 253:1    0    2G  0 lvm  [SWAP]
sr0            11:0    1  6.6G  0 rom  /media/cdrom
```
### **6.5 添加硬盘设备**
添加的新硬盘按照前文讲解的udev服务命名规则，第二个被识别的SATA设备应该会被保存为/dev/sdb，这个就是硬盘设备文件了。但在开始使用该硬盘之前还需要进行分区操作，例如从中取出一个2GB的分区设备以供后面的操作使用。
fdisk命令用于新建、修改及删除磁盘的分区表信息，英文全称为“format disk”，语法格式为“fdisk磁盘名称”。
在Linux系统中，管理硬盘设备最常用的方法就当属fdisk命令了。它提供了集添加、删除、转换分区等功能于一身的“一站式分区服务”。不过与前面讲解的直接写到命令后面的参数不同，这条命令的参数（见表6-5）是交互式的一问一答的形式，因此在管理硬盘设备时特别方便，可以根据需求动态调整。

| 表6-5 | fdisk命令中的参数以及作用 |
| --- | --- |
| 参数 | 作用 |
| m | 查看全部可用的参数 |
| n | 添加新的分区 |
| d | 删除某个分区信息 |
| l | 列出所有可用的分区类型 |
| t | 改变某个分区的类型 |
| p | 查看分区表信息 |
| w | 保存并退出 |
| q | 不保存直接退出 |

首先使用fdisk命令来尝试管理/dev/sdb硬盘设备。在看到提示信息后输入参数p来查看硬盘设备内已有的分区信息，其中包括了硬盘的容量大小、扇区个数等信息：
```bash
[root@linuxprobe ~]# fdisk /dev/sdb

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x88b2c2b0.

Command (m for help): **p**
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x88b2c2b0
```
输入参数n尝试添加新的分区。系统会要求用户是选择继续输入参数p来创建主分区，还是输入参数e来创建扩展分区。这里输入参数p来创建一个主分区：
```bash
Command (m for help): **n**
Partition type
p   primary (0 primary, 0 extended, 4 free)
e   extended (container for logical partitions)
Select (default p): **p**
```
在确认创建一个主分区后，系统要求用户先输入主分区的编号。在前文得知，主分区的编号范围是1～4，因此这里输入默认的1就可以了。接下来系统会提示定义起始的扇区位置，这不需要改动，敲击回车键保留默认设置即可，系统会自动计算出最靠前的空闲扇区的位置。最后，系统会要求定义分区的结束扇区位置，这其实就是要去定义整个分区的大小是多少。我们不用去计算扇区的个数，只需要输入+2G即可创建出一个容量为2GB的硬盘分区。
```bash
Partition number (1-4, default 1): **1**
First sector (2048-41943039, default 2048): **此处敲击回车即可**
Last sector, +sectors or +size{K,M,G,T,P} (2048-41943039, default 41943039): **+2G**

Created a new partition 1 of type 'Linux' and of size 2 GiB.
```
再次使用参数p来查看硬盘设备中的分区信息。果然就能看到一个名称为/dev/sdb1、起始扇区位置为2048、结束扇区位置为4196351的主分区了。这时千万不要直接关闭窗口，而应该敲击参数w后按回车键，这样分区信息才是真正地写入成功啦。
```bash
Command (m for help): **p**
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x88b2c2b0

Device     Boot Start     End Sectors Size Id Type
/dev/sdb1        2048 4196351 4194304   2G 83 Linux

Command (m for help): **w**
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
分区信息中第6个字段的Id值是一个编码，用于标识该分区的作用，可帮助用户快速了解该分区的作用，一般没必要修改。使用l参数查看一下磁盘编码都有哪些，然后在6.6节进行SWAP操作时再修改吧：
```bash
Command (m for help): **l**

0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris
1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden or  c6  DRDOS/sec (FAT-
4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx
5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data
6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility
8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt
9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access
a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O
b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor
c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi ea  Rufus alignment
e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         eb  BeOS fs
f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ee  GPT
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        ef  EFI (FAT-12/16/
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f0  Linux/PA-RISC b
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f1  SpeedStor
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f4  SpeedStor
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      f2  DOS secondary
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fb  VMware VMFS
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fc  VMware VMKCORE
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fd  Linux raid auto
1c  Hidden W95 FAT3 75  PC/IX           bc  Acronis FAT32 L fe  LANstep
1e  Hidden W95 FAT1 80  Old Minix       be  Solaris boot    ff  BBT
```
在上述步骤执行完毕之后，Linux系统会自动把这个硬盘主分区抽象成/dev/sdb1设备文件。可以使用file命令查看该文件的属性，但我在讲课和工作中发现，有些时候系统并没有自动把分区信息同步给Linux内核，而且这种情况似乎还比较常见（但不能算作严重的bug）。可以输入partprobe命令手动将分区信息同步到内核，而且一般推荐连续两次执行该命令，效果会更好。如果使用这个命令都无法解决问题，那么就重启计算机吧，这个“杀手锏”百试百灵，一定会有用的。
```bash
[root@linuxprobe ]# file /dev/sdb1
/dev/sdb1: cannot open `/dev/sdb1' (No such file or directory)
[root@linuxprobe ]# partprobe
[root@linuxprobe ]# partprobe
[root@linuxprobe ]# file /dev/sdb1
/dev/sdb1: block special
```
如果硬件存储设备没有进行格式化，则Linux系统无法得知怎么在其上写入数据。因此，在对存储设备进行分区后还需要进行格式化操作。在Linux系统中用于格式化操作的命令是mkfs。这条命令很有意思，因为在Shell终端中输入mkfs名后再敲击两下用于补齐命令的Tab键，会有如下所示的效果：
```bash
[root@linuxprobe ~]# mkfs
mkfs         mkfs.ext2    mkfs.ext4    mkfs.minix   mkfs.vfat
mkfs.cramfs  mkfs.ext3    mkfs.fat     mkfs.msdos   mkfs.xfs
```
对！这个mkfs命令很贴心地把常用的文件系统名称用后缀的方式保存成了多个命令文件，用起来也非常简单—mkfs.文件类型名称。例如要将分区为XFS的文件系统进行格式化，则命令应为mkfs.xfs /dev/sdb1。
```bash
[root@linuxprobe ~]# mkfs.xfs /dev/sdb1
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=131072 blks
=                       sectsz=512   attr=2, projid32bit=1
=                       crc=1        finobt=1, sparse=1, rmapbt=0
=                       reflink=1
data     =                       bsize=4096   blocks=524288, imaxpct=25
=                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
=                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```
终于完成了存储设备的分区和格式化操作，接下来就是要来挂载并使用存储设备了。与之相关的步骤也非常简单：首先是创建一个用于挂载设备的挂载点目录；然后使用mount命令将存储设备与挂载点进行关联；最后使用df -h命令来查看挂载状态和硬盘使用量信息。
```bash
[root@linuxprobe ~]# mkdir /newFS
[root@linuxprobe ~]# mount /dev/sdb1 /newFS
[root@linuxprobe ~]# df -h
Filesystem             Size  Used Avail Use% Mounted on
devtmpfs               969M     0  969M   0% /dev
tmpfs                  984M     0  984M   0% /dev/shm
tmpfs                  984M  9.6M  974M   1% /run
tmpfs                  984M     0  984M   0% /sys/fs/cgroup
/dev/mapper/rhel-root   17G  3.9G   14G  23% /
/dev/sr0               6.7G  6.7G     0 100% /media/cdrom
/dev/sda1             1014M  152M  863M  15% /boot
tmpfs                  197M   16K  197M   1% /run/user/42
tmpfs                  197M  3.5M  194M   2% /run/user/0
**/dev/sdb1              2.0G   47M  2.0G   3% /newFS**
```
du命令用查看分区或目录所占用的磁盘容量大小，英文全称为“disk usage”，语法格式为“du -sh目录名称”。
既然存储设备已经顺利挂载，接下来就可以尝试通过挂载点目录向存储设备中写入文件了。在写入文件之前，先来看一个用于查看文件数据占用量的du命令。简单来说，该命令就是用来查看一个或多个文件占用了多大的硬盘空间。
在使用Window系统时，我们总会遇到“C盘容量不足，清理垃圾后又很快被占满”的情况。在Linux系统中可以使用du -sh /*命令来查看在Linux系统根目录下所有一级目录分别占用的空间大小，在1s之内就能找到哪个目录占用的空间最多：
```bash
[root@linuxprobe ~]# du -sh /*
0	/bin
113M	/boot
0	/dev
29M	/etc
12K	/home
0	/lib
0	/lib64
6.7G	/media
0	/mnt
0	/newFS
0	/opt
0	/proc
8.6M	/root
9.6M	/run
0	/sbin
0	/srv
0	/sys
12K	/tmp
3.5G	/usr
155M	/var
```
先从某些目录中复制过来一批文件，然后查看这些文件总共占用了多大的容量：
```bash
[root@linuxprobe ~]# cp -rf /etc/* /newFS
[root@linuxprobe ~]# ls /newFS
adjtime                     hostname                  profile.d
aliases                     hosts                     protocols
alsa                        hosts.allow               pulse
alternatives                hosts.deny                qemu-ga
anacrontab                  hp                        qemu-kvm
asound.conf                 idmapd.conf               radvd.conf
………………省略部分输入信息………………
[root@linuxprobe ~]# du -sh /newFS
39M /newFS/
```
细心的读者一定还记得，前面在讲解mount命令时提到，使用mount命令挂载的设备文件会在系统下一次重启的时候失效。如果想让这个设备文件的挂载永久有效，则需要把挂载的信息写入配置文件中：
```bash
[root@linuxprobe ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Tue Jul 21 05:03:40 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
/dev/mapper/rhel-root                     /                      xfs      defaults        0 0
UUID=812b1f7c-8b5b-43da-8c06-b9999e0fe48b /boot                  xfs      defaults        0 0
/dev/mapper/rhel-swap                     swap                   swap     defaults        0 0
/dev/cdrom                                /media/cdrom           iso9660  defaults        0 0
**/dev/sdb1                                 /newFS                 xfs      defaults        0 0**
```
**出现问题?大胆提问!**
因读者们硬件不同或操作错误都可能导致实验配置出错，请耐心再仔细看看操作步骤吧，不要气馁~
Linux技术交流学习请加读者群（**推荐**）：[https://www.linuxprobe.com/club](https://www.linuxprobe.com/club)

- 本群特色：确保每一位群友都是《Linux就该这么学》的读者，答疑更有针对性，不定期领取定制礼品。
### **6.6 添加交换分区**
交换（SWAP）分区是一种通过在硬盘中预先划分一定的空间，然后把内存中暂时不常用的数据临时存放到硬盘中，以便腾出物理内存空间让更活跃的程序服务来使用的技术，其设计目的是为了解决真实物理内存不足的问题。只有当真实的物理内存耗尽后才会调用交换分区的资源。
交换分区的划分建议：在生产环境中，交换分区的大小一般为真实物理内存的1.5～2倍。为了让大家更明显地感受交换分区空间的变化，这里取出一个大小为5GB的主分区作为交换分区资源：
```bash
[root@linuxprobe ~]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Command (m for help): **n**
Partition type
p   primary (1 primary, 0 extended, 3 free)
e   extended (container for logical partitions)
Select (default p): **p**
Partition number (2-4, default 2): **敲击回车即可**
First sector (4196352-41943039, default 4196352): **敲击回车即可**
Last sector, +sectors or +size{K,M,G,T,P} (4196352-41943039, default 41943039): **+5G**

Created a new partition 2 of type 'Linux' and of size 5 GiB.
```
在上面的操作结束后，我们就得到了一个容量为5GB的新分区。然后尝试修改硬盘的标识码，这里将其改成82（Linux swap）以方便以后知道它的作用：
```bash
Command (m for help): **t**
Partition number (1,2, default 2): **2**
Hex code (type L to list all codes): **82**

Changed type of partition 'Linux' to 'Linux swap / Solaris'.

Command (m for help): **p**
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x88b2c2b0

Device     Boot   Start      End  Sectors Size Id Type
/dev/sdb1          2048  4196351  4194304   2G 83 Linux
**/dev/sdb2       4196352 14682111 10485760   5G 82 Linux swap / Solaris**
```
搞定！敲击w参数退出分区表编辑工具：
```bash
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
下面来看一下两个与交换分区相关的简单命令。
mkswap命令用于对新设备进行交换分区格式化，英文全称为“make swap”，语法格式为“mkswap设备名称”。
```bash
[root@linuxprobe ~]# mkswap /dev/sdb2
Setting up swapspace version 1, size = 5 GiB (5368705024 bytes)
no label, UUID=45a4047c-49bf-4c88-9b99-f6ac93908485
```
swapon命令用于激活新的交换分区设备，英文全称为“swap on”，语法格式为“swapon设备名称”。
使用swapon命令把准备好的SWAP硬盘设备正式挂载到系统中。可以使用free -m命令查看交换分区的大小变化（由2047MB增加到7167MB）：
```bash
[root@linuxprobe ~]# free -m
total        used        free      shared  buff/cache   available
Mem:           1966        1391         105          12         469         384
**Swap:          2047           9        2038**
[root@linuxprobe ~]# swapon /dev/sdb2
[root@linuxprobe ~]# free -m
total        used        free      shared  buff/cache   available
Mem:           1966        1395         101          12         469         380
**Swap:          7167           9        7158**
```
为了能够让新的交换分区设备在重启后依然生效，需要按照下面的格式将相关信息写入配置文件中，并记得保存：
```bash
[root@linuxprobe ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Tue Jul 21 05:03:40 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
/dev/mapper/rhel-root                        /               xfs        defaults    1 1
UUID=812b1f7c-8b5b-43da-8c06-b9999e0fe48b    /boot           xfs        defaults    1 2
/dev/mapper/rhel-swap                        swap            swap       defaults    0 0
/dev/cdrom                                   /media/cdrom    iso9660    defaults    0 0
/dev/sdb1                                    /newFS          xfs        defaults    0 0
**/dev/sdb2                                    swap            swap       defaults    0 0**
```
### **6.7 磁盘容量配额**
可以使用quota技术进行磁盘容量配额管理，从而限制用户的硬盘可用容量或所能创建的最大文件个数。quota技术还有软限制和硬限制的功能。
**软限制**：当达到软限制时会提示用户，但仍允许用户在限定的额度内继续使用。
**硬限制**：当达到硬限制时会提示用户，且强制终止用户的操作。
RHEL 8系统中已经安装了quota磁盘容量配额服务程序包，但存储设备却默认没有开启对quota技术的支持，此时需要手动编辑配置文件并重启一次系统，让系统中的启动目录（/boot）能够支持quota磁盘配额技术。
```bash
[root@linuxprobe ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Tue Jul 21 05:03:40 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
/dev/mapper/rhel-root                        /             xfs        defaults         1 1
UUID=812b1f7c-8b5b-43da-8c06-b9999e0fe48b    /boot         xfs        defaults**,uquota**  1 2
/dev/mapper/rhel-swap                        swap          swap       defaults         0 0
/dev/cdrom                                   /media/cdrom  iso9660    defaults         0 0
/dev/sdb1                                    /newFS        xfs        defaults         0 0
/dev/sdb2                                    swap          swap       defaults         0 0
[root@linuxprobe ~]# reboot
```
另外，对于学习过早期的Linux系统，或者具有RHEL 5/6系统使用经验的读者来说，这里需要特别注意。早期的Linux系统要想让硬盘设备支持quota磁盘容量配额服务，使用的是usrquota参数，而RHEL 7/8系统使用的则是uquota参数。在重启系统后使用mount命令查看，即可发现/boot目录已经支持quota磁盘配额技术了：
```
[root@linuxprobe ~]# mount | grep boot
/dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,**usrquota**)
```
接下来创建一个用于检查quota磁盘容量配额效果的用户tom，并针对/boot目录增加其他人的写权限，保证用户能够正常写入数据：
```
[root@linuxprobe ~]# useradd tom
[root@linuxprobe ~]# chmod -R o+w /boot
```
xfs_quota命令用于管理设备的磁盘容量配额，语法格式为“xfs_quota [参数] 配额 文件系统”。
这是一个专门针对XFS文件系统来管理quota磁盘容量配额服务而设计的命令。其中，-c参数用于以参数的形式设置要执行的命令；-x参数是专家模式，让运维人员能够对quota服务进行更多复杂的配置。接下来使用xfs_quota命令来设置用户tom对/boot目录的quota磁盘容量配额。具体的限额控制包括：硬盘使用量的软限制和硬限制分别为3MB和6MB；创建文件数量的软限制和硬限制分别为3个和6个。
```bash
[root@linuxprobe ~]# xfs_quota -x -c 'limit bsoft=3m bhard=6m isoft=3 ihard=6 tom' /boot
[root@linuxprobe ~]# xfs_quota -x -c report /boot
User quota on /boot (/dev/sda1)
Blocks
User ID          Used       Soft       Hard    Warn/Grace
---------- --------------------------------------------------
root           114964          0          0     00 [--------]
tom                 0       3072       6144     00 [--------]
```
上面所使用的参数分为两组，分别是isoft/ihard与bsoft/bhard，下面深入讲解一下。在6.3节中曾经讲过，在Linux系统中每个文件都会使用一个独立的inode信息块来保存属性信息，一个文件对应一个inode信息块，所以isoft和ihard就是通过限制系统最大使用的inode个数来限制了文件数量。bsoft和bhard则是代表文件所占用的block大小，也就是文件占用的最大容量的总统计。
soft是软限制，超过该限制后也只是将操作记录写到日志中，不对用户行为进行限制。而hard是硬限制，一旦超过系统就会马上禁止，用户再也不能创建或新占任何的硬盘容量。
当配置好上述各种软硬限制后，尝试切换到一个普通用户，然后分别尝试创建一个体积为5MB和8MB的文件。可以发现，在创建8MB的文件时受到了系统限制：
```
[root@linuxprobe ~]# su - tom
[tom@linuxprobe ~]$ cd /boot
[tom@linuxprobe boot]$ dd if=/dev/zero of=/boot/tom bs=5M count=1
1+0 records in
1+0 records out
5242880 bytes (5.2 MB, 5.0 MiB) copied, 0.00298178 s, 1.8 GB/s
[tom@linuxprobe boot]$ dd if=/dev/zero of=/boot/tom bs=8M count=1
dd: error writing '/boot/tom': Disk quota exceeded
1+0 records in
0+0 records out
4194304 bytes (4.2 MB, 4.0 MiB) copied, 0.00398607 s, 1.1 GB/s
```
edquota命令用于管理系统的磁盘配额，英文全称为“edit quota”，语法格式为“edquota [参数] 用户名”。
在为用户设置了quota磁盘容量配额限制后，可以使用edquota命令按需修改限额的数值。其中，-u参数表示要针对哪个用户进行设置；-g参数表示要针对哪个用户组进行设置，如表6-6所示。
|表6-6|edquota命令中可用的参数以及作用| |参数|作用| |-u|对某个用户进行设置| |-g|对某个用户组进行设置| |-p|复制原有的规则到新的用户/组| |-t|限制宽限期限|
edquota命令会调用Vi或Vim编辑器来让root管理员修改要限制的具体细节，记得用wq保存退出。下面把用户tom的硬盘使用量的硬限额从5MB提升到8MB：
```bash
[tom@linuxprobe ~]$ exit
[root@linuxprobe ~]# edquota -u tom
Disk quotas for user tom (uid 1001):
Filesystem                   blocks       soft       hard     inodes     soft     hard
/dev/sda1                      4096       3072   **    8192  **        1        3        6
[root@linuxprobe ~]# su - tom
[tom@linuxprobe ~]$ cd /boot
[tom@linuxprobe boot]$ dd if=/dev/zero of=/boot/tom bs=8M count=1
1+0 records in
1+0 records out
8388608 bytes (8.4 MB, 8.0 MiB) copied, 0.0185476 s, 452 MB/s
```
### **6.8 VDO虚拟数据优化**
VDO（Virtual Data Optimize，虚拟数据优化）是一种通过压缩或删除存储设备上的数据来优化存储空间的技术。VDO是红帽公司收购了Permabit公司后获取的新技术，并与2019-2020年前后，多次在RHEL 7.5/7.6/7.7上进行测试，最终随RHEL 8系统正式公布。VDO技术的关键就是对硬盘内原有的数据进行删重操作，它有点类似于我们平时使用的网盘服务，在第一次正常上传文件时速度特别慢，在第二次上传相同的文件时仅作为一个数据指针，几乎可以达到“秒传”的效果，无须再多占用一份空间，也不用再漫长等待。除了删重操作，VDO技术还可以对日志和数据库进行自动压缩，进一步减少存储浪费的情况。VDO针对各种类型文件的压缩效果如表6-7所示。

| 表6-7 | 对各种类型文件压缩效果汇总表 |
| --- | --- |

| 文件名 | 描述 | 类型 | 原始大小（KB） | 实际占用空间（KB） |
| --- | --- | --- | --- | --- |
| dickens | 狄更斯文集 | 英文原文 | 9953 | 9948 |
| mozilla | Mozilla的1.0可执行文件 | 执行程序 | 50020 | 33228 |
| mr | 医用resonanse图像 | 图片 | 9736 | 9272 |
| nci | 结构化的化学数据库 | 数据库 | 32767 | 10168 |
| ooffice | Open  office
 1.01 DLL | 可执行程序 | 6008 | 5640 |
| osdb | 基准测试用的MySQL格式示例数据库 | 数据库 | 9849 | 9824 |
| reymont | 瓦迪斯瓦夫·雷蒙特的书 | PDF | 6471 | 6312 |
| samba | samba源代码 | src源码 | 21100 | 11768 |
| sao | 星空数据 | 天文格式的bin文件 | 7081 | 7036 |
| webster | 辞海 | HTML | 40487 | 40144 |
| xml | XML文件 | HTML | 5220 | 2180 |
| x-ray | 透视医学图片 | 医院数据 | 8275 | 8260 |

VDO技术支持本地存储和远程存储，可以作为本地文件系统、iSCSI或Ceph存储下的附加存储层使用。红帽公司在VDO介绍页面中提到，在部署虚拟机或容器时，建议采用逻辑存储与物理存储为10∶1的比例进行配置，即1TB物理存储对应10TB逻辑存储；而部署对象存储时 （例如使用Ceph）则采用逻辑存储与物理存储为3∶1的比例进行配置，即使用1TB物理存储对应3TB逻辑存储。
简而言之，VDO技术能省空间！
有两种特殊情况需要提前讲一下。其一，公司服务器上已有的dm-crypt之类的技术是可以与VDO技术兼容的，但记得要先对卷进行加密再使用VDO。因为加密会使重复的数据变得有所不同，因此删重操作无法实现。要始终记得把加密层放到VDO之下，如图6-14所示。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684061591432-5235ba24-69e9-42e9-935e-4a1866524364.png#averageHue=%23fbfdfc&clientId=u62c829e8-bcc7-4&from=paste&id=u3ad7493c&originHeight=341&originWidth=332&originalType=url&ratio=1&rotation=0&showTitle=false&size=56886&status=done&style=none&taskId=u1443c985-03d8-4c52-81b0-569f8407b01&title=)
图6-14 VDO技术拓扑图
其二，VDO技术不可叠加使用，1TB的物理存储提升成10TB的逻辑存储没问题，但是再用10TB翻成100TB就不行了。左脚踩右脚，真的没法飞起来。
通过6.5节的学习，相信同学们已经把对硬盘进行分区、格式化、挂载操作的方法拿捏得死死的了。我们把虚拟机关闭，添加一块容量为20GB的新SATA硬盘进来，开机后就能看到这块名称为/dev/sdc的新硬盘了：
```bash
[root@linuxprobe ~]# ls -l /dev/sdc
brw-rw----. 1 root disk 8, 32 Jan 6 22:26 /dev/sdc

RHEL/CentOS 8系统中默认已经启用了VDO技术。VDO技术现在是红帽公司自己的技术，兼容性自然没得说。如果您所用的系统没有安装VDO的话也不要着急，用dnf命令即可完成安装：

[root@linuxprobe ~]# dnf install kmod-kvdo vdo
Updating Subscription Management repositories.
Unable to read consumer identity
This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
Last metadata expiration check: 0:01:56 ago on Wed 06 Jan 2021 10:37:19 PM CST.
Package kmod-kvdo-6.2.0.293-50.el8.x86_64 is already installed.
Package vdo-6.2.0.293-10.el8.x86_64 is already installed.
Dependencies resolved.
Nothing to do.
Complete!
```
首先，创建一个全新的VDO卷。
新添加进来的物理设备就是使用vdo命令来管理的，其中name参数代表新的设备卷的名称；device参数代表由哪块磁盘进行制作；vdoLogicalSize参数代表制作后的设备大小。依据红帽公司推荐的原则，20GB硬盘将翻成200GB的逻辑存储：
```bash
[root@linuxprobe ~]# vdo create --name=storage --device=/dev/sdc --vdoLogicalSize=200G
Creating VDO storage
Starting VDO storage
Starting compression on VDO storage
VDO instance 0 volume is ready at /dev/mapper/storage
```
**Tips**
Linux命令行严格区别大小写，vdoLogicalSize参数中的L与S字母必须大写。
在创建成功后，使用status参数查看新建卷的概述信息：
```bash
[root@linuxprobe ~]# vdo status --name=storage
VDO status:
Date: '2021-01-06 22:51:33+08:00'
Node: linuxprobe.com
Kernel module:
Loaded: true
Name: kvdo
Version information:
kvdo version: 6.2.0.293
Configuration:
File: /etc/vdoconf.yml
Last modified: '2021-01-06 22:49:33'
VDOs:
storage:
Acknowledgement threads: 1
Activate: enabled
Bio rotation interval: 64
Bio submission threads: 4
Block map cache size: 128M
Block map period: 16380
Block size: 4096
CPU-work threads: 2
Compression: enabled
Configured write policy: auto
Deduplication: enabled
………………省略部分输出信息………………
```
有上可见，在输出信息中包含了VDO卷创建的时间、主机名、版本、是否压缩（Compression）及是否删重（Deduplication）等关键信息。
接下来，对新建卷进行格式化操作并挂载使用。
新建的VDO卷设备会被乖乖地存放在/dev/mapper目录下，并以设备名称命名，对它操作就行。另外，挂载前可以用udevadm settle命令对设备进行一次刷新操作，避免刚才的配置没有生效：
```bash
[root@linuxprobe ~]# mkfs.xfs /dev/mapper/storage
meta-data=/dev/mapper/storage    isize=512    agcount=4, agsize=13107200 blks
=                       sectsz=4096  attr=2, projid32bit=1
=                       crc=1        finobt=1, sparse=1, rmapbt=0
=                       reflink=1
data     =                       bsize=4096   blocks=52428800, imaxpct=25
=                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=25600, version=2
=                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@linuxprobe ~]# udevadm settle
[root@linuxprobe ~]# mkdir /storage
[root@linuxprobe ~]# mount /dev/mapper/storage /storage
```
如果想查看设备的实际使用情况，使用vdostats命令即可。human-readable参数的作用是将存储容量自动进位，以人们更易读的方式输出（比如，显示20G而不是20971520K）：
```bash
[root@linuxprobe ~]# vdostats --human-readable
Device                    Size      Used Available Use% Space saving%
/dev/mapper/storage      20.0G      4.0G     16.0G  20%           99%
```
这里显示的Size是实际物理存储的空间大小（即20.0GB是硬盘的大小），如果想看逻辑存储空间，可以使用df命令进行查看：
```bash
[root@linuxprobe ~]# df -h
Filesystem             Size  Used Avail Use% Mounted on
devtmpfs               969M     0  969M   0% /dev
tmpfs                  984M     0  984M   0% /dev/shm
tmpfs                  984M  9.6M  974M   1% /run
tmpfs                  984M     0  984M   0% /sys/fs/cgroup
/dev/mapper/rhel-root   17G  3.9G   14G  23% /
/dev/sr0               6.7G  6.7G     0 100% /media/cdrom
/dev/sda1             1014M  152M  863M  15% /boot
tmpfs                  197M   16K  197M   1% /run/user/42
tmpfs                  197M  3.5M  194M   2% /run/user/0
/dev/sdb1              2.0G   47M  2.0G   3% /newFS
**/dev/mapper/storage    200G  2.4G  198G   2% /storage**
```
随便复制一个大文件过来，看看占用了多少容量，以及空间节省率（Space saving）是多少：
```bash
[root@linuxprobe ~]# ls -lh /media/cdrom/images/install.img
-r--r--r--. 1 root root 448M Apr 4 2019 /media/cdrom/images/install.img
[root@linuxprobe ~]# cp /media/cdrom/images/install.img /storage/
[root@linuxprobe ~]# ls -lh /storage/install.img
-r--r--r--. 1 root root 448M Jan  6 23:06 /storage/install.img
[root@linuxprobe ~]# vdostats --human-readable
Device                    Size      Used Available Use% Space saving%
/dev/mapper/storage      20.0G      4.4G     15.6G  22%           18%
```
效果不明显，再复制一份相同的文件过来，看看这次占用了多少空间：
```bash
[root@linuxprobe ~]# cp /media/cdrom/images/install.img /storage/rhel.img
[root@linuxprobe ~]# vdostats --human-readable
Device                    Size      Used Available Use% Space saving%
/dev/mapper/storage      20.0G      4.5G     15.5G  22%           55%
```
是不是感觉很棒？！原先448MB的文件这次只占用了不到100MB的容量，空间节省率也从18%提升到了55%。当然这还仅仅是两次操作而已，好处就已经如此明显了。
最后，将设备设置成永久挂载生效，一直提供服务。
VDO设备卷在创建后会一直存在，但需要手动编辑/etc/fstab文件后才能在下一次重启后自动挂载生效，为我们所用。对于这种逻辑存储设备，其实不太建议使用/dev/mapper/storage作为设备名进行挂载。不如试试前面所说的UUID吧：
```bash
[root@linuxprobe ~]# blkid /dev/mapper/storage
/dev/mapper/storage: UUID="cd4e9f12-e16a-415c-ae76-8de069076713" TYPE="xfs"
```
打开/etc/fstab文件，把对应的字段填写完整。建议再加上_netdev参数，表示等系统及网络都启动后再挂载VDO设备卷，以保证万无一失。
```bash
[root@linuxprobe ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Tue Jul 21 05:03:40 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
/dev/mapper/rhel-root                        /             xfs        defaults           1 1
UUID=812b1f7c-8b5b-43da-8c06-b9999e0fe48b    /boot         xfs        defaults,uquota    1 2
/dev/mapper/rhel-swap                        swap          swap       defaults           0 0
/dev/cdrom                                   /media/cdrom  iso9660    defaults           0 0
/dev/sdb1                                    /newFS        xfs        defaults           0 0
/dev/sdb2                                    swap          swap       defaults           0 0
**UUID=cd4e9f12-e16a-415c-ae76-8de069076713    /storage      xfs        defaults,_netdev   0 0**
```
### **6.9 软硬方式链接**
在引领大家学习完本章所有的硬盘管理知识之后，刘遄老师终于可以放心大胆地讲解Linux系统中的“快捷方式”了。在Windows系统中，快捷方式就是指向原始文件的一个链接文件，可以让用户从不同的位置来访问原始的文件；原文件一旦被删除或剪切到其他地方，会导致链接文件失效。但是，这个看似简单的东西在Linux系统中可不太一样。
Linux系统中存在软链接和硬链接两种不同的类型。

- *软链接（soft link）：**也叫符号链接（symbolic link），仅仅包含所链接文件的名称和路径，很像一个记录地址的标签。当原始文件被删除或移动后，新的链接文件也会随之失效，不能被访问。可以针对文件、目录设置软链接，跨文件系统进行链接也不是问题。从这一点来看，它与Windows系统的“快捷方式”具有一样的性质。用户访问软链接的效果如图6-15所示。
- *硬链接（hard link）：**可以将它理解为一个“指向原始文件block的指针”，系统会创建出一个与原来一模一样的inode信息块。所以，硬链接文件与原始文件其实是一模一样的，只是名字不同。每添加一个硬链接，该文件的inode个数就会增加1；而且只有当该文件的inode个数为0时，才算彻底将它删除。换言之，由于硬链接实际上是指向原文件block的指针，因此即便原始文件被删除，依然可以通过硬链接文件来访问。需要注意的是，由于技术的局限性，不能跨分区对目录文件进行硬链接。用户访问硬链接的效果如图6-16所示。

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684061591496-5f0a429b-9de6-492a-9c21-f130458b096b.png#averageHue=%23fefefc&clientId=u62c829e8-bcc7-4&from=paste&id=u2e2789b3&originHeight=254&originWidth=646&originalType=url&ratio=1&rotation=0&showTitle=false&size=75127&status=done&style=none&taskId=u68e5a6f0-cd6a-4b77-af90-c00c179d40f&title=)
图6-15 软链接原理示意图
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684061592382-cde6d924-42c2-46d1-8fa8-3e0d17786f3a.png#averageHue=%23fefefc&clientId=u62c829e8-bcc7-4&from=paste&id=u2b6b3d9d&originHeight=262&originWidth=650&originalType=url&ratio=1&rotation=0&showTitle=false&size=119984&status=done&style=none&taskId=u613a527c-22fa-4f03-b4d0-ea20f0a83d8&title=)
图6-16 硬链接原理示意图
**Tips**
大家翻开手头这本书的目录页，看一下目录标题和对应的页码就应该能够理解了。链接文件就是指向实际内容所在位置的一个标签，通过这个标签，可以找到对应的数据。
ln命令用于创建文件的软硬链接，英文全称为“link”，语法格式为“ln [参数]原始文件名 链接文件名”。
ln命令的可用参数以及作用如表6-8所示。在使用ln命令时，是否添加-s参数，将创建出性质不同的两种“快捷方式”。因此如果没有扎实的理论知识和实践经验做铺垫，尽管能够成功完成实验，但永远不会明白为什么会成功。

| 表6-8 | ln命令中可用的参数以及作用 |
| --- | --- |
| 参数 | 作用 |
| -s | 创建“符号链接”（如果不带-s参数，则默认创建硬链接） |
| -f | 强制创建文件或目录的链接 |
| -i | 覆盖前先询问 |
| -v | 显示创建链接的过程 |

为了更好地理解软链接、硬链接的不同性质，我们先创建出一个文件，为其创建一个软链接：
```bash
[root@linuxprobe ~]# echo "Welcome to linuxprobe.com" > old.txt
[root@linuxprobe ~]# ln -s old.txt new.txt
[root@linuxprobe ~]# cat old.txt
Welcome to linuxprobe.com
[root@linuxprobe ~]# cat new.txt
Welcome to linuxprobe.com
[root@linuxprobe ~]# ls -l old.txt
-rw-r--r-- 1 root root 26 Jan 11 00:08 old.txt
```
原始文件名为old，新的软链接文件名为new。删掉原始文件后，软链接文件立刻就无法读取了：
```bash
[root@linuxprobe ~]# rm -f old.txt
[root@linuxprobe ~]# cat new.txt
cat: readit.txt: No such file or directory
```
接下来针对原始文件old创建一个硬链接，即相当于针对原始文件的硬盘存储位置创建了一个指针。这样一来，新创建的这个硬链接就不再依赖于原始文件的名称等信息，也不会因为原始文件的删除而导致无法读取了。同时可以看到创建硬链接后，原始文件的硬盘链接数量增加到了2。
```bash
[root@linuxprobe ~]# echo "Welcome to linuxprobe.com" > old.txt
[root@linuxprobe ~]# ln old.txt new.txt
[root@linuxprobe ~]# cat old.txt
Welcome to linuxprobe.com
[root@linuxprobe ~]# cat new.txt
Welcome to linuxprobe.com
[root@linuxprobe ~]# ls -l old.txt
-rw-r--r-- **2** root root 26 Jan 11 00:13 old.txt
```
这是一个非常有意思的现象。创建的硬链接文件竟然会让文件属性第二列的数字变成了2，这个数字表示的是文件的inode信息块的数量。相信同学们已经非常肯定地知道，即便删除了原始文件，新的文件也会一如既往地可以读取，因为只有当文件inode数量被“清零”时，才真正代表这个文件被删除了。
```bash
[root@linuxprobe ~]# rm -f old.txt
[root@linuxprobe ~]# cat new.txt
Welcome to linuxprobe.com
```
# 九、**Linux用户管理**
用户信息配置文件在`/etc/passwd`中
用户ID和用户组ID：uid和gid，uid为0时，代表root用户，普通用户的uid从1000开始
创建普通用户：`useradd zhangsan`
创建普通用户并指定所属用户组，且指定SHELL：`useradd zhangsan -g CWB /bin/bash`
普通用户的家目录默认在`/home`目录下，以用户名命令目录，并且会在目录中创建3个`.bash*`文件，这些文件可以在`/etc/skel`目录中获取
常用命令解释器
```bash
/bin/sh	默认
/bin/bash	默认
/sbin/nologin	虚拟用户，无法登录
/dash	ubuntu
csh	unix
tsh	unix
```
| 命令 | 作用 |
| --- | --- |
| useradd | 创建用户 |
| usermod | 修改用户信息 |
| userdel | 删除用户及配置文件 |
| passwd | 更改用户密码 |
| chpasswd | 批量更新用户密码,无参数，直接使用，输入后回车每行输入 ‘用户名:密码’，ctrl+d结束 |
| change | 修改用户密码属性 |
| id | 查看用户UID、GID、组信息 |
| su | 切换用户 |
| sudo | 用root身份执行命令 |
| visduo | 编辑sudoers配置文件 |

**useradd 参数:**
```bash
-c<备注>：加上备注文字，备注文字会保存在/etc/passwd的备注栏中
-d：指定用户家目录
-D：变更预设值
-e：指定账户有效期
-f：指定密码过期多少天后关闭该账户
-g：指定用户所属组
-G：指定用户所属的附加群组
-m：自动建立用户的登入目录
-M：不自动建立用户的登入目录
-n：取消建立以用户名称为名的群组
-r：建立系统账号
-s：指定用户登入后使用的shell
-u：指定用户id
```
**用户创建流程：**

1. useradd zhangsan
2. 系统读取`/etc/login.defs`（用户定义文件），和`/etc/default/useradd`（用户默认配置文件）两个文件中定义的队则创建新用户
3. 向`/etc/passwd`和`/etc/group`文件中添加用户和组信息，向`/etc/shadow`和`/etc/gshadow`中添加密码信息
4. 根据`/etc/default/useradd`文件中配置的信息创建用户家目录
5. 把`/etc/shel`中所有的文件复制到新用户家目录中

**useradd参数**
```bash
-c/d/e/f/g/G/s/u 与useradd参数作用相同
-l：修改用户账号名称
-L：锁定用户密码，使密码无效
-U：解除密码锁定
```
**userdel参数**
```bash
-f：强制删除用户，即使用户当前已登录
-r：删除用户的同时，删除与用户相关的所有文件
```

- whomin 查看当前登录用户
- who/w 同上
- last 显示上次登录的用户的详细信息
- lastlog 类同last

**切换用户**
`su - 用户名`    ##不加`-`不切换环境变量

# 十、**Linux权限管理之ACL权限**
**目录**

- 1、什么是 ACL 权限？
- 2、查看分区 ACL 权限是否开启：dump2fs
   - ①、查看当前系统有哪些分区：df -h
   - ②、查看指定分区详细文件信息：dumpe2fs -h 分区路径
- 3、开启分区 ACL 权限
   - ①、临时开启分区 ACL 权限
   - ②、永久开启分区 ACL 权限
- 4、设定 ACL 权限：setfacl 选项 文件名
   - ①、给用户设定 ACL 权限：setfacl -m u:用户名:权限 指定文件名
   - ②、给用户组设定 ACL 权限:setfacl -m g:组名:权限 指定文件名
- 5、查看 ACL 权限：getfacl 文件名
- 6、最大有效权限 mask
- 7、删除 ACL 权限
- 8、递归 ACL 权限
- 9、默认 ACL 权限
- [10、总结](https://www.cnblogs.com/ysocean/p/7801329.html#_label9)

---

通过前面的两篇博客我们介绍了Linux系统的用户管理，Linux用户和用户组管理之相关配置文件 讲解了用户管理的相关配置文件，包括用户信息文件/etc/passwd，用户密码文件/etc/shadow；然后介绍了用户组信息文件/etc/group，用户组密码文件/etc/gshadow。用户的家目录，以及用户的模板目录；Linux用户和用户组管理之用户管理命令 讲解了管理用户和用户组的命令，包括新建、修改、查看等等以及用的比较多的切换用户命令 su。那么用户管理结束之后，我们将进入linux的权限管理介绍，本篇博客介绍的是Linux权限管理的ACL权限。
### 1、什么是 ACL 权限？
比如有如下场景：
某大牛在QQ群内直播讲解Linux系统的权限管理，讲解完之后，他在一个公有的Linux系统中创建了一个 /project 目录，里面存放的是课后参考资料。那么 /project 目录对于大牛而言是所有者，拥有读写可执行（rwx）权限，对于QQ群内的所有用户他们都分配的一个所属组里面，也都拥有读写可执行（rwx）权限，而对于 QQ 群外的其他人，那么我们不给他访问/project 目录的任何权限，那么 /project 目录的所有者和所属组权限都是（rwx），其他人权限无。
问题来了，这时候直播有旁听的人参与（不属于QQ群内），听完之后，我们允许他访问/project目录查看参考资料，但是不能进行修改，也就是拥有（r-x）的权限，这时候我们该怎么办呢？我们知道一个文件只能有一个所属组，我们将他分配到QQ群所在的所属组内，那么他拥有了写的权限，这是不被允许的；如果将这个旁听的人视为目录/project 的其他人，并且将/project目录的其他人权限改为（r-x），那么不是旁听的人也能访问我们/project目录了，这显然也是不被允许的。怎么解决呢？
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684071305710-335ba751-d928-4c98-a503-00f1f2afaee2.png#averageHue=%23f9f5ec&clientId=u62c829e8-bcc7-4&from=paste&id=u6dedd3b4&originHeight=393&originWidth=785&originalType=url&ratio=1&rotation=0&showTitle=false&size=189163&status=done&style=none&taskId=u7d3e019b-9aed-4109-840e-2d295b434b2&title=)
我们想想windows系统里面给某个文件分配权限的办法：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684071305425-65fc97e9-7b26-4a09-b62f-0f0587bd5b53.png#averageHue=%23f7f6f5&clientId=u62c829e8-bcc7-4&from=paste&id=ufd7841ec&originHeight=664&originWidth=534&originalType=url&ratio=1&rotation=0&showTitle=false&size=33221&status=done&style=none&taskId=uac9fe5a9-487b-4c18-8ef2-8322cbaaa78&title=)
如上图，我们想要让某个用户不具备某个权限，直接不给他分配这个目录的相应权限就行了。那么对应到Linux系统也是这样，我们给指定的用户指定目录分配指定的权限，也就是 ACL 权限分配。
### 2、查看分区 ACL 权限是否开启：dump2fs
我们看某个文件（Linux系统中目录也是文件，一切皆是文件）是否支持 ACL 权限，首先要看文件所在的分区是否支持 ACL 权限。
### ①、查看当前系统有哪些分区：df -h
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684071305427-89cec99d-d4b4-44cf-8a6e-e62c2530898f.png#averageHue=%230b0805&clientId=u62c829e8-bcc7-4&from=paste&id=u30119580&originHeight=159&originWidth=464&originalType=url&ratio=1&rotation=0&showTitle=false&size=13294&status=done&style=none&taskId=ueeede81b-e7eb-4ce7-8330-e48dfdcc7bd&title=)
### ②、查看指定分区详细文件信息：dumpe2fs -h 分区路径
下面是查看 根分区/ 的详细文件信息
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684071305549-90fd98b4-4019-4172-90c7-53e20fa2897a.png#averageHue=%23090503&clientId=u62c829e8-bcc7-4&from=paste&id=u9a4dbc18&originHeight=347&originWidth=847&originalType=url&ratio=1&rotation=0&showTitle=false&size=37641&status=done&style=none&taskId=u6223900b-b896-4740-b16f-5dd650dc75a&title=)
### 3、开启分区 ACL 权限
#### ①、临时开启分区 ACL 权限
```bash
mount -o remount,acl /
```
重新挂载根分区，并挂载加入 acl 权限。注意这种命令开启方式，如果系统重启了，那么根分区权限会恢复到初始状态。
#### ②、永久开启分区 ACL 权限
一、修改配置文件 /etc/fstab
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684071305487-605174aa-c115-4fa6-ae18-1ddb3b04e970.png#averageHue=%23050000&clientId=u62c829e8-bcc7-4&from=paste&id=ua763cc4f&originHeight=294&originWidth=1052&originalType=url&ratio=1&rotation=0&showTitle=false&size=41590&status=done&style=none&taskId=u149f37a9-2355-4deb-8c00-997fac3c5ed&title=)
上面是修改根分区拥有 acl 权限
```bash
UUID=490ed737-f8cf-46a6-ac4b-b7735b79fc63 /                       ext4    defaults,acl11
```
二、重新挂载文件系统或重启系统，使得修改生效
```bash
mount -o remount /
```
### 4、设定 ACL 权限：setfacl 选项 文件名
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684071306448-85fab0f6-b59d-46f5-9781-e72c5394e7a4.png#averageHue=%23efefef&clientId=u62c829e8-bcc7-4&from=paste&id=ua0e23d11&originHeight=289&originWidth=498&originalType=url&ratio=1&rotation=0&showTitle=false&size=44764&status=done&style=none&taskId=u901aef16-9ea8-4592-ac4c-cab9ab8bac8&title=)
### ①、给用户设定 ACL 权限：setfacl -m u:用户名:权限 指定文件名
### ②、给用户组设定 ACL 权限:setfacl -m g:组名:权限 指定文件名
注意：我们给用户或用户组设定 ACL 权限其实并不是真正我们设定的权限，是与 mask 的权限“相与”之后的权限才是用户的真正权限，一般默认mask权限都是rwx，与我们所设定的权限相与就是我们设定的权限。mask 权限下面我们会详细讲解
范例：所有者root用户在根目录下创建一个文件目录/project，然后创建一个QQ群所属组，所属组里面创建两个用户zhangsan和lisi。所有者和所属组权限和其他人权限是770。
然后创建一个旁听用户 pt，给他设定/project目录的 ACL 为 r-x。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684071306552-d67742a5-a7df-4043-98fe-0270bf8ae5bf.png#averageHue=%230f0503&clientId=u62c829e8-bcc7-4&from=paste&id=ue78c7bb3&originHeight=264&originWidth=743&originalType=url&ratio=1&rotation=0&showTitle=false&size=40432&status=done&style=none&taskId=ue1937244-7673-470e-b3a5-404c776d89b&title=)
目录 /project 的所有者和所属组其他人权限设定为 770。接下来我们创建旁听用户 pt，并赋予 acl 权限 rx
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684071306855-865b18c2-91d6-4899-bb23-d30e40507343.png#averageHue=%23080403&clientId=u62c829e8-bcc7-4&from=paste&id=u20b0c6d9&originHeight=502&originWidth=628&originalType=url&ratio=1&rotation=0&showTitle=false&size=46099&status=done&style=none&taskId=ud518aa8a-f02e-4609-96d2-88b117de53c&title=)
为了验证 pt 用户对于 /project 目录没有写权限，我们用 su 命令切换到 pt 用户，然后进入 /project 目录，在此目录下创建文件，看是否能成功：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684071307033-016c2279-b9c2-4123-bd96-495b8ae80e88.png#averageHue=%230a0705&clientId=u62c829e8-bcc7-4&from=paste&id=u649c5eb5&originHeight=156&originWidth=360&originalType=url&ratio=1&rotation=0&showTitle=false&size=8538&status=done&style=none&taskId=u81ad53fb-fea5-48b5-ad05-0636f3f0e60&title=)
上面提示权限不够，说明 acl 权限赋予成功，注意如下所示，如果某个目录或文件下有 + 标志，说明其具有 acl 权限。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684071307032-3d562717-62ab-4176-980a-fb88b0df5e69.png#averageHue=%230c0403&clientId=u62c829e8-bcc7-4&from=paste&id=u5bc70b93&originHeight=82&originWidth=603&originalType=url&ratio=1&rotation=0&showTitle=false&size=8789&status=done&style=none&taskId=ub9f6f6cd-0141-42c1-b759-59f117c7c83&title=)
### 5、查看 ACL 权限：getfacl 文件名
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684071307656-a97720b3-a3ea-4bb6-ba41-f32a9607058b.png#averageHue=%23040302&clientId=u62c829e8-bcc7-4&from=paste&id=u9d8c57de&originHeight=246&originWidth=525&originalType=url&ratio=1&rotation=0&showTitle=false&size=13142&status=done&style=none&taskId=uf67e5402-9f0b-4baf-82db-c76a4daf63e&title=)
### 6、最大有效权限 mask
前面第4点我们讲过，我们给用户或用户组设定 ACL 权限其实并不是真正我们设定的权限，是与 mask 的权限“相与”之后的权限才是用户的真正权限，一般默认mask权限都是rwx，与我们所设定的权限相与就是我们设定的权限。
我们通过 getfacl 文件名 也能查看 mask 的权限，那么我们怎么设置呢？
```bash
setfacl -m m:权限 文件名
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35951150/1684071308002-16216bbe-e93b-4fe0-b8be-a43d0869f211.png#averageHue=%23050201&clientId=u62c829e8-bcc7-4&from=paste&id=u2065f390&originHeight=264&originWidth=707&originalType=url&ratio=1&rotation=0&showTitle=false&size=18242&status=done&style=none&taskId=ua1d32064-cdbb-451e-8a76-bd170381a1e&title=)
### 7、删除 ACL 权限
①、删除指定用户的 ACL 权限
```bash
setfacl -x u:用户名 文件名
```
②、删除指定用户组的 ACL 权限
```bash
setfacl -x g:组名 文件名
```
③、删除文件的所有 ACL 权限
```bash
setfacl -b 文件名
```
### 8、递归 ACL 权限
通过加上选项 -R 递归设定文件的 ACL 权限，所有的子目录和子文件也会拥有相同的 ACL 权限。
```bash
setfacl -m u:用户名:权限 -R 文件名
```
### 9、默认 ACL 权限
如果给父目录设定了默认的 ACL 权限，那么父目录中所有新建的子文件会继承父目录的 ACL 权限。
```bash
setfacl -m d:u:用户名:权限 文件名
```

# 十一、**Linux主要配置文件**
## 1、网络配置文件：
### ① Debian
`/etc/network/interfaces  #IP地址`
```bash
auto enp2s0
iface enp2s0 inet static
address 192.168.50.240
netmask 255.255.255.0
gateway 192.168.50.254
```
`/etc/resolv.conf`   ##DNS服务
```bash
nameserver 114.114.114.114
nameserver 119.29.29.29
```
### ② Centos
`/etc/sysconfig/network-scripts/ifcfg-ens33`
```bash
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="dhcp"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens33"
UUID="5520443A-5011-4CBC-A405-E4CC7B82CDED"
DEVICE="ens33"
ONBOOT="yes"
```
`/etc/resolv.conf`
```bash
search	localdomain
nameserver	192.168.x.x
```
## 2、系统信息
hostname:系统名称
host
```bash
127.0.0.1       localhost
127.0.1.1       GZY
192.168.50.240  krb1.example.com GZY

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```
`/etc/motd`  :  开机信息
`/etc/os-release` :  系统版本信息
`/proc/meminfo` :  内存信息
`/proc/cpuinfo` : CPU信息
`/proc/loadavg` :  系统负载信息，uptime  的结果
`/proc/mounts` :   已加载的文件系统的列表
## 3、var目录下文件
/var/log:  记录系统及软件运行信息文件所在的目录
/var/log/messages:   系统级别日志文件
/var/log/secure:   用户登录信息日志文件
/var/log/dmesg:  硬件信息加载日志文件

# 十二、NFS
### 1、安装nfs server
```bash
apt-get install nfs-kernel-server
```
### 2、配置参数
```bash
vi /etc/exports
```
```bash
写入：

/mnt *(rw,sync,no_subtree_check,no_root_squash)
```
参数：
```bash
/mnt   ：共享的目录
*       ：指定哪些用户可以访问
            *  所有可以ping同该主机的用户
            192.168.1.*  指定网段，在该网段中的用户可以挂载
            192.168.1.12 只有该用户能挂载
(ro,sync,no_root_squash)：  权限
        ro : 只读
        rw : 读写
        sync :  同步
        no_root_squash: 不降低root用户的权限
    其他选项man 5 exports 查看
#path-to-share是要共享的目录，根据自己情况修改,*表示允许所有的网段访问，也可以使用具体的IP
#   Ubuntu nfs常用的参数有：
#   ro 只读访问
#   rw 读写访问sync 所有数据在请求时写入共享
#   async nfs在写入数据前可以响应请求
#   secure nfs通过1024以下的安全TCP/IP端口发送
#   insecure nfs通过1024以上的端口发送
#   wdelay 如果多个用户要写入nfs目录，则归组写入（默认）
#   no_wdelay 如果多个用户要写入nfs目录，则立即写入，当使用async时，无需此设置。
#   hide 在nfs共享目录中不共享其子目录
#   no_hide 共享nfs目录的子目录
#   subtree_check 如果共享/usr/bin之类的子目录时，强制nfs检查父目录的权限（默认）
#   no_subtree_check 和上面相对，不检查父目录权限
#   all_squash 共享文件的UID和GID映射匿名用户anonymous，适合公用目录。
#   no_all_squash 保留共享文件的UID和GID（默认）
#   root_squash root用户的所有请求映射成如anonymous用户一样的权限（默认）
#   no_root_squas root用户具有根目录的完全管理访问权限
#   anonuid=xxx 指定nfs服务器/etc/passwd文件中匿名用户的UID
#   anongid=xxx 指定nfs服务器/etc/passwd文件中匿名用户的GID
```
使exports的修改立即生效
```bash
exportsfs -a
```
查看共享目录
```bash
showmount -e
```
