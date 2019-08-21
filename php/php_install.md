# PHP编译安装

## 安装步骤

```bash
# 安装依赖包
yum install -y gcc gcc-c++  make zlib zlib-devel pcre pcre-devel  libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers
#下载
wget https://www.php.net/distributions/php-7.3.8.tar.gz
#解压
tar -zxvf php-7.3.8.tar.gz
cd php-7.3.8
# 编译
./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --enable-fpm --enable-inline-optimization --disable-debug --disable-rpath --enable-shared --enable-opcache --with-mysql --with-mysqli --with-mysql-sock --enable-pdo --with-pdo-mysql --with-gettext --enable-mbstring --with-iconv --with-mcrypt --with-mhash --with-openssl --enable-bcmath --enable-soap --with-libxml-dir --enable-pcntl --enable-shmop --enable-sysvmsg --enable-sysvsem --enable-sysvshm --enable-sockets --with-curl --with-zlib --enable-zip --enable-bz2 --with-readline --without-sqlite3 --without-pdo-sqlite --with-pear --with-libdir=/lib/x86_64-linux-gnu --with-gd --with-jpeg-dir=/usr/lib --enable-gd-native-ttf --enable-xml
# 安装
make && make install
```

## 配置环境变量

```bash
echo 'export PATH="$PATH:/usr/local/php/bin"' >> /etc/profile
source /etc/profile
```

## 配置文件

```bash
# pwd
# /downloads/php-7.3.8
# php.ini
cp php.ini-production /usr/local/php/etc/php.ini
# php-fpm.conf
cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
# vi 
# 去掉`pid = run/php-fpm.pid`一行前面的‘;’
# www.conf
cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf
```

## 配置启动脚本

```bash
# pwd
# /downloads/php-7.3.8
cp sapi/fpm/init.d.php-fpm.in /etc/init.d/php-fpm
chmod +x /etc/init.d/php-fpm
```

```vim
# 修改/etc/init.d/php-fpm内容
prefix=/usr/local/php
exec_prefix=${prefix}

php_fpm_BIN=${exec_prefix}/sbin/php-fpm
php_fpm_CONF=${prefix}/etc/php-fpm.conf
php_fpm_PID=${prefix}/var/run/php-fpm.pid
```

fpm.conf配置参考[链接](https://www.cnblogs.com/yangjinqiang/p/8257820.html)

## 添加至服务

```bash
# 添加服务
chkconfig --add php-fpm
# 开机启动
chkconfig php-fpm on
# 重载配置文件
systemctl daemon-reload
```
