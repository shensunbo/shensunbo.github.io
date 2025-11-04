---
layout:       post
title:        "RenderDoc in wsl2 ubuntu"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - renderdoc
    - wsl2
---

直接安装的版本会出现段错误

# 使用官方编译的版本
## 下载 renderdoc
wget https://renderdoc.org/stable/latest/renderdoc_latest.tar.gz
## 解压
tar -xvf renderdoc_latest.tar.gz
## 进入目录
cd renderdoc-*
## 运行
./bin/qrenderdoc

## 安装依赖项
```
Requirements:
-------------

* libxcb
* libX11
* libX11-xcb
* libGL

These should be available on any system with X installed. You can always
build RenderDoc yourself from source. For more information, github.

To run qrenderdoc, Qt is statically linked but additional system
libraries are needed:

* libstdc++ >= 6.0.21 (from GCC 5.x)
* libfontconfig1 >= 2.11.0
* libfreetype6 >= 2.4.8
* libproxy >= 0.4.7
```

# done
![renderdoc](/img/in-post/blah/renderdoc.png)