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