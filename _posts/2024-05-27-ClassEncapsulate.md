---
layout:       post
title:        "封装成库"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - 库
    - 封装
---

1. 使用前向声明和成员指针，减少需要引用的头文件。前向声明配合指针可以将头文件引用从头文件转移到源文件  
```
class B;

class A{
    std::shared_ptr<B> b;
}
```