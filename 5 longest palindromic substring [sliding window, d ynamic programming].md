# 5. longest palindromic substring [sliding window, dynamic programming]

给你一个字符串 s，找到 s 中最长的回文子串。

示例 1：

- 输入：s = "babad"
- 输出："bab"
- 解释："aba" 同样是符合题意的答案。

示例 2：

- 输入：s = "cbbd"
- 输出："bb"

示例 3：

- 输入：s = "a"
- 输出："a"

示例 4：

- 输入：s = "ac"
- 输出："a"

sliding window

```go
func longestPalindrome(s string) string {
    sLend := len(s)
    if sLend == 1 || s == "" {
        return s
    }
    maxLend := 0
    left_index := 0
    right_index := 0

    extended := func(left int, right int) {
        for {
            if left < 0 || right >= sLend {
                break
            }
            if s[left] == s[right] {
                if dif := right - left; dif > maxLend {
                    left_index = left
                    right_index = right
                    maxLend = dif
                }
                left--
                right++
            } else {
                return
            }
        }
    }

    for i, _ := range s {
        extended(i, i + 1)
        extended(i - 1, i + 1)
    }
    if maxLend > 0 {
        return s[left_index:right_index + 1]
    } else {
        return string(s[0])
    }

}
```

1. Initialization
    - Initialization a `maxLend`，used to recording longest palindromic length
    - Initialization `right_index` and `left_index`，used to recording start index and end index of longest substring
2. Check
    - If string just one characte or empty string，return the string itself
3. Traverse string
    - 遍歷字串並呼叫函式檢查字串兩種情境，字串長度是偶數或奇數，例 `“abba”` or `“aabaa”`
    - left 與 right 指標所指內容相同，就自中心向外擴展
    - Once the current maximum length is exceeded, record it and note the left and right index positions
4. Return result
    - If `maxLend` greater than 0, return the left index to right index the substring of string
    - If `maxLend` equals 0, return the string first characte，because task case  is like `“cb”`

複雜度：

- 時間複雜度：***O(n^2)*** 由於圍繞中心擴展回文可能需要 O(n) 時間
- 空間複雜度：***O(1)***。

dynamic programming

```go
func longestPalindrome(s string) string {
    if s == "" {
        return s
    }
    dp := make([][]bool, len(s) + 1)
    for i, _ := range s {
        dp[i] = make([]bool, len(s) + 1)
    }
    left := 0
    maxLength := 0
    for j := 0; j < len(s); j++ {
        dp[j][j] = true
        for i := 0; i < j; i++ {
            if j - i == 1 {
                dp[i][j] = s[i] == s[j]
            } else {
                dp[i][j] = s[i] == s[j] && dp[i + 1][j - 1]
            }
            if dp[i][j] && j - i > maxLength {
                maxLength = j - i
                left = i
            }
        }
    }
    return s[left:left + maxLength + 1]

}
```

1. Initialization
    - Initialization a `dp` ,is a boolean two-dimensional array
    - Initialization `left` and `maxLength`, used to recording current maximum length and first position
2. 定義狀態
    - 如果 `dp[i][j] == true`，說明從位置 `i` 到位置 `j` 的子串是回文
    - 如果 `dp[i][j] == false`，說明子串不是回文。
3. 初始條件
    - 單個字符總是回文，即 `dp[j][j] = true`
4. 狀態轉移方程
    - 若 `j - i == 1`  (子串長度為2)，即 `dp[i][j] = s[i] == s[j]` 兩個字符相等即是回文
    - 若 `j - i > 1` (子串長度大於2)，即 `dp[i][j] = s[i] == s[j] && dp[i + 1][j - 1]` 適用子串長度為3的回文，例`”aba”`，首尾字符相等，去掉首尾字符後的子串也必須是回文，基於自洽性僅需檢查一層即可，因為檢查回文是自中心向外擴散，`dp[i][j] == true` 意味著裡面完全符合回文
5. 獲取結果
    - 用變量 `left` 和 `maxlength` 記錄當前發現的最長回文子串的起始位置和長度

複雜度：

- 時間複雜度：***O(n^2)***
- 空間複雜度：***O(n^2) 使用二維數組來表示***位置 `i` 到位置 `j` 的子串狀態

url: [https://leetcode.com/problems/longest-palindromic-substring](https://leetcode.com/problems/longest-palindromic-substring)