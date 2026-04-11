---
layout:       post
title:        "heapTrack 分析程序内存使用"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - tools
---

heapTrack 是一个开源的内存分析工具，可以帮助开发者分析程序的内存使用情况，找出内存泄漏和性能瓶颈。它支持多种编程语言，包括 C++、Python 和 Java 等。
# inatsll heapTrack
```bash
- sudo apt install heaptrack heaptrack-gui
- heaptrack --version
```

# basic usage
```bash
- heaptrack -- your_program
```

- 使用GUI： heaptrack_gui heaptrack.your_program.pid.gz
![peak_homepage](/img/in-post/post_mem/peak_homepage.png)
![mem](/img/in-post/post_mem/mem.png)
![flame](/img/in-post/post_mem/flame.png)

- 使用命令行： heaptrack_print heaptrack.your_program.pid.gz
AI 工具可以使用这个分析程序
```
total runtime: 57.95s.
calls to allocation functions: 959512 (16558/s)
temporary memory allocations: 196971 (3399/s)
peak heap memory consumption: 737.16M
peak RSS (including heaptrack overhead): 784.24M
total memory leaked: 60.98K
suppressed leaks: 185.76K
```

# 结合top和htop分析程序内存占用情况
`2701 shensun+  20   0 2578356 267680 117376 S  32.3   1.6   0:29.99 refactor_test`