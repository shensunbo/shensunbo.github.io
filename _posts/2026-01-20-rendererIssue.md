---
layout:       post
title:        "renderer渲染器问题及解决方案"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - renderer
    - rendering
    - opengl
---
# 模型加载速度问题
## assimp的aiProcessPreset_TargetRealtime_Quality配置加载速度慢
- 问题： aiProcessPreset_TargetRealtime_Quality 包含了 CalcTangentSpace 这个选项，会计算切线空间，导致加载速度变慢
- 解决方案：使用DCC工具预先计算好切线空间，导出模型时选择包含切线空间的数据，然后在加载模型时去掉 CalcTangentSpace 选项
- 实践：

# 纹理图片加载速度问题
[texture loading speed issue](https://shensunbo.github.io/2026/01/09/renderer/)

# FPS 问题
[fps issue](https://shensunbo.github.io/2026/01/09/renderer/)