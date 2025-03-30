# 21. Merge Two Sorted Lists [linked list]

You are given the heads of two sorted linked lists `list1` and `list2`.

Merge the two lists into one **sorted** list. The list should be made by splicing together the nodes of the first two lists.

Return *the head of the merged linked list*.

**Example 1:**

![](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)

```
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]

```

**Example 2:**

```
Input: list1 = [], list2 = []
Output: []

```

**Example 3:**

```
Input: list1 = [], list2 = [0]
Output: [0]

```

**Constraints:**

- The number of nodes in both lists is in the range `[0, 50]`.
- `100 <= Node.val <= 100`
- Both `list1` and `list2` are sorted in **non-decreasing** order.

首解

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func mergeTwoLists(list1 *ListNode, list2 *ListNode) *ListNode {
    result := &ListNode{}
    head := result
    if list1 == nil && list2 == nil {
        return nil
    }
    
    for list1 != nil || list2 != nil {

        switch {
            case list1 == nil :
                result.Val = list2.Val
                list2 = list2.Next
            case list2 == nil :
                result.Val = list1.Val
                list1 = list1.Next
            case list1.Val < list2.Val :
                result.Val = list1.Val
                list1 = list1.Next
            case list2.Val < list1.Val :
                result.Val = list2.Val
                list2 = list2.Next
            case list1.Val == list2.Val :
                result.Val = list1.Val
                list1 = list1.Next
        }

        if list1 != nil || list2 != nil {
            result.Next = &ListNode{}
            result = result.Next
        }
    }
    return head
}
```

優化解

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func mergeTwoLists(list1 *ListNode, list2 *ListNode) *ListNode {
    dummy := &ListNode{}
    tail := dummy

    for list1 != nil && list2 != nil {
        if list1.Val < list2.Val {
            tail.Next = list1
            list1 = list1.Next
        } else {
            tail.Next = list2
            list2 = list2.Next
        }
        tail = tail.Next
    }
    if list1 == nil {
        tail.Next = list2
    } else {
        tail.Next = list1
    }
    return dummy.Next
}
```

兩個解法的差異首先在於看待角度的不同

首解用節點內部本身去操作，迴圈結束前判斷兩個鏈表指標是否到底，決定是否創建下一個節點

優化解用`Next` 的替換去操作，最先創建一個虛擬節點，並回傳虛擬節點的`Next` 作為結果

優化解好處在於不需處理初始化的部分，如果`list1` 與`list2` 同為空時，不需特別處理空結構問題，可直接回傳作為結果

並且在比較大小結果後，可直接替換節點本身，不需特別處理下一個新節點是否初始化的問題

## 時間複雜度

由于 `list1` 和 `list2` 各自最多遍历一次，每个元素最多访问一次，所以时间复杂度是：

O(m+n)

## **為什麼要用虛擬節點 (dummy node)?**

**目標**：我們想要合併兩個已排序的鏈表 `list1` 和 `list2`，並返回合併後的新鏈表。

**問題**：

1. **如果我們不使用虛擬節點，該如何處理 head？**
    - 直接操作 `list1` 和 `list2`，但這樣會在第一個節點的選擇上帶來額外的判斷邏輯。
    - 例如，如果 `list1.Val < list2.Val`，我們就要讓 `head = list1`，否則 `head = list2`。接下來，每次我們要移動 `head`，都需要額外的條件判斷，讓代碼變得更複雜。
2. **虛擬節點解決了什麼問題？**
    - 虛擬節點 `dummy` 只是一個佔位符，不會影響真正的結果。
    - 它的 `Next` 指向最終合併後的鏈表的第一個節點。
    - 這樣我們可以統一操作 `tail` 來追蹤合併的鏈表，而不需要額外處理 `head` 的初始化問題。

---

## **虛擬節點的工作方式**

假設：

```
plaintext
複製編輯
list1: 1 → 3 → 5
list2: 2 → 4 → 6

```

執行過程：

1. `dummy` 是一個空節點，它的 `Next` 會指向合併後的鏈表。
2. `tail` 開始時指向 `dummy`，然後我們比較 `list1.Val` 和 `list2.Val`，把較小的值連接到 `tail.Next`。
3. `tail` 指向 `tail.Next`，不斷重複這個過程。

最終：

```
plaintext
複製編輯
dummy → 1 → 2 → 3 → 4 → 5 → 6

```

因為 `dummy` 本身只是個空節點，我們 `return dummy.Next`，這樣返回的才是我們合併後的鏈表。

---

## **為什麼 `dummy` 不影響結果？**

關鍵在於 **我們不返回 `dummy`，而是返回 `dummy.Next`**。

**示例：**

```go
go
複製編輯
dummy := &ListNode{}
tail := dummy

```

- `dummy` 本身的 `Val` 沒有被用到，真正的鏈表從 `dummy.Next` 開始。
- `tail` 負責將節點串起來。
- 最後 `return dummy.Next`，跳過 `dummy` 這個佔位符，返回我們真正合併的結果。

**這樣的好處是：**

- **避免特殊情況處理**（例如 `list1` 或 `list2` 為空時）。
- **代碼更簡潔**，因為我們不需要額外判斷 `head` 的初始值。