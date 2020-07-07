---
title: "laraveladmin的坑"
date: 2020-05-23T20:14:05+08:00
draft: true
tag: ["laravel"]
categaries: ["IT技术"]
---

laraveladmin的安装除了首页，访问其他路由页面都出现404

nginx 的配置修改下

解决办法
在location里面加上　try_files uriuriuri/ /index.php?$query_string;
如果配置文件中存在　try_files uriuriuri/ =404;需要将它注释掉或者删掉，否则会报错
我的nginx配置文件如下

```nginx
server {
    listen 80;

    server_name www.cocapai.com;
    
    root /data/wwwroot/default/cocapai/public/;
    index index.html index.php index.htm;
    
    location / {
        #try_files $uri $uri/ =404;
        try_files $uri $uri/ /index.php?$query_string;  <font color=red>//主要是这一行</font>
    }
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
    #
    #   # With php7.2-cgi alone:
    #   fastcgi_pass 127.0.0.1:9000;
    #   # With php7.0-fpm:
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    
    }

}
```