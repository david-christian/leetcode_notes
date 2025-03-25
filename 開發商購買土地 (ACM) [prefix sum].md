# 開發商購買土地 (ACM) [prefix sum]

### **题目描述**

在一个城市区域内，被划分成了n * m个连续的区块，每个区块都拥有不同的权值，代表着其土地价值。目前，有两家开发公司，A 公司和 B 公司，希望购买这个城市区域的土地。

现在，需要将这个城市区域的所有区块分配给 A 公司和 B 公司。

然而，由于城市规划的限制，只允许将区域按横向或纵向划分成两个子区域，而且每个子区域都必须包含一个或多个区块。 为了确保公平竞争，你需要找到一种分配方式，使得 A 公司和 B 公司各自的子区域内的土地总价值之差最小。

注意：区块不可再分。

### **输入描述**

第一行输入两个正整数，代表 n 和 m。

接下来的 n 行，每行输出 m 个正整数。

### **输出描述**

请输出一个整数，代表两个子区域内土地总价值之间的最小差距。

### **输入示例**

`3 3
1 2 3
2 1 3
1 2 3`

### **输出示例**

`0`

### **提示信息**

如果将区域按照如下方式划分：

1 2 | 3

2 1 | 3

1 2 | 3

两个子区域内土地总价值之间的最小差距可以达到 0。

数据范围：

1 <= n, m <= 100；

n 和 m 不同时为 1。

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strconv"
    "strings"
    "math"
)

func main() {
		// 讀取整個輸入
    scanner := bufio.NewScanner(os.Stdin)

    var params []string
    
    // 逐行讀出放進陣列
    for scanner.Scan() {
        line := scanner.Text()
        params = append(params, line)
    }
    nm := strings.Fields(params[0])
    // 取 n 跟 m
    n, _ := strconv.Atoi(nm[0])
    m, _ := strconv.Atoi(nm[1])

    // 初始化參數矩陣
    matrix := make([][]int, n)
    for i := range matrix {
        matrix[i] = make([]int, m)
        mArray := strings.Fields(params[i + 1])
        for j := 0; j < m; j++ {
            matrix[i][j], _ = strconv.Atoi(mArray[j])
        }
    }

    // preMatrix := make([][]int, n)
    
    // for i := range preMatrix {
    //     preMatrix[i] = make([]int, m)
    //     for j := range preMatrix[i] {
    //         leftSum := 0
    //         topSum := 0
    //         if j - 1 >= 0 {
    //             leftSum = preMatrix[i][j - 1]
    //         }
    //         if i - 1 >= 0 {
    //             topSum = preMatrix[i - 1][j]
    //         }
    //         preMatrix[i][j] = preMatrix[i][j] + leftSum + topSum
    //         if j - 1 >= 0 && i - 1 >= 0 {
    //             preMatrix[i][j] -= preMatrix[i - 1][j - 1]
    //         }
    //     }
    // }

    // 初始化前綴和矩陣，第一行與第一列多增加零值，方便公式計算
    preMatrix := make([][]int, n + 1)
    for i := range preMatrix {
        preMatrix[i] = make([]int, m + 1)
    }
    
    for i := 1; i < n + 1; i++ {
        for j := 1; j < m + 1; j++ {
            preMatrix[i][j] = matrix[i - 1][j - 1] + preMatrix[i][j - 1] + preMatrix[i - 1][j] - preMatrix[i - 1][j - 1]
        }
    }

    //  土地權值總和
    totalSum := preMatrix[n][m]
    
    // 宣告一個最大數去比較找出最小差值
    minDiff := math.MaxInt64

    // 按上下區域找最小差值
    for i := 1; i < n; i++ {
        topSum := preMatrix[i][m]
        downSum := totalSum - topSum
        diff := int(math.Abs(float64(topSum - downSum)))
        if minDiff > diff {
            minDiff = diff
        }
    }

    // 按左右區域找最小差值
    for i := 1; i < m; i++ {
        leftSum := preMatrix[n][i]
        rightSum := totalSum - leftSum
        diff := int(math.Abs(float64(leftSum - rightSum)))
        if minDiff > diff {
            minDiff = diff
        }  
    }

    fmt.Println(minDiff)

}
```

1. 初始化題目的二維矩陣
2. 初始化前綴和二維矩陣，前綴和二維矩陣每一個值`preMatrix[i][j]`代表 `preMatrix[0 ~ i][0 ~ j]` 範圍內值的總和
3. 求值公式等於本身的值 + `preMatrix[i - 1][j]` + `preMatrix[i][j - 1]` - `preMatrix[i - 1][j - 1]` 
4. 為什麼要減 `preMatrix[i - 1][j - 1]` ？`preMatrix[i - 1][j]` 與 `preMatrix[i][j - 1]` 會有一個`preMatrix[i - 1][j - 1]` 區域重複計算到，所以要減去
5. 在 `preMatrix[0][m]` （第一行）和`preMatrix[n][0]` （第一列）初始一行和一列零值方便計算，因為當 n = 0 和 m = 0 的公式計算的時候，需要判斷上方與左方是否有值
6. 可以透過 `preMatrix[i][m]` 與 `preMatrix[n][i]`  去得出 上半部與左半部的總和，透過 `totalSum` 去換算出 下半部與右半部的總和
7. 枚舉這些差值，找出最小差值即是答案