---
layout: post
title:  "[OJ] Burst Balloons"
date:   2015-12-04 00:00:00 +0800
categories: OJ
---

[Burst Balloons (Medium)](https://leetcode.com/problems/burst-balloons/)

这题没有做出来. 自己的思路停留在暴力的解法, 时间复杂度很高:

1. 初始化`maxCount = 0`.
1. 对于当前长度为`k`的数组`nums`, 从`0`到`k - 1`逐个选取第`i`个气球扎破
    1. 计算扎破气球得到的金币数, `count = nums[i - 1] * nums[i] * nums[i + 1]`.
    2. 从`nums`中删掉`nums[i]`.
    3. 查询`m[nums]`是否存在, 不存在则递归调用`maxCoins(nums)`并插入`m[nums]`.
    4. `count += m[nums]`后就是本轮的最优解, 若`count > maxCount`则更新`maxCount`.
    5. 恢复`nums[i]`回到`nums`中.
1. 返回`maxCount`.

```cpp
class Solution {
private:
    map<vector<int>, int> m;
public:
    int maxCoins(vector<int>& nums) {
        int len = nums.size();
        if (len <= 0) return 0;
        if (len == 1) return nums[0];
        int maxCount = 0;
        for (int i = 0; i < len; ++i) {
            int val = nums[i];
            int count = nums[i];
            if (i - 1 >= 0) {
                count *= nums[i - 1];
            }
            if (i + 1 < len) {
                count *= nums[i + 1];
            }
            nums.erase(nums.begin() + i);
            auto it = m.find(nums);
            if (it == m.end()) {
                count += (m[nums] = maxCoins(nums));
            } else {
                count += it->second;
            }
            maxCount = max(maxCount, count);
            nums.insert(nums.begin() + i, val);
        }
        return maxCount;
    }
};
```

这个算法如果不加memo, 在第`i`次选择时有`n - i`个选择 `(i = 0 ~ n-1)`, 所以一共有`n!`次选择, 每次选择都要进行`O(n)`时间复杂度的删气球和回填气球的操作<span class="todo">查找`vector`的时间复杂度是多少?</span>, 故时间复杂度`O(n*n!)`, `n`层递归故空间复杂度`O(n)`.

如上加了memo之后, 对于每种输入情况只计算一次, 对于`k`个气球, 有`C(n, k)`中情况 `(k = 1~n)`, 一共是`2^n`种情况, 同上要考虑增删气球的时间复杂度, 所以时间复杂度最好也是`O(n*2^n)`. 这`2^n`种情况都要保存在memo中, 每种输入的平均长度是`O(n)`级别的, 因此空间复杂度是`O(n*2^n)`.

总之, TLE.

---

看了[Share some analysis and explanations](https://leetcode.com/discuss/72216/share-some-analysis-and-explanations)之后写下了下面的算法.

```cpp
class Solution {
private:
    map<pair<int, int>, int> m;
    int maxCoins(vector<int> &nums, int left, int right, int lv, int rv) {
        if (left > right) return 0;
        if (left == right) return nums[left] * lv * rv;
        auto coor = make_pair(left, right);
        auto it = m.find(coor);
        if (it != m.end()) {
            return it->second;
        }
        int maxCount = 0;
        for (int i = left; i <= right; ++i) {
            int count = nums[i] * lv * rv
            + maxCoins(nums, left, i - 1, lv, nums[i])
            + maxCoins(nums, i + 1, right, nums[i], rv);
            maxCount = max(maxCount, count);
        }
        m[coor] = maxCount;
        return maxCount;
    }
public:
    int maxCoins(vector<int>& nums) {
        int len = nums.size();
        if (len <= 0) return 0;
        return maxCoins(nums, 0, len - 1, 1, 1);
    }
};
// Runtime: 1428ms
```

时间复杂度`O(n^3)`. 起止点共有`C(n, 2)`个组合, 是`O(n^2)`级别的. 对于每个组合要遍历一遍, 找最大. 所以整体是`O(n^3)`.

空间复杂度`O(n^2)`.

小优化:

1. 删掉0. (Runtime: 1300ms).
2. 改用数组做map (Runtime: 36ms). <span class="todo">没想到数组比map好用这么多, 为什么?</span>

```cpp
class Solution {
private:
    int maxCoins(vector<int> &nums, int left, int right, int lv, int rv, int* memo, int n) {
        if (left > right) return 0;
        if (left == right) return nums[left] * lv * rv;
        if (memo[left * n + right] != 0) return memo[left * n + right];
        int maxCount = 0;
        for (int i = left; i <= right; ++i) {
            int count = nums[i] * lv * rv
            + maxCoins(nums, left, i - 1, lv, nums[i], memo, n)
            + maxCoins(nums, i + 1, right, nums[i], rv, memo, n);
            maxCount = max(maxCount, count);
        }
        memo[left * n + right] = maxCount;
        return maxCount;
    }
public:
    int maxCoins(vector<int>& nums) {
        int len = nums.size();
        if (len <= 0) return 0;
        int n = 0;
        for (int x : nums) if (x > 0) nums[n++] = x;
        int memo[n][n] = {};
        return maxCoins(nums, 0, n - 1, 1, 1, (int*)memo, n);
    }
};
```

---

标准答案:

```
class Solution {
public:
    int maxCoins(vector<int>& iNums) {
        int nums[iNums.size() + 2];
        int n = 1;
        for (int x : iNums) if (x > 0) nums[n++] = x;
        nums[0] = nums[n++] = 1;
        
        int dp[n][n] = {};
        for (int k = 2; k <= n; ++k) {
            for (int L = 0; L <= n - k; ++L) {
                int R = L + k;
                for (int i = L + 1; i < R; ++i) {
                    dp[L][R] = max(dp[L][R], nums[L] * nums[i] * nums[R] + dp[L][i] + dp[i][R]);
                }
            }
        }
        return dp[0][n - 1];
    }
};
// Runtime: 12ms
```

其中`dp[i][j]`表示第`i`个气球到第`j`个气球能获取的最大金币数 (`i`最小为`-1`, `j`最大为`n`).