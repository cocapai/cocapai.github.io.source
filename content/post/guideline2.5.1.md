---
title: "Guideline 2.5.1 - Performance - Software Requirements"
date: 2019-07-13T08:20:25+08:00
draft: true
tags: ["ios"]
categories: ["ios"]
---

提示如下：

Your app uses the "prefs:root=" non-public URL scheme, which is a private entity. The use of non-public APIs is not permitted on the App Store because it can lead to a poor user experience should these APIs change.

查找prefs:root=,找到类似的代码如下：

```objectivec
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"prefs:root=Privacy"]];//隐私设置
```
改为
```objectivec
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:UIApplicationOpenSettingsURLString] options:@{} completionHandler:nil];
```