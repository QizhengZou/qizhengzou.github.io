---
title: "DIP_base_02"
date: 2022-02-24T10:44:08+08:00
lastmod: 2022-02-28
tags: [DIP]
categories: [School courses]
slug: Image Enhancement
draft: true
---
# Image Enhancement
There are two broad categories of image enhancement techniques
- Spatial domain techniques
    - Direct manipulation操作 of image pixels
        - Point processing
        - Neighbourhood operations
- Frequency domain techniques
    - Manipulation of Fourier transform傅里叶变换 or wavelet transform小波变换 of an image
## Basic Spatial Domain Image Enhancement
Most spatial domain enhancement operations can be reduced to the form:  g (x, y) = T[ f (x, y)]
- where f (x, y) is the input image, g (x, y) is the processed image and T is some operator defined over some neighbourhood of (x, y)

The simplest spatial domain operations occur when the neighbourhood is simply the pixel itself: **Point Processing**.
In this case T is referred to as a grey level transformation function or a point processing operation.
Point processing operations take the form:
s = T ( r )
where s refers to the processed image pixel value and r refers to the original image pixel value.

Point processing
- The neighborhood is of size 1×1
- Gray-level transformation

Mask processing掩码处理 or filtering
- The neighborhood is defined as a mask, filter, or window.
- Filtering

## point processing techniques:
- Thresholding阈值化
    - Thresholding transformations are particularly useful for segmentation in which we want to isolate an object of interest from a background.
    - s=1.0(r>threshold)
    - s=0.0(r<=threshold)
- Logarithmic transformation对数转化
    - The general form of the log transformation is s = c * log(1 + r)
    - Log functions are particularly useful when the input grey level values may have an extremely large range of values.
    - In Fourier transform, we usually encounter spectrum values that ranges from 0 to 106 or higher. But image display system cannot reproduce such a wide range of intensity values.



- Power law transforms
- Grey level slicing
- Bit plane slicing数位面切割
- Image subtraction
- Image averaging

