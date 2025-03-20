# 209. Minimum Size Subarray Sum[sliding window]

```go
func minSubArrayLen(target int, nums []int) int {
    sum := 0
    result := len(nums) + 1
    i := 0

    for j := 0; j < len(nums); j++ {
        sum += nums[j]
        for sum >= target {
            subLen := j - i + 1
            if subLen < result {
                result = subLen
            }
            sum -= nums[i]
            i++
        }
    }

    if result == len(nums) + 1 {
        return 0
    } else {
        return result
    }

}
```

长度最小的子数组

给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 ≥ s 的长度最小的 连续 子数组，并返回其长度。如果不存在符合条件的子数组，返回 0。

邊界條件

- 一個元素也算子數組

滑動視窗的思路，`j` 表示視窗的結束位置，起始值為 0，`i` 表示起始位置，起始值為 0

`result` 紀錄目前出現過的最小子數組長度，起始值為 `nums` 長度 + 1，這個數值為最大，子數組長度不會大於 `nums` 長度

`sum` 紀錄目前視窗裡值的總和

每一輪迭代時，視窗擴大（終止位置右移），`sum` 增加 `nums[j]`，一但 `sum` 符合 `>= target` 

進行一個迭代，迭代目的是為了視窗縮小（起始位置右移），試著在符合 `>= target`  的條件下縮小視窗

迭代中計算視窗長度 `subLen` ，與 `result` 比較及紀錄，並右移起始位置`i` 和減去不在視窗內的值

直到 `sum` 不符合條件，回到外圍迭代，繼續移動結束位置，直到 `sum` 又符合條件

時間複雜度：

每個元素進入滑動視窗的時候操作一次，離開滑動視窗的時候操作一次，總共兩次

所以時間複雜度 n x 2，也就是 O(n)

重點整理：

因為子數組必須是連續的，一但 `sum` 符合條件，就試著縮小視窗的起始位置，找最小的長度