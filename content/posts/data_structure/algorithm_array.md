---
title: "Algorithm_array"
date: 2022-01-06T08:17:33+08:00
lastmod: 2022-01-06
tags: [data structure]
categories: [Coding]
slug: array algorithm 
draft: false
---
# 数组
## 数组基础
数组是存放在连续内存空间上的相同类型数据的集合。 

因为数组的在内存空间的地址是连续的，所以我们在删除或者增添元素的时候，就难免要移动其他元素的地址。

数组元素无法删除，只能覆盖。
## 二分查找
有序数组、无重复元素便可想到用二分法查找。注意区间左闭右闭还是左闭右开。
简单实现：
```go
func search(nums []int, target int) int {
    left := 0
    right := len(nums) - 1
    for left <= right {
        middle := left + (right - left)/2
        if nums[middle] > target {
            right = middle - 1
        }else if nums[middle] < target {
            left = middle + 1
        }else{
            return middle;
        }
    }
    return -1
}
```
## 移除元素
双指针法（快慢指针法）： 通过一个快指针和慢指针在一个for循环下完成两个for循环的工作。
```go
func removeElement(nums []int, val int) int {
    length:=len(nums)
    res:=0
    for i:=0;i<length;i++{
        if nums[i]!=val {
            nums[res]=nums[i]
            res++
        }
    }
    return res
}
```
```go
func removeElement(nums []int, val int) int {
    //自己第一次写的解
    num := 0

    for i, v := range nums {
        if v == val {
            num++
        } else {
            nums[i-num] = v
        }
    }
    return len(nums)-num
}
```
## 有序数组的平方
双指针法：
```go
func sortedSquares(nums []int) []int {
	n := len(nums)
	i, j, k := 0, n-1, n-1
	ans := make([]int, n)
	for i <= j {
		lm, rm := nums[i]*nums[i], nums[j]*nums[j]
		if lm > rm {
			ans[k] = lm
			i++
		} else {
			ans[k] = rm
			j--
		}
		k--
	}
	return ans
}
```
## 长度最小的子数组
**滑动窗口**
```go
func minSubArrayLen(target int, nums []int) int {
    i := 0
    l := len(nums)  // 数组长度
    sum := 0        // 子数组之和
    result := l + 1 // 初始化返回长度为l+1，目的是为了判断“不存在符合条件的子数组，返回0”的情况
    for j := 0; j < l; j++ {
        sum += nums[j]
        for sum >= target {
            subLength := j - i + 1
            if subLength < result {
                result = subLength
            }
            sum -= nums[i]
            i++
        }
    }
    if result == l+1 {
        return 0
    } else {
        return result
    }
}
```
## 螺旋矩阵II
模拟行为：
```go
func generateMatrix(n int) [][]int {
    top, bottom := 0, n-1
    left, right := 0, n-1
    num := 1
    tar := n * n
    matrix := make([][]int, n)
    for i := 0; i < n; i++ {
        matrix[i] = make([]int, n)
    }
    for num <= tar {
        for i := left; i <= right; i++ {
            matrix[top][i] = num
            num++
        }
        top++
        for i := top; i <= bottom; i++ {
            matrix[i][right] = num
            num++
        }
        right--
        for i := right; i >= left; i-- {
            matrix[bottom][i] = num
            num++
        }
        bottom--
        for i := bottom; i >= top; i-- {
            matrix[i][left] = num
            num++
        }
        left++
    }
    return matrix
}
```
## 总结
