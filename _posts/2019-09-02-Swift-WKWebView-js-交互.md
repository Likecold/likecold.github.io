---
layout:     post
title:      Swift WKWebView JS 交互
subtitle:   原生交互
date:       2019-09-02
author:     ZY
header-img: img/post-bg-cook.jpg
catalog: true
tags:
    - iOS Swift
---

## 前言

开发中经常需要用到h5，WkWebview为我们提供了便捷的h5和原生交互的方法。

### 创建对象


     let url = 'http://www.baidu.com'
     let request = URLRequest(url: url!)
     let userContent = WKUserContentController.init()
     // 此处设置app的 scheme  很重要  js发送消息需要用到
     userContent.add(self, name: "demoScheme")
     let config = WKWebViewConfiguration.init();
     config.userContentController = userContent;
     self.wkWebview = WKWebView.init(frame:CGRect.init(x: 0, y: 0, width: SCREENWIDTH, height: SCREENHEIGHT>=812 ? SCREENHEIGHT-88 : SCREENHEIGHT-64), configuration: config);
     self.view.addSubview(self.wkWebview!);
     //  设置代理遵循代理并实现代理
     self.wkWebview?.navigationDelegate = self;
     self.wkWebview?.uiDelegate = self;
     self.wkWebview?.load(request)
     self.wkWebview?.sizeToFit()
     

### 实现代理 收发消息


    //  webview 加载完成 发送数据给js端
    func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
        //  发送消息给web
        //  data 和 js端沟通数据格式 以字符串形式传递
        let data = ""
        //  在js相应代码里实现appDidSendData方法  当页面加载完成时会调用js中的该方法
        let jsFunc = "appDidSendData(\(data))"
        self.wkWebview?.evaluateJavaScript(jsFunc, completionHandler: nil);
        print("发送成功")
    }
    
    // 收到js端发送过来的消息
    func userContentController(_ userContentController: WKUserContentController, didReceive message: WKScriptMessage) {
        let receivedDataStr = message.body;
        let receivedData = JSON.init(receivedDataStr);
        //  根据js端发送的数据来进行不同的操作
        switch receivedData["type"].stringValue {
        case "1":
            break
        case "2":
            break
        case "3":
            break
        case "4":
            break
        case "5":
            break
        default:
            break
        }
        print("收到消息")            
    }
    
### js端发送消息
    
    // xxx为WKWebview 初始化时设置的 scheme
    window.webkit.messageHandlers.xxx.postMessage(JSON.stringify(json))

### 参考

- [WKWebview](https://developer.apple.com/documentation/webkit/wkwebview)
 

