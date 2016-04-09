---
layout: post
title:  "[LeetCode] Range Sum Query - Immutable"
date:   2015-12-20 00:00:00 +0800
categories: OJ
---

[303. Range Sum Query - Immutable](https://leetcode.com/problems/range-sum-query-immutable/)

```cpp
class NumArray {
private:
    vector<int> v;
public:
    NumArray(vector<int> &nums) {
        int n = nums.size();
        v = vector<int>(n + 1);
        int sum = 0;
        for (int i = 1; i <= n; ++i) {
            sum += nums[i - 1];
            v[i] = sum;
        }
    }

    int sumRange(int i, int j) {
        return v[j + 1] - v[i];
    }
};
//596 ms
```

算法思路: `sumRange(i, j) = sumRange(0, j) - sumRange(0, i - 1)` (记`sumRange(0, -1)=0`).

时间复杂度: `O(n)`.

空间复杂度: `O(n)`.

---

[C++ O(1) queries - just 2 extra lines of code](https://leetcode.com/discuss/70991/c-o-1-queries-just-2-extra-lines-of-code)

```cpp
//@rantos22
class NumArray {
public:
    NumArray(vector<int> &nums) : psum(nums.size()+1, 0) {
        partial_sum( nums.begin(), nums.end(), psum.begin()+1);
    }

    int sumRange(int i, int j) {
        return psum[j+1] - psum[i];
    }
private:
    vector<int> psum;
};
```

使用了[`partial_sum`函数](http://www.cplusplus.com/reference/numeric/partial_sum/?kw=partial_sum).