# 在CentOS7中安装MySQL5.7

## 1. 卸载

查看MySQL组件：

```bash
rpm -qa | grep -i mysql
```

卸载MySQL：

```bash
yum remove -y mysql mysql-libs mysql-common
```

删除原有库和配置文件：

```bash
rm -rf /var/lib/mysql
rm /etc/my.cnf
```

查看其他MySQL相关文件，进行删除：

```bash
find / -name mysql
```

## 2. 使用RPM方式进行安装

下载yum源，下载之后可以在当前目录下查看到rpm文件：

```bash
wget https://repo.mysql.com//mysql80-community-release-el7-3.noarch.rpm
```

安装yum源：

```bash
rpm -ivh mysql80-community-release-el7-3.noarch.rpm
```

查看MySQL的yum存储库中的所有子存储库，并查看其子存储库的状态是禁用还是启用：

```bash
[root@xii ~]# yum repolist all | grep mysql
mysql-cluster-7.5-community/x86_64 MySQL Cluster 7.5 Community   disabled
mysql-cluster-7.5-community-source MySQL Cluster 7.5 Community - disabled
mysql-cluster-7.6-community/x86_64 MySQL Cluster 7.6 Community   disabled
mysql-cluster-7.6-community-source MySQL Cluster 7.6 Community - disabled
mysql-cluster-8.0-community/x86_64 MySQL Cluster 8.0 Community   disabled
mysql-cluster-8.0-community-source MySQL Cluster 8.0 Community - disabled
mysql-connectors-community/x86_64  MySQL Connectors Community    enabled:    221
mysql-connectors-community-source  MySQL Connectors Community -  disabled
mysql-tools-community/x86_64       MySQL Tools Community         enabled:    135
mysql-tools-community-source       MySQL Tools Community - Sourc disabled
mysql-tools-preview/x86_64         MySQL Tools Preview           disabled
mysql-tools-preview-source         MySQL Tools Preview - Source  disabled
mysql55-community/x86_64           MySQL 5.5 Community Server    disabled
mysql55-community-source           MySQL 5.5 Community Server -  disabled
mysql56-community/x86_64           MySQL 5.6 Community Server    disabled
mysql56-community-source           MySQL 5.6 Community Server -  disabled
mysql57-community/x86_64           MySQL 5.7 Community Server    disabled
mysql57-community-source           MySQL 5.7 Community Server -  disabled
mysql80-community/x86_64           MySQL 8.0 Community Server    enabled:    301
mysql80-community-source           MySQL 8.0 Community Server -  disabled
```

从上面的结果可以看出启用了MySQL 8.0的下载源，而禁用了MySQL 5.7的下载源，如果想安装MySQL 5.7则需要禁用MySQL 8.0下载源而启用MySQL 5.7下载源。

禁用MySQL 8.0的下载源：

```bash
yum-config-manager --disable mysql80-community
```

启用MySQL 5.7的下载源：

```bash
yum-config-manager --enable mysql57-community
```

可以查看设置后的MySQL的yum存储库中子存储库的最新状态。

安装MySQL 5.7：

```bash
yum install -y mysql-community-server
```

## 3. 安装过程

在安装的过程中下载了：

mysql-community-common-5.7.36-1.el7.x86_64.rpm

mysql-community-libs-5.7.36-1.el7.x86_64.rpm

mysql-community-client-5.7.36-1.el7.x86_64 .rpm

libaio-0.3.109-13.el7.x86_64.rpm

mysql-community-server-5.7.36-1.el7.x86_64.rpm

## 4. 安装后的状态

查看MySQL安装后的相关文件：

```bash
[root@xii ~]# find / -name mysql
/usr/share/mysql
/usr/lib64/mysql
/usr/bin/mysql
/var/lib/mysql
/etc/logrotate.d/mysql
/etc/selinux/targeted/active/modules/100/mysql
```

## 5. 配置MySQL

```bash
vim /etc/my.cnf
```

配置文件如下：

```bash
[mysqld]

datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

# 0:大小写敏感 1:大小写不敏感
lower_case_table_names=1
# 默认字符集
character-set-server=utf8
# 默认时区
default-time_zone = '+8:00'
```

## 6. 启动MySQL

```bash
systemctl start mysqld
```

## 7. 设置root用户的密码

安装了MySQL 5.7 之后其初始密码不再默认为空，MySQL会生成一个密码作为初始密码，初始密码会输出到MySQL的日志文件中，日志文件的位置在`/var/log/mysqld.log`中，该位置在my.cnf中进行配置。

查看初始密码：

```bash
cat /var/log/mysqld.log | grep password
```

以默认密码登录MySQL：

```bash
mysql -uroot -p'4+yTPU<dkI3j'
```

MySQL 5.7以后对密码的强度有了要求，必须是字母 + 数字 + 符号组成，如果想设置一些简单的密码，需要先进行设置：

```mysql
# 设置密码长度最低位数
mysql> set global validate_password_length=4;
# 设置密码强度级别
mysql> set global validate_password_policy=0;
```

validate_password_policy 取值说明：

| Policy        | Tests Performe                             |
| ------------- | ------------------------------------------ |
| 0 或者 LOW    | 满足长度                                   |
| 1 或者 MEDIUM | 数字、小写或者大写字母和特殊字符           |
| 2 或者 STRONG | 满足长度，数字、小写或者大写字母和特殊字符 |

修改密码：

```mysql
mysql> alter user 'root'@'localhost' identified by '******';
```

## 8. 配置远程连接

要想远程连接MySQL服务，首先需要对防火墙进行配置。而在阿里云中则需要配置安全组。

授权语法：

```mysql
grant 权限 on 数据库对象 to 用户
```

赋予ROOT用户从任何地址访问所有数据库对象的权限：

```mysql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '******' WITH GRANT OPTION;
# 刷新权限
FLUSH PRIVILEGES;
```

命令解析：

- ALL PRIVILEGES ：表示授予所有的权限，可以指定具体的权限。
- \*.\* ：表示所有库中的所有表。
- 'root'@'%' ：@前表示用户名，%表示任意IP地址，也可以指定具体的IP地址。
- IDENTIFIED BY 'PASSWORD' ：需要使用密码登录。
- WITH GRANT OPTION ：该用户可以给其他用户授权。





