---
layout: post
title: 图像幅度谱（amplitude spectrum）与相位谱（phase spectrum）
category: 图像
description: 相位谱的一些理解
tags: ["图像处理"]
---

傅立叶变换后的复数系数，可以分解为两个部分即幅度谱（amplitude spectrum）与相位谱（phase spectrum）。
如果你当初提取幅度谱后扔掉了相位谱，那么当然无法恢复。相位谱中存储的是图像中像素的位置信息，幅度谱存储的是不同频率的全局能量信息，两者互补才能实现无差恢复。

While the amplitude spectrum reveals the presence of particular basis images in an image, the phase spectrum encodes their relative shifts. 
Thus, without phase information, the spatial coherence of the image is destroyed to such extent that it is impossible to recognise depicted objects. 
Without amplitude information, the relative brightnesses of these objects cannot be restored, although the boundaries between them can be found. 
Because phase is so important to keep the overall visuall appearance of an image, most of image processing operations in the frequency domain do not alter the phase spectrum and manipulate only the amplitude spectrum.

ps:geometry of images有一次作业是打乱图像的phase看结果，一般人工的图像（斑纹之类的）结果是一团乱，自然图像（风景啥的）结果还能看见一些低频部分（轮廓）。