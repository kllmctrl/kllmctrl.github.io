---
title: Color Mix - Lua
date:   2014-12-8 00:00:00 +0700
categories: [Tech, Lua]
tags: [lua]
---


>混合两种颜色

---
例子:  
蓝 + 黄 = 绿  
蓝 + 红 = 紫  


### 使用 CMYK 原理混合颜色

```lua
-- color mix using CMYK mixing
local function colorToCymk(aColor)
    local cyan = 255 - aColor.r
    local magenta = 255 - aColor.g
    local yellow = 255 - aColor.b
    local black   = math.min(cyan, magenta, yellow);
    local cyan    = ((cyan - black) / (255 - black));
    local magenta = ((magenta - black) / (255 - black));
    local yellow  = ((yellow  - black) / (255 - black));
  return {c=cyan,m=magenta,y=yellow,k=black/255};
end

local function colorToRgba(aColor)
    local R=0
    local G=0
    local B=0
    R = aColor.c * (1.0 - aColor.k) + aColor.k
    G = aColor.m * (1.0 - aColor.k) + aColor.k
    B = aColor.y * (1.0 - aColor.k) + aColor.k
    R = math.floor((1.0 - R) * 255.0 + 0.5)
    G = math.floor((1.0 - G) * 255.0 + 0.5)
    B = math.floor((1.0 - B) * 255.0 + 0.5)
    return ccc3(R,G,B);
end

local function colorMix(aColor1,aColor2)
    local C = 0
    local M = 0
    local Y = 0
    local K = 0
    local A = 0
    local colors = {aColor1,aColor2}
    for i,v in ipairs(colors) do
        local tempColor = colorToCymk(v)
        C = C + tempColor.c
        M = M + tempColor.m
        Y = Y + tempColor.y
        K = K + tempColor.k
        -- A = A + tempColor.a --default 255
    end
    C = C/2 -- 2:colors length
    M = M/2
    Y = Y/2
    K = K/2
    A = A/2
    local aColorCymk = {c=C,m=M,y=Y,k=K}
    local aColorRgb = colorToRgba(aColorCymk)
    return aColorRgb
end
```
