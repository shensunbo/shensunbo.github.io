---
layout:       post
title:        "使用FreeType字体库为NV12的图像叠加水印"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - FreeType
    - NV12
    - waterMark
---
[reference](https://github.com/shensunbo/yuv_tools)

- CPU方案，仅适用于简单的字符水印，比如时间戳等。复杂的水印建议使用GPU方案。
> ~~这个方案存在字体边缘有黑色边框的问题~~
![waterMark2](/img/in-post/mine/waterMark2.png)
- 😁 fixed
![waterMark3](/img/in-post/mine/waterMark3.png)

# TODO list
- ~~优化处理速度，目前X86上叠花费大概300us，FT_Load_Char总共占了200us，后续修改为将字符预渲染到查找表中，查表替代实时渲染。~~ 已经修改为了预渲染的方式，叠加水印占用70us左右

# NV12 图像像素格式
一个6x4的nv12格式图像的内存数据排列
![waterMark1](/img/in-post/mine/waterMark1.png)

# 使用freetype生成像素位图的步骤

## 1. 初始化运行环境
```c
FT_Init_FreeType(FT_Library *alibrary)
```

## 2. 加载字体文件
```c
FT_New_Face(FT_Library   library,
            const char*  filepathname,
            FT_Long      face_index,
            FT_Face     *aface);
```

## 3. 设置字体大小
```c
FT_Set_Pixel_Sizes(FT_Face  face,
                   FT_UInt  pixel_width,
                   FT_UInt  pixel_height);
```

## 4. 像素叠加
- 使用 `FT_Load_Char` 加载指定字符的字形数据
- 将获取的位图数据叠加到图像上

## 代码示例
```
void Watermark::Nv12AddDateWatermark(unsigned char* nv12Buf, int width, int height, const char *text){
    unsigned int picSize = width * height * 3 / 2;
    unsigned int index = 0;
    int text_alpha = 0;
    int x = m_x_pos;

    for (const char* p = text; *p; p++) {
        if (FT_Load_Char(face, *p, FT_LOAD_RENDER)) {
            mylog(E, "Failed to load glyph %c", *p);
            continue;
        }

        FT_GlyphSlot slot = face->glyph;

        // Calculate the y position for bottom alignment
        int y_pos = m_y_pos - slot->bitmap_top;// Align the bottom

        // Merge text bitmap (monochrome) onto NV12 image with alpha masking
        for (int i = 0; i < slot->bitmap.rows; i++) {
            for (int j = 0; j < slot->bitmap.width; j++) {
                text_alpha = slot->bitmap.buffer[i * slot->bitmap.pitch + j];  // Alpha value (0-255) of text pixel

                // Only overlay if text pixel is not fully transparent (adjust threshold as needed)
                // int y_pos = y + i - slot->bitmap_top;
                if (text_alpha > 0) {
                    // Overlay text pixel on Y plane
                    index = (y_pos + i) * width + x + j;
                    float alpha = slot->bitmap.buffer[i * slot->bitmap.pitch + j] / 255.0f;
                    nv12Buf[index] = (unsigned char)((1.0f - alpha) * nv12Buf[index] + alpha * 255);
                    mylog(E, " %d", slot->bitmap.buffer[i * slot->bitmap.pitch + j]);
                    assert(index < picSize);
                }
            }
        }

        x += slot->advance.x >> 6;  // Advance to the next character position
    }
}
```