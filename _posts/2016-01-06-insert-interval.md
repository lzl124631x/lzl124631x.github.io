---
layout: post
title:  "[OJ] Insert Interval"
date:   2016-01-06 00:00:00 +0800
tags: OJ
---


[LintCode #30. Insert Interval (Easy)](http://www.lintcode.com/en/problem/insert-interval/)

[LeetCode #57. Insert Interval (Hard)](https://leetcode.com/problems/insert-interval/)

```cpp
class Solution {
public:
    vector<Interval> insert(vector<Interval> &intervals, Interval newInterval) {
        vector<Interval> v;
        auto it = intervals.begin();
        while (it != intervals.end() && it->end < newInterval.start) {
            v.push_back(*it);
            ++it;
        }
        auto left = it;
        while (it != intervals.end() && it->start <= newInterval.end) ++it;
        auto right = it;
        if (right - left != 0) {
            v.push_back(Interval(min(left->start, newInterval.start), max((right - 1)->end, newInterval.end)));
        } else {
            v.push_back(newInterval);
        }
        while (it != intervals.end()) {
            v.push_back(*it);
            ++it;
        }
        return v;
    }
};
```

思路:

* 第一个`while`循环跳过所有不会与`newInterval`相交且小于`newInterval`的区间.
* 中间一段计算相交区间.
* 最后一个`while`循环跳过所有不会与`newInterval`相交且大于`newInterval`的区间.

时间复杂度: `O(n)`

空间复杂度: `O(n)`

---

参照[九章算法的JAVA解法](http://www.jiuzhang.com/solutions/insert-interval/)改写的C++版.

```cpp
class Solution {
public:
    vector<Interval> insert(vector<Interval> &intervals, Interval newInterval) {
        vector<Interval> v;
        int pos = 0;
        for (auto interval : intervals) {
            if (interval.end < newInterval.start) {
                v.push_back(interval);
                ++pos;
            } else if (interval.start > newInterval.end) {
                v.push_back(interval);
            } else {
                newInterval.start = min(newInterval.start, interval.start);
                newInterval.end = max(newInterval.end, interval.end);
            }
        }
        v.insert(v.begin() + pos, newInterval);
        return v;
    }
};
```

这算法简洁在于: 一个循环, 通过两个`if`滤掉所有不相交的区间; (若有)剩下的区间一定与`newInterval`相交, 将这些区间合并到`newInterval`里, 最后插入即可.

唯一欠缺的地方在于插入操作会有额外的时间开销.