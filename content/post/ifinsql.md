---
title: "sql语句里的条件语句"
date: 2020-05-14T11:56:58+08:00
draft: true
tags: ["sql"]
categories: ["IT技术"]
cover: "https://www.cocapai.com/uploads/HoPkVtmsgbGEB8BFSqTqbLxuK3jG0RSbXvEch9Gl.png"
---

sql语句里的条件选择主要是用到 …… case when …… end

```sql
SELECT * FROM tp_wl_job j WHERE ( addtime > case when contacts='3个' then 1585626783 when contacts='1个' then 1583553183 when contacts='6个' then 1570420383 when contacts='8个' then 0 end ) ORDER BY id desc LIMIT 0,40
```

![](https://www.cocapai.com/uploads/HoPkVtmsgbGEB8BFSqTqbLxuK3jG0RSbXvEch9Gl.png)

