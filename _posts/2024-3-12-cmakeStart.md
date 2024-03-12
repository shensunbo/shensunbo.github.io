---
layout:       post
title:        "camke 入门"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - cmake
---

1. 初始目录，使用两个静态库 
```
├── include
│   ├── byeworld.h
│   └── helloworld.h
├── lib
│   ├── libbyeworld.a
│   └── libhelloworld.a
└── src
    └── main.cpp
``` 

2. 根目录下创建一个`CMakeLists.txt`, 内容如下  
  
```
 # not necessary
cmake_minimum_required(VERSION 3.10)

 # set project name
project(HelloWorld)

 # add .h file path
include_directories(include)

 # add lib file path
link_directories(lib)

 # add executable file
add_executable(helloExe src/main.cpp)

 # link lib 
target_link_libraries(helloExe  byeworld helloworld)

``` 
3. 根目录下创建一个`build`文件来存放输出文件，`build`文件夹下执行 `cmake ..` __(`cmake` 后跟`CMakeLists.txt`文件的路径)__ 
4. 执行完之后会输出一些文件，其中有`makefile` 
5. 在 `build`文件夹下执行`make`指令，构建项目，执行结果如下 
```
:~/code/testCode/cmakeTest/build$ make
Scanning dependencies of target helloExe
[ 50%] Building CXX object CMakeFiles/helloExe.dir/src/main.cpp.o
[100%] Linking CXX executable helloExe
[100%] Built target helloExe
``` 
6. 运行编译出的可执行文件 
```
~/code/testCode/cmakeTest/build$ ./helloExe 
 hello the damn world 
 bye world
``` 