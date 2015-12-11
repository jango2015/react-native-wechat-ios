# react-native-wechat-ios
微信SDK集成示例，现已完成微信认证登录，之后将陆续包装分享等其他功能。

## 如何安装

#### 1. 下载包文件
```
$ npm i react-native-wechat-ios
```

#### 2. 链接库文件到你的项目中
参考 https://facebook.github.io/react-native/docs/linking-libraries-ios.html#content

###### 给RCTWeChat添加头文件搜索路径：`$(SRCROOT)/../../react-native/React`，并选择`recursive`。

###### 因为需要在 AppDelegate.m 文件中导入 RCTWeChat.h，所以需要在你的项目中添加一个头文件搜索路径：
`$(SRCROOT)/../node_modules/react-native-wechat-ios/RCTWeChat`，并选择`recursive`。

###### 根据微信SDK要求需要添加以下库文件
  - [x] SystemConfiguration.framework   
  - [x] libz.dylib
  - [x] libsqlite3.0.dylib
  - [x] libc++.dylib
  - [x] CoreTelephony.framework

> Xcode7 可能找不到*.dylib库，解决方法参考这里
http://www.jianshu.com/p/1f575e4d1033

## 如何使用

### 1. 重写AppDelegate的handleOpenURL和openURL方法：

需要导入`RCTWeChat.h`
```objective-c
#import "RCTWeChat.h"
```

```objective-c
- (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url
{
  return [[RCTWeChat shareInstance] handleOpenURL: url];
}

- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation {
  
  return [[RCTWeChat shareInstance] handleOpenURL: url];
  
}

```

### 2. 订阅`didRecvAuthResponse`事件获取认证处理结果
认证成功后由Native端触发该事件，通知React Native端。

```javascript
import {NativeAppEventEmitter} from 'react-native';

NativeAppEventEmitter.addListener(
  'didRecvAuthResponse',
  (response) => AlertIOS.alert(JSON.stringify(response))
);

```
###### 返回值(response):
* `response.code`
* `response.state`
* `response.errCode`

### 3. 发起认证
```javascript
import WeChat from 'react-native-wechat-ios';

let scope = 'snsapi_userinfo';
let state = 'wechat_sdk_test'; 
WeChat.sendAuthReq(scope, state, (res) => {
    alert(res); // true or false
});
```

## 已完成的方法，`callback`都是可选的
- registerApp(appid, appdesc, callback)向微信注册应用ID, `appdesc`可选
```javascript
// 向微信注册应用ID
WeChat.registerApp('你的微信应用ID', (res) => {
    alert(res); // true or false
});
```
- isWXAppInstalled(callback) 检测是否已安装微信
```javascript
WeChat.isWXAppInstalled((res) => {
    alert('isWXAppInstalled: '+res); // true or false
});
```

- getWXAppInstallUrl(callback)获取微信的itunes安装地址

- isWXAppSupportApi(callback)判断当前微信的版本是否支持OpenApi

- getApiVersion(callback)获取当前微信SDK的版本号

- openWXApp(callback)打开微信客户端

- sendAuthReq(scope, state, callback) 发起认证请求
```javascript
let state = 'wechat_sdk_test'; 
let scope = 'snsapi_userinfo';
WeChat.sendAuthReq(state, scope, (res) => {
    alert(res); // true or false
});
```



## 更新日志
##### 2015.12.11
* 方法的回调函数变为可选的
* 新增WXApi方法，详见方法列表
* `sendAuthRequest`方法改为`sendAuthReq`
* 事件名`finishedAuth`改为`didRecvAuthResponse`

## Example
记得要将 AppDelegate.m 文件中的IP换成自己的:

```Objective-c
jsCodeLocation = [NSURL URLWithString:@"http://172.16.5.70:8081/index.ios.bundle?platform=ios&dev=true"];
```

![截图](./demo.gif)

