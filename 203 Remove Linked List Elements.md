# 203. Remove Linked List Elements

Given the `head` of a linked list and an integer `val`, remove all the nodes of the linked list that has `Node.val == val`, and return *the new head*.

**Example 1:**

![](https://assets.leetcode.com/uploads/2021/03/06/removelinked-list.jpg)

```
Input: head = [1,2,6,3,4,5,6], val = 6
Output: [1,2,3,4,5]

```

**Example 2:**

```
Input: head = [], val = 1
Output: []

```

**Example 3:**

```
Input: head = [7,7,7,7], val = 7
Output: []

```

**Constraints:**

- The number of nodes in the list is in the range `[0, 104]`.
- `1 <= Node.val <= 50`
- `0 <= val <= 50`

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func removeElements(head *ListNode, val int) *ListNode {
    dummy := &ListNode{}
    dummy.Next = head
    tail := dummy
    for tail.Next != nil {
        currenNode := tail.Next
        if currenNode.Val == val {
            tail.Next = currenNode.Next
        } else {
            tail = tail.Next
        }
        
    }
    return dummy.Next
    
}
```

使用虛擬頭節點來優化頭部點的判斷處理

以當前節點的下一節點的角度來操作

實作時犯了一個錯誤，原先是寫這樣

```go
if currenNode.Val == val {
    tail.Next = currenNode.Next
} 
tail = tail.Next
```

這樣會有兩個問題

1. 有進行刪除節點的下一圈，會漏掉判斷一個節點
2. 如果刪除的節點是最後一個，tail 會指向 nil，再下一圈（最後一個判斷）時，會因 [`tail.Next](http://tail.Next) != nil` 引發訪問 nil 的 panic

## 結論

如果有刪除節點的情況下，因 tail 的下一節點已發生改變，tail 這個指針位置不移動，因為解決問題的思路一直是以當前節點的下一節點為角度