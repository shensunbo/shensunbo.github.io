---
layout:       post
title:        "google test notice"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - gtest
    - unit test
---
# 配置问题
## 使用 `ctest -V`，输出到命令行的测试结果没有颜色 
1. 可以使用 `GTEST_COLOR=1 ctest -V`，但这会污染测试报告 
    ```
    #!/bin/bash

    previous_dir=$(pwd)
    script_path=$(dirname $(realpath $0))

    cd $script_path/output/<test script path>

    if [ "$1" = "--color" ]; then
        GTEST_COLOR=1 ctest "$@" --output-on-failure
    else
        ctest "$@" --output-on-failure
    fi


    cd $previous_dir
    ```
2. 可以直接执行单元测试`bin`文件，默认有颜色

