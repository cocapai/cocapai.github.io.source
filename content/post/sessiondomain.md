---
title: "二级域名的session共用"
date: 2015-09-22T16:19:25+08:00
draft: true
tags: ["session"]
categories: ["IT技术"]
---

不同的二级域名要公用登录状态时，cookie的sessionid是不同，所以不能同步登录状态。

一定要用的话就是下面这样

```php
//在入口文件里加两行
ini_set('session.cookie_path', '/');
ini_set('session.cookie_domain', 'cocapai.com');
```

这样所有的子域名的session是相同的，当然前提是这些子域名要在一个服务器上