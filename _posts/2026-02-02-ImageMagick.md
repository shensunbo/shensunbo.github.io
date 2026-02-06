---
layout:       post
title:        "ImageMagick"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - ImageMagick 
    - image
---

# 修改分辨率
```
# 进入纹理目录
cd "path/to/your/texture/directory"

# 创建输出目录
mkdir -p "../TEX_2K"

# 批量处理所有PNG文件
for file in *.png; do
    echo "处理: $file"
    convert "$file" -resize 2048x2048 "../TEX_2K/$file"
done

echo "处理完成！文件保存在: TEX_2K 目录"
```
