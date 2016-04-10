---
layout: post
title:  "[OJ] Wildcard Matching (Hard)"
date:   2016-02-24 00:00:00 +0800
tags: OJ
---

[LintCode 192. Wildcard Matching (Hard)](http://www.lintcode.com/en/problem/wildcard-matching/#)  
[LeetCode 44. Wildcard Matching (Hard)](https://leetcode.com/problems/wildcard-matching/)

第二次刷还是被这题虐. 其实就是跪在一个地方, 就是关于`mustFail`的地方.  
当`*p && !*s`的时候, 说明`s`已经被用完了, `p`还没有被穷尽, 这种情况下要直接退出所有的递归返回`false`, 因为`s`都匹配不完`p`, 那么`s+1, s+2...`等字符串更不可能匹配`p`.

```cpp
class Solution {
private:
    bool mustFail = false;
public:
    bool isMatch(const char *s, const char *p) {
        if (!s || !p) return false;
        while (*s && *p && *s == *p || *p == '?') {
            ++s;
            ++p;
        }
        if (*p == '*') {
            while (*p == '*') ++p;
            if (!*p) return true;
            do {
                while (*s && !(*s == *p || *p == '?')) ++s;
                if (isMatch(s, p)) return true;
                if (mustFail) return false;
                ++s;
            } while (*s);
            return false;
        }
        if (*p && !*s) {
            mustFail = true;
        }
        return !*s && !*p;
    }
};
```

时间复杂度: `O(mn)`  
空间复杂度: `O(1)`(不考虑递归堆栈开销).