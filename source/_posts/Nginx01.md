---
title: Nginx 反向代理和负载均衡
date: 2016/8/16 11:42
tags: nginx

---
[阿里tengine参考手册](http://tengine.taobao.org/book/)

1. 实现源码安装nginx 并且伪装服务器信息
2. 配置负载均衡和反向代理tomcat默认200并发,nginx 50K web服务器并发提高tomcat应用服务器的访问体验
3. 实现lvs + keeplived 高可用心跳vrrp协议 (未实现)

<!-- more -->
添加centos 阿里云repo
---
- mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
- `wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo`
- yum clean all
- yum makecache
- 先安装nginx依赖库 `yum -y install pcre zlib gcc-c++ openssl`

编译安装nginx
---
* [官网下载最新stable 稳定版本的nginx源码安装包](https://nginx.org/download/nginx-1.12.1.tar.gz)
* 修改nginx 的版本和server_name 伪装防止网络攻击

![](http://ot2hu9qoc.bkt.clouddn.com/17-8-16/13225024.jpg)

*  具体操作步骤
	1. 先解压 tar xvf nginx-1.12.1.tar.gz
	2. 进入目录 cd nginx-1.12.1/src 修改三个源码文件 `nginx.h` ` ngx_http_header_filter_module.c ``ngx_http_special_response.c`
	3. find / name  `nginx.h` 找到后  直接编辑文件

![](http://ot2hu9qoc.bkt.clouddn.com/17-8-16/83843159.jpg)


*  然后编译安装前 先`yum install 依赖库gcc pcre zlib openssl` 服务器统一放到mkdir -p  /usr/local/server/这个目录下
*  **`chown root. /usr/local/sbin/nginx`   `chmod u+s /usr/local/sbin/nginx`将文件赋予root权限才能开启1024以下端口**


* 配置nginx.conf 在nginx/sbin/nginx -t 测试配置文件是否正确
	1. nginx -s reload 重新加载配置不重启
	2. nginx -s stop

* 配置tomcat 服务器 修改端口号 域名  和配置文件对应 实现反向代理和负载均衡
* 开放防火墙端口规则`vi /etc/sysconfig/iptables` 开放对应端口号

* 然后修改本机hosts文件127.0.0.1 对应域名 最后访问www.goo.co.jp ,基本功能实现图

![](http://ot2hu9qoc.bkt.clouddn.com/17-8-16/17224896.jpg)

![](http://ot2hu9qoc.bkt.clouddn.com/17-8-16/17127966.jpg)



* 配置文件如下

```shell
	./configure \
	--prefix=/usr/local/server/nginx \
	--pid-path=/var/run/nginx/nginx.pid \
	--lock-path=/var/lock/nginx.lock \
	--error-log-path=/var/log/nginx/error.log \
	--http-log-path=/var/log/nginx/access.log \
	--with-http_gzip_static_module \
	--http-client-body-temp-path=/var/temp/nginx/client \
	--http-proxy-temp-path=/var/temp/nginx/proxy \
	--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
	--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
	--http-scgi-temp-path=/var/temp/nginx/scgi
	注意：启动nginx之前，上边将临时文件目录指定为/var/temp/nginx，需要在/var下创建temp及nginx目录
	[root@localhost sbin]# mkdir /var/temp/nginx/client -p


		#user  nobody;
		worker_processes  1;

		#error_log  logs/error.log;
		#error_log  logs/error.log  notice;
		#error_log  logs/error.log  info;

		#pid        logs/nginx.pid;


		events {
		    worker_connections  1024;
		}


		http {
		    include       mime.types;
		    default_type  application/octet-stream;

		    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
		    #                  '$status $body_bytes_sent "$http_referer" '
		    #                  '"$http_user_agent" "$http_x_forwarded_for"';

		    #access_log  logs/access.log  main;

		    sendfile        on;

		    #tcp_nopush     on;
		    server_tokens  off;  # 隐藏版本号

				client_max_body_size 100m;
				#注意存在文件上传的接口需要配置 `client_max_body_size 100m;` 默认是1m;
		    #keepalive_timeout  0;
		    keepalive_timeout  65;

		    #gzip  on;


		    server {
		        listen      80;
		        server_name     www.test1.com;

		        #charset koi8-r;

		        #access_log  logs/host.access.log  main;

		        location / {
		            root   html-test1;
		            index  index.html index.htm;
		        }
		}

		    server {
		        listen      80;
		        server_name  www.test.com;

		        #charset koi8-r;

		        #access_log  logs/host.access.log  main;

		        location / {
		            root   html-test;
		            index  index.html index.htm;
		        }
		}
		    server {
		        listen       81;
		        server_name  localhost;

		        #charset koi8-r;

		        #access_log  logs/host.access.log  main;

		        location / {
		            root   html81;
		            index  index.html index.htm;
		        }
		}

		    server {
		        listen       80;
		        server_name  localhost;

		        #charset koi8-r;

		        #access_log  logs/host.access.log  main;

		        location / {
		            root   html;
		            index  index.html index.htm;
		        }



		        location = /50x.html {
		            root   html;
		        }


		    }

		    upstream goo{

		        server 192.168.140.128:8011;
		        server 192.168.140.128:8012 weight=3;
		    }

		       server {
		        listen       80;
		        server_name  www.goo.co.jp;

		        #charset koi8-r;

		        #access_log  logs/host.access.log  main;

		        location / {
		            proxy_pass  http://goo;
		            index  index.html index.htm;
		        }



		        location = /50x.html {
		            root   html;
		        }


		    }


		    upstream yandex{

		        server 192.168.140.128:8080;
		    }

		       server {
		        listen       80;
		        server_name  www.yandex.com;

		        #charset koi8-r;

		        #access_log  logs/host.access.log  main;

		        location / {
		            proxy_pass  http://yandex;
		            index  index.html index.htm;
		        }



		        location = /50x.html {
		            root   html;
		        }


		    }






		}

		
#!/bin/bash
#
# chkconfig: - 85 15
# description: nginx is a World Wide Web server. It is used to serve
# Source function library.
. /etc/rc.d/init.d/functions

# Source networking configuration.
. /etc/sysconfig/network

# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0

nginx="/usr/local/nginx/tengine-2.2.3/sbin/nginx"
prog=$(basename $nginx)

NGINX_CONF_FILE="/usr/local/nginx/tengine-2.2.3/conf/nginx.conf"

#[ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx

lockfile=/var/lock/subsys/nginx

#make_dirs() {
#   # make required directories
#   user=`nginx -V 2>&1 | grep "configure arguments:" | sed 's/[^*]*--user=\([^ ]*\).*/\1/g' -`
#   options=`$nginx -V 2>&1 | grep 'configure arguments:'`
#   for opt in $options; do
#       if [ `echo $opt | grep '.*-temp-path'` ]; then
#           value=`echo $opt | cut -d "=" -f 2`
#           if [ ! -d "$value" ]; then
#               # echo "creating" $value
#               mkdir -p $value && chown -R $user $value
#           fi
#       fi
#   done
#}

start() {
    [ -x $nginx ] || exit 5
    [ -f $NGINX_CONF_FILE ] || exit 6
#    make_dirs
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
    sleep 1
    start
}

reload() {
    configtest || return $?
    echo -n $"Reloading $prog: "
#  -HUP是nginx平滑重启参数
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

* 正向代理
```shell
  正向代理发生在 client 端，用户能感知到的，并且是用户主动发起的代理。

比如：翻墙。
我们不能访问外网，但是可以访问代理服务器，然后代理服务器帮我们从外网中获取数据。但是在使用之前，用户往往需要主动在client端配置代理。

黑客为了隐藏身份，用的就是正向代理。

|客户端+代理服务器|-->|目标服务器|
```

* 反向代理
```shell
反向代理发生在 server端，从用户角度看是不知道发生了代理的（这个只有服务器工程师才知道）。

比如：
用户访问 服务器A，服务器A就给用户返回了数据。
但是服务器A上其实并没有数据，它是偷偷从服务器B上获取数据，然后再返回给用户的。
这个过程是在 server 端发生的，用户并不知道（只有服务器运维人员才知道）。

|客户端|-->|代理服务器+目标服务器|
```

* 负载均衡
```shell 
负载均衡是反向代理的一种运用。
客户端访问服务器，服务器会把请求分发给其它多个不同的服务器（即反向代理），从而减轻了单个服务器处理海量请求的压力，不会出现崩溃。

做了反向代理才能实现负载均衡。负载均衡是做反向代理的目的之一。
反向代理，是有把请求转发的能力，这个是基础
负载均衡，是把请求转发到不同的服务器上，均衡各个服务器
```

参考
---

* [nginx 在线配置生成](https://www.digitalocean.com/community/tools/nginx)
* [正向代理和反向代理](https://segmentfault.com/a/1190000012549192)

