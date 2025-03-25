# 區間和 (ACM) [prefix sum]

### **题目描述**

给定一个整数数组 Array，请计算该数组在每个指定区间内元素的总和。

### **输入描述**

第一行输入为整数数组 Array 的长度 n，接下来 n 行，每行一个整数，表示数组的元素。随后的输入为需要计算总和的区间下标：a，b （b > = a），直至文件结束。

### **输出描述**

输出每个指定区间内元素的总和。

### **输入示例**

`5
1
2
3
4
5
0 1
1 3`

### **输出示例**

`3
9`

### **提示信息**

数据范围：0 < n <= 100000

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strconv"
    "strings"
)

func main() {
		// 讀取整個輸入
    scanner := bufio.NewScanner(os.Stdin)

    var nums []string
    
    // 逐行讀出放進陣列
    for scanner.Scan() {
        line := scanner.Text()
        nums = append(nums, line)
    }

    n, _ := strconv.Atoi(nums[0])
    vec := make([]int, n)
    vec[0], _ = strconv.Atoi(nums[1])
    
    for i := 1; i < n; i++ {
        value, _ := strconv.Atoi(nums[i + 1])
        vec[i] = value + vec[i - 1]
    }

    for j := n + 1; j < len(nums); j++ {
        fields := strings.Fields(nums[j])
        left, _ := strconv.Atoi(fields[0])
        right, _ := strconv.Atoi(fields[1])

        if left == 0 {
            fmt.Println(vec[right])
        } else {
           fmt.Println(vec[right] - vec[left - 1]) 
        }
    }
}
```

“計算索引區間內總和”，這個次數不固定，也許 1 個，也許 100 個

暴力解：

每題都跑迴圈去計算每一題區間總和，時間複雜度為 O(n ^ 2)

前綴和：

前綴和重複利用計算過的子數組之和

題目數組: `nums = [1, 2, 3, 4, 5]` 

前綴和：`p = [1, 3, 6, 10, 15]` 

`p[i]`  表示 `nums[0]` 累加到 `nums[i]` 的總和

如果想要計算 `nums[2]` 到 `nums[4]` 的總和，只需要 `p[4]`  - `p[1]` 即可

因為：

`p[4] = nums[0] + nums[1] + nums[2] + nums[3] + nums[4]`

`p[1] = nums[0] + nums[1]` 

`p[4] - p[1] = nums[2] + nums[3] + nums[4]`