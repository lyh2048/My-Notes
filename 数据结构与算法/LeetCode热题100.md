# 🔥  LeetCode 热题 HOT 100

精选 100 道力扣（LeetCode）上最热门的题目，适合初识算法与数据结构的新手和想要在短时间内高效提升的人，熟练掌握这 100 道题，你就已经具备了在代码世界通行的基本能力。

## 简单

### 两数之和

[题目链接](https://leetcode-cn.com/problems/two-sum/)

思路：

直接用暴力解法的时间复杂度是O(n^2)，为了降低时间复杂度，我们可以使用哈希表来快速返回要求元素的下标。

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(target - nums[i])) {
                return new int[]{map.get(target - nums[i]), i};
            }
            map.put(nums[i], i);
        }
        return new int[0];
    }
}
```

### 有效的括号

[题目链接](https://leetcode-cn.com/problems/valid-parentheses/)

思路：

括号匹配是栈的经典应用之一，因此该题可以使用栈来解决。

```java
class Solution {
    public boolean isValid(String s) {
        int n = s.length();
        if (n % 2 == 1) {
            return false;
        }
        Map<Character, Character> map = new HashMap<>();
        map.put(')', '(');
        map.put(']', '[');
        map.put('}', '{');
        Deque<Character> stack = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);
            if (map.containsKey(c)) {
                if (stack.isEmpty() || stack.peek() != map.get(c)) {
                    return false;
                } else {
                    stack.pop();
                }
            } else {
                stack.push(c);
            }
        }
        return stack.isEmpty();
    }
}
```

### 合并两个有序链表

[题目链接](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode ans = new ListNode();
        ListNode p = ans;
        while (list1 != null && list2 != null) {
            if (list1.val <= list2.val) {
                p.next = list1;
                p = list1;
                list1 = list1.next;
            } else {
                p.next = list2;
                p = list2;
                list2 = list2.next;
            }
        }
        if (list1 != null) {
            p.next = list1;
        }
        if (list2 != null) {
            p.next = list2;
        }
        return ans.next;
    }
}
```

### 最大子数组和

[题目链接](https://leetcode-cn.com/problems/maximum-subarray/)

思路：

动态规划，用f(i)表示以第i个数结尾的连续子数组的最大和，则f(i) = max{ f(i-1) + nums[i], nums[i] }，最终结果是数组f中的最大值

由于当前位置的值只和前一个位置的值有关，因此，我们可以进一步优化空间复杂度，使用一个变量来存储对于当前位置的上一个位置的值。

时间复杂度：O(n)

空间复杂度：O(1)

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int ans = nums[0], pre = 0;
        for (int i = 0; i < nums.length; i++) {
            pre = Math.max(nums[i], pre + nums[i]);
            ans = Math.max(ans, pre);
        }
        return ans;
    }
}
```



分治法求解：

```java
```



[⬆回到顶部](#简单)

## 中等



[⬆回到顶部](#中等)

## 困难



[⬆回到顶部](#困难)

