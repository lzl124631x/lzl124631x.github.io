---
layout: post
title:  "[OJ] Find Minimum in Rotated Sorted Array"
date:   2016-01-24 00:00:00 +0800
tags: OJ
---

[LintCode 159.  Find Minimum in Rotated Sorted Array (Medium)](http://www.lintcode.com/en/problem/find-minimum-in-rotated-sorted-array/)

[LeetCode 153. Find Minimum in Rotated Sorted Array (Medium)](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

这题看着简单, 但是条件总容易搞错. @_@...

思路是在当前区间有序的时候立即停止, 然后*某个点*(详见代码)就是答案.

我没有做`nums.size() == 0`的判断, 因为这种情况应该抛个异常什么的, 给什么`int`值都可能是有效值(如果`nums`中的数没有指定范围).

# 解法1

这个解法要注意一种情况, 就是如果进行`R = M - 1`之后数组有序了, 最小点应该在`R + 1`处.

```cpp
class Solution {
public:
    int findMin(vector<int> &num) {
        int n = num.size();
        int L = 0, R = n - 1;
        while (L <= R) {
            int M = (R - L) / 2 + L;
            if (num[M] > num[R]) {
                L = M + 1;
            } else if (num[M] < num[L]) {
                R = M - 1;
            } else break;
        }
        return R + 1 < n && num[R + 1] < num[L] ? num[R + 1] : num[L];
    }
};
```

# 解法1.1

根据上面的分析, 发现那么只进行`R = M`就可以避免上面的情况了, 于是又写了个更精简的版本.

[九章的解法](http://www.jiuzhang.com/solutions/find-minimum-in-rotated-sorted-array/)中, `target`可以换做`num[R]`, 循环条件换成`while(L < R)`, 返回值直接写成`num[L]`, 其实就是这个解法的无`break`版本, 循环次数会比这个解法多几次, 因为它要一直找到`L == R`才会结束.

```cpp
class Solution {
public:
    int findMin(vector<int> &num) {
        int n = num.size();
        int L = 0, R = n - 1;
        while (L < R) {
            int M = (R - L) / 2 + L;
            if (num[M] > num[R]) {
                L = M + 1;
            } else if (num[M] < num[L]) {
                R = M;
            } else break;
        }
        return num[L];
    }
};
```


# 解法1.2

受[这篇博文](http://www.cnblogs.com/ganganloveu/p/4081438.html)启发, 判断"是否有序"可以放到`while`的判断条件中.

```cpp
class Solution {
public:
    int findMin(vector<int> &num) {
        int n = num.size();
        int L = 0, R = n - 1;
        while (L < R && num[L] > num[R]) {
            int M = (R - L) / 2 + L;
            if (num[M] > num[R]) {
                L = M + 1;
            } else {
                R = M;
            }
        }
        return num[L];
    }
};
```

时间复杂度: `O(logn)`

空间复杂度: `O(1)`