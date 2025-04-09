# 24. Swap Nodes in Pairs [Linked List]

Given a linked list, swap every two adjacent nodes and return its head. You must solve the problem without modifying the values in the list's nodes (i.e., only nodes themselves may be changed.)

**Example 1:**

**Input:** head = [1,2,3,4]

**Output:** [2,1,4,3]

**Explanation:**

![](https://assets.leetcode.com/uploads/2020/10/03/swap_ex1.jpg)

**Example 2:**

**Input:** head = []

**Output:** []

**Example 3:**

**Input:** head = [1]

**Output:** [1]

**Example 4:**

**Input:** head = [1,2,3]

**Output:** [2,1,3]

**Constraints:**

- The number of nodes in the list is in the range `[0, 100]`.
- `0 <= Node.val <= 100`

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func swapPairs(head *ListNode) *ListNode {
	dummy := &ListNode{}
	dummy.Next = head
	pre := dummy

	for head != nil && head.Next != nil {
		pre.Next = head.Next
		back := head.Next
		head.Next = back.Next
		back.Next = head
		pre = head
		head = pre.Next
	}
	return dummy.Next
}
```

這題要看待的角度節點有 前、中、後、後後，四個節點

所以當要進行操作時，中不能為 `nil` ，後也不能為 `nil` 

前改變指向

中與後交換位置

中指向後後

![截圖 2025-02-20 上午11.16.03.png](24%20Swap%20Nodes%20in%20Pairs%20%5BLinked%20List%5D%201a0fa8754944801ba9baed6d2f187fd6/%E6%88%AA%E5%9C%96_2025-02-20_%E4%B8%8A%E5%8D%8811.16.03.png)

另外要注意的是交換的規則為兩兩交換

所以假設已進行一輪交換，下一輪開始的前，為這一輪交換後的後

例如這一輪交換完為 cur 2 1 3 4，那麼下一輪的前就是 1