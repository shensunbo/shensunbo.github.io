---
layout:       post
title:        "gdb, tui and ddd"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - debug
    - gdb
---

1. debug info
    ```
    file RtvSvmServiceServer
    RtvSvmServiceServer: ELF 64-bit LSB pie executable, x86-64, version 1 (GNU/Linux), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]   =8c510aeb45ba419898dbd4e42193bb3fb89ecbb1, for GNU/Linux 3.2.0, with debug_info, not stripped
    ```

2. 访问 vector : `p ((gpo::cockpit::icrf::v1_0::cameraEvent*)clientEventbuffer.__begin_)[0]`