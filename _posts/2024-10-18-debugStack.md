---
layout:       post
title:        "LINUX 调试栈"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - stack
    - debug
---

1. info proc mappings 打印内存映射信息
```
(gdb) info proc mappings
process 104188
Mapped address spaces:

          Start Addr           End Addr       Size     Offset objfile
            0x400000           0x4d0000    0xd0000        0x0 /home/root/shensunbo/bin/RtvSvmServiceServer
            0x4e0000           0x4e1000     0x1000    0xd0000 /home/root/shensunbo/bin/RtvSvmServiceServer
            0x4e1000           0x4e2000     0x1000    0xd1000 /home/root/shensunbo/bin/RtvSvmServiceServer
            0x4e2000           0x5af000    0xcd000        0x0 [heap]
      0xffffd3c40000     0xffffd4078000   0x438000        0x0 /dmabuf:
      0xffffd4078000     0xffffd44b0000   0x438000        0x0 /dmabuf:
.........
      0xfffff7ff9000     0xfffff7ffb000     0x2000        0x0 [vvar]
      0xfffff7ffb000     0xfffff7ffc000     0x1000        0x0 [vdso]
      0xfffff7ffc000     0xfffff7ffe000     0x2000    0x29000 /lib/ld-linux-aarch64.so.1
      0xfffff7ffe000     0xfffff8000000     0x2000    0x2b000 /lib/ld-linux-aarch64.so.1
      0xfffffffdf000    0x1000000000000    0x21000        0x0 [stack]

```