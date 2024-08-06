---
title: iOS app logcat
date:   2020-06-24 00:00:00 +0700
categories: [Tech, iOS]
tags: [ios, logcat]
---


>iOS app log view on termial not use Xcode


### How to use

in termial

```shell
idevicesyslog | grep game

# idevicesyslog | grep game
# idevicesyslog | grep game --color=always
# idevicesyslog | grep game | grep -v WebKit | grep -v CFNetwork | grep -v assertiond
```





### issue

#### 1. ERROR: Could not start service com.apple.syslog_relay.

> fix 2020-06-22

```shell
brew uninstall libimobiledevice usbmuxd
brew install -v --HEAD --build-from-source usbmuxd libimobiledevice
```

