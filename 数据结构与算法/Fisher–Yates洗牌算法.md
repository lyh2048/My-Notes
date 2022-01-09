# Fisher–Yates shuffle 洗牌算法

## 概述

简单来说，Fisher–Yates shuffle算法是一个用来将一个有限集合生成一个随机排列的算法（数组随机排序）。

这个算法生成的随机排列是等概率的，同时，这个算法非常高效。

## 原始版

Fisher–Yates shuffle算法的原始版本使用了一个随机数表来提供随机数，给出了1到N的数字的随机排列，具体步骤如下：

1. 写下从1到N的数字
2. 取一个从1到剩下的数字（包括这个数字）的随机数k
3. 从低位开始，得到第k个数字（这个数字还没有被取出），把它写在独立的一个列表的最后一位
4. 重复第2步，直到所有的数字都被取出
5. 第3步写出的这个序列，现在就是原始数字的随机排列

已经证明如果第2步取出的数字是真随机的，那么最后得到的排列一定也是。

## 现代版

算法的现代版是为计算机设计的。现代版的描述与原始略有不同，因为如果按照原始方法，计算机会花费很多无用的时间去计算上述第3步的剩余数字。这里的方法是在每次迭代时交换这个被取出的数字到原始列表的最后。这样就将时间复杂度从O(n^2)减少到了O(n)。

算法伪代码：

```java
-- To shuffle an array a of n elements (indices 0..n-1):
for i from n−1 downto 1 do
     j ← random integer such that 0 ≤ j ≤ i
     exchange a[j] and a[i]
```



## Java代码实现

```java
package org.example;

import java.util.Arrays;

public class Shuffle {
    // 将数组值的顺序随机化，返回一个新数组
    // 根据Fisher-Yates算法重新排序数组的元素
    public static <T> T[] shuffle(T[] input) {
        T[] arr = Arrays.copyOf(input, input.length);
        int m = arr.length;
        while (m > 0) {
            int i = (int) Math.floor(Math.random() * m--);
            T tmp = arr[i];
            arr[i] = arr[m];
            arr[m] = tmp;
        }
        return arr;
    }
}

```



## 总结

总之，Fisher–Yates shuffle算法是一个非常高效又公平的随机排序算法，如果有随机排序数组的需求，用这个就对了！

## 参考

[Fisher–Yates shuffle](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle)

