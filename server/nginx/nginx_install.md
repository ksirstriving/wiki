# nginx安装

## 安装步骤

```bash
wget http://nginx.org/download/nginx-1.16.1.tar.gz
tar -xf nginx-1.16.1.tar.gz
cd nginx-1.16.1.tar.gz
#开启perl支持
yum -y install perl-devel perl-ExtUtils-Embed
./configure --with-http_perl_module
make && make install
```

nginx安装目录`/usr/local/nginx/`

## 配置服务

将下面内容复制到`/etc/init.d/nginx`，并添加可执行权限  

```bash
#!/bin/sh
#
# nginx - this script starts and stops the nginx daemin
#
# chkconfig:   - 85 15
# description:  Nginx is an HTTP(S) server, HTTP(S) reverse \
#               proxy and IMAP/POP3 proxy server
# processname: nginx
# config:      /usr/local/nginx/conf/nginx.conf
# pidfile:     /usr/local/nginx/logs/nginx.pid

# Source function library.
. /etc/rc.d/init.d/functions

# Source networking configuration.
. /etc/sysconfig/network

# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0

nginx="/usr/local/nginx/sbin/nginx"
prog=$(basename $nginx)

NGINX_CONF_FILE="/usr/local/nginx/conf/nginx.conf"

lockfile=/var/lock/subsys/nginx

start() {
    [ -x $nginx ] || exit 5
    [ -f $NGINX_CONF_FILE ] || exit 6
    echo -n $"Starting $prog: "
    daemon $nginx -c $NGINX_CONF_FILE
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}

stop() {
    echo -n $"Stopping $prog: "
    killproc $prog -QUIT
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}

restart() {
    configtest || return $?
    stop
    start
}

reload() {
    configtest || return $?
    echo -n $"Reloading $prog: "
    killproc $nginx -HUP
    RETVAL=$?
    echo
}

force_reload() {
    restart
}

configtest() {
  $nginx -t -c $NGINX_CONF_FILE
}

rh_status() {
    status $prog
}

rh_status_q() {
    rh_status >/dev/null 2>&1
}

case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart|configtest)
        $1
        ;;
    reload)
        rh_status_q || exit 7
        $1
        ;;
    force-reload)
        force_reload
        ;;
    status)
        rh_status
        ;;
    condrestart|try-restart)
        rh_status_q || exit 0
            ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
        exit 2
esac
```

将服务加入chkconfig管理列表

```bash
chkconfig --add /etc/init.d/nginx
```

设置开机启动

```bash
chkconfig nginx on
```

使用systemctl管理nginx服务

```bash
systemctl start nginx
systemctl stop nginx
systemctl reload nginx  
systemctl restart nginx
```

## 常见问题

1、nginx启动后，浏览器无法访问  
查看云服务器的安全组是否开启了80端口

2、使用`systemctl start nginx`启动时，命令卡住
使用`systemctl status nginx`命令查看状态


```bash
[root@iZm5ea582ilcz7maaszqw6Z init.d]# systemctl status nginx
● nginx.service - SYSV: Nginx is an HTTP(S) server, HTTP(S) reverse proxy and IMAP/POP3 proxy server
   Loaded: loaded (/etc/rc.d/init.d/nginx; bad; vendor preset: disabled)
   Active: failed (Result: timeout) since 五 2019-08-23 15:36:17 CST; 1min 44s ago
     Docs: man:systemd-sysv-generator(8)
   CGroup: /system.slice/nginx.service
           ├─4436 nginx: master process /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
           └─4438 nginx: worker process

8月 23 15:30:43 iZm5ea582ilcz7maaszqw6Z systemd[1]: Starting SYSV: Nginx is an HTTP(S) server, HTTP(S) reverse proxy and IMAP...ver...
8月 23 15:30:43 iZm5ea582ilcz7maaszqw6Z nginx[4423]: Starting nginx: [  OK  ]
8月 23 15:30:43 iZm5ea582ilcz7maaszqw6Z systemd[1]: PID file /usr/local/nginx/logs/nginx.pid not readable (yet?) after start.
8月 23 15:36:17 iZm5ea582ilcz7maaszqw6Z systemd[1]: nginx.service start operation timed out. Terminating.
8月 23 15:36:17 iZm5ea582ilcz7maaszqw6Z systemd[1]: Failed to start SYSV: Nginx is an HTTP(S) server, HTTP(S) reverse proxy a...erver.
8月 23 15:36:17 iZm5ea582ilcz7maaszqw6Z systemd[1]: Unit nginx.service entered failed state.
8月 23 15:36:17 iZm5ea582ilcz7maaszqw6Z systemd[1]: nginx.service failed.
```

发现无法读取PID file文件。将nginx.conf里面的pid_file属性配置为`/usr/local/nginx/logs/nginx.pid`即可。
