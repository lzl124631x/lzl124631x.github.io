---
layout: post
title:  "[OJ] Find Minimum in Rotated Sorted Array II"
date:   2016-01-25 00:00:00 +0800
categories: OJ
---

[LintCode 160. Find Minimum in Rotated Sorted Array II (Medium)](http://www.lintcode.com/en/problem/find-minimum-in-rotated-sorted-array-ii/)

[LeetCode 154. Find Minimum in Rotated Sorted Array II (Hard)](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/)

# 解法1

自己做了半个小时, 想分情况判断`num[mid]`与`num[start]`, `num[mid]`与`num[end]`的大小关系, 每个关系分`>, =, <`的情况, 于是就是9种情况...

```
>, >, L = M + 1
>, =, 选L
>, <, 选L
=, >, L = M + 1
=, =, 二分递归
=, <, 选L
<, >, 不可能
<, =, R = M
<, <, R = M
```

然后就是下面这蛋疼的代码...

```cpp
class Solution {
private:
    int rec(vector<int> &num, int start, int end) {
        if (start == end) return num[start];
        while (start + 1 < end) {
            int mid = start + (end - start) / 2;
            if (num[mid] > num[start]) {
                if (num[mid] > num[end]) {
                    start = mid + 1;
                } else {
                    return num[start];
                }
            } else if (num[mid] == num[start]) {
                if (num[mid] > num[end]) {
                    start = mid + 1;
                } else if (num[mid] == num[mid]) {
                    return min(rec(num, start, mid), rec(num, mid + 1, end));
                } else {
                    return num[start];
                }
            } else {
                end = mid;
            }
        }
        return min(num[start], num[end]);
    }
public:
    int findMin(vector<int> &num) {
        return rec(num, 0, num.size() - 1);
    }
};
```

# 解法1.1

观察9种情况发现有些情况可以合并.

```cpp
x, >, L = M + 1
<, x, R = M
x, =, --R
else 选L
```

```cpp
class Solution {
public:
    int findMin(vector<int> &num) {
        if (num.empty()) return 0;
        int start = 0, end = num.size() - 1;
        while (start < end) {
            int mid = start + (end - start) / 2;
            if (num[mid] > num[end]) {
                start = mid + 1;
            } else if (num[mid] < num[start]) {
                end = mid;
            } else if (num[mid] == num[end]) {
                --end;
            } else {
                return num[start];
            }
        }
        return num[start];
    }
};
```

# 解法2

参考[这篇博文](http://www.cnblogs.com/ganganloveu/p/4081483.html), 在Find Minimum in Rotated Sorted Array代码的基础上稍事修改就可以得到下面的代码. 当`num[M] == num[R]`时, 将`R`左移一位即可.

```cpp
class Solution {
public:
    int findMin(vector<int> &num) {
        int n = num.size();
        int L = 0, R = n - 1;
        while (L < R && num[L] >= num[R]) {
            int M = (R - L) / 2 + L;
            if (num[M] > num[R]) {
                L = M + 1;
            } else if (num[M] < num[R]) {
                R = M;
            } else {
                --R;
            }
        }
        return num[L];
    }
};
```

时间复杂度: `O(logn)` (在数组恒值的最差情况下退化到`O(n)`)

空间复杂度: `O(1)`