---
title: Sublime Text
date:   2014-5-5 00:00:00 +0700
categories: [Tech, Tool]
tags: [sublimetext]
---


>Sublime Text 工具使用技巧







### install Package Control

Ctrl + Shift + P

input:

	Package Control: Install Package

then：

	Install Package


### Pretty JSON

CTRL + SHIFT + P  and input

	Package Control: Install Package

input

	Pretty JSON

ENTER then
->Preferences > Package Settings > Pretty JSON > Settings - Default​
press：

	Cmd+Ctrl+J 

format JSON


### support chinese package

subline text2 支持中文，安装ConvertToUTF8

设置在同一个窗口打开：

	open_files_in_new_window：false


### Lua--IDE

sublime text2

tools ->new build system

	{
	    "cmd": ["/usr/local/bin/lua", "$file"],
	    "file_regex": "^(?:lua:)?[\t ](...*?):([0-9]*):?([0-9]*)",
	    "selector": "source.lua"
	}

保存为Lua.sublime-build,选择默认bulid System:Lua


### 安装GitHub插件

#### sublime text2

	Shift-CMD-P --》 Add Repository

	paste github url

	Shift-CMD-P --》 Install Package

	Shift-CMD-P --》 XXXXX(your new package name)


#### sublime text3

	download the github xxx.zip

	change name xxx.sublime-package

	put the xxx.sublime-package to Packages flode

	reboot sublime



### Quick Key for SublimeText

1. shift+"<-"/"->" :selected word
2. ctrl+shift+k:delete line
3. shift+cmd+d:copy line and paste
4. cmd+"["/"]":format code
5. cmd + enter:new line next line insert
6. cmd + shift +enter:new line before line insert



### More Themes

http://scotch.io/bar-talk/the-best-sublime-text-3-themes-of-2014


