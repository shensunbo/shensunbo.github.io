---
layout:       post
title:        "before main"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - codeCommon
    - c++
    - main
---

1. 不要使用`std::cout`, 在main函数开始之前cout可能未初始化完成，导致段错误  
2. `__attribute__((constructor))` 可以存在多个，同一文件中，按声明顺序， 函数执行顺序为：从上到下。在不同文件中，执行顺序不确定

```
#include <iostream>
#include <cstdio>
// before main test demo
// for GCC

static int i = 0;

__attribute__((constructor)) void beforeMain() {
    i++;
    printf("Before main() - __attribute__((constructor)), i %d\n", i);
    return; 
}

__attribute__((destructor)) void afterMain() {
    printf("After main() - __attribute__((destructor))\n");
    return; 
}

int main(int argc, char *argv[])
{
    std::cout << "Hello, World! i: " << i << std::endl;
    return 0;
}
```