---
layout:       post
title:        "Android renderer learn blog"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - Android 
    - renderer
---
# 仓库
[github repo](https://github.com/shensunbo/android_native)

# code
## cpp 部分目录结构
```
app/src/main/
├── cpp/                          # 你的 C++ 源代码
│   ├── native_lib.cpp
│   └── ...
├── jniLibs/                      # 预编译的 .so 库文件（按 ABI 分类）
│   ├── arm64-v8a/
│   │   └── libthirdparty.so
│   ├── armeabi-v7a/
│   │   └── libthirdparty.so
│   ├── x86/
│   │   └── libthirdparty.so
│   └── x86_64/
│       └── libthirdparty.so
└── cppLibs/                      # 第三方 C++ 库（源码/头文件）
    └── thirdparty/
        ├── include/              # 头文件
        │   └── *.h
        └── lib/                  # 静态库或源码
            └── *.a 或 *.cpp
```

## build assimp for android
```
# 设置 NDK 路径
export ANDROID_NDK=/opt/ndk

# 创建 Android 构建目录
mkdir -p build-android-x86_64
cd build-android-x86_64

# 配置 CMake (针对 Android x86_64)
cmake .. \
  -DCMAKE_TOOLCHAIN_FILE=$ANDROID_NDK/build/cmake/android.toolchain.cmake \
  -DANDROID_ABI=x86_64 \
  -DANDROID_PLATFORM=android-29 \
  -DCMAKE_BUILD_TYPE=Release \
  -DASSIMP_BUILD_TESTS=OFF \
  -DASSIMP_BUILD_ASSIMP_TOOLS=OFF \
  -DASSIMP_NO_EXPORT=ON
  
# 编译
cmake --build . -j$(nproc)

# strip 多余符号以减小库大小
strip --strip-unneeded bin/libassimp.so

# 验证生成的库
file bin/libassimp.so*
```
> bin/libassimp.so: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, BuildID[sha1]=1e3bb0c14cb676968780c11b989ab7d277ff8860, with debug_info, not stripped

