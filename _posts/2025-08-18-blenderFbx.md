---
layout:       post
title:        "blender 模型处理"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - blender
    - fbx
---
# pack and unpack
## pack
1. 可以将外部的文件如纹理图片等，嵌入到blender工程中。路径 `file`-`external data`-`pack resources`.
2. 如果纹理图片是嵌入在模型文件中的，在该纹理的image属性中，路径显示是灰色的，且unpack按钮高亮

## unpack
1. 嵌入在FBX或者blender工程中的图片，可以解包，路径 `file`-`external data`-`unpack resources`.

## find missing files
1. 如果工程中的纹理图片路径错误，找不到，可以通过`file`-`external data`-`find missing files`来重新匹配

# material 
## 给同一个object的不同部分指定不同的材质
1. 编辑模式，选中要改变材质的面
2. 材质属性里面选中要赋予的材质，assign

# 减面 Decimate
# 减面 Decimate

使用 Blender 的 Decimate 修改器对 3D 模型进行简化，裁剪三角形的数量。Decimate Modifier（简化修改器）允许您减少网格的顶点/面数，同时将形状变化降到最低。有三种模式：

### 1. Collapse
- 最常用的简化方案，通过将面的顶点合并来减少多边形数量。
- 通过调整 Ratio 参数，控制要保留的面片比例（0~1）。
    - 0 表示移除所有多边形，1 表示保留原始多边形。
    - 例如，0.5 意味着模型多边形数量减少到一半。

### 2. Unsubdivide
- 适用于逆向处理，减少细分的边数。
- 尝试将细分表面反向为较少的多边形数。
- Iterate 参数用于控制反向细分的次数，每增加一次迭代，移除更多多边形。
- 适合已经经过 Subdivision Surface 修改的模型，目的是减少面数而尽量保留外观。

### 3. Planar
- 用于平面区域的简化。
- 可以指定一个角度阈值，只有当相邻面之间的角度小于该值时，才会合并面。
- Planar 模式会分析模型的每个面并检查相邻面之间的角度。如果相邻面的法向量之间的角度小于指定阈值，则这些面会被合并。

---

- 三个模式可以通过添加多个修改器同时使用，但一个修改器只能使用一种模式。
- 使用修改器可以在不破坏原始网格的情况下进行改变，允许你随时调整或删除修改器而不影响基础模型。