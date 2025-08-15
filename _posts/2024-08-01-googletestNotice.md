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

[https://github.com/shensunbo/google_test_insight](https://github.com/shensunbo/google_test_insight)
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
3. `EXPECT_CALL` 中的 `WillOnce` 实际上给`Mock`接口指定了一个返回对象，如果代码逻辑中有对于接口返回的对象的处理的话，需要对这个地方进行特殊处理。比如返回的是一个类对象，且你需要使用这个类中的成员或指针等等。
