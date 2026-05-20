---
layout:       post
title:        "android 浮窗视图圆角bugfix"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - graphics
    - android
---
目前实现浮窗视图的方式是通过在android侧创建一个透明的egl surface，在C++侧完成绘制后，android的窗口管理器会根据surface上的内容与背景进行混合，如果alpha的值不为0，混合后的结果会有一个半透明的效果。  
这个会导致一个问题，在不是圆角的部分，如果有半透明物体，比如打开了透明底盘模式，在混合的时候会混合android的后台界面。如果使用OSG的默认混合模式，就会有这种现象。  
解决方法是将透明度混合模式也单独设置,保证混合之后的透明度值为1：  
```
 osg::ref_ptr<osg::BlendFunc> tmp_bf = new osg::BlendFunc(
    osg::BlendFunc::SRC_ALPHA, 
    osg::BlendFunc::ONE_MINUS_SRC_ALPHA,
    osg::BlendFunc::ZERO,
    osg::BlendFunc::ONE
  );
  tmp_pSt->setAttributeAndModes(tmp_bf.get(), osg::StateAttribute::ON);
```
如果不设置透明度混合模式，默认RGB 和 Alpha 使用相同因子，即混合后alpha值不为1