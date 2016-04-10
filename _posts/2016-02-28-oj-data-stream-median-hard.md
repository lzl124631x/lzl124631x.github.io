---
layout: post
title:  "[OJ] Data Stream Median (Hard)"
date:   2016-02-28 15:02:28 +0800
tags: OJ
---

[LintCode 81. Data Stream Median (Hard)](http://www.lintcode.com/en/problem/data-stream-median/)

思路:
1. 用一个大根堆保存较小的一半数, 一个小根堆保存较大的一半数.  
2. 每次根据`num`和两个堆顶的数据决定往哪个堆里面放.  
3. 放完后进行平衡确保两个堆的`size`差不超过1.  
4. 利用两个堆的`size`和堆顶值计算`median`.

大根堆可以表示为`priority_queue<int, vector<int>, less<int>>`, 其实`priority_queue<int>`默认就是大根堆.  
小根堆可以表示为`priority_queue<int, vector<int>, greater<int>>`.

```cpp
class Solution {
public:
    vector<int> medianII(vector<int> &nums) {
        priority_queue<int, vector<int>, less<int>> sm;
        priority_queue<int, vector<int>, greater<int>> gt;
        vector<int> v;
        for (int n : nums) {
            if (gt.empty() || n > gt.top()) {
                gt.push(n);
            } else {
                sm.push(n);
            }
            if (sm.size() > gt.size() + 1) {
                int val = sm.top();
                sm.pop();
                gt.push(val);
            }
            if (gt.size() > sm.size() + 1) {
                int val = gt.top();
                gt.pop();
                sm.push(val);
            }
            if (gt.size() > sm.size()) {
                v.push_back(gt.top());
            } else {
                v.push_back(sm.top());
            }
        }
        return v;
    }
};
```

[LeetCode 295. Find Median from Data Stream (Hard)](https://leetcode.com/problems/find-median-from-data-stream/)

```cpp
class MedianFinder {
private:
    priority_queue<int, vector<int>, less<int>> sm;
    priority_queue<int, vector<int>, greater<int>> gt;
public:
    // Adds a number into the data structure.
    void addNum(int num) {
        if (gt.empty() || num > gt.top()) {
            gt.push(num);
        } else {
            sm.push(num);
        }
    }

    // Returns the median of current data stream
    double findMedian() {
        while (sm.size() > gt.size() + 1) {
            int val = sm.top();
            sm.pop();
            gt.push(val);
        }
        while (gt.size() > sm.size() + 1) {
            int val = gt.top();
            gt.pop();
            sm.push(val);
        }
        if (gt.size() > sm.size()) {
            return gt.top();
        } else if (sm.size() > gt.size()) {
            return sm.top();
        } else {
            return (gt.top() + sm.top()) / 2.0;
        }
    }
};
```

时间复杂度: `O(nlogn)`  
空间复杂度: `O(n)`