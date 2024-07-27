---
title: Cocos2dx life cycle
date:   2015-7-20 00:00:00 +0700
categories: [Tech, Game, Cocos2dx]
tags: [cocos2dx]
---


>介绍使用cocos2d-x的程序正常启动、回到后台，回到前台、强制退出的生命周期


#### 1、Cocos2dx - C++ 层

```c
bool AppDelegate::applicationDidFinishLaunching(){
}

void AppDelegate::applicationWillEnterForeground(){
}

void AppDelegate::applicationDidEnterBackground(){
}
```



#### 2、iOS - AppController - Object-C 层

```c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
}

- (void)applicationWillResignActive:(UIApplication *)application {
}

- (void)applicationDidBecomeActive:(UIApplication *)application {
}

- (void)applicationDidEnterBackground:(UIApplication *)application {
}

- (void)applicationWillEnterForeground:(UIApplication *)application {
}

- (void)applicationWillTerminate:(UIApplication *)application {
}
```

#### 3、Andorid - Activity - Java层

```c
public class MainActivity extends Cocos2dxActivity{
     protected void onCreate(Bundle savedInstanceState){
     }

@Override
    protected void onResume() {
super.onResume();
}

@Override
    protected void onPause() {
super.onPause();
}

@Override
    protected void onStart() {
super.onStart();
}

@Override
    protected void onStop() {
super.onStop();
}

}
```

#### 4、调试

##### 4.1、正常启动执行顺序：

4.1.1 iOS
didFinishLaunchingWithOptions(objc)
applicationDidFinishLaunching(c++)
applicationDidBecomeActive

4.1.2 Android
onCreate
onStart
onResume
applicationDidFinishLaunching

##### 4.2、退出后台

4.2.1 iOS
applicationWillResignActive(objc)
applicationDidEnterBackground(objc)
applicationDidEnterBackground(c++)

4.2.2 Android
onPause
onStop
applicationDidEnterBackground

##### 4.3、再次启动

4.3.1 iOS
applicationWillEnterForeground(objc)
applicationWillEnterForeground(c++)
applicationDidBecomeActive(objc)

4.3.2 Android
onStart
onResume
applicationWillEnterForeground

##### 4.4、中断程序

4.4.1 iOS

applicationWillResignActive(objc)
applicationDidEnterBackground(objc)
applicationDidEnterBackground(c++)
applicationWillTerminate(objc)

4.4.2 Android

onPause
applicationDidEnterBackground(c++)
onStop

