# 安装redis

## 安装步骤

```bash
wget http://download.redis.io/releases/redis-5.0.5.tar.gz
tar -xf redis-5.0.5.tar.gz
cd redis-5.0.5.tar.gz
make PREFIX=/usr/local/redis install
# redis.conf
mkdir /usr/local/redis/etc
cp redis.conf /usr/local/redis/etc/
```

## 配置文件

```vim
# vim /usr/local/redis/etc/redis.conf
# 修改以下参数

# 使redis以守护进程模式运行
daemonize yes

# 设置客户端最大闲置时间，超时后断开连接
timeout

# 设置日志级别 
# debug: 大量信息，用于开发、测试
# verbose: 比debug级别简略些
# notice: 一般用于生产环境
# warning: 只记录错误日志
loglevel = verbose

# 日志输出文件
# stdout表示输出到 /dev/null
# logfile "stdout"
logfile "/var/log/redis/redis.log"
```

## 配置环境变量

```bash
echo 'export PATH="$PATH:/usr/local/redis/bin"' >> /etc/profile
source /etc/profile
```

## 配置启动脚本

```vim
#!/bin/sh
#
# Simple Redis init.d script conceived to work on Linux systems
# as it does use of the /proc filesystem.

### BEGIN INIT INFO
# Provides:     redis_6379
# Default-Start:        2 3 4 5
# Default-Stop:         0 1 6
# Short-Description:    Redis data structure server
# Description:          Redis data structure server. See https://redis.io
### END INIT INFO

REDISPORT=6379
EXEC=/usr/local/redis/bin/redis-server
CLIEXEC=/usr/local/redis/bin/redis-cli

PIDFILE=/var/run/redis_${REDISPORT}.pid
# CONF="/etc/redis/${REDISPORT}.conf"
CONF="/usr/local/redis/etc/redis.conf"

case "$1" in
    start)
        if [ -f $PIDFILE ]
        then
                echo "$PIDFILE exists, process is already running or crashed"
        else
                echo "Starting Redis server..."
                $EXEC $CONF
        fi
        ;;
    stop)
        if [ ! -f $PIDFILE ]
        then
                echo "$PIDFILE does not exist, process is not running"
        else
                PID=$(cat $PIDFILE)
                echo "Stopping ..."
                $CLIEXEC -p $REDISPORT shutdown
                while [ -x /proc/${PID} ]
                do
                    echo "Waiting for Redis to shutdown ..."
                    sleep 1
                done
                echo "Redis stopped"
        fi
        ;;
    *)
        echo "Please use start or stop as first argument"
        ;;
esac
```

## 将redis添加至服务

```bash
# 将上一步的启动脚本复制到下面路径
cp redis /etc/init.d/redis
# 添加执行权限
chmod +x /etc/init.d/redis
# 添加服务
chkconfig --add redis
# 开机启动
chkconfig redis on
# 重载配置文件
systemctl daemon-reload
```

## 命令

```bash
# 开启
systemctl start redis
# 停止
systemctl stop redis
# 查看状态
systemctl status redis
# 查看进程
ps -ef | grep redis
# 查看端口
netstat -an | grep 6379
# 登录客户端
redis-cli
```
