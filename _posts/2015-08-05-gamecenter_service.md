---
title: Game center service in game
date:   2015-8-5 00:00:00 +0700
update:   2020-06-21 00:00:00 +0700
categories: [Tech, Game]
tags: [gamecenter]
---

>Add mobile os game center service in game (iOS: Game Center, Android: Play Games Services)


## iOS - Game Center 排行榜

Game Center 是苹果自带的游戏排行榜，在客户端中有单独的Game Center应用，里面汇集了添加Game Center的游戏。

添加步骤:  
1) 在游戏的 App ID 开启 Game Center 服务  
2) 工程中添加 GameKit.framework  
3) Info添加支持gamekit -> Required device capabilities:gamekit:YES  





排行榜功能实现

### 1.1 登陆验证用户


```objc
[[GKLocalPlayer localPlayer] authenticateWithCompletionHandler:^(NSError *error){
         if(error == nil){
             //认证成功
         }else{
             NSLog(@"authenticateWithCompletionHandler fail-%@",[NSString stringWithFormat: @"Reason: %@", [error localizedDescription]]);
         }
     }];
```


#### 1.2 显示历史排行榜

```objc
GKLeaderboardViewController *leaderboardController = [[GKLeaderboardViewController alloc] init];
if (leaderboardController != nil){
    UIApplication* clientApp = [UIApplication sharedApplication];
    UIWindow* topWindow = [clientApp keyWindow];
    if (!topWindow){
        topWindow = [[clientApp windows] objectAtIndex:0];
    }
    [topWindow addSubview:kllmctrl_VC.view];
    leaderboardController.leaderboardDelegate = self;
    [kllmctrl_VC presentModalViewController: leaderboardController animated: YES];
}
```

#### 1.3 获取最高得分

该方法在用户验证成功后调用

```objc

	[GKLeaderboard loadLeaderboardsWithCompletionHandler:^(NSArray *leaderboards, NSError *nsError) {
        if( nsError != nil ){
            NSLog(@"Error:%@",[nsError description]);
            return ;
        }
        for( GKLeaderboard* board in leaderboards ){
            board.playerScope = GKLeaderboardPlayerScopeFriendsOnly ;
            board.timeScope = GKLeaderboardTimeScopeAllTime ;
            
            NSRange range = {.location = 1, .length = 1};
            board.range = range ;
            
            [board loadScoresWithCompletionHandler:^(NSArray *scores, NSError *error) {
                if (error == nil) {
                    //  得到历史最高分记录 board.localPlayerScore.value
                }
            }];
        }
    }];

```

#### 1.4 提交积分到排行榜

```objc

	GKLocalPlayer *localPlayer = [GKLocalPlayer localPlayer];
    if ([localPlayer isAuthenticated] == NO){
        NSLog(@"You must authenticate the local player first.");
        return NO;
    }
    GKScore *score = [[[GKScore alloc]
                       initWithCategory:kllmctrl_Leaderboard_name] autorelease];
    
    score.value = (int64_t)kllmctrl_score;    
    [score reportScoreWithCompletionHandler:^(NSError *error) {
        if (error){
            NSLog(@"Failed to report the error. Error = %@", error);
        }
    }];

```

### 2、android - Play Games Services 排行榜


Play Games Services: 自带的google services 服务，sdk已经包含在google services内。

添加步骤
1）工程配置文件xml添加
	<meta-data android:name="com.google.android.gms.games.APP_ID"
            android:value="@string/app_id" />
        <meta-data android:name="com.google.android.gms.version"
                   android:value="@integer/google_play_services_version"/>

排行榜功能实现


参考sample: [playgameservices/android-basic-samples: Google Play game services - Android samples](https://github.com/playgameservices/android-basic-samples)

