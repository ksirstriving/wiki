# 安装 MySql 5.7

## 1.安装步骤

### 1.1 下载mysql

```bash
# 32位安装包
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.27-linux-glibc2.12-i686.tar.gz
# 64位安装包
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz
```

### 1.2 重命名&新建目录

```bash
# 解压
tar -xzvf mysql.tar.gz
# 移动&重命名
mv /path/to/mysql /usr/local/mysql
# 新建data目录，用于存放mysql数据文件 datadir
mkdir /var/data
```

### 1.3 新建用户组、用户名

```bash
groupadd mysql
useradd mysql -g mysql
```

### 1.4 目录授权给mysql

```bash
chown -R mysql.mysql /usr/local/mysql
chown -R mysql.mysql /var/data
```

### 1.5 初始化mysql

```bash
/usr/local/mysql/bin/mysqld --initalize --user=mysql --basedir=/usr/local/mysql/ --datadir=/var/data
```

正常情况会返回以下内容，最后一行为初始化的密码
> 2019-08-17T08:06:10.786845Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).  
> 2019-08-17T08:06:12.003299Z 0 [Warning] InnoDB: New log files created, LSN=45790  
> 2019-08-17T08:06:12.143389Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.  
> 2019-08-17T08:06:12.210721Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: e0ae1846-c0c5-11e9-9d71-aaaa001123e8.  
> 2019-08-17T08:06:12.214684Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.  
> 2019-08-17T08:06:12.216313Z 1 [Note] A temporary password is generated for root@localhost: `rgXphos(Y2G>`

如果出现如下错误

> /usr/local/mysql/bin/mysqld: error while loading shared libraries: libnuma.so.1: cannot open shared object file: No such file or directory

需要安装numactl

```bash
yum -y install numactl
```

如果返回下面内容，表示datadir目录下以有文件，需要清空后再执行，如果还存在报错，需要检查mysql是否有该文件的权限

> [ERROR] --initialize specified but the data directory has files in it. Aborting.

## 2 配置

### 2.1 my.cnf

```vim
[mysqld]
basedir=/usr/local/mysql
datadir=/var/data
socket=/tmp/mysql.sock
user=mysql
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

# LOG
log_timestamps=SYSTEM

# ERROR_LOG
log_error=/var/log/mysql/error.log

# GENERAGE LOG
general_log=on
general_log_file=/var/log/mysql/mysql.log

# SLOW LOG
slow_query_log=on
long_query_time=2
slow_query_log_file=/var/log/mysql/slow.log
```

### 2.2 添加mysql至service

```bash
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
```

### 2.3 重置密码

```sql
mysql> ALTER USER USER() IDENTIFIED BY '123456';
```
