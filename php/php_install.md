# PHP编译安装

1.安装依赖包

```bash
 yum install -y gcc gcc-c++  make zlib zlib-devel pcre pcre-devel  libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers
```

2.下载php安装包

```bash
#下载
wget https://www.php.net/distributions/php-7.3.8.tar.gz
#解压
tar -zxvf php-7.3.8.tar.gz
```

3.编译

```bash
cd /tmp/php-7.3.8

#32位
./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --enable-fpm --with-fpm-user=www --with-fpm-group=www --with-mysqli --with-pdo-mysql --with-iconv-dir --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir=/usr --enable-xml --disable-rpath --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --enable-mbregex --enable-mbstring --with-mcrypt --enable-ftp --with-gd --enable-gd-native-ttf --with-openssl --with-mhash --enable-pcntl --enable-sockets --with-xmlrpc --enable-zip --enable-soap --without-pear --with-gettext --disable-fileinfo --enable-maintainer-zts

#64位
./configure --prefix=/usr/local/php --enable-fpm --enable-inline-optimization --disable-debug --disable-rpath --enable-shared --enable-opcache --with-mysql --with-mysqli --with-mysql-sock --enable-pdo --with-pdo-mysql --with-gettext --enable-mbstring --with-iconv --with-mcrypt --with-mhash --with-openssl --enable-bcmath --enable-soap --with-libxml-dir --enable-pcntl --enable-shmop --enable-sysvmsg --enable-sysvsem --enable-sysvshm --enable-sockets --with-curl --with-zlib --enable-zip --enable-bz2 --with-readline --without-sqlite3 --without-pdo-sqlite --with-pear --with-libdir=/lib/x86_64-linux-gnu --with-gd --with-jpeg-dir=/usr/lib --enable-gd-native-ttf --enable-xml
```

## 编译参数说明

| 参数        | 说明 |
| ----------- | ---- |
| `--prefix`  | 指定php安装目录    |
| `--with`    |      |
| `--enable`  |      |
| `--disable` |      |
