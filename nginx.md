# （一）、nginx-1.16安装文档

- 本示例为CentOS7系统
- 下载的源码包均放在/usr/local/src目录下

### 1. 安装一些编译工具和必要工具

```

yum install -y wget gcc gcc-c++ make openssl-devel vim

```
### 2. 更换下系统的镜像源为国内的

```

# 浏览器访问
https://opsx.alibaba.com/mirror

# 找到自己的系统，右边点击帮助，然后按照说明复制粘贴命令

```

### 3. 下载nginx源码包

```
## 浏览器访问
http://nginx.org/en/download.html

## 下载最新稳定版本
wget http://nginx.org/download/nginx-1.16.1.tar.gz

```

### 4. 下载pcre库，用于支持nginx的重写rewrite

```
## 浏览器访问
https://sourceforge.net/projects/pcre/files/pcre/

## 选择一个版本
wget https://sourceforge.net/projects/pcre/files/pcre/8.43/pcre-8.43.tar.gz/download

```

### 5. ```tar```命令解压下载的源码包

### 6. 创建nginx用户

```

groupadd -r nginx

useradd -r -g nginx nginx

```

### 7. 编译安装nginx

```

cd nginx-1.16.1/

./configure --prefix=/usr/local/nginx --user=nginx --group=nginx --with-http_ssl_module --with-http_stub_status_module --with-pcre=/usr/local/src/pcre-8.43

make && make install

```

### 8. 启动nginx服务

```

/usr/local/nginx/sbin/nginx

```

访问测试

```

curl http://127.0.0.1

```

### 9. 站点配置

1. 编辑nginx配置文件

```
vim /usr/local/nginx/conf/nginx.conf

# 在http{}里面添加 include vhost/*.conf;（不要写到server{}里面去了）

http {
  ...
  server{}
  ...
  include vhost/*.conf; 
}

```

2. /usr/local/nginx/conf/下创建vhost这个目录

```

mkdir vhost

```

3. 在vhost目录下添加自定义的站点了。例如：

```

vim /usr/local/nginx/conf/vhost/default.conf

```

```
server {
    listen 80;
    server_name 127.0.0.1;
    root        /data/wwwroot/;
    index       index.php;

    access_log  /data/wwwlogs/default_access.log;
    error_log   /data/wwwlogs/default_error.log;
}
```

4. 最后reload之前先检查配置是否正确

```

/usr/local/nginx/sbin/nginx -t

```

5. 提示信息为OK successful就可以reload的了

```

/usr/local/nginx/sbin/nginx -s reload

```

### 10. 开机自启动设置

- CentOS7 有两种方式管理服务：一种是service，文件放在/etc/init.d/下；另一种是systemctl，文件放在/usr/systemd/system/下，systemctl兼容service的文件

- 以systemctl方式为例，编辑内容：


```
vim /usr/systemd/system/nginx.service
```

>  代码引用： https://www.nginx.com/resources/wiki/start/topics/examples/systemd/ 

```

[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t
ExecStart=/usr/sbin/nginx
ExecReload=/usr/sbin/nginx -s reload
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target

```

做些修改，nginx执行文件改为我们安装的路径

```

[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target

```

加入开启自动

```

systemctl enable nginx.service

```

> 查看systemctl其他命令： https://man.linuxde.net/systemctl


## 安装示例

- [（二）、php-7.1安装文档](./php.md)
- [（三）、mysql-5.7安装文档](./mysql.md)

