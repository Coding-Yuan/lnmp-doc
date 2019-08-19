# （二）、php-7.1安装文档

- 本示例为CentOS7系统
- 下载的源码包均放在/usr/local/src目录下

### 1. 下载源码包，然后解压

```

wget https://www.php.net/distributions/php-7.1.31.tar.gz

```

### 2. 安装PHP的一些依赖

```

yum install -y bison bison-devel zlib-devel libmcrypt-devel mcrypt mhash-devel libxml2-devel libcurl-devel bzip2-devel readline-devel libedit-devel sqlite-devel

yum -y install libjpeg-devel
yum -y install libpng-devel
yum -y install freetype-devel
yum install libc-client-devel

```

### 3. 编译安装

```

cd php-7.1.31/

./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --with-config-file-scan-dir=/usr/local/php/conf.d --with-zlib-dir --with-freetype-dir --enable-mbstring --with-libxml-dir=/usr/local/libxml --enable-soap --enable-calendar --with-curl --with-mcrypt --with-zlib --with-gd  --disable-rpath --enable-inline-optimization --with-bz2 --with-zlib --enable-sockets --enable-sysvsem --enable-sysvshm --enable-pcntl --enable-mbregex --enable-exif --enable-bcmath --with-mhash --enable-zip --with-pcre-regex --with-mysql --with-pdo-mysql --with-mysqli --with-jpeg-dir=/usr/local/libjpeg --with-png-dir=/usr/local/libpng --enable-gd-native-ttf --with-openssl --with-fpm-user=www --with-fpm-group=www --with-libdir=lib64 --enable-ftp --with-imap --with-imap-ssl --with-kerberos --with-gettext --with-xmlrpc --with-xsl --enable-opcache --enable-fpm --enable-xml --enable-shmop --enable-session --enable-ctype --with-iconv-dir --with-iconv

# configure如果有错误时，根据提示yum安装缺少的依赖，或百度

# 确认无报错后执行

make && make install

```

#### 4. 配置php

```

cp php.ini-production /usr/local/php/etc/php.ini

cd /usr/local/php/etc

cp php-fpm.conf.default php-fpm.conf

vim php-fpm.conf

```

修改[www]中的内容，设置PHP-FPM运行用户为www

```
listen = 127.0.0.1:9000
listen.owner = www
listen.group = www
listen.mode = 0666
user = www
group = www

```

创建www这个用户

```

useradd www -s /sbin/nologin -M

```

#### 5. 启动php-fpm

```

/usr/local/php/sbin/php-fpm -c /usr/local/php/etc/php.ini -y /usr/local/php/etc/php-fpm.conf

ps -ef | grep php-fpm 就可以看到服务已启动

```

#### 5. nginx与php配合使用

vim /usr/local/nginx/conf/vhost/default.conf为例

server{} 中添加：

```

location ~ \.php$ {
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_pass 127.0.0.1:9000;
    # fastcgi_pass unix:/dev/shm/php-cgi.sock;
    try_files $uri =404;
}

```

#### 6. 测试

vim /data/wwwroot/index.php

```

<?php

phpinfo();

```

访问 http://127.0.0.1

## 安装示例

- [（三）、mysql-5.7安装文档](./mysql.md)