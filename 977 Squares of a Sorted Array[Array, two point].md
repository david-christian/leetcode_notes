# 977. Squares of a Sorted Array[Array, two point]

給定一個有排序的升序陣列，回傳該陣列每一個元素平方和後，並排序過的升序陣列

```go
func sortedSquares(nums []int) []int {
    result := make([]int, len(nums))
    k := len(result)
    j := len(result) - 1
    for i := 0; i <= j; {
        k --
        if nums[i] * nums[i] > nums[j] * nums[j] {
            result[k] = nums[i] * nums[i]
            i ++
        } else {
            result[k] = nums[j] * nums[j]
            j --
        }
    }
    return result
}
```

本題可容許負數，因為負數平方和後負負得正的特性，能確定一條件，平方和後較大的元素不是在相對前面就是相對後面，不會在中間

1. 正確答案的陣列長度與參數相同，所以創建 `result` 
2. 每一次迭代求當前最大元素 ，首先 `k = k - 1` ，透過 `k` 指定 `result` 的索引
3. 雙指針 `i` 與 `j` 指向 `nums`  頭尾，比較兩者所指向元素平方和，`result`透過索引 `k`賦值平方和結果，指針移動向中間靠攏
4. 迴圈結束條件為 `i > j` ，最後一圈為 `i` 與 `j` 指向同一元素

時間複雜度： O(n)

空間複雜度：O(n)