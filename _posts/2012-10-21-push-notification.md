---
title: Notification - iOS
date:   2012-10-21 00:00:00 +0700
categories: [Tech, iOS]
tags: [ios]
---


>实现iOS 远程推送客户端和服务端。


### 准备资料

#### 在app证书新建推送证书

（下载并安装...记住保留p12(cer.p12和key.p12)+cer+mobileprovision以后更新包用到，避免撤销后服务端要重新配置新的p12）




#### 生成app在服务端需要的许可证文件

1）进入Provisioning Portal, 下载Certificates在development下的证书。 

2） 找到需要测试的app id,然后enable它在development下的Apple Push Notification service: Development Push SSL Certificate。需要输入1）中的签名证书才可以生成一个aps_developer_identity.cer.

3) 双击aps_developer_identity.cer，会打开系统的key chain. 在My certificates下找到Apple Development Push Services。需要为certificate和它之下的private key各自export出一个.p12文件。(会出现设置密码过程)

4）需要将上面的2个.p12文件转成.pem格式：

```ruby
openssl pkcs12 -clcerts -nokeys -out cert.pem -in cert.p12
openssl pkcs12 -nocerts -out key.pem -in key.p12
```


5）如果需要对key不进行加密：

``` ruby

openssl rsa -in key.pem -out key.unencrypted.pem

```


6）然后就可以合并两个.pem文件, 这个ck.pem就是服务端需要的证书了。

``` ruby

cat cert.pem key.unencrypted.pem > ck.pem

```


### 代码实现

#### IOS代码：

代理

``` ruby

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions 
{ 
//**** 消息推送 ****  
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];  
return YES;  
}

```


``` ruby

#pragma mark -  
#pragma mark push delegate  
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken{  
    NSString *deviceTokenString=[NSString stringWithFormat:@"%@",deviceToken];  
    deviceTokenString = [deviceTokenString stringByReplacingOccurrencesOfString:@"<" withString:@""];  
    deviceTokenString = [deviceTokenString stringByReplacingOccurrencesOfString:@">" withString:@""];  
    deviceTokenString = [deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""];  
}  
- (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error{  
}  
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo{

[[UIApplication sharedApplication] applicationIconBadgeNumber:0];
}

```



#### 服务端测试php代码：


``` ruby

<?php  
$deviceToken = '设备号.....';  
$pass = '......';//ck.pem密码  
  
//定义消息和声音  
$message = $_GET['message'] or $message = $argv[1] or $message = 'message by kllmctrl';   
$badge = (int)$_GET['badge'] or $badge = 3;//数量  
$sound = $_GET['sound'] or $sound = 'default';//声音  
  
//要再客户端显示的对话框  
$body = array();  
$body['aps'] = array('alert' => $message);  
if ($badge)  
  $body['aps']['badge'] = $badge;  
if ($sound)  
  $body['aps']['sound'] = $sound;  
  
//文件路径  
$ctx = stream_context_create();  
stream_context_set_option($ctx, 'ssl', 'local_cert', './ck.pem');//ck.pem所在的路径  
//配置路径  
stream_context_set_option($ctx, 'ssl', 'passphrase', $pass);  
  
//链接apple  
$fp = stream_socket_client('ssl://gateway.sandbox.push.apple.com:2195', $err, $errstr, 60, STREAM_CLIENT_CONNECT, $ctx);  
if (!$fp) {  
    print "Failed to connect $err $errstr\n";  
    return;  
}  
else {  
   print "Connection OK\n<br/>";  
}  
  
//发送消息  
$payload = json_encode($body);  
$msg = chr(0) . pack("n",32) . pack('H*', str_replace(' ', '', $deviceToken)) . pack("n",strlen($payload)).$payload;  
print "Sending message :" . $payload . "\n";    
fwrite($fp, $msg);   
fclose($fp);  
?> 

```



### 测试


``` ruby

http://localhost:8888/push.php

```

注意：iphone描述文件里面的证书要有效。



