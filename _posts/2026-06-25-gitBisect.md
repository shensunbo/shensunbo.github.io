---
layout:       post
title:        "git bisect 定位哪个 commit 引入了 bug"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - tools
---
# step 1
确定一个已知的坏的 commit（包含 bug）和一个已知的好的 commit（不包含 bug）。 
```bash
git bisect start
git bisect bad HEAD
git bisect good v1.0
```

# step 2
Git 会自动切换到一个中间的 commit，运行测试或检查代码以确定该 commit 是否包含 bug。根据结果标记该 commit 为好或坏：
```bash
git bisect good  # 如果该 commit 没有 bug
git bisect bad   # 如果该 commit 有 bug
```

# step 3
重复步骤 2，Git 会继续缩小范围，直到找到引入 bug 的具体 commit。最终，Git 会输出引入 bug 的 commit 的哈希值和提交信息。

todo: real eg.