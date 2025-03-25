# 27. Remove Element [Array, Fast Slow Point]

Given an integer array `nums` and an integer `val`, remove all occurrences of `val` in `nums` [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm). The order of the elements may be changed. Then return *the number of elements in* `nums` *which are not equal to* `val`.

Consider the number of elements in `nums` which are not equal to `val` be `k`, to get accepted, you need to do the following things:

- Change the array `nums` such that the first `k` elements of `nums` contain the elements which are not equal to `val`. The remaining elements of `nums` are not important as well as the size of `nums`.
- Return `k`.

**Custom Judge:**

The judge will test your solution with the following code:

```
int[] nums = [...]; // Input array
int val = ...; // Value to remove
int[] expectedNums = [...]; // The expected answer with correct length.
                            // It is sorted with no values equaling val.

int k = removeElement(nums, val); // Calls your implementation

assert k == expectedNums.length;
sort(nums, 0, k); // Sort the first k elements of nums
for (int i = 0; i < actualLength; i++) {
    assert nums[i] == expectedNums[i];
}

```

If all assertions pass, then your solution will be **accepted**.

**Example 1:**

```
Input: nums = [3,2,2,3], val = 3
Output: 2, nums = [2,2,_,_]
Explanation: Your function should return k = 2, with the first two elements of nums being 2.
It does not matter what you leave beyond the returned k (hence they are underscores).

```

**Example 2:**

```
Input: nums = [0,1,2,2,3,0,4,2], val = 2
Output: 5, nums = [0,1,4,0,3,_,_,_]
Explanation: Your function should return k = 5, with the first five elements of nums containing 0, 0, 1, 3, and 4.
Note that the five elements can be returned in any order.
It does not matter what you leave beyond the returned k (hence they are underscores).

```

**Constraints:**

- `0 <= nums.length <= 100`
- `0 <= nums[i] <= 50`
- `0 <= val <= 100`

```go
func removeElement(nums []int, val int) int {
    slow := 0

    for fast := 0; fast < len(nums); fast++ {
        if nums[fast] == val {
            continue
        }
        nums[slow] = nums[fast]
        slow ++
    }
    
    return slow
}
```

使用快慢指針演算法，讓時間複雜度控制在 O(n)

關鍵很重要的三步

1. 如果快指針遇到要刪除的元素，就跳下一圈，不執行後面
2. 慢指針映射的陣列元素被覆蓋為快指針映射的陣列元素
3. 慢指針往前移動

慢指針會在迴圈結束前往前移動，如果尚未遇到需刪除元素前，每一輪覆蓋時，快慢指針指向同一元素

當遇到需刪除元素時，慢指針維持不動，直到快指針指向不需刪除元素時，覆蓋慢指針指向元素，慢指針接著移動

這裡能得知，慢紙針最後所到達位置，即為正確答案長度

這題用這個演算法解題有一很大前提，就是答案不關心正確答案長度以後的元素

舉例`[1, 2, 4, 4]`  `val = 2` ，執行完演算法後會變成 `[1, 4, 4, 4]` ，但答案只關心正確答案 `slow = 3` 的 `[1, 4, 4]`