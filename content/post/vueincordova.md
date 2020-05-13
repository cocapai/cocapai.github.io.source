---
title: "vue里调用cordova插件"
date: 2019-03-13T09:30:40+08:00
draft: true
tags: ["Vue", "Cordova"]
categories: ["IT技术"]
---

```js
document.addEventListener(
    'deviceready', 
    function () { 
    	console.log('Device is Ready!'); 
    	window.appAvailability.check(
            'baidumap',             
            () => {console.log('有')},
            () => {console.log('没有')}
        )
    }, false);
```
上面的window.appAvailability是根据cordova插件列表里的名字来的 cordova_plugins.js是存的插件列表