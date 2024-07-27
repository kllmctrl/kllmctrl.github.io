---
title: Lua Binding - cocos2dx c++ class
date:   2015-6-2 00:00:00 +0700
categories: [Tech, Game, Cocos2dx]
tags: [cocos2dx]
---


>自定义 C++ 类绑定 Lua

---
例子:  
用C++实现一个 MyClass 类，需要在 Lua 中使用  
local test = my.MyClass:create()  
print("lua bind: " .. test:foo(99))  





### 自定义 C++ 类

Put them in Classes floder

MyClass.h
MyClass.cpp

MyClass.h

```c
    #include "cocos2d.h"
    using namespace cocos2d;

    class MyClass : public Ref
    {
    public:
      MyClass()   {};
      ~MyClass()  {};
      bool init() { return true; };
      CREATE_FUNC(MyClass);
      int foo(int i);
    };

```

MyClass.cpp

```c

    #include "MyClass.h"

    int MyClass::foo(int i)
    {
      return i + 100;
    }

```


### 配置 .ini

复制 frameworks/cocos2d-x/tools/tolua/**.ini 到 frameworks/cocos2d-x/tools/tolua/MyClass.ini

config that to link the MyClass.h path and  MyClass lua pageage.

```ruby
    [MyClass]

    prefix = MyClass

    target_namespace = my

    android_headers = 
    -I%(androidndkdir)s/platforms/android-14/arch-arm/usr/include 
    -I%(androidndkdir)s/sources/cxx-stl/gnu-libstdc++/4.7/libs/armeabi-v7a/include 
    -I%(androidndkdir)s/sources/cxx-stl/gnu-libstdc++/4.7/include 
    -I%(androidndkdir)s/sources/cxx-stl/gnu-libstdc++/4.8/libs/armeabi-v7a/include 
    -I%(androidndkdir)s/sources/cxx-stl/gnu-libstdc++/4.8/include
    android_flags = -D_SIZE_T_DEFINED_ 

    clang_headers = -I%(clangllvmdir)s/lib/clang/3.3/include 
    clang_flags = -nostdinc -x c++ -std=c++11

    cocos_headers = -I%(cocosdir)s/cocos -I%(cocosdir)s/cocos/platform/android
    cocos_flags = -DANDROID

    cxxgenerator_headers = 

    extra_arguments = %(android_headers)s %(clang_headers)s %(cxxgenerator_headers)s 
    %(cocos_headers)s %(android_flags)s %(clang_flags)s %(cocos_flags)s 
    %(extra_flags)s 

    headers = %(cocosdir)s/../runtime-src/XXX/Classes/MyClass.h

    classes = MyClass

    skip =

    rename_functions = 

    rename_classes = 

    remove_prefix = 

    classes_have_no_parents = 

    base_classes_to_skip = 

    abstract_classes = 

    script_control_cpp = no

```

修改 genbindings.py 中的 129 行代码


```ruby
cmd_args = {#'cocos2dx.ini' : ('cocos2d-x', 'lua_cocos2dx_auto'), \
    #'cocos2dx_extension.ini' : ('cocos2dx_extension', 
    lua_cocos2dx_extension_auto'), \
    #'cocos2dx_ui.ini' : ('cocos2dx_ui', 'lua_cocos2dx_ui_auto'), \
    #'cocos2dx_studio.ini' : ('cocos2dx_studio', 'lua_cocos2dx_studio_auto'), \
    #'cocos2dx_spine.ini' : ('cocos2dx_spine', 'lua_cocos2dx_spine_auto'), \
    #'cocos2dx_physics.ini' : ('cocos2dx_physics', 'lua_cocos2dx_physics_auto'), \
    #'cocos2dx_experimental_video.ini' : ('cocos2dx_experimental_video', 
    'lua_cocos2dx_experimental_video_auto'), \
    #'cocos2dx_experimental.ini' : ('cocos2dx_experimental', 
    'lua_cocos2dx_experimental_auto'), \
    #'cocos2dx_controller.ini' : ('cocos2dx_controller', 
    'lua_cocos2dx_controller_auto'), \
    'MyClass.ini' : ('MyClass', 'lua_MyClass_auto'), \
    }
```


运行脚本

```ruby

    python genbindings.py

```

如果脚本执行成功，即生成文件

lua_MyClass_auto.hpp
lua_MyClass_auto.cpp

in frameworks/cocos2d-x/cocos/scripting/lua-bindings/auto/


### 在工程注册 MyClass 类

in AppDelegate.cpp

```c

    #include "lua_MyClass_auto.hpp"

```

and

```c
register_all_MyClass(engine->getLuaStack()->getLuaState());

```


### 在 Lua 中使用 MyClass 类

```lua
local test = my.MyClass:create()
print("lua bind: " .. test:foo(99))
```


### IOS

add 
lua_MyClass_auto.hpp
lua_MyClass_auto.cpp
to cocos2d_lua_bindings.xcodeproj

add
MyClass.h
MyClass.cpp
to project

in cocos2d_lua_bindings.xcodeproj add MyClass.h 's path

User Header Search Paths

```ruby
$(SRCROOT)/../../../../../runtime-src/XXX/Classes

```

then build and run->Sucesss


### Android

add MyClass.cpp file to 

```ruby
LOCAL_SRC_FILES := hellolua/main.cpp \
                   ../../Classes/AppDelegate.cpp \
                   ../../Classes/MyClass.cpp

```


and then modify lua-bindings 's Android.mk


```ruby

LOCAL_SRC_FILES

add auto/lua_MyClass_auto.cpp

LOCAL_C_INCLUDES

$(LOCAL_PATH)/../../../../runtime-src/XXX/Classes
```


