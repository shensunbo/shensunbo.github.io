---
layout:       post
title:        "编译、链接与库"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - linux
---
1. 链接顺序
    ```
    This is because the linker resolves symbols in the order that the libraries are listed on the command line. If a library is listed before the library it depends on, the linker will not be able to resolve the symbols, resulting in an error.

    So, in the example I gave earlier:

    A depends on B
    B depends on C
    The correct linking order would actually be:

    A
    B
    C
    ```
2. 查看依赖的库
    ```
    1. on target platform
        ldd <libname>
    2. not on target platform
        objdump -x <libname> | grep NEEDED
        readelf -d <libname>| grep NEEDED
        nm -D <libname> | grep NEEDED
        elfdump -d <libname> | grep NEEDED
    ```
3.恢复默认的交叉编译选项
    `unset LD_LIBRARY_PATH`