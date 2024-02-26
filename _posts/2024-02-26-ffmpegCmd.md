---
layout:       post
title:        "ffmpeg 命令"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - ffmpeg
---

## ffmpeg
### yuv格式转换 
`ffmpeg -s 1920x1536 -pix_fmt uyvy422 -i c.raw  -pix_fmt yuv420p  c2.yuv`
### yuv修改分辨率
`ffmpeg -s 1920x1536 -pix_fmt nv12 -i d.raw -vf "scale=1080:720:flags=bicubic" -sws_flags bicubic -pix_fmt nv12 -y d1.yuv`
### 添加水印
`ffmpeg -i output.mp4 -vf "drawtext=text='hello world':fontsize=24:fontcolor=white:x=10:y=10" osd.mp4`
### 添加metadata
`ffmpeg -i output.mp4 -metadata title="gengQingLin hangazi" -metadata artist="gengQingLin" -metadata year="2024" output_metadata.mp4`
### 截取
#### 截取片段
`ffmpeg -i car.mp4 -t 3 -c copy car1.mp4`
#### 截取关键帧
`ffmpeg -i output.mp4 -vf "select='eq(pict_type,I)'" -vsync vfr frames\frame_%04d.png`
#### 截取所有帧
`ffmpeg -i car.mp4 -vf "select=1" -vsync vfr frames\frame_%04d.png`
### 获取详细信息
`ffmpeg -i output.mp4 -vf "select=gt(scene\,0.003)" -f null -`
## ffplay
### 播放yuv
`ffplay -pixel_format yuv420p -video_size 480x272 -window_title "love sucks" -x 1080 -y 720 -loop 3 -i ds_480x272.yuv`

## ffprobe
```
-show_format
-show_streams
-show_data

-show_entries format_tags
-show_entries stream_tags

```
