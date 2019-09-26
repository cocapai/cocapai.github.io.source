---
title: postmessage实现iframe跨域传值
date: 2019-09-25 15:24:49
tags:
---

发送消息：window.parent.postMessage(‘message’, ‘*’);
接收消息：window.addEventListener(‘message’, function (data) {console.log(‘接收消息后的动作’);}, false);
总之就是在一个页面上发送消息，在另一个页面上接收消息