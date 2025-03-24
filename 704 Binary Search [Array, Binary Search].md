# 704. Binary Search [Array, Binary Search]

# [**704. Binary Search**](https://leetcode.com/problems/binary-search/)

Given an array of integers `nums` which is sorted in ascending order, and an integer `target`, write a function to search `target` in `nums`. If `target` exists, then return its index. Otherwise, return `-1`.

You must write an algorithm with `O(log n)` runtime complexity.

**Example 1:**

```
Input: nums = [-1,0,3,5,9,12], target = 9
Output: 4
Explanation: 9 exists in nums and its index is 4

```

**Example 2:**

```
Input: nums = [-1,0,3,5,9,12], target = 2
Output: -1
Explanation: 2 does not exist in nums so return -1

```

**Constraints:**

- `1 <= nums.length <= 104`
- `104 < nums[i], target < 104`
- All the integers in `nums` are **unique**.
- `nums` is sorted in ascending order.

```go
func search(nums []int, target int) int {
    left := 0
    right := len(nums) - 1

    for right >= left {
        mid := (left + right) / 2 
        if nums[mid] == target {
            return mid
        }
        if nums[mid] < target {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }

    return -1

}
```

1. 定義兩個指針指向陣列的頭尾
2. 跑一迴圈，結束條件是當 right 大於等於 left
3. 在每一輪中，定義一個 mid ，數值是 left 與 right 的中間值
4. 如果 `nums[mid]`，是 target 的話，就找到正確答案
5. 不是的話開始限縮範圍，如果 target 大於 `nums[mid]` ，代表 target 在 mid 的右邊範圍，所以 left = mid + 1，right 不動，因為它指向的值是最大的
6. 如果 target 小於 `nums[mid]` ，代表 target 在 mid 的左邊範圍，所以 right = mid - 1，left 不動，因為它指向的值是最小的
7. 每一輪找中間值，並且比較其值與 target 的大小關係，切半當前範圍，最終 left 與 right 會指向同一個元素，如果還是沒有 target，就會因為 +1 -1  而跳出迴圈

*二分查找得要在有排序的數組才能使用

時間複雜度：O(log n)

空間複雜度：O(1)