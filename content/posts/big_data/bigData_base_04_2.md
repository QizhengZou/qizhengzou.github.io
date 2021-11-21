---
title: "BigData_base_04_2"
date: 2021-11-15T15:54:13+08:00
lastmod: 2021-11-15
tags: [big_data]
categories: [School Courses]
slug: Understanding data
draft: false
---
## 数据理解的主要任务
数据理解在统计学中称为exploratory data analysis（EDA）：    
- 借助人类对数据的观察和“模式”识别能力
- 对数据进行初步研究，以便更好地理解它的特性
- 有助于选择合适的数据预处理和数据分析、挖掘技术

- 任务一：每个属性取值分布统计：
    - 对给定数据集的各个属性的取值分布情况进行统计概括
    - 例：结构化的信贷数据集
- 任务二：多个属性取值分布统计
    - 对给定数据集的多个属性的取值分布情况进行统计概括
    - 例如，利用散点图查看属性之间具有的相关性，数据的分布情况
- 任务三：数据的总体质量评估
    - 数据存在误差属性值缺失噪声和不一致性等潜在的数据质量问题
    - 例：原始的信贷数据集

理解数据的主要方法：
- 汇总统计方法（Summary statistics），或称为概况性统计描述
- 传统可视化方法（Visualization），
    - 包括OLAP（Online Analytical Processing）
- 基于数据挖掘结果的可视化方法
    - 通过聚类、离群点检测的结果分析数据的特点
    - 高维数据的低维嵌入（low-dimensional embedding）可视化

## 基于统计描述的数据理解方法
- 集中趋势（central tendency）度量：
    - 数值型数据
        - 均值
        - 分位数
        - 几何均值
    - 数值型数据
        - 均值
        - 分位数
        - 几何均值
    - 数值型数据的集中趋势度量方法
        - 均值
            - 简单平均数
            - 截尾均值
            - 中位数
            - 四分位数
            - 几何平均数 
    - 类别型数据的集中趋势度量方法
        - 众数(mode)：一组数据中出现频率最高的属性值
    - 集中趋势度量方法比较
    ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211115163455.png)
- 散布（spread）程度度量：
    - 数值型数据
        - 极差
        - 四分位距
        - 方差、标准差
    - 类别数据
        - 异众比率
    - 数值型数据的散布程度度量方法
        - 极差
        - 四分位距
        - 方差
    - 类别型数据的散布程度度量方法
        - 异众比率
    - 散布程度度量方法比较
    ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211115163843.png)
- 偏态度量
    - 偏态（skewness）是描述数值型数据分布的对称性的度量指标
    - 数据分布的对称性可以分为对称、左偏或右偏。
    - 在数据分布的统计描述中，偏态度量是集中趋势度量和散布度量的有益补充
- 峰度度量
    - 数据分布的峰度是描述数值型数据分布的几何形态陡峭程度的统计量
        - 以正态分布为基准定义的
        ![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211115164215.png)

## 数据可视化的传统方法
数据可视化
- 可视化是将数据转换为可视的形式，以图形或表格的形式显示信息，以便能够借此分析数据的特征，以及数据项或属性之间的关系。
- 数据可视化是理解数据的重要工具
    - 人类具有分析大量视觉信息的能力
    - 可以检测普遍模式和趋势
    - 可以检测异常值和异常模式

鸢尾花数据集：   
使用鸢尾花（Iris）数据集说明数据探索技术
- 该数据集可从UCI的机器学习库中得到 http://www.ics.uci.edu/~mlearn/MLRepository.html
- 来自统计学家Douglas Fisher
- 三种花（类）：
    - Setosa (Se)
    - Virginica (Vi) 
    - Versicolour (Ve)
- 四个（非类）属性：
    - 萼片宽度和长度
    - 花瓣宽度和长度

- 柱状图
    - 用于类别型数据：图中每一个类别的计数统计以“条柱”的形式展示，其中数据的类别的频次比较以“条柱”的高度作为评判标准。
- 直方图
    - 直方图用于数值型数据：数值属性值频次的统计可视化
        - 其横坐标为数值的取值跨度，纵坐标为数值的频率或频次
- 散点图
    - 散点图主要应用于数值型数据，其作用主要是观察两个（或三个）数值型属性的数据之间的关联性
    - 散点图可以直观的反映集中数据和离群数据的分布，还能直接反映数据集内存在的分组情况，还可反映属性组合的数据区分能力
- Iris数据集的散点图矩阵
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211115164608.png)
- 箱线图
    - 数值型属性，五数归纳（Five Number Summary）
    - Minimum, Q1(25%), Median, Q3(75%), Maximum
    - 箱线图(Box Plots) 
        - 另一种显示数据分布的方法
        - 箱线图的箱体：四分位距(IQR,Interquartile range), IQR =Q3-Q1
        - 上下边缘（whisker）:
            - min(Q3+1.5IQR, Maximum)
            - max(Q1-1.5IQR, Minimum) 
        - 这样计算上下边缘可区分异常值
    - 箱线图可以用来比较属性的类区分能力（discrimination capability）
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211115164847.png)
- 数据矩阵图
    - 数据矩阵图，将数值映射到不同的颜色上，从而可以将一张数据规模不大的表进行可视化
    - 例：1949-1960年各月的航班飞行次数累积表
## 高维数据的低维嵌入可视化方法
- 雷达图
    - 可对每一个（类）多维数据对象进行可视化
- 平行坐标图
    - 平行坐标系(Parallel Coordinates )
        - 用于绘制高维数据的属性值
        - 使用一组平行坐标轴
        - 对象每个属性的值被绘制为对应坐标轴上的点，将这些点用线连接起来
        - 因此，每个对象表示为一条线
        - 通常，对象趋于分成少数几个组，组内的点具有类似的属性值
        - 在查看此类分组时，对属性进行排序非常重要
        
![](https://raw.githubusercontent.com/QizhengZou/Drawing_bed/main/20211115165600.png)





