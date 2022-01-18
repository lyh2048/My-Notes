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

### 爬楼梯

[题目链接](https://leetcode-cn.com/problems/climbing-stairs/)

思路：用f(i)表示爬到i的方法数，则`f(i) = f(i-1) + f(i-2)`，故最终结果为f(n) 

由于f(i)只和f(i-1)与f(i-2)有关，所以我们用两个变量来存储f(i-1)和f(i-2)，从而降低算法的空间复杂度

```java
class Solution {
    public int climbStairs(int n) {
        int p = 0, q = 0, ans = 1;
        for (int i = 1; i <= n; i++) {
            p = q;
            q = ans;
            ans = p + q;
        }
        return ans;
    }
}
```

### 二叉树的中序遍历

[题目链接](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

思路：二叉树的中序遍历是先访问左子树，再访问根结点，最后访问右子树，对于左子树和右子树的访问也采用相同的策略

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        if (root == null) {
            return ans;
        }
        ans.addAll(inorderTraversal(root.left));
        ans.add(root.val);
        ans.addAll(inorderTraversal(root.right));
        return ans;
    }
}
```

迭代算法：

```java
```

### 对称二叉树

[题目链接](https://leetcode-cn.com/problems/symmetric-tree/)

思路：树的左子树与右子树镜像对称，需要它们的两个根结点具有相同的值，每个树的右子树都与另一个树的左子树镜像对称，我们可以实现这样一个递归函数，通过「同步移动」两个指针的方法来遍历这棵树，p 指针和 q 指针一开始都指向这棵树的根，随后 p 右移时，q左移，p 左移时，q 右移。每次检查当前 p 和 q 结点的值是否相等，如果相等再判断左右子树是否对称。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return isSymmetric(root, root);
    }

    public boolean isSymmetric(TreeNode p, TreeNode q) {
        if (p == null && q == null) {
            return true;
        }
        if (p == null || q == null) {
            return false;
        }
        return p.val == q.val && isSymmetric(p.left, q.right) && isSymmetric(p.right, q.left);
    }
}
```



迭代算法：

```java
```

### 二叉树的最大深度

[题目链接](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

思路：空树的最大深度为0，如果该树不是空树的话，那么该树的最大深度为max(左子树最大深度，右子树最大深度) + 1

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    }
}
```



[⬆回到顶部](#简单)

## 中等

### 两数相加

[题目链接](https://leetcode-cn.com/problems/add-two-numbers/)

思路：链表操作+加法模拟

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
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode ans = new ListNode();
        ListNode p = ans;
        int t = 0;
        while (l1 != null && l2 != null) {
            int v = l1.val + l2.val + t;
            ListNode temp;
            if (v > 9) {
                t = v / 10;
                temp = new ListNode(v % 10);
            } else {
                t = 0;
                temp = new ListNode(v);
            }
            p.next = temp;
            p = temp;
            l1 = l1.next;
            l2 = l2.next;
        }
        while (l1 != null) {
            int v = l1.val + t;
            ListNode temp;
            if (v > 9) {
                t = v / 10;
                temp = new ListNode(v % 10);
            } else {
                t = 0;
                temp = new ListNode(v);
            }
            p.next = temp;
            p = temp;
            l1 = l1.next;
        }
        while (l2 != null) {
            int v = l2.val + t;
            ListNode temp;
            if (v > 9) {
                t = v / 10;
                temp = new ListNode(v % 10);
            } else {
                t = 0;
                temp = new ListNode(v);
            }
            p.next = temp;
            p = temp;
            l2 = l2.next;
        }
        if (t != 0) {
            ListNode temp = new ListNode(t);
            p.next = temp;
            p = temp;
        }
        return ans.next;
    }
}
```



[⬆回到顶部](#中等)

## 困难



[⬆回到顶部](#困难)

