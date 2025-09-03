---
layout:       post
title:        "交叉编译android native cpp lib"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - android
    - native cpp
---
# build(for android studio simulator)
```
    export ANDROID_NDK=/opt/ndk

    cmake -DCMAKE_TOOLCHAIN_FILE=$ANDROID_NDK/build/cmake/android.toolchain.cmake \
        -DANDROID_ABI=x86_64 \
        -DANDROID_PLATFORM=android-29 \
        ..

    cmake --build .
```

# 库文件需要放到ABI目录下
```
    src/
    └── main/
        ├── jniLibs/          # ✅ 推荐目录
        │   ├── arm64-v8a/
        │   │   └── librgba2jpg.so
        │   ├── armeabi-v7a/
        │   │   └── librgba2jpg.so
        │   └── x86_64/
        │       └── librgba2jpg.so
        └── cpp/
            └── CMakeLists.txt
```

# app gradle 指定ABI目录
```
    android {
        sourceSets {
            named("main") {  // ✅ 使用 named("main") 而不是直接写 main
                    jniLibs.setSrcDirs(listOf("src/main/jniLibs"))  // ✅ 使用 setSrcDirs 和 listOf
                }
            }
    }
```