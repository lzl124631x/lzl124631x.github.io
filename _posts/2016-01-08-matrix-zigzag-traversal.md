---
layout: post
title:  "[OJ] Matrix Zigzag Traversal"
date:   2016-01-08 00:00:00 +0800
categories: OJ
---

[LintCode #46. Matrix Zigzag Traversal (Easy)](http://www.lintcode.com/en/problem/matrix-zigzag-traversal/)

```cpp
class Solution {
public:
    vector<int> printZMatrix(vector<vector<int> > &matrix) {
        vector<int> v;
        if (matrix.size() == 0) return v;
        int m = matrix.size(), n = matrix[0].size(), cnt = m * n;
        int i = 0, j = 0;
        int d[][2] = { { -1, 1 }, { 1, -1 } };
        int dir = 0;
        while (v.size() < cnt) {
            while (i >= 0 && i < m && j >= 0 && j < n) {
                v.push_back(matrix[i][j]);
                i += d[dir][0];
                j += d[dir][1];
            }
            i -= d[dir][0];
            j -= d[dir][1];
            if (dir == 0) {
                if (j + 1 < n) ++j;
                else ++i;
            } else {
                if (i + 1 < m) ++i;
                else ++j;
            }
            dir = (dir + 1) % 2;
        }
        return v;
    }
};
```

思路:

* 斜着走的方向只有"右上"(`dir=0`)和"左下"(`dir=1`). 按"右上", "左下"的顺序交替走.
* 当走到边界的时候, 要么"向右走一步", 要么"向下走一步".
	* 如果正在向"**右**上"走, 优先"向**右**走一步", 若不能则"向**下**走一步".
	* 如果正在向"**左**下"走, 优先"向**下**走一步", 若不能则"向**右**走一步".

时间复杂度: `O(m*n)`

空间复杂度: `O(1)`

---


参照[这篇博文](http://algorithm.yuanbin.me/zh-cn/problem_misc/matrix_zigzag_traversal.html)的思路.

```cpp
class Solution {
public:
    /**
     * @param matrix: a matrix of integers
     * @return: a vector of integers
     */
    vector<int> printZMatrix(vector<vector<int> > &matrix) {
        vector<int> v;
        if (matrix.size() == 0) return v;
        int m = matrix.size(), n = matrix[0].size();
        int sum = 0, x = 0, dx = -1;
        while (sum < m + n - 1) {
            while (x >= 0 && x < m && sum - x >= 0 && sum - x < n) {
                v.push_back(matrix[x][sum - x]);
                x += dx;
            }
            x -= dx;
            sum++;
            if ((dx == -1 && sum - x >= n)
            || (dx == 1 && x < m - 1)) {
                ++x;
            }
            dx = -dx;
        }
        return v;
    }
};
```

思路:

* 观察下标规律.

```
(0, 0)
(0, 1), (1, 0)
(2, 0), (1, 1), (0, 2)
(0, 3), (1, 2), (2, 1)
(2, 2), (1, 3)
(2, 3)
```

可以看到各行`x`与`y`坐标的和是常数, 且该和逐行递增.

* 偶数行`x`递减, 奇数行`x`递增.
* 原解法有个缺陷是"矩阵越细长, 空循环就越多". 我的解法对这点进行了优化.

时间复杂度: `O(m*n)`

空间复杂度: `O(1)`