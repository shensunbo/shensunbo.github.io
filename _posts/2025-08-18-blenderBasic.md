---
layout:       post
title:        "blender 常用操作"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - blender
---
# 修改物体本地坐标系原点
1. 进入编辑模式，选中要作为新的原点的点
2. 对齐游标，按 Shift + S → Cursor to Selected
3. 返回物体模式，设置原点：右键 → Set Origin → Origin to 3D Cursor

# 测量
1. 按住ctrl吸附到点或者边
2. 按住xyz固定坐标轴

# 给物体增加控制节点
1. 创建一个空物体(empty)，移动到合适的位置，将要控制的物体添加为这个空物体的子物体。可以用来控制物体的旋转、缩放等。

# 合并物体
1. 合并物体并不会合并材质槽，会同时保留所有材质，如何只需要一种材质，需要使用材质旁边的减号，remove material slot