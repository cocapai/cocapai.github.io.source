---
title: "First"
date: 2020-05-11T14:23:02+08:00
draft: true
tags: ["cordova","ios","wkwebview"]
---

## cordova改wkwebview打包ios应用，直接启动不了

cordova改为wkwebview后启动不了了，提示如下错误：

```
*** Terminating app due to uncaught exception 'NSUnknownKeyException', reason: '[<WKWebViewConfiguration 0x11de16c30> setValue:forUndefinedKey:]: this class is not key value coding-compliant for the key _alwaysRunsAtForegroundPriority.'
```

百度大法找个各种方法都不管用，最后这样搞定的，原因不知道

```ios
NSString* str = @"X2Fsd2F5c1J1bnNBdEZvcmVncm91bmRQcmlvcml0eQ==";
```

改为：

```ios
NSString* str = @"YWx3YXlzUnVuc0F0Rm9yZWdyb3VuZFByaW9yaXR5";
```

