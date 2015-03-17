---
layout: post
title: 在Nginx上配置PHP
tags:
  - php
  - nginx
---

在 Arch Linux 上安装 Nginx 和 PHP，作为自己的笔记，只记录最小化的步骤，不讨论更多的细节。

### 安装步骤

安装 nginx, php, php-fpm。具体步骤省略

更改 php 配置文件 /etc/php/php.ini
{% highlight bash %}
# 修改 open_basedir 为 nginx 的 html 目录（不确定这步是否必须）
open_basedir = /usr/share/nginx/html/
{% endhighlight %}

<!--more-->

更改 php-fpm 配置文件 /etc/php/php-fpm.conf，打开监听 socket
{% highlight bash %}
; listen = 127.0.0.1:9000
listen = /run/php-fpm/php-fpm.sock
{% endhighlight %}

更改 nginx 配置文件 /etc/nginx/nginx.conf，添加 php 处理
{% highlight bash %}
# 在 server 块中添加如下代码
location ~ \.php$ {
    root   /usr/share/nginx/
    #fastcgi_pass   127.0.0.1:9000;
    fastcgi_pass   unix:/run/php-fpm/php-fpm.sock;
    fastcgi_index  index.php;
    # 注意这里和部分网上的文章不同，用了$document_root, 而不是/scripts
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include        fastcgi_params;  
    try_files $uri = 404;
}
{% endhighlight %}

启动 php-fpm 和 nginx
