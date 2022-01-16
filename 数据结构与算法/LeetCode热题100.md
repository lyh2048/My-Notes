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



[⬆回到顶部](#简单)

## 中等



[⬆回到顶部](#中等)

## 困难



[⬆回到顶部](#困难)

