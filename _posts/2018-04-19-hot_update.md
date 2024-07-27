---
title: Unity 热更新
date: 2018-04-19 00:00:00 +0800
categories: [Tech, Unity]
tags: [unity, tolua, xlua]
---


# 技术研究

## XLua

> 腾讯2017年开源的手游热更新方案

- 开发只用C#
- 运行也是C#，性能可以秒杀lua
- 出问题了才用Lua来改掉C#出问题的部位，替换甚至做到不用重启游戏
- 补丁需要预打入
- 开头加入几个il指令对性能的开销


xLua会在C#编译成il后插入一个处理，该处理会从il层面为每个打了Hotfix标签的类型的函数开头加入几个il指令，如果用C#描述是这样的


```c

public int Add(int a, int b)
{
    return a + b;
}

// 加了il指令：
static Func<object,int,="" int=""> hotfix_Add = null;

public int Add(int a, int b)
{
    if (hotfix_Add !=null) return hotfix_Add(this, a, b);
    return a + b;
}

```


如果lua中执行了hotfix调用，hotfix_Add会指向一个lua的适配函数。



## toLua

开发平台成熟稳定，Bug修复迅速

- 开发者众多，资源丰富
- 静态方法，性能优
- 有成功商业产品案例（啪啪三国、超神战队、酷鱼吧捕鱼、绝地战警、这不是刀塔等） 
- 都是基于原生版本的改进；未来，两者会合并成一个插件


Unity静态绑定lua的一个解决方案，它通过C#提供的反射信息分析代码并生成包装的类。它是一个用来简化在C#中集成lua的插件，可以自动生成用于在lua中访问Unity的绑定代码，并把C#中的常量、变量、函数、属性、类以及枚举暴露给lua。它是从cstolua衍变而来。从它的名字可以看出，它是集成了原来的tolua代码通过二次封装写了一个C#与tolua(c)的一个中间层。



