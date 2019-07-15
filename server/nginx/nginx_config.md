# nginx.config

[TOC]

## main

> 全局配置。该部分配置与业务功能无关

* `worker_processes 2`  
worker 角色的工作进程的个数。  
推荐配置为 `grep ^processor /proc/cpuinfo | wc -l`  

* `worker_cpu_affinity`  
设置cpu粘性。在高并发情况下，用来降低多cpu核切换造成的寄存器等现场重建带来的性能损耗。  
例：四核 `worker_cpu_affinity 0001 0010 0100 1000;`

* `worker_rlimit_nofile`  
worker进程最大打开文件数，默认不设置。
可以限制为操作系统最大的限制`worker_rlimit_nofile 65535`

## events

> 控制Nginx处理连接的方式

* `worker_connections`  
每一个worker进程能并发处理的最大连接数。  
计算公式：  
nginx作为反向代理服务器时，`最大连接数 = worker_processes * worker_connections / 4`  
nginx作为http服务器时，`最大连接数 = worker_processes * worker_connections / 2`  
**这个值的大小不可以超过`worker_rlimit_nofile`**

* `use epoll`  
nginx默认使用epoll事件模型。  
linux系统使用epoll模型，OpenBSD、FreeBSD使用kqueue模型，当系统不支持这两个模型时，才使用select模型。

## http

> 提供http服务相关的一些配置参数。

* `sendfile`  
配置为`on`则开启高效文件传输模式，减少用户空间到内核空间的上下文切换。  
如果是用来下载、进行磁盘IO重负载的应用，可以设置为`off`，用来平衡磁盘与网络I/O处理速度，降低系统负载。

* `keepalive_timeout`  
长连接超时时间，单位为秒。  
当请求大量小文件的时候，可以减少重新建立连接时的开销，但如果存在大文件上传的情景，如果设置的时间内没有传输完毕，就会导致上传失败。同时，如果设置的时间过长，在用户量大时，长时间保持长连接会造成大量资源被占用。

* `send_timeout`  
服务端向客户端传输数据的超时时间。

* `client_max_body_size`  
允许客户端请求的最大单文件字节数。  
例：`client_max_body_size 10m`

* `client_body_buffer_size`  
缓冲区代理缓冲用户端请求的最大字节数。  
例：`client_body_buffer_size 128k`

### http_proxy

> 该模块实现nginx作为反向代理服务器的功能，包括缓存功能。

* `proxy_connect_timeout`  
反向代理与后端节点服务器连接的超时时间，单位秒。  
例：`proxy_connect_timeout 60`

* `proxy_read_timeout`
与代理服务器连接成功后，等待返回结果的超时时间，单位秒。  
例：`proxy_read_timeout 60`

* `proxy_buffer_size`  
设置缓冲区大小，默认与`proxy_buffers`大小一致，实际可以将这个指令值设置的小一些。  
例：`proxy_buffer_size 4k`

* `proxy_buffers`  
设置缓冲区的数量和大小。nginx从代理的后端服务器获取的响应时间，会放置到缓冲区。  
例：`proxy_buffers 4 32k`

* `proxy_busy_buffers_size`  
设置系统在高负荷下运行时的`proxy_buffers`值，官方推荐为`proxy_buffers`值的两倍。

* `proxy_max_temp_file_size`  
当`proxy_buffers`放不下后端服务器的相应内容时，会将一部分数据保存到硬盘的临时文件中，该指令值是用来设置最大临时文件的大小，默认值为`1024M`，设置为`0`时表示禁用。

* `proxy_temp_file_write_size`  
当缓存被代理服务器响应到临时文件时，这个指令值限制了每次写临时文件的大小。  
例：`proxy_temp_file_write_size 64k`

### http_gzip

> 该模块实现了实时压缩数据流的功能。

* `gzip`  
值为`on`表示开启gzip压缩输出，减少网络传输。  
例：`gzip on`

* `gzip_min_length`  
设置允许压缩的页面最小字节数。默认值是`20k`，建议设置成大于`1k`的值，因为小于1k可能会越压越大。  
例：`gzip_min_length 1k`

* `gzip_buffers`  
设置系统获取几个单位的缓存来存储gzip压缩结果数据流。  
例：`gzip_buffers 4 16k` 表示按照原始数据大小，以16k为单位的4倍申请内存。

* `gzip_http_version`  
用于识别http协议的版本。目前基本为`1.1`  
例：`gzip_http_version 1.1`

* `gzip_comp_level`  
设置gzip压缩比等级（1-9），值越大，压缩比率越高，但处理速度越慢。推荐使用`6`  
例：`gzip_comp_level 6`

* `gzip_types`  
设置匹配的mime类型，只对匹配的类型进行压缩，`text/html`类型总会被压缩。  
例：`gzip_types text/plain application/x-javascript text/css application/xml`

* `gzip_proxied`  
当nginx作为反向代理服务器时需要配置。该指令控制后端服务器返回的结果是否压缩。匹配的前提是后端服务器必须返回包含`Via`的header头。  
例：`gzip_proxied any`

* `gzip_vary`  
是否传输gzip压缩标志，在返回头中添加'`Vary: Accept-Encoding`'。有助于帮助浏览器确认返回数据是否经过了压缩处理。  
例：`gzip_vary on`

## server

> http服务上支持多个虚拟主机，每个虚拟主机对应一个server模块。  
> 在提供mail服务代理时，每个server通过监听地址或端口来区分

* `listen`  
监听端口，默认`80`。小于`1024`的要用root启动。  
例：`listen *:80`、`listen 127.0.0.1:80`

* `server_name`  
服务器名称，可通过正则匹配。  
例：`localhost`、`www.example.com`

## upstream

> 该模块通过简单的调度算法实现客户端IP到后端服务器的负载均衡  
> `upstream`后接负载均衡器的名字  
> 后端服务器以`host:port options;`格式写在`{}`中。

例：

```bash
upstream backend{
  hash $remote_addr consistent;
  server backend1.example.com:12345 weight=5;
  server 127.0.0.1:12345 max_fails=3 fail_timeout=30s
  server unix:/tmp/backend3;
}
```

## location

> http服务中，某些特定的URL对应的一系列配置。

* `root /var/www/html`  
定义服务器的默认网站根目录位置。  
如果`location`的URL匹配的是子目录或者文件，则`root`配置无作用。  
一般放在`/`或`server`下。

* `index index.jsp index.html index.htm`
定义路径下默认访问的文件名，一般和`root`放在一起

* `proxy_pass http:/backend`
请求专项backend定义的服务器列表，对应`upstream`负载均衡器。

* `proxy_redirect off`

* `proxy_set_header Host $host;`

* `proxy_set_header X-Real-IP $remote_addr;`

* `proxy_set_header X-Forwarded-For $porxy_add_x_forwarded_for;`

### 访问控制

* `allow`  
* `deny`  
