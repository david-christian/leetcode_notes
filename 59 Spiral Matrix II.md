# 59. Spiral Matrix II

Given a positive integer `n`, generate an `n x n` `matrix` filled with elements from `1` to `n2` in spiral order.

**Example 1:**

![](https://assets.leetcode.com/uploads/2020/11/13/spiraln.jpg)

```
Input: n = 3
Output: [[1,2,3],[8,9,4],[7,6,5]]

```

**Example 2:**

```
Input: n = 1
Output: [[1]]

```

**Constraints:**

- `1 <= n <= 20`

```go
func generateMatrix(n int) [][]int {
    startX, startY := 0, 0
    off := 1
    center := n / 2
    count := 1
    loop := n / 2

    result := make([][]int, n)
    for i := range result {
        result[i] = make([]int, n)
    }

    for loop > 0 {
        i, j := startX, startY

        // up edge x: move right y: hold
        for ; i <  n - off; i++ {
            result[j][i] = count
            count ++
        }

        // right edge x: hold y: move down
        for ; j < n - off; j++ {
            result[j][i] = count
            count ++
        }

        // done edge x: move left y: hold
        for ; i > off - 1; i-- {
            result[j][i] = count
            count ++
        }

        // left edge x: hold y: move up
        for ; j > off - 1; j-- {
            result[j][i] = count
            count ++
        }

        startX ++
        startY ++
        off ++
        loop --
    }

    if n % 2 == 1 {
        result[center][center] = n * n
    }

    return result

}
```

1. 從外往內填入元素，一圈外迴圈處理一個四邊形環，先計算迴圈總數，公式為 `floor(n / 2)` 
    
    基于螺旋矩阵的几何特性。在一个 n * n 的正方形矩阵中：
    
    - 第 1 层遍历 4*(n-1) 个元素
    - 第 2 层遍历 4*(n-3) 个元素
    - 第 3 层遍历 4*(n-5) 个元素
    - 以此类推...
2. 按著 1 ~ n 的順序填入，直接用`count` 遞增代表要填入的數字
3. 填入順序由 上：左到右，右：上到下，下：右到左，左：下到上
4. 填入四個邊元素時，按左閉右開的原則
    
5. `off` 代表每條邊在每一圈填充的時候，透過 `n - off` 和 `off - 1`表示何時結束，往內圈處理時，`off` 的值也會遞增
6. `startX` 與 `startY` 代表在填充上下邊和左右邊時的起始位置，往內圈處理時，`startX` 與 `startY` 的值也會遞增，每一輪外圍迴圈，`i` 與`j` 重新更新為`startX` 和`startY` ，開始新一輪的四邊形填充
7. 如果 n 是基數，代表二維矩陣總元素量是基數，中間一定會留下一個中心未填空，該元素也是二維矩陣 n * n 中最大的數