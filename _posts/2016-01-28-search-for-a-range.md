---
layout: post
title:  "[OJ] Search for a Range"
date:   2016-01-28 00:00:00 +0800
tags: OJ
---

[LintCode 61. Search for a Range (Medium)](http://www.lintcode.com/en/problem/search-for-a-range/)

[LeetCode 34. Search for a Range (Medium)](https://leetcode.com/problems/search-for-a-range/)

```cpp
class Solution {
private:
    int findBound(vector<int> &A, int target, bool findLowerBound) {
        int L = 0, R = A.size() - 1;
        while (L <= R) {
            int M = L + (R - L) / 2;
            if (A[M] < target) {
                L = M + 1;
            } else if (A[M] == target) {
                if (findLowerBound) {
                    R = M - 1;
                } else {
                    L = M + 1;
                }
            } else {
                R = M - 1;
            }
        }
        int res = findLowerBound ? L : R;
        return res >= 0 && res < A.size() && A[res] == target ? res : -1;
    }
public:
    vector<int> searchRange(vector<int> &A, int target) {
        vector<int> v;
        v.push_back(findBound(A, target, true));
        v.push_back(findBound(A, target, false));
        return v;
    }
};
```

时间复杂度: `O(logn)`

空间复杂度: `O(1)`