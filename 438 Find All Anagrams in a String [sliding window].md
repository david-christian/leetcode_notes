# 438. Find All Anagrams in a String [sliding window]

Given two strings s and p, return an array of all the start indices of p's anagrams in s. You may return the answer in any order.

給定兩個字串 s 和 p，傳回 s 中 p 的所有字謎的起始索引的陣列。您可以按任意順序返回答案。

**Example 1:**

```
Input: s = "cbaebabacd", p = "abc"
Output: [0,6]
Explanation:
The substring with start index = 0 is "cba", which is an anagram of "abc".
The substring with start index = 6 is "bac", which is an anagram of "abc".

```

**Example 2:**

```
Input: s = "abab", p = "ab"
Output: [0,1,2]
Explanation:
The substring with start index = 0 is "ab", which is an anagram of "ab".
The substring with start index = 1 is "ba", which is an anagram of "ab".
The substring with start index = 2 is "ab", which is an anagram of "ab".
```

```go
func findAnagrams(s string, p string) []int {
    if s == "" || len(p) > len(s) {
        return []int{}
    }
    pCount := make(map[byte]int)
    for _, v := range p {
        pCount[byte(v)] ++
    }
    needCount := len(pCount)
    haveCount := 0
    windowCount := make(map[byte]int)
    result := []int{}
    left := 0
    for right, v := range s {
        if pCount[byte(v)] == 0 {
            left = right + 1
            haveCount = 0
            windowCount = make(map[byte]int)
            continue
        }
        windowCount[byte(v)] ++
        if windowCount[byte(v)] == pCount[byte(v)] {
            haveCount ++
        }
        for windowCount[byte(v)] > pCount[byte(v)] {
            if windowCount[s[left]] == pCount[s[left]] {
                haveCount --
            }
            windowCount[s[left]] --
            left ++
        }
        if haveCount == needCount {
            result = append(result, left)
            windowCount[s[left]] --
            left ++
            haveCount --
        }
        

    }
    return result
}
```

1. 初始化
    - 初始化一個 hashmap pCount 並遍歷 p 字串，紀錄 p 字串字元出現次數
    - 初始化一個 hashmap windowCount 動態記錄 s 字串中滑動視窗裡字元出現次數
    - 初始化 needCount & haveCount 紀錄所需次數（去重）和 目前次數（去重）
2. 檢查長度
    - 如果 string s 等於空字串，或者 string p 長度大於 string s 則傳回空數組
3. 遍歷 string s & 滑動視窗
    - left 與 right 皆從 index 0 開始
    - 如果檢查的字符 (right 的位置) 沒有在 pCount 中，則表示前面達成的條件作廢，將 left 移動到 right + 1 的位置，重置 windowCount & haveCount，並跳過該圈
    - 將檢查到的字符次數紀錄在 windowCount ，當字符在 windowCount 的次數與 pCount 的次數相等時，haveCount + 1
    - 如果次數大於時，代表目前的滑動視窗無法相等 string p ，要開始將 left 指標往前，先檢查 left 指標指向的字符是否有完成 haveCount，有的話需將次數 - 1，並也將 windowCount 次數 - 1，將 left 指標往前，直到當前字符在 windowCount 的次數小於等於 pCount
    - 如果 haveCount 與 needCount 相等，將 left 指標所在的 index 紀錄在結果中，為了尋找下一個結果，將 當前 left 指標所在的字符 windowCount 次數 - 1，haveCount - 1，並將 left 指標往前一個位置
4. 回傳結果

時間複雜度：O(n)

最佳解

```go
func findAnagrams(s string, p string) []int {
	var list []int
	freqS := make([]int, 26)
	freqP := make([]int, 26)

	if len(s) < len(p) {
		return list
	}

	for i := 0; i < len(p); i++ {
		freqS[int(s[i]-'a')]++
		freqP[int(p[i]-'a')]++
	}

	start := 0
	end := len(p)

	if fmt.Sprint(freqS) == fmt.Sprint(freqP) {
		list = append(list, start)
	}

	for end < len(s) {
		freqS[int(s[start]-'a')]--
		freqS[int(s[end]-'a')]++

		if fmt.Sprint(freqS) == fmt.Sprint(freqP) {
			list = append(list, start+1)
		}

		start++
		end++
	}

	return list
}
```

1. 初始化
    - 初始化兩個數組 freqS 和 freqP ，數組大小為 26，因為題目僅限制小寫字母，分別追蹤 string s 和 string p 的頻率
2. 檢查長度
    - 如果 string s 等於空字串，或者 string p 長度大於 string s 則傳回空數組
3. 計數頻率
    - 遍歷 string p 並計算每個字符出現的頻率，將計數儲存在 freqP 的位置中，以 ‘a’ 的 ASCII 值（十進制）97，通過 s[i] - ‘a’ 下映 0 - 25
    - 同時也將 string s 等同 string p 長度視窗內的字符出現頻率，計數儲存在 freqS 的位置中
4. 檢查
    - 比較 freqP 與 freqS 兩個陣列是否相等，並將起始索引 0 記錄到結果
5. 滑動視窗
    - 初始化兩個指標 start 和 end ，start 最初為 0，end 最初為 p.length (start + 1 ~ end 為檢查範圍)
    - 逐一字符滑動視窗，直到 end 指標超出 string s 的長度，在每一次中將 start 指向的字符從 freqS 的頻率中 - 1 ，end 指向的字符，從 freqP 的頻率中 + 1 ，也就是說 start + 1 ~ end 所涵蓋的字符為檢查範圍，比較 freqP 與 freqS 兩個陣列是否相等，並將索引( start + 1 )的 記錄到結果中

時間複雜度：O(n)