---
layout: post
title:  "[OJ] Additive Number"
date:   2015-12-20 00:00:00 +0800
tags: OJ
---

[306. Additive Number](https://leetcode.com/problems/additive-number/)

```cpp
class Solution {
private:
    string stringAddition(string &a, string &b) {
        int l1 = a.size(), l2 = b.size();
        int len = max(l1, l2) + 2;
        char str[len];
        auto i1 = a.crbegin(), i2 = b.crbegin();
        int i = 0, carry = 0;
        bool b1 = (i1 != a.crend()), b2 = (i2 != b.crend());
        while (b1 || b2) {
            int da = 0, db = 0;
            if (b1) da = *(i1++) - '0';
            if (b2) db = *(i2++) - '0';
            int d = da + db + carry;
            carry = d / 10;
            d %= 10;
            str[i++] = '0' + d;
            b1 = (i1 != a.crend()), b2 = (i2 != b.crend());
        }
        if (carry) {
            str[i++] = '0' + carry;
        }
        str[i] = '\0';
        for (int j = 0, k = i - 1; j < k; ++j, --k) {
            swap(str[j], str[k]);
        }
        return string(str);
    }
public:
    bool isAdditiveNumber(string num) {
        int n = num.size();
        for (int l1 = 1; l1 <= n; ++l1) {
            for (int l2 = 1; l2 <= n; ++l2) {
                int end = l1 + l2;
                if (end >= n) break;
                string a = num.substr(0, l1);
                string b = num.substr(l1, l2);
                while (end != n) {
                    string c = stringAddition(a, b);
                    if (num.substr(end, c.size()) != c) break;
                    a = b;
                    b = c;
                    end += c.size();
                }
                if (end == n) {
                    return true;
                }
            }
        }
        return false;
    }
};
// 0ms
```

算法思路: 字符串加法 + 回溯

时间复杂度: `O(n^3)`

空间复杂度: `O(n)`