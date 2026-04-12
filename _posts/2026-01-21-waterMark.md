---
layout:       post
title:        "使用FreeType字体库为NV12的图像叠加水印"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - graphics
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
void FastWatermark::Nv12AddDateWatermark(unsigned char* nv12Buf, int width, int height, const char *text){
    unsigned int picSize = width * height * 3 / 2;
    int x = m_x_pos;

    for (const char* p = text; *p; p++) {
        // if (FT_Load_Char(face, *p, FT_LOAD_RENDER)) {
        //     mylog(E, "Failed to load glyph %c", *p);
        //     continue;
        // }
        // FT_GlyphSlot slot = face->glyph;

        auto it = glyphCache.find(*p);
        if (it == glyphCache.end()) {
            mylog(E, "Glyph not found in cache: %c", *p);
            assert(false);
            continue;
        }

        const CachedGlyph& glyph = it->second;
        int y_pos = m_y_pos - glyph.bitmap_top;

        for (int i = 0; i < glyph.height; i++) {
            for (int j = 0; j < glyph.width; j++) {
                uint8_t alpha = glyph.bitmap[i * glyph.pitch + j];
                
                if (alpha > 0) {
                    uint32_t index = (y_pos + i) * width + x + j;
                    if (index >= picSize) continue;  // 边界检查
                    
                    float alpha_ratio = alpha / 255.0f;
                    nv12Buf[index] = static_cast<uint8_t>(
                        (1.0f - alpha_ratio) * nv12Buf[index] + alpha_ratio * 255
                    );
                }
            }
        }

        x += glyph.advance;
    }
}
```