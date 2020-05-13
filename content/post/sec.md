---
title: "cordova改wkwebview后不能使用tel拨号，baidumap调起百度地图"
date: 2020-05-11T14:28:00+08:00
draft: true
tags: ["wkwebview", "百度地图"]
---

# cordova改wkwebview后不能使用tel拨号，baidumap调起百度地图

安装了这个插件cordova-plugin-wkwebview-engine

修改插件中CDVWKWebViewEngine.m文件

```objectivec
- (void) webView: (WKWebView *) webView decidePolicyForNavigationAction: (WKNavigationAction*) navigationAction decisionHandler: (void (^)(WKNavigationActionPolicy)) decisionHandler
{
    NSURL* url = [navigationAction.request URL];
    CDVViewController* vc = (CDVViewController*)self.viewController;
    
    /*---------------修改部分 增加下面这些----------------------------*/
	NSString *scheme = [url scheme];
    UIApplication *app = [UIApplication sharedApplication];
    //电话,短信,邮件
    if ([scheme isEqualToString:@"tel"]||[scheme isEqualToString:@"sms"] ||[scheme isEqualToString:@"mailto"]) {
        if ([app canOpenURL:url]) {
            CGFloat version = [[[UIDevice currentDevice]systemVersion]floatValue];
            if (version >= 10.0) {
                /// 大于等于10.0系统使用此openURL方法
                [[UIApplication sharedApplication] openURL:url options:@{} completionHandler:nil];
            } else {
                [[UIApplication sharedApplication] openURL:url];
            }
        }
    }
    // 打开appstore
    if ([url.absoluteString containsString:@"ituns.apple.com"]) {
        if ([app canOpenURL:url]) {
            [app openURL:url];
            decisionHandler(WKNavigationActionPolicyCancel);
            return;
        }
    }
    /*修改结束*/
    …………
```