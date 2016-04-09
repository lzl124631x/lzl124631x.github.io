---
layout: post
title:  "[OJ] Count of Smaller Numbers After Self"
date:   2015-12-20 00:00:00 +0800
categories: OJ
---

[315. Count of Smaller Numbers After Self](https://leetcode.com/problems/count-of-smaller-numbers-after-self/)

```cpp
class Solution {
public:
    vector<int> countSmaller(vector<int>& nums) {
        int n = nums.size();
        vector<int> v(n);
        for (int i = n - 1; i >= 0; --i) {
            int val = nums[i];
            int L = i + 1, R = n - 1;
            while (L <= R) {
                int M = L + (R - L) / 2;
                if (nums[M] >= val) {
                    L = M + 1;
                } else {
                    R = M - 1;
                }
            }
            for (int j = i; j < R; ++j) {
                nums[j] = nums[j + 1];
            }
            nums[R] = val;
            v[i] = n - R - 1;
        }
        return v;
    }
};
// 1320 ms
```

算法思想: 从右端折半插入. 对于每个数字来说, `数组长度 - 插入后的位置 - 1`就是该数字的`count`.

时间复杂度: `O(n^2)`.

空间复杂度: `O(1)`.

<span class="my-comment">还有更快的方法. 以后更新.</span>