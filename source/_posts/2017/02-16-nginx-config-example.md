---
title: Nginx 静态服务器和反向代理样例
date: 2017-02-16 15:49:19
tags:
    - nginx
---

Nginx 配置文件样例, 包括一个代理服务器(到 API server) 和一个静态服务器.

<!--more-->

```
user deploy;
worker_processes  2;

events {
	worker_connections  1024;
}

http {
	include mime.types;
	default_type application/octet-stream;

	sendfile on;
	keepalive_timeout 65;

	# API v1 upstream
	upstream apiserver_v1 {
		server 127.0.0.1:3000;
	}

	# API server
	server {
		listen 443 ssl;
		server_name api.salumbaby.com;

		ssl_certificate /home/taot/tmp/salumbaby/server.crt;
		ssl_certificate_key /home/taot/tmp/salumbaby/server.key;

		location /api/v1 {
			rewrite ^/api/v1/(.*) /$1 break;
			proxy_redirect off;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header X-Forwareded-For $proxy_add_x_forwarded_for;
			proxy_pass http://apiserver_v1;
		}
	}

	# Web server
	server {
		listen 80;
		server_name salumbaby.com www.salumbaby.com;

		gzip on;

		location / {
			root /home/taot/deploy/salam-baby-api/site/;
			index index.html index.htm;
			error_page 404 @fallback;
		}

		location @fallback {
			rewrite ^(.*)$ / permanent;
		}

		error_page 404 /404.html;

		# redirect server error pages to the static page /50x.html
		#
		error_page 500 502 503 504 /50x.html;
		location = /50x.html {
			root /usr/share/nginx/html;
		}
	}
}
```
