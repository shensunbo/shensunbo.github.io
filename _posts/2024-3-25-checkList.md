---
layout:       post
title:        "检查表"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - 检查表
    - checklist
---
# 类检查表
1. 是否需要构造函数
2. 成员是否私有
3. 是否需要无参构造函数
4. 是否每个构造函数初始化所有数据成员
5. 是否需要析构函数
6. 是否需要虚析构函数，基类需要虚析构函数，否则使用基类指针指向子类，析构可能出错
7. 是否需要复制构造函数，禁止复制需要将复制构造函数声明为私有
8. 删除数组时使用delete []
9. 复制构造函数和赋值构造函数参数加const
10. 引用是否要为const
11. 成员函数声明为const

# 函数检查表  
