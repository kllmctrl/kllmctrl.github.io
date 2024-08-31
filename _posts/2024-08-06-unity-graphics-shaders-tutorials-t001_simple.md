---
title: 如何在 Unity 中编写着色器：分步指南
date:  2024-08-06 00:00:00 +0800
categories: [Tech, Unity]
tags: [shader]
media_subpath: /media
---


![](unity-graphics-shaders-tutorials-t001_simple__tTR6hCIi.png)


特效在游戏中可以增强游戏体验并让您的游戏世界栩栩如生。在本文中，我们将逐步介绍如何使用 Shader 脚本。


## 开发环境

- Windows 10
- Unity 2020.3
- Rider 2023


## 步骤 1：创建场景

第一步是在Unity项目中创建一个新的场景，选择带有摄像机和灯光的模板。保存为Simple.unity

![](unity-graphics-shaders-tutorials-t001_simple__b5gZjNTh.png)

创建的场景如下

![](unity-graphics-shaders-tutorials-t001_simple__YZilzOtt.png)


## 步骤 2：创建3D物体

在Unity Editor的 Hierarchy 鼠标右键添加一个球体，"3D Object" -> "Sphere"

![](unity-graphics-shaders-tutorials-t001_simple__ftrYOMr4.png)


![](unity-graphics-shaders-tutorials-t001_simple__gb6kER5m.png)


## 步骤 3：创建材质

在Project视图的Assets文件夹下创建一个材质球。

![](unity-graphics-shaders-tutorials-t001_simple__i8E0xAcQ.png)


命名为 SimpleMat, 将材质球拖到场景创建的球体 Sphere


## 步骤 4：创建Shader脚本

在Project视图的Assets文件夹下创建一个Shader脚本。

![](unity-graphics-shaders-tutorials-t001_simple__R6alOiaM.png)

命名为SimpleShader

```shader
Shader "mc/SimpleShader"
{
    SubShader
    {
        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            struct appdata
            {
                float4 vertex : POSITION;
            };

            struct v2f
            {
                float4 vertex : SV_POSITION;
            };

            // 顶点着色器
            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                return o;
            }

            // 片元着色器
            fixed4 frag (v2f i) : SV_Target
            {
                fixed4 col = fixed4(1.0, 0.0, 0.5, 1.0);
                return col;
            }
            ENDCG
        }
    }
}
```

可以通过修改 frag 函数中返回的 col值，改变场景中球体的显示颜色

