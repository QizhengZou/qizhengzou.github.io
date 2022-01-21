# Algorithm_dynamicProgramming

# 动态规划
有很多重叠子问题，优先考虑使用动态规划。

与贪心的区别：贪心不会考虑之前的状态，只考虑局部最优。
## 理论基础
**dp步骤：**
- 确定dp数组（dp table）以及下标的含义
- 确定递推公式
- dp数组如何初始化
- 确定遍历顺序
- 举例推导dp数组

debug:把dp数组打印出来
## 斐波那契数
```go
func fib(n int) int {
    if n < 2 {
        return n
    }
    a, b, c := 0, 1, 0
    for i := 1; i < n; i++ {
        c = a + b
        a, b = b, c
    }
        return c
}
```
## 爬楼梯
假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。

示例 1：
```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1 阶 + 1 阶
2 阶
```

```go
func climbStairs(n int) int {
    if n==1{
        return 1
    }
    dp:=make([]int,n+1)
    dp[1]=1
    dp[2]=2
    for i:=3;i<=n;i++{
        dp[i]=dp[i-1]+dp[i-2]
    }
    return dp[n]
}
```
### 使用最小花费爬楼梯
数组的每个下标作为一个阶梯，第 i 个阶梯对应着一个非负数的体力花费值 cost[i]（下标从 0 开始）。

每当你爬上一个阶梯你都要花费对应的体力值，一旦支付了相应的体力值，你就可以选择向上爬一个阶梯或者爬两个阶梯。

请你找出达到楼层顶部的最低花费。在开始时，你可以选择从下标为 0 或 1 的元素作为初始阶梯。

示例 1：

输入：cost = [10, 15, 20] 输出：15 解释：最低花费是从 cost[1] 开始，然后走两步即可到阶梯顶，一共花费 15 
```go
func minCostClimbingStairs(cost []int) int {
	dp := make([]int, len(cost))
	dp[0], dp[1] = cost[0], cost[1]
	for i := 2; i < len(cost); i++ {
		dp[i] = min(dp[i-1], dp[i-2]) + cost[i]
	}
	return min(dp[len(cost)-1], dp[len(cost)-2])
}

func min(a, b int) int {
	if a < b {
		return a
	}
	return b
}
```
## 不同路径
### I
一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？
```go
func uniquePaths(m int, n int) int {
	dp := make([][]int, m)
	for i := range dp {
		dp[i] = make([]int, n)
		dp[i][0] = 1
	}
	for j := 0; j < n; j++ {
		dp[0][j] = 1
	}
	for i := 1; i < m; i++ {
		for j := 1; j < n; j++ {
			dp[i][j] = dp[i-1][j] + dp[i][j-1]
		}
	}
	return dp[m-1][n-1]
}
```
### II
一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？
```go
func uniquePathsWithObstacles(obstacleGrid [][]int) int {
	m,n:= len(obstacleGrid),len(obstacleGrid[0])
	// 定义一个dp数组
	dp := make([][]int,m)
	for i,_ := range dp {
		dp[i] = make([]int,n)
	}
	// 初始化
	for i:=0;i<m;i++ {
		// 如果是障碍物, 后面的就都是0, 不用循环了
		if obstacleGrid[i][0] == 1 {
			break
		}
		dp[i][0]=1
	}
	for i:=0;i<n;i++ {
		if obstacleGrid[0][i] == 1 {
			break
		}
		dp[0][i]=1
	}
	// dp数组推导过程
	for i:=1;i<m;i++ {
		for j:=1;j<n;j++ {
			// 如果obstacleGrid[i][j]这个点是障碍物, 那么我们的dp[i][j]保持为0
           	 	if obstacleGrid[i][j] != 1 {
				// 否则我们需要计算当前点可以到达的路径数
				dp[i][j] = dp[i-1][j]+dp[i][j-1]
			}
		}
	}
	// debug遍历dp
	//for i,_ := range dp {
	//	for j,_ := range dp[i] {
	//		fmt.Printf("%.2v,",dp[i][j])
	//	}
	//	fmt.Println()
	//}
	return dp[m-1][n-1]
}
```
## 整数拆分
给定一个正整数 n，将其拆分为至少两个正整数的和，并使这些整数的乘积最大化。 返回你可以获得的最大乘积。
```go
func integerBreak(n int) int {
    /**
    动态五部曲
    1.确定dp下标及其含义
    2.确定递推公式
    3.确定dp初始化
    4.确定遍历顺序
    5.打印dp
    **/
    dp:=make([]int,n+1)
    dp[1]=1
    dp[2]=1
    for i:=3;i<n+1;i++{
        for j:=1;j<i-1;j++{
// i可以差分为i-j和j。由于需要最大值，故需要通过j遍历所有存在的值，取其中最大的值作为当前i的最大值，在求最大值的时候，一个是j与i-j相乘，一个是j与dp[i-j].
            dp[i]=max(dp[i],max(j*(i-j),j*dp[i-j]))
        }
    }
    return dp[n]
}
func max(a,b int) int{
    if a>b{
        return a
    }
    return b
}
```
## 不同的二叉搜索树
给定一个整数 n，求以 1 ... n 为节点组成的二叉搜索树有多少种？
```go
func numTrees(n int)int{
	dp:=make([]int,n+1)
	dp[0]=1
	for i:=1;i<=n;i++{
		for j:=1;j<=i;j++{
			dp[i]+=dp[j-1]*dp[i-j]
		}
	}
	return dp[n]
}
```
## 0-1背包理论基础
### I
暴力的解法是指数级别的时间复杂度。进而才需要动态规划的解法来进行优化
[代码随想录详解](https://programmercarl.com/%E8%83%8C%E5%8C%85%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%8001%E8%83%8C%E5%8C%85-1.html#_01-%E8%83%8C%E5%8C%85)

```go

func test_2_wei_bag_problem1(weight, value []int, bagweight int) int {
	// 定义dp数组
	dp := make([][]int, len(weight))
	for i, _ := range dp {
		dp[i] = make([]int, bagweight+1)
	}
	// 初始化
	for j := bagweight; j >= weight[0]; j-- {
		dp[0][j] = dp[0][j-weight[0]] + value[0]
	}
	// 递推公式
	for i := 1; i < len(weight); i++ {
		//正序,也可以倒序
		for  j := weight[i];j<= bagweight ; j++ {
			dp[i][j] = max(dp[i-1][j], dp[i-1][j-weight[i]]+value[i])
		}		
	}
	return dp[len(weight)-1][bagweight]
}

func max(a,b int) int {
	if a > b {
		return a
	}
	return b
}

func main() {
	weight := []int{1,3,4}
	value := []int{15,20,30}
	test_2_wei_bag_problem1(weight,value,4)
}
```
### II
[代码随想录详解](https://programmercarl.com/%E8%83%8C%E5%8C%85%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%8001%E8%83%8C%E5%8C%85-2.html#%E4%B8%80%E7%BB%B4dp%E6%95%B0%E7%BB%84-%E6%BB%9A%E5%8A%A8%E6%95%B0%E7%BB%84)

```go

func test_1_wei_bag_problem(weight, value []int, bagWeight int) int {
	// 定义 and 初始化
	dp := make([]int,bagWeight+1)
	// 递推顺序
	for i := 0 ;i < len(weight) ; i++ {
		// 这里必须倒序,区别二维,因为二维dp保存了i的状态
		for j:= bagWeight; j >= weight[i] ; j-- {
			// 递推公式
			dp[j] = max(dp[j], dp[j-weight[i]]+value[i])
		}
	}
	//fmt.Println(dp)
	return dp[bagWeight]
}

func max(a,b int) int {
	if a > b {
		return a
	}
	return b
}


func main() {
	weight := []int{1,3,4}
	value := []int{15,20,30}
	test_1_wei_bag_problem(weight,value,4)
}
```
## 分割等和子集
给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

注意: 每个数组中的元素不会超过 100 数组的大小不会超过 200

示例 1: 输入: [1, 5, 11, 5] 输出: true 解释: 数组可以分割成 [1, 5, 5] 和 [11].

示例 2: 输入: [1, 2, 3, 5] 输出: false 解释: 数组不能分割成两个元素和相等的子集.
```go
// 分割等和子集 动态规划
// 时间复杂度O(n^2) 空间复杂度O(n)
func canPartition(nums []int) bool {
    sum := 0
    for _, num := range nums {
        sum += num
    }
    // 如果 nums 的总和为奇数则不可能平分成两个子集
    if sum % 2 == 1 {
        return false
    }
    
    target := sum / 2
    dp := make([]int, target + 1)

    for _, num := range nums {
        for j := target; j >= num; j-- {
            if dp[j] < dp[j - num] + num {
                dp[j] = dp[j - num] + num
            }
        }
    }
    return dp[target] == target
}
```
## 最后一块石头的重量II
有一堆石头，每块石头的重量都是正整数。

每一回合，从中选出任意两块石头，然后将它们一起粉碎。假设石头的重量分别为 x 和 y，且 x <= y。那么粉碎的可能结果如下：

如果 x == y，那么两块石头都会被完全粉碎； 如果 x != y，那么重量为 x 的石头将会完全粉碎，而重量为 y 的石头新重量为 y-x。 最后，最多只会剩下一块石头。返回此石头最小的可能重量。如果没有石头剩下，就返回 0。

示例： 输入：[2,7,4,1,8,1] 输出：1 解释： 组合 2 和 4，得到 2，所以数组转化为 [2,7,1,8,1]， 组合 7 和 8，得到 1，所以数组转化为 [2,1,1,1]， 组合 2 和 1，得到 1，所以数组转化为 [1,1,1]， 组合 1 和 1，得到 0，所以数组转化为 [1]，这就是最优值。

```go
func lastStoneWeightII(stones []int) int {
	// 15001 = 30 * 1000 /2 +1
	dp := make([]int, 15001)
	// 求target
	sum := 0
	for _, v := range stones {
		sum += v
	}
	target := sum / 2
	// 遍历顺序
	for i := 0; i < len(stones); i++ {
		for j := target; j >= stones[i]; j-- {
			// 推导公式
			dp[j] = max(dp[j], dp[j-stones[i]]+stones[i])
		}
	}
	return sum - 2 * dp[target]
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}
```
## 目标和
给定一个非负整数数组，a1, a2, ..., an, 和一个目标数，S。现在你有两个符号 + 和 -。对于数组中的任意一个整数，你都可以从 + 或 -中选择一个符号添加在前面。

返回可以使最终数组和为目标数 S 的所有添加符号的方法数。

示例：

输入：nums: [1, 1, 1, 1, 1], S: 3
输出：5
```
解释：
-1+1+1+1+1 = 3
+1-1+1+1+1 = 3
+1+1-1+1+1 = 3
+1+1+1-1+1 = 3
+1+1+1+1-1 = 3
```
一共有5种方法让最终目标和为3。

可回溯可dp

```go
func findTargetSumWays(nums []int, target int) int {
	sum := 0
	for _, v := range nums {
		sum += v
	}
	if target > sum {
		return 0
	}
	if (sum+target)%2 == 1 {
		return 0
	}
	// 计算背包大小
	bag := (sum + target) / 2
	// 定义dp数组
	dp := make([]int, bag+1)
	// 初始化
	dp[0] = 1
	// 遍历顺序
	for i := 0; i < len(nums); i++ {
		for j := bag; j >= nums[i]; j-- {
			//推导公式
			dp[j] += dp[j-nums[i]]
			//fmt.Println(dp)
		}
	}
	return dp[bag]
}
```

## 一和零
给你一个二进制字符串数组 strs 和两个整数 m 和 n 。

请你找出并返回 strs 的最大子集的大小，该子集中 最多 有 m 个 0 和 n 个 1 。

如果 x 的所有元素也是 y 的元素，集合 x 是集合 y 的 子集 。

示例 1：

输入：strs = ["10", "0001", "111001", "1", "0"], m = 5, n = 3 输出：4

解释：最多有 5 个 0 和 3 个 1 的最大子集是 {"10","0001","1","0"} ，因此答案是 4 。 其他满足题意但较小的子集包括 {"0001","1"} 和 {"10","1","0"} 。{"111001"} 不满足题意，因为它含 4 个 1 ，大于 n 的值 3 。

```go
func findMaxForm(strs []string, m int, n int) int {
	// 定义数组
	dp := make([][]int, m+1)
	for i,_ := range dp {
		dp[i] = make([]int, n+1 )
	}
	// 遍历
	for i:=0;i<len(strs);i++ {
		zeroNum,oneNum := 0 , 0
		//计算0,1 个数
		//或者直接strings.Count(strs[i],"0")
		for _,v := range strs[i] {
			if v == '0' {
				zeroNum++
			}
		}
		oneNum = len(strs[i])-zeroNum
		// 从后往前 遍历背包容量
		for j:= m ; j >= zeroNum;j-- {
			for k:=n ; k >= oneNum;k-- {
				// 推导公式
				dp[j][k] = max(dp[j][k],dp[j-zeroNum][k-oneNum]+1)
			}
		}
		//fmt.Println(dp)
	}
	return dp[m][n]
}

func max(a,b int) int {
	if a > b {
		return a
	}
	return b
}
```
## 完全背包理论基础
每件物品都有无限个（也就是可以放入背包多次）
```go


// test_CompletePack1 先遍历物品, 在遍历背包
func test_CompletePack1(weight, value []int, bagWeight int) int {
	// 定义dp数组 和初始化
	dp := make([]int, bagWeight+1)
	// 遍历顺序
	for i := 0; i < len(weight); i++ {
		// 正序会多次添加 value[i]
		for j := weight[i]; j <= bagWeight; j++ {
			// 推导公式
			dp[j] = max(dp[j], dp[j-weight[i]]+value[i])
			// debug
			//fmt.Println(dp)
		}
	}
	return dp[bagWeight]
}

// test_CompletePack2 先遍历背包, 在遍历物品
func test_CompletePack2(weight, value []int, bagWeight int) int {
	// 定义dp数组 和初始化
	dp := make([]int, bagWeight+1)
	// 遍历顺序
	// j从0 开始
	for j := 0; j <= bagWeight; j++ {
		for i := 0; i < len(weight); i++ {
			if j >= weight[i] {
				// 推导公式
				dp[j] = max(dp[j], dp[j-weight[i]]+value[i])
			}
			// debug
			//fmt.Println(dp)
		}
	}
	return dp[bagWeight]
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}

func main() {
	weight := []int{1, 3, 4}
	price := []int{15, 20, 30}
	fmt.Println(test_CompletePack1(weight, price, 4))
	fmt.Println(test_CompletePack2(weight, price, 4))
}
```
## 零钱兑换II
给定不同面额的硬币和一个总金额。写出函数来计算可以凑成总金额的硬币组合数。假设每一种面额的硬币有无限个。

示例 1:

输入: amount = 5, coins = [1, 2, 5] 输出: 4 解释: 有四种方式可以凑成总金额: 5=5 5=2+2+1 5=2+1+1+1 5=1+1+1+1+1

```go
func change(amount int, coins []int) int {
	// 定义dp数组
	dp := make([]int, amount+1)
	// 初始化,0大小的背包, 当然是不装任何东西了, 就是1种方法
	dp[0]  = 1
	// 遍历顺序
	// 遍历物品
	for i := 0 ;i < len(coins);i++ {
		// 遍历背包
		for j:= coins[i] ; j <= amount ;j++ {
			// 推导公式
			dp[j] += dp[j-coins[i]]
		}
	}
	return dp[amount]
}
```
## 组合总和（IV）
给定一个由正整数组成且不存在重复数字的数组，找出和为给定目标正整数的组合的个数。

示例:

nums = [1, 2, 3] target = 4

所有可能的组合为： (1, 1, 1, 1) (1, 1, 2) (1, 2, 1) (1, 3) (2, 1, 1) (2, 2) (3, 1)

请注意，顺序不同的序列被视作不同的组合。

因此输出为 7

```go
func combinationSum4(nums []int, target int) int {
	//定义dp数组
	dp := make([]int, target+1)
	// 初始化
	dp[0] = 1
	// 遍历顺序, 先遍历背包,再循环遍历物品
	for j:=0;j<=target;j++ {
		for i:=0 ;i < len(nums);i++ {
			if j >= nums[i] {
				dp[j] += dp[j-nums[i]]
			}
		}
	}
	return dp[target]
}
```
## 爬楼梯（进阶）
假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。
```
示例 1： 输入： 2 输出： 2 解释： 有两种方法可以爬到楼顶。
1 阶 + 1 阶
2 阶
```

```go
func climbStairs(n int) int {
	//定义
	dp := make([]int, n+1)
	//初始化
	dp[0] = 1
	// 本题物品只有两个1,2
	m := 2
	// 遍历顺序
	for j := 1; j <= n; j++ {	//先遍历背包
		for i := 1; i <= m; i++ {	//再遍历物品
			if j >= i {
				dp[j] += dp[j-i]
			}
			//fmt.Println(dp)
		}
	}
	return dp[n]
}
```
## 零钱兑换
给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

你可以认为每种硬币的数量是无限的。

示例 1： 输入：coins = [1, 2, 5], amount = 11 输出：3 解释：11 = 5 + 5 + 1
```go

// 版本一, 先遍历物品,再遍历背包
func coinChange1(coins []int, amount int) int {
	dp := make([]int, amount+1)
	// 初始化dp[0]
	dp[0] = 0
	// 初始化为math.MaxInt32
	for j := 1; j <= amount; j++ {
		dp[j] = math.MaxInt32
	}

	// 遍历物品
	for i := 0; i < len(coins); i++ {
		// 遍历背包
		for j := coins[i]; j <= amount; j++ {
			if dp[j-coins[i]] != math.MaxInt32 {
				// 推导公式
				dp[j] = min(dp[j], dp[j-coins[i]]+1)
				//fmt.Println(dp,j,i)
			}
		}
	}
	// 没找到能装满背包的, 就返回-1
	if dp[amount] == math.MaxInt32 {
		return -1
	}
	return dp[amount]
}

// 版本二,先遍历背包,再遍历物品
func coinChange2(coins []int, amount int) int {
	dp := make([]int, amount+1)
	// 初始化dp[0]
	dp[0] = 0
	// 遍历背包,从1开始
	for j := 1; j <= amount; j++ {
		// 初始化为math.MaxInt32
		dp[j] = math.MaxInt32
		// 遍历物品
		for i := 0; i < len(coins); i++ {
			if j >= coins[i] && dp[j-coins[i]] != math.MaxInt32 {
				// 推导公式
				dp[j] = min(dp[j], dp[j-coins[i]]+1)
				//fmt.Println(dp)
			}
		}
	}
	// 没找到能装满背包的, 就返回-1
	if dp[amount] == math.MaxInt32 {
		return -1
	}
	return dp[amount]
}

func min(a, b int) int {
	if a < b {
		return a
	}
	return b
}
```
## 完全平方数
给定正整数 n，找到若干个完全平方数（比如 1, 4, 9, 16, ...）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。

给你一个整数 n ，返回和为 n 的完全平方数的 最少数量 。

完全平方数 是一个整数，其值等于另一个整数的平方；换句话说，其值等于一个整数自乘的积。例如，1、4、9 和 16 都是完全平方数，而 3 和 11 不是。

示例 1： 输入：n = 12 输出：3 解释：12 = 4 + 4 + 4

```go

// 版本一,先遍历物品, 再遍历背包
func numSquares1(n int) int {
	//定义
	dp := make([]int, n+1)
	// 初始化
	dp[0] = 0
	for i := 1; i <= n; i++ {
		dp[i] = math.MaxInt32
	}
	// 遍历物品
	for i := 1; i <= n; i++ {
		// 遍历背包
		for j := i*i; j <= n; j++ {
			dp[j] = min(dp[j], dp[j-i*i]+1)
		}
	}

	return dp[n]
}

// 版本二,先遍历背包, 再遍历物品
func numSquares2(n int) int {
	//定义
	dp := make([]int, n+1)
	// 初始化
	dp[0] = 0
	// 遍历背包
	for j := 1; j <= n; j++ {
		//初始化
		dp[j] = math.MaxInt32
		// 遍历物品
		for i := 1; i <= n; i++ {
			if j >= i*i {
				dp[j] = min(dp[j], dp[j-i*i]+1)
			}
		}
	}

	return dp[n]
}

func min(a, b int) int {
	if a < b {
		return a
	}
	return b
}
```
## 单词拆分
给定一个非空字符串 s 和一个包含非空单词的列表 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。

说明：

拆分时可以重复使用字典中的单词。

你可以假设字典中没有重复的单词。

示例 1： 输入: s = "leetcode", wordDict = ["leet", "code"] 输出: true 解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。

```go
func wordBreak(s string,wordDict []string) bool  {
	wordDictSet:=make(map[string]bool)
	for _,w:=range wordDict{
		wordDictSet[w]=true
	}
	dp:=make([]bool,len(s)+1)
	dp[0]=true
	for i:=1;i<=len(s);i++{
		for j:=0;j<i;j++{
			if dp[j]&& wordDictSet[s[j:i]]{
				dp[i]=true
				break
			}
		}
	}
	return dp[len(s)]
}
```
## 打家劫舍
### I
你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

示例 1： 输入：[1,2,3,1] 输出：4 解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。   偷窃到的最高金额 = 1 + 3 = 4 。

```go

func rob(nums []int) int {
	if len(nums)<1{
		return 0
	}
	if len(nums)==1{
		return nums[0]
	}
	if len(nums)==2{
		return max(nums[0],nums[1])
	}
	dp :=make([]int,len(nums))
	dp[0]=nums[0]
	dp[1]=max(nums[0],nums[1])
	for i:=2;i<len(nums);i++{
		dp[i]=max(dp[i-2]+nums[i],dp[i-1])
	}
	return dp[len(dp)-1]
}

func max(a, b int) int {
	if a>b{
		return a
	}
	return b
}
```
### II
你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 围成一圈 ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。

给定一个代表每个房屋存放金额的非负整数数组，计算你 在不触动警报装置的情况下 ，能够偷窃到的最高金额。

示例 1：

输入：nums = [2,3,2] 输出：3 解释：你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
```go

// 打家劫舍Ⅱ 动态规划
// 时间复杂度O(n) 空间复杂度O(n)
func rob(nums []int) int {
    if len(nums) == 1 {
        return nums[0]
    }
    if len(nums) == 2 {
        return max(nums[0], nums[1])
    }
    
    result1 := robRange(nums, 0)
    result2 := robRange(nums, 1)
    return max(result1, result2)
}

// 偷盗指定的范围
func robRange(nums []int, start int) int {
    dp := make([]int, len(nums))
    dp[1] = nums[start]
    
    for i := 2; i < len(nums); i++ {
        dp[i] = max(dp[i - 2] + nums[i - 1 + start], dp[i - 1])
    }
    
    return dp[len(nums) - 1]
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```

### III
在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。

计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。
动态规划
```go
func rob(root *TreeNode) int {
	res := robTree(root)
	return max(res[0], res[1])
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}

func robTree(cur *TreeNode) []int {
	if cur == nil {
		return []int{0, 0}
	}
	// 后序遍历
	left := robTree(cur.Left)
	right := robTree(cur.Right)

    // 考虑去偷当前的屋子
	robCur := cur.Val + left[0] + right[0]
    // 考虑不去偷当前的屋子
	notRobCur := max(left[0], left[1]) + max(right[0], right[1])

    // 注意顺序：0:不偷，1:去偷
	return []int{notRobCur, robCur}
}
```
## 买卖股票的最佳时机
### I
给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。

你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。
```
示例 1：
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```

```go
func maxProfit(prices []int) int {
	length:=len(prices)
	if length==0{return 0}
	dp:=make([][]int,length)
	for i:=0;i<length;i++{
		dp[i]=make([]int,2)
	}
	
	dp[0][0]=-prices[0]
	dp[0][1]=0
	for i:=1;i<length;i++{
		dp[i][0]=max(dp[i-1][0],-prices[i])
		dp[i][1]=max(dp[i-1][1],dp[i-1][0]+prices[i])
	}
	return dp[length-1][1]
}

func max(a,b int)int {
    if a>b{
        return a 
    }
    return b 
}
```
### II
给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
```
示例 1:
输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4。随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
```

```go

// 买卖股票的最佳时机Ⅱ 动态规划
// 时间复杂度：O(n) 空间复杂度：O(n)
func maxProfit(prices []int) int {
    dp := make([][]int, len(prices))
    status := make([]int, len(prices) * 2)
    for i := range dp {
        dp[i] = status[:2]
        status = status[2:]
    }
    dp[0][0] = -prices[0]
    
    for i := 1; i < len(prices); i++ {
        dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i])
        dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i])
    }
    
    return dp[len(prices) - 1][1]
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```

```go
func maxProfit(prices []int) int {
    //创建数组
    dp:=make([][]int,len(prices))
    for i:=0;i<len(prices);i++{
        dp[i]=make([]int,2)
    }
    dp[0][0]=-prices[0]
    dp[0][1]=0
    for i:=1;i<len(prices);i++{
        dp[i][0]=max(dp[i-1][0],dp[i-1][1]-prices[i])
        dp[i][1]=max(dp[i-1][1],dp[i-1][0]+prices[i])
    }
    return dp[len(prices)-1][1]
}
func max(a,b int)int{
    if a<b{
        return b
    }
    return a
}
```
### III
给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

示例 1: 输入：prices = [3,3,5,0,0,3,1,4] 输出：6 解释：在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3。

```go
func maxProfit(prices []int) int {
    dp:=make([][]int,len(prices))
    for i:=0;i<len(prices);i++{
        dp[i]=make([]int,5)
    }
    dp[0][0]=0
    dp[0][1]=-prices[0]
    dp[0][2]=0
    dp[0][3]=-prices[0]
    dp[0][4]=0
    for i:=1;i<len(prices);i++{
        dp[i][0]=dp[i-1][0]
        dp[i][1]=max(dp[i-1][1],dp[i-1][0]-prices[i])
        dp[i][2]=max(dp[i-1][2],dp[i-1][1]+prices[i])
        dp[i][3]=max(dp[i-1][3],dp[i-1][2]-prices[i])
        dp[i][4]=max(dp[i-1][4],dp[i-1][3]+prices[i])
    }
    return dp[len(prices)-1][4]
}
func max(a,b int)int{
    if a>b{
        return a
    }
    return b
}
```
### IV
给定一个整数数组 prices ，它的第 i 个元素 prices[i] 是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

示例 1： 输入：k = 2, prices = [2,4,1] 输出：2 解释：在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2。
版本一：
```go
// 买卖股票的最佳时机IV 动态规划
// 时间复杂度O(kn) 空间复杂度O(kn)
func maxProfit(k int, prices []int) int {
    if k == 0 || len(prices) == 0 {
        return 0
    }
    
    dp := make([][]int, len(prices))
    status := make([]int, (2 * k + 1) * len(prices))
    for i := range dp {
        dp[i] = status[:2 * k + 1]
        status = status[2 * k + 1:]
    }
    for j := 1; j < 2 * k; j += 2 {
        dp[0][j] = -prices[0]
    }
    
    for i := 1; i < len(prices); i++ {
        for j := 0; j < 2 * k; j += 2 {
            dp[i][j + 1] = max(dp[i - 1][j + 1], dp[i - 1][j] - prices[i])
            dp[i][j + 2] = max(dp[i - 1][j + 2], dp[i - 1][j + 1] + prices[i])
        }
    }
    return dp[len(prices) - 1][2 * k]
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
```go
func maxProfit(k int, prices []int) int {
    if len(prices)==0{
        return 0
    }
    dp:=make([][]int,len(prices))
    for i:=0;i<len(prices);i++{
        dp[i]=make([]int,2*k+1)
    }
    for i:=1;i<len(dp[0]);i++{
        if i%2!=0{
            dp[0][i]=-prices[0]
        }
    }
    for i:=1;i<len(prices);i++{
        dp[i][0]=dp[i-1][0]
        for j:=1;j<len(dp[0]);j++{
            if j%2!=0{
                 dp[i][j]=max(dp[i-1][j],dp[i-1][j-1]-prices[i])
            }else {
                dp[i][j]=max(dp[i-1][j],dp[i-1][j-1]+prices[i])
            }
        }
    }
    return dp[len(prices)-1][2*k]
}
func max(a,b int)int{
    if a>b{
        return a
    }
    return b
}
```
### 最佳买卖股票时机含冷冻期
给定一个整数数组，其中第 i 个元素代表了第 i 天的股票价格 。

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:
- 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
- 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

示例: 输入: [1,2,3,0,2] 输出: 3 解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]

```go
// 最佳买卖股票时机含冷冻期 动态规划
// 时间复杂度O(n) 空间复杂度O(n)
func maxProfit(prices []int) int {
    n := len(prices)
    if n < 2 {
        return 0
    }

    dp := make([][]int, n)
    status := make([]int, n * 4)
    for i := range dp {
        dp[i] = status[:4]
        status = status[4:]
    }
    dp[0][0] = -prices[0]
    
    for i := 1; i < n; i++ {
        dp[i][0] = max(dp[i - 1][0], max(dp[i - 1][1] - prices[i], dp[i - 1][3] - prices[i]))
        dp[i][1] = max(dp[i - 1][1], dp[i - 1][3])
        dp[i][2] = dp[i - 1][0] + prices[i]
        dp[i][3] = dp[i - 1][2]
    }
    
    return max(dp[n - 1][1], max(dp[n - 1][2], dp[n - 1][3]))
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
### 最佳买卖股票时机含手续费
给定一个整数数组 prices，其中第 i 个元素代表了第 i 天的股票价格 ；非负整数 fee 代表了交易股票的手续费用。

你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。

返回获得利润的最大值。

注意：这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。

示例 1: 输入: prices = [1, 3, 2, 8, 4, 9], fee = 2 输出: 8

解释: 能够达到的最大利润: 在此处买入 prices[0] = 1 在此处卖出 prices[3] = 8 在此处买入 prices[4] = 4 在此处卖出 prices[5] = 9 总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.

```go
// 买卖股票的最佳时机含手续费 动态规划
// 时间复杂度O(n) 空间复杂度O(n)
func maxProfit(prices []int, fee int) int {
    n := len(prices)
    dp := make([][2]int, n)
    dp[0][0] = -prices[0]
    for i := 1; i < n; i++ {
        dp[i][1] = max(dp[i-1][1], dp[i-1][0] + prices[i] - fee)
        dp[i][0] = max(dp[i-1][0], dp[i-1][1] - prices[i])
    }
    return dp[n-1][1]
}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
## 股票问题总结
[代码随想录](https://programmercarl.com/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92-%E8%82%A1%E7%A5%A8%E9%97%AE%E9%A2%98%E6%80%BB%E7%BB%93%E7%AF%87.html#%E5%8D%96%E8%82%A1%E7%A5%A8%E7%9A%84%E6%9C%80%E4%BD%B3%E6%97%B6%E6%9C%BA)
## 最长上升子序列
给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。

子序列是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。

示例 1： 输入：nums = [10,9,2,5,3,7,101,18] 输出：4 解释：最长递增子序列是 [2,3,7,101]，因此长度为 4 。

```go
func lengthOfLIS(nums []int ) int {
  dp := []int{}
  for _, num := range nums {
      if len(dp) ==0 || dp[len(dp) - 1] < num {
      dp = append(dp, num)
    } else {
          l, r := 0, len(dp) - 1
          pos := r
          for l <= r {
              mid := (l + r) >> 1
              if dp[mid] >= num {
                  pos = mid;
                  r = mid - 1
              } else {
                  l = mid + 1
              }
          }
          dp[pos] = num
      }//二分查找
  }
    return len(dp)
}
```
## 最长连续递增序列
给定一个未经排序的整数数组，找到最长且 连续递增的子序列，并返回该序列的长度。

连续递增的子序列 可以由两个下标 l 和 r（l < r）确定，如果对于每个 l <= i < r，都有 nums[i] < nums[i + 1] ，那么子序列 [nums[l], nums[l + 1], ..., nums[r - 1], nums[r]] 就是连续递增子序列。

示例 1： 输入：nums = [1,3,5,4,7] 输出：3 解释：最长连续递增序列是 [1,3,5], 长度为3。 尽管 [1,3,5,7] 也是升序的子序列, 但它不是连续的，因为 5 和 7 在原数组里被 4 隔开。

动态规划：
```py
class Solution:
    def findLengthOfLCIS(self, nums: List[int]) -> int:
        if len(nums) == 0:
            return 0
        result = 1
        dp = [1] * len(nums)
        for i in range(len(nums)-1):
            if nums[i+1] > nums[i]: #连续记录
                dp[i+1] = dp[i] + 1
            result = max(result, dp[i+1])
        return result
```
贪心法：
```py
class Solution:
    def findLengthOfLCIS(self, nums: List[int]) -> int:
        if len(nums) == 0:
            return 0
        result = 1 #连续子序列最少也是1
        count = 1
        for i in range(len(nums)-1):
            if nums[i+1] > nums[i]: #连续记录
                count += 1
            else: #不连续，count从头开始
                count = 1
            result = max(result, count)
        return result
```
## 最长重复子数组
给两个整数数组 A 和 B ，返回两个数组中公共的、长度最长的子数组的长度。

示例：

输入： A: [1,2,3,2,1] B: [3,2,1,4,7] 输出：3 解释： 长度最长的公共子数组是 [3, 2, 1] 。

```go
func findLength(A []int, B []int) int {
	m, n := len(A), len(B)
	res := 0
	dp := make([][]int, m+1)
	for i := 0; i <= m; i++ { 
		dp[i] = make([]int, n+1)
	}

	for i := 1; i <= m; i++ {
		for j := 1; j <= n; j++ {
			if A[i-1] == B[j-1] {
				dp[i][j] = dp[i-1][j-1] + 1
			}
			if dp[i][j] > res {
				res = dp[i][j]
			}
		}
	}
	return res
}
```
## 最长公共子序列
给定两个字符串 text1 和 text2，返回这两个字符串的最长公共子序列的长度。

一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。

例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。

若这两个字符串没有公共子序列，则返回 0。

示例 1:
```
输入：text1 = "abcde", text2 = "ace"
输出：3
解释：最长公共子序列是 "ace"，它的长度为 3。
```

```go
func longestCommonSubsequence(text1 string, text2 string) int {
	t1 := len(text1)
	t2 := len(text2)
	dp:=make([][]int,t1+1)
	for i:=range dp{
		dp[i]=make([]int,t2+1)
	}

	for i := 1; i <= t1; i++ {
		for j := 1; j <=t2; j++ {
			if text1[i-1]==text2[j-1]{
				dp[i][j]=dp[i-1][j-1]+1
			}else{
				dp[i][j]=max(dp[i-1][j],dp[i][j-1])
			}
		}
	}
	return dp[t1][t2]
}

func max(a,b int)int  {
	if a>b{
		return a 
	}
	return b
}
```
## 不相交的线
我们在两条独立的水平线上按给定的顺序写下 A 和 B 中的整数。

现在，我们可以绘制一些连接两个数字 A[i] 和 B[j] 的直线，只要 A[i] == B[j]，且我们绘制的直线不与任何其他连线（非水平线）相交。

以这种方法绘制线条，并返回我们可以绘制的最大连线数。
```go
func maxUncrossedLines(A []int, B []int) int {
	m, n := len(A), len(B)
	dp := make([][]int, m+1)
    for i := range dp {
        dp[i] = make([]int, n+1)
    }

	for i := 1; i <= len(A); i++ {
		for j := 1; j <= len(B); j++ {
			if (A[i - 1] == B[j - 1]) {
				dp[i][j] = dp[i - 1][j - 1] + 1
			} else {
				dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
			}
		}
	}
	return dp[m][n]

}

func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}
```
## 最大子序和
给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

示例: 输入: [-2,1,-3,4,-1,2,1,-5,4] 输出: 6 解释: 连续子数组 [4,-1,2,1] 的和最大，为 6

```go
// solution
// 1, dp
// 2, 贪心

func maxSubArray(nums []int) int {
    n := len(nums)
    // 这里的dp[i] 表示，最大的连续子数组和，包含num[i] 元素
    dp := make([]int,n)
    // 初始化，由于dp 状态转移方程依赖dp[0]
    dp[0] = nums[0]
    // 初始化最大的和
    mx := nums[0]
    for i:=1;i<n;i++ {
        // 这里的状态转移方程就是：求最大和
        // 会面临2种情况，一个是带前面的和，一个是不带前面的和
        dp[i] = max(dp[i-1]+nums[i],nums[i])
        mx = max(mx,dp[i])
    }
    return mx
}

func max(a,b int) int{
    if a>b {
        return a 
    }
    return b
}
```
## 判断子序列
给定字符串 s 和 t ，判断 s 是否为 t 的子序列。

字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，"ace"是"abcde"的一个子序列，而"aec"不是）。

示例 1： 输入：s = "abc", t = "ahbgdc" 输出：true

示例 2： 输入：s = "axc", t = "ahbgdc" 输出：false

```go
func isSubsequence(s string, t string) bool {
    dp := make([][]int,len(s)+1)
    for i:=0;i<len(dp);i++{
        dp[i] = make([]int,len(t)+1)
    }
    for i:=1;i<len(dp);i++{
        for j:=1;j<len(dp[i]);j++{
            if s[i-1] == t[j-1]{
                dp[i][j] = dp[i-1][j-1] +1
            }else{
                dp[i][j] = dp[i][j-1]
            }
        }
    }
    return dp[len(s)][len(t)]==len(s)
}
```
## 不同的子序列
给定一个字符串 s 和一个字符串 t ，计算在 s 的子序列中 t 出现的个数。

字符串的一个 子序列 是指，通过删除一些（也可以不删除）字符且不干扰剩余字符相对位置所组成的新字符串。（例如，"ACE" 是 "ABCDE" 的一个子序列，而 "AEC" 不是）

题目数据保证答案符合 32 位带符号整数范围。

```go
func numDistinct(s string, t string) int {
    dp:= make([][]int,len(s)+1)
    for i:=0;i<len(dp);i++{
        dp[i] = make([]int,len(t)+1)
    }
    // 初始化
    for i:=0;i<len(dp);i++{
        dp[i][0] = 1
    }
    // dp[0][j] 为 0，默认值，因此不需要初始化
    for i:=1;i<len(dp);i++{
        for j:=1;j<len(dp[i]);j++{
            if s[i-1] == t[j-1]{
                dp[i][j] = dp[i-1][j-1] + dp[i-1][j]
            }else{
                dp[i][j] = dp[i-1][j]
            }
        }
    }
    return dp[len(dp)-1][len(dp[0])-1]
}
```
## 两个字符串的删除操作
给定两个单词 word1 和 word2，找到使得 word1 和 word2 相同所需的最小步数，每步可以删除任意一个字符串中的一个字符。

示例：
```
输入: "sea", "eat"
输出: 2 解释: 第一步将"sea"变为"ea"，第二步将"eat"变为"ea"
```
## 编辑距离
给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：
- 插入一个字符
- 删除一个字符
- 替换一个字符

示例 1： 输入：word1 = "horse", word2 = "ros" 输出：3 解释： horse -> rorse (将 'h' 替换为 'r') rorse -> rose (删除 'r') rose -> ros (删除 'e')
```go

func minDistance(word1 string, word2 string) int {
	m, n := len(word1), len(word2)
	dp := make([][]int, m+1)
	for i := range dp {
		dp[i] = make([]int, n+1)
	}
	for i := 0; i < m+1; i++ {
		dp[i][0] = i // word1[i] 变成 word2[0], 删掉 word1[i], 需要 i 部操作
	}
	for j := 0; j < n+1; j++ {
		dp[0][j] = j // word1[0] 变成 word2[j], 插入 word1[j]，需要 j 部操作
	}
	for i := 1; i < m+1; i++ {
		for j := 1; j < n+1; j++ {
			if word1[i-1] == word2[j-1] {
				dp[i][j] = dp[i-1][j-1]
			} else { // Min(插入，删除，替换)
				dp[i][j] = Min(dp[i][j-1], dp[i-1][j], dp[i-1][j-1]) + 1
			}
		}
	}
	return dp[m][n]
}
func Min(args ...int) int {
	min := args[0]
	for _, item := range args {
		if item < min {
			min = item
		}
	}
	return min
}
```
### 编辑距离总结
[代码随想录](https://programmercarl.com/%E4%B8%BA%E4%BA%86%E7%BB%9D%E6%9D%80%E7%BC%96%E8%BE%91%E8%B7%9D%E7%A6%BB%EF%BC%8C%E5%8D%A1%E5%B0%94%E5%81%9A%E4%BA%86%E4%B8%89%E6%AD%A5%E9%93%BA%E5%9E%AB.html#%E5%88%A4%E6%96%AD%E5%AD%90%E5%BA%8F%E5%88%97)
## 回文子串
给定一个字符串，你的任务是计算这个字符串中有多少个回文子串。

具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。

示例 1：

输入："abc" 输出：3 解释：三个回文子串: "a", "b", "c"

```go
func countSubstrings(s string) int {
    res:=0
    dp:=make([][]bool,len(s))
    for i:=0;i<len(s);i++{
        dp[i]=make([]bool,len(s))
    }

    for i:=len(s)-1;i>=0;i--{
        for j:=i;j<len(s);j++{
            if s[i]==s[j]{
                if j-i<=1{
                    res++
                    dp[i][j]=true
                }else if dp[i+1][j-1]{
                    res++
                    dp[i][j]=true
                }
            }
        }
    }
    return res
}
```
## 最长回文子序列
给定一个字符串 s ，找到其中最长的回文子序列，并返回该序列的长度。可以假设 s 的最大长度为 1000 。

示例 1: 输入: "bbbab" 输出: 4 一个可能的最长回文子序列为 "bbbb"。

示例 2: 输入:"cbbd" 输出: 2 一个可能的最长回文子序列为 "bb"。

```go
func longestPalindromeSubseq(s string) int {
	lenth:=len(s)
	dp:=make([][]int,lenth)
	for i:=0;i<lenth;i++{
		for j:=0;j<lenth;j++{
			if dp[i]==nil{
				dp[i]=make([]int,lenth)
			}
			if i==j{
				dp[i][j]=1
			}
		}
	}
	for i:=lenth-1;i>=0;i--{
		for j:=i+1;j<lenth;j++{
			if s[i]==s[j]{
				dp[i][j]=dp[i+1][j-1]+2
			}else {
				dp[i][j]=max(dp[i+1][j],dp[i][j-1])
			}
		}
	}

	return dp[0][lenth-1]
}
```
## DP总结
[代码随想录](https://programmercarl.com/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E6%80%BB%E7%BB%93%E7%AF%87.html#%E5%8A%A8%E5%88%92%E5%9F%BA%E7%A1%80)

