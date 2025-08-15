---
layout:       post
title:        "多线程注意事项"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - 多线程
---

### 类  
1. 给多线程使用的类，类成员函数应该尽量避免使用静态变量和全局变量，应该尽量保证接口是无状态的、可重入的和线程安全的  
