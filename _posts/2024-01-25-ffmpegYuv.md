---
layout:       post
title:        "ffmpeg yuv图片格式转换"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - ffmpeg
---
1. 查看yuv格式的图片可以使用使用7yuv
2. ffmpeg转换命令  
`ffmpeg -s 1920x1536 -pix_fmt uyvy422 -i b.raw  -pix_fmt nv12 b.yuv`
3. 使用ffplay也可以查看yuv格式的图片  
`ffplay -pixel_format uyvy422 -video_size 1920x1536 -i b.raw`

*tips:*
1. 要将输入文件的文件名修改为.raw, ffmpeg不识别`.uyvy`和`.yuyv`等格式
2. 要查到源文件格式在ffmpeg中对应的 `-pix_fmt` 名字， 如`.uyvy`对应 `uyvy422`
3. 输出文件要以`.yuv`命名，不然也会报错