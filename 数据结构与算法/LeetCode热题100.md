# ð¥  LeetCode ç­é¢ HOT 100

ç²¾é 100 éåæ£ï¼LeetCodeï¼ä¸æç­é¨çé¢ç®ï¼éååè¯ç®æ³ä¸æ°æ®ç»æçæ°æåæ³è¦å¨ç­æ¶é´åé«ææåçäººï¼çç»ææ¡è¿ 100 éé¢ï¼ä½ å°±å·²ç»å·å¤äºå¨ä»£ç ä¸çéè¡çåºæ¬è½åã

## ç®å

### ä¸¤æ°ä¹å

[é¢ç®é¾æ¥](https://leetcode-cn.com/problems/two-sum/)

æè·¯ï¼

ç´æ¥ç¨æ´åè§£æ³çæ¶é´å¤æåº¦æ¯O(n^2)ï¼ä¸ºäºéä½æ¶é´å¤æåº¦ï¼æä»¬å¯ä»¥ä½¿ç¨åå¸è¡¨æ¥å¿«éè¿åè¦æ±åç´ çä¸æ ã

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

### ææçæ¬å·

[é¢ç®é¾æ¥](https://leetcode-cn.com/problems/valid-parentheses/)

æè·¯ï¼

æ¬å·å¹éæ¯æ çç»å¸åºç¨ä¹ä¸ï¼å æ­¤è¯¥é¢å¯ä»¥ä½¿ç¨æ æ¥è§£å³ã

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

### åå¹¶ä¸¤ä¸ªæåºé¾è¡¨

[é¢ç®é¾æ¥](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

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

### æå¤§å­æ°ç»å

[é¢ç®é¾æ¥](https://leetcode-cn.com/problems/maximum-subarray/)

æè·¯ï¼

å¨æè§åï¼ç¨f(i)è¡¨ç¤ºä»¥ç¬¬iä¸ªæ°ç»å°¾çè¿ç»­å­æ°ç»çæå¤§åï¼åf(i) = max{ f(i-1) + nums[i], nums[i] }ï¼æç»ç»ææ¯æ°ç»fä¸­çæå¤§å¼

ç±äºå½åä½ç½®çå¼åªååä¸ä¸ªä½ç½®çå¼æå³ï¼å æ­¤ï¼æä»¬å¯ä»¥è¿ä¸æ­¥ä¼åç©ºé´å¤æåº¦ï¼ä½¿ç¨ä¸ä¸ªåéæ¥å­å¨å¯¹äºå½åä½ç½®çä¸ä¸ä¸ªä½ç½®çå¼ã

æ¶é´å¤æåº¦ï¼O(n)

ç©ºé´å¤æåº¦ï¼O(1)

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



åæ²»æ³æ±è§£ï¼

```java
```

### ç¬æ¥¼æ¢¯

[é¢ç®é¾æ¥](https://leetcode-cn.com/problems/climbing-stairs/)

æè·¯ï¼ç¨f(i)è¡¨ç¤ºç¬å°içæ¹æ³æ°ï¼å`f(i) = f(i-1) + f(i-2)`ï¼ææç»ç»æä¸ºf(n) 

ç±äºf(i)åªåf(i-1)ä¸f(i-2)æå³ï¼æä»¥æä»¬ç¨ä¸¤ä¸ªåéæ¥å­å¨f(i-1)åf(i-2)ï¼ä»èéä½ç®æ³çç©ºé´å¤æåº¦

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

### äºåæ çä¸­åºéå

[é¢ç®é¾æ¥](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

æè·¯ï¼äºåæ çä¸­åºéåæ¯åè®¿é®å·¦å­æ ï¼åè®¿é®æ ¹ç»ç¹ï¼æåè®¿é®å³å­æ ï¼å¯¹äºå·¦å­æ åå³å­æ çè®¿é®ä¹éç¨ç¸åçç­ç¥

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

è¿­ä»£ç®æ³ï¼

```java
```

### å¯¹ç§°äºåæ 

[é¢ç®é¾æ¥](https://leetcode-cn.com/problems/symmetric-tree/)

æè·¯ï¼æ çå·¦å­æ ä¸å³å­æ éåå¯¹ç§°ï¼éè¦å®ä»¬çä¸¤ä¸ªæ ¹ç»ç¹å·æç¸åçå¼ï¼æ¯ä¸ªæ çå³å­æ é½ä¸å¦ä¸ä¸ªæ çå·¦å­æ éåå¯¹ç§°ï¼æä»¬å¯ä»¥å®ç°è¿æ ·ä¸ä¸ªéå½å½æ°ï¼éè¿ãåæ­¥ç§»å¨ãä¸¤ä¸ªæéçæ¹æ³æ¥éåè¿æ£µæ ï¼p æéå q æéä¸å¼å§é½æåè¿æ£µæ çæ ¹ï¼éå p å³ç§»æ¶ï¼qå·¦ç§»ï¼p å·¦ç§»æ¶ï¼q å³ç§»ãæ¯æ¬¡æ£æ¥å½å p å q ç»ç¹çå¼æ¯å¦ç¸ç­ï¼å¦æç¸ç­åå¤æ­å·¦å³å­æ æ¯å¦å¯¹ç§°ã

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



è¿­ä»£ç®æ³ï¼

```java
```

### äºåæ çæå¤§æ·±åº¦

[é¢ç®é¾æ¥](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

æè·¯ï¼ç©ºæ çæå¤§æ·±åº¦ä¸º0ï¼å¦æè¯¥æ ä¸æ¯ç©ºæ çè¯ï¼é£ä¹è¯¥æ çæå¤§æ·±åº¦ä¸ºmax(å·¦å­æ æå¤§æ·±åº¦ï¼å³å­æ æå¤§æ·±åº¦) + 1

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



[â¬åå°é¡¶é¨](#ç®å)

## ä¸­ç­

### ä¸¤æ°ç¸å 

[é¢ç®é¾æ¥](https://leetcode-cn.com/problems/add-two-numbers/)

æè·¯ï¼é¾è¡¨æä½+å æ³æ¨¡æ

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



[â¬åå°é¡¶é¨](#ä¸­ç­)

## å°é¾



[â¬åå°é¡¶é¨](#å°é¾)

