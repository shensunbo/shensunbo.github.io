---
layout:       post
title:        "Blender 加载 fbx 模型错位"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - blender
    - fbx
---
> 使用blender导入fbx后，一些部件会错位，比如汽车的轮子和车门等

# 坐标系
1. Blender 使用右手坐标系，Z轴向上，Y轴向前，X轴向右
2. unity 使用左手坐标系，Y轴向上，Z轴向前，X轴向右
> 跟这个应该没什么关系

# 临时解决方案
使用unity导入fbx，然后再导出fbx，最后再用blender导入
> blender导入fbx会新建mesh名称，原来的mesh名称会作为object名称
> 这种做法会导致控制节点的改变，影响旋转