---
layout:       post
title:        "使用blender为模型添加logo纹理"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - blender
    - fbx
---
TODO: no pics for now

---
1. 使用blender导入要修改的模型
2. 切换到材质视图
3. 选中要添加logo的部件，并在编辑器中选中材质
4. 打开Base Color 编辑器，选中 Image Texture
5. 打开要添加的纹理图片
6. 边缘选择Extend，以便观察logo效果
7. 打开UV Editing
8. 建议切换到材质视图，以便观察实时的纹理应用效果
9. 如果模型默认的UV布局不正确，使用UV->进行一个基本的UV展开(因为logo需要我们手动调节放置的位置)
10. 将展开的平面全部移动到纹理图片之外
11. 切换到 face select 模式，选中几个面来测试一下模型与展开平面的对应关系(按住shift连续选择)
12. 选中需要应用logo的区域
13. 调整展开视图的大小、位置和缩放
14. 返回layout视图查看叠加效果
15. 导出

---
- note
由于一般3D查看的的纹理效果对于超出纹理范围的处理都是线性重复，所以会出现下图所示的效果。在自己的渲染器中，需要特殊处理
    1. GL_TEXTURE_WRAP_S， GL_TEXTURE_WRAP_T 属性需要设置为 GL_CLAMP_TO_BORDER
    2. 需要对logo纹理没有覆盖到的区域，根据透明度进行特殊处理
