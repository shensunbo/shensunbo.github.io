---
layout:       post
title:        "opencv build"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - opencv
---

# build linux version 
## build_ti_linux.sh
```
#!/bin/bash

source /opt/ospas120-2023.10/environment-setup-aarch64-oe-linux

rm -rf build_ti_linux
mkdir build_ti_linux
cd build_ti_linux

cmake  \ -DBUILD_TESTS=OFF \ -DBUILD_PERF_TESTS=OFF \ -DBUILD_opencv_highgui=OFF \ -DBUILD_opencv_video=OFF \ -DBUILD_opencv_videoio=OFF \ -DBUILD_opencv_dnn=OFF \ -DBUILD_opencv_ml=OFF \ -DBUILD_opencv_videostab=OFF \ -DBUILD_opencv_objdetect=OFF \ -DBUILD_opencv_superres=OFF \ -DCMAKE_INSTALL_PREFIX=./install ..
# make VERBOSE=1 -j10
cmake --build . -j 10
```

# build qnx710 version 
## build_ti_qnx.sh (put in root path)
```
#!/bin/bash

source /home/qnx710/qnxsdp-env.sh

rm -rf build_ti_qnx710
mkdir build_ti_qnx710
cd build_ti_qnx710

cmake .. -DTARGET_CPU:STRING=qnx -DCMAKE_TOOLCHAIN_FILE=../platforms/linux/aarch64-qnx.cmake \
            -DCPPBUILD_TARGET_CPU_TYPE:STRING=qnx -DQNXNTO=true -DCMAKE_SYSTEM_PROCESSOR=aarch64 \
            -DBUILD_TESTS=OFF -DBUILD_PERF_TESTS=OFF -DWITH_CUDA=OFF -DWITH_VTK=OFF -DWITH_MATLAB=OFF -DBUILD_DOCS=OFF \
            -DBUILD_opencv_python3=OFF -DBUILD_opencv_python2=OFF -DWITH_IPP=OFF -DBUILD_SHARED_LIBS=ON \
            -DBUILD_opencv_apps=OFF  -DWITH_OPENCL=OFF   \
            -DBUILD_JAVA=OFF -DBUILD_FAT_JAVA_LIB=OFF -DWITH_PROTOBUF=OFF -DWITH_QUIRC=OFF \
            -DBUILD_opencv_highgui=OFF -DBUILD_opencv_video=OFF \
            -DBUILD_opencv_videoio=OFF  -DBUILD_opencv_dnn=OFF  -DBUILD_opencv_ml=OFF  -DBUILD_opencv_videostab=OFF \
            -DWITH_PNG=OFF -DWITH_JPEG=OFF\
            -DBUILD_opencv_objdetect=OFF  -DBUILD_opencv_superres=OFF  -DCMAKE_INSTALL_PREFIX=./install


# make VERBOSE=1 -j10
cmake --build . -j 16
```

## aarch64-qnx.cmake (put in platforms/linux/aarch64-qnx.cmake)
```
if("$ENV{QNX_HOST}" STREQUAL "")
    message(FATAL_ERROR "QNX_HOST environment variable not found. Please set the variable to your host's build tools")
endif()
if("$ENV{QNX_TARGET}" STREQUAL "")
    message(FATAL_ERROR "QNX_TARGET environment variable not found. Please set the variable to the qnx target location")
endif()

if(CMAKE_HOST_WIN32)
    set(HOST_EXECUTABLE_SUFFIX ".exe")
    #convert windows paths to cmake paths
    file(TO_CMAKE_PATH "$ENV{QNX_HOST}" QNX_HOST)
    file(TO_CMAKE_PATH "$ENV{QNX_TARGET}" QNX_TARGET)
else()
    set(QNX_HOST "$ENV{QNX_HOST}")
    set(QNX_TARGET "$ENV{QNX_TARGET}")
endif()

message(STATUS "using QNX_HOST ${QNX_HOST}")
message(STATUS "using QNX_TARGET ${QNX_TARGET}")

set(QNX TRUE)
set(CMAKE_SYSTEM_NAME QNX)
set(CMAKE_C_COMPILER ${QNX_HOST}/usr/bin/qcc)
set(CMAKE_CXX_COMPILER ${QNX_HOST}/usr/bin/qcc)
set(CMAKE_ASM_COMPILER ${QNX_HOST}/usr/bin/qcc)
set(CMAKE_AR "${QNX_HOST}/usr/bin/nto${CMAKE_SYSTEM_PROCESSOR}-ar${HOST_EXECUTABLE_SUFFIX}" CACHE PATH "archiver")
set(CMAKE_RANLIB "${QNX_HOST}/usr/bin/nto${CMAKE_SYSTEM_PROCESSOR}-ranlib${HOST_EXECUTABLE_SUFFIX}" CACHE PATH "ranlib")

if ("${GCC_VER}" STREQUAL "")
    set(GCC_VERSION "" CACHE STRING "gcc_ver")
else()
    set(GCC_VERSION "${GCC_VER}," CACHE STRING "gcc_ver")
endif()

# qnx arch is only valid for arm (le)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -D_QNX_SOURCE ${EXTRA_CMAKE_C_FLAGS}" CACHE STRING "c_flags")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}   -D_QNX_SOURCE ${EXTRA_CMAKE_CXX_FLAGS}" CACHE STRING "cxx_flags")

set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${EXTRA_CMAKE_LINKER_FLAGS}" CACHE STRING "exe_linker_flags")
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} ${EXTRA_CMAKE_LINKER_FLAGS}" CACHE STRING "so_linker_flags")

#set(CMAKE_FIND_ROOT_PATH ${VSOMEIP_EXTERNAL_DEPS_INSTALL};${VSOMEIP_EXTERNAL_DEPS_INSTALL}/${CPUVARDIR};${QNX_TARGET};${QNX_TARGET}/${CPUVARDIR})
set(CMAKE_FIND_ROOT_PATH ${QNX_TARGET}/${CMAKE_SYSTEM_PROCESSOR}le)

# set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -lstdc++")
# set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -lstdc++")

set(CMAKE_SKIP_RPATH TRUE CACHE BOOL "If set, runtime paths are not added when using shared libraries.")
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)

# set(CMAKE_SYSTEM_PROCESSOR aarch64)
set(GCC_COMPILER_VERSION "" CACHE STRING "GCC Compiler version")
set(GNU_MACHINE "aarch64-unknown-nto-qnx7.1.0" CACHE STRING "GNU compiler triple")
include("${CMAKE_CURRENT_LIST_DIR}/arm.toolchain.cmake")
set(arch nto${CMAKE_SYSTEM_PROCESSOR})
set(CMAKE_AS "$ENV{QNX_HOST}/usr/bin/${arch}-as")
set(CMAKE_AR "$ENV{QNX_HOST}/usr/bin/${arch}-ar")
set(CMAKE_C_COMPILER "$ENV{QNX_HOST}/usr/bin/${arch}-gcc")
set(CMAKE_CXX_COMPILER "$ENV{QNX_HOST}/usr/bin/${arch}-g++")
set(CMAKE_LINKER "$ENV{QNX_HOST}/usr/bin/${arch}-ld")

set(CMAKE_C_COMPILER_TARGET "gcc_ntoaarch64le")
set(CMAKE_CXX_COMPILER_TARGET "gcc_ntoaarch64le")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -D_XOPEN_SOURCE=600 -D_POSIX_C_SOURCE=200112L -D_QNX_SOURCE")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fexceptions -D_XOPEN_SOURCE=600 -D_POSIX_C_SOURCE=200112L -D_QNX_SOURCE")

message(STATUS "CMAKE_AS: ${CMAKE_AS}")
message(STATUS "CMAKE_AR: ${CMAKE_AR}")
message(STATUS "CMAKE_C_COMPILER: ${CMAKE_C_COMPILER}")
message(STATUS "CMAKE_CXX_COMPILER: ${CMAKE_CXX_COMPILER}")
message(STATUS "CMAKE_LINKER: ${CMAKE_LINKER}")


# SET(CMAKE_SYSROOT "/home/dean/qc/qnx710/target/qnx7")
# SET(CMAKE_C_FLAGS "--sysroot=${CMAKE_SYSROOT} -I/home/dean/qc/qnx710/target/qnx7/aarch64le ${CMAKE_C_FLAGS}"  CACHE INTERNAL "" FORCE)
# SET(CMAKE_C_LINK_FLAGS "--sysroot=${CMAKE_SYSROOT} ${CMAKE_C_LINK_FLAGS}"  CACHE INTERNAL "" FORCE)
# SET(CMAKE_CXX_FLAGS "--sysroot=${CMAKE_SYSROOT} -I/home/dean/qc/qnx710/target/qnx7/aarch64le ${CMAKE_CXX_FLAGS}"  CACHE INTERNAL "" FORCE)
# SET(CMAKE_CXX_LINK_FLAGS "--sysroot=${CMAKE_SYSROOT} ${CMAKE_CXX_LINK_FLAGS}"  CACHE INTERNAL "" FORCE)
```

## platforms/linux/arm.toolchain.cmake
```
diff --git a/platforms/linux/arm.toolchain.cmake b/platforms/linux/arm.toolchain.cmake
index 184997fba5..60a5c4db58 100644
--- a/platforms/linux/arm.toolchain.cmake
+++ b/platforms/linux/arm.toolchain.cmake
@@ -45,7 +45,8 @@ else()
 endif()
 
 if(NOT DEFINED ARM_LINUX_SYSROOT AND DEFINED GNU_MACHINE)
-  set(ARM_LINUX_SYSROOT /usr/${GNU_MACHINE}${FLOAT_ABI_SUFFIX})
+#   set(ARM_LINUX_SYSROOT /usr/${GNU_MACHINE}${FLOAT_ABI_SUFFIX})
+  set(ARM_LINUX_SYSROOT /home/qnx710/target/qnx7/usr/include)
 endif()
 
 if(NOT DEFINED CMAKE_CXX_FLAGS)
@@ -63,9 +64,9 @@ if(NOT DEFINED CMAKE_CXX_FLAGS)
     set(CMAKE_EXE_LINKER_FLAGS    "${CMAKE_EXE_LINKER_FLAGS} -Wl,-z,nocopyreloc")
   endif()
   if(CMAKE_SYSTEM_PROCESSOR STREQUAL arm)
-    set(ARM_LINKER_FLAGS "-Wl,--fix-cortex-a8 -Wl,--no-undefined -Wl,--gc-sections -Wl,-z,noexecstack -Wl,-z,relro -Wl,-z,now")
+    set(ARM_LINKER_FLAGS "-Wl,--fix-cortex-a8 -Wl,--no-undefined -Wl,--gc-sections -Wl,-z,noexecstack -Wl,-z,relro -Wl,-z,now,-lc")
   elseif(CMAKE_SYSTEM_PROCESSOR STREQUAL aarch64)
-    set(ARM_LINKER_FLAGS "-Wl,--no-undefined -Wl,--gc-sections -Wl,-z,noexecstack -Wl,-z,relro -Wl,-z,now")
+    set(ARM_LINKER_FLAGS "-Wl,--no-undefined -Wl,--gc-sections -Wl,-z,noexecstack -Wl,-z,relro -Wl,-z,now,-lc")
   endif()
   set(CMAKE_SHARED_LINKER_FLAGS "${ARM_LINKER_FLAGS} ${CMAKE_SHARED_LINKER_FLAGS}")
   set(CMAKE_MODULE_LINKER_FLAGS "${ARM_LINKER_FLAGS} ${CMAKE_MODULE_LINKER_FLAGS}")
```

## CMakeLists.txt
```
diff --git a/CMakeLists.txt b/CMakeLists.txt
index 0693731a8b..ab17b5cdf7 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -773,7 +773,8 @@ if(UNIX OR MINGW)
     elseif(EMSCRIPTEN)
       # no need to link to system libs with emscripten
     elseif(QNXNTO)
-      set(OPENCV_LINKER_LIBS ${OPENCV_LINKER_LIBS} m)
+      set(OPENCV_LINKER_LIBS ${OPENCV_LINKER_LIBS} m c)
+      message(STATUS "QNXNTO env ------ ")
     elseif(MINGW)
       set(OPENCV_LINKER_LIBS ${OPENCV_LINKER_LIBS} pthread)
     else()
```

## modules/core/src/system.cpp
```
diff --git a/modules/core/src/system.cpp b/modules/core/src/system.cpp
index 9d5304ac5a..3566f743ec 100644
--- a/modules/core/src/system.cpp
+++ b/modules/core/src/system.cpp
@@ -45,6 +45,7 @@
 #include <atomic>
 #include <iostream>
 #include <ostream>
+#include <unistd.h>
 
 #include <opencv2/core/utils/configuration.private.hpp>
 #include <opencv2/core/utils/trace.private.hpp>
```

## modules/core/src/parallel.cpp
```
diff --git a/modules/core/src/parallel.cpp b/modules/core/src/parallel.cpp
index d81577cfed..bdee38beb5 100644
--- a/modules/core/src/parallel.cpp
+++ b/modules/core/src/parallel.cpp
@@ -1009,7 +1009,8 @@ int getNumberOfCPUs_()
 
 #if !defined(_WIN32) && !defined(__APPLE__) && defined(_SC_NPROCESSORS_ONLN)
 
-    static unsigned cpu_count_sysconf = (unsigned)sysconf( _SC_NPROCESSORS_ONLN );
+    // static unsigned cpu_count_sysconf = (unsigned)sysconf( _SC_NPROCESSORS_ONLN );
+    static unsigned cpu_count_sysconf = 4;
     ncpus = minNonZero(ncpus, cpu_count_sysconf);
 
 #endif
```

