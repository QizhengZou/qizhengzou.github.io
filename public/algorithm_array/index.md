# Algorithm_array

> 学习[代码随想录](https://programmercarl.com/)笔记

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

# 数组
## 数组基础
数组是存放在连续内存空间上的相同类型数据的集合。 

因为数组的在内存空间的地址是连续的，所以我们在删除或者增添元素的时候，就难免要移动其他元素的地址。

数组元素无法删除，只能覆盖。
## 二分查找
有序数组、无重复元素便可想到用二分法查找。**注意**区间左闭右闭还是左闭右开。
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


func search(nums []int,t int)int{
    left,middle:=0
    right:=len(nums)
    for left<=right{
        num=(left+right)/2
        if nums[num]==t{
            return num
        }
        if nums[num]>t{
            right=num-1
        }else{
            left=num+1
        }
    }
}

func search(nums []int,t int)int{
    if middle:=len(nums)/2;nums[middle]==t{
        return middle
    }
    if len(nums)==1{
        return -1
    }
    if nums[middle]>t{
        return search(nums[:middle],t)
    }else{
        return search(nums[middle+1:])
    }
    
}
func recureionSearch(nums []int,t int)
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

func removeElement(nums []int, val int) int {
   
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
给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 ≥ s 的长度最小的 连续 子数组，并返回其长度。如果不存在符合条件的子数组，返回 0。
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

