---
layout: post
title:  "[OJ] Permutation Index"
date:   2015-12-28 00:00:00 +0800
categories: OJ
---

[LintCode 197. Permutation Index (Easy)](http://www.lintcode.com/problem/permutation-index)

[LintCode 198. Permutation Index II (Medium)](http://www.lintcode.com/en/problem/permutation-index-ii/)

感觉这两道题主要考察计算排列组合的能力.

# Permutation Index

举例:

```
123 -> 1
132 -> 2
213 -> 3
231 -> 4
312 -> 5
321 -> 6
```

以321为例进行分析:

首先考虑第一位3: 3右边比3小的数字有**两个**(1和2), 所以以1和2为首位的数字排在3xx的前面, 这样的数字有`2 * 2! = 4`个. 所以以3开头的数字至少排第5.

3已经考虑完, 看后面两位.

```
12 -> 1
21 -> 2
```

在321中, 2右边比2小的数字有**一个**(1), 所以以1为首位的数字排在2x的前面, 这样的数字有`1 * 1! = 1`个.

最后一个1, 只有一个数字, 排在它前面的数字是0个.

综上, 321前面排了5个数字, 所以它的Permutation Index是6.

按照这个思路, 对于从右数第`i`位`A[i]`(`i = 0, 1, 2...`), 若它的右边有`k`个数字小于`A[i]`, 那么这一位就会在Permutation Index中贡献`k * i!`.

```cpp
class Solution {
public:
    /**
     * @param A an integer array
     * @return a long integer
     */
    long long permutationIndex(vector<int>& A) {
        int N = A.size();
        long long index = 1;
        long long mul = 1;
        for (int i = N - 2; i >= 0; --i) {
            int cnt = 0;
            for (int j = i + 1; j < N; ++j) {
                if (A[j] < A[i]) ++cnt;
            }
            index += cnt * mul;
            mul *= N - i;
        }
        return index;
    }
};
```

时间复杂度: `O(n^2)`

空间复杂度: `O(1)`

# Permutation Index II

做完了Permutation Index我看了下[九章的解答](http://www.jiuzhang.com/solutions/permutation-index/). 我去, 怎么这么复杂. 看了一半看不下去了, 然后发现九章上的Permutation Index II用的一样的解法, 说明九章只是把第二题的解法直接复制到第一题里了.

自己想这题花了好久好久, 顿感高中数学忘得差不多了(T_T).

举例:

```
11223 -> 1
11232 -> 2
11322 -> 3
12123 -> 4
12132 -> 5
12213 -> 6
12231 -> 7
12312 -> 8
12321 -> 9
13122 -> 10
```

考虑13122:

第一位1, 没有比1再小的数字了, 所以1开头的数字是从第一个开始的.

第二位3, 3右边比3小的数字有1和2.

* 如果1和3互换位置, 后面三位将是数字2,2,3. 这三个数字的组合数是`3! / 2! = 3`个.
* 如果2和3互换位置, 后面三位将是数字1,2,3. 这三个数字的组合数是`3! = 6`个.

所以因为第二位比3小(首位是1)而排在13xxx前面的数字有9个. 因此13xxx一定是从第10个开始的.

至此, 结合上一题, 已经能看出规律: 对于从右数第`i`位`A[i]`, 看它右边的每一个比`A[i]`小的数字, 假设`A[j] < A[i] (j > i)`, 那么假想`A[j]`和`A[i]`互换位置后, 计算右边的`i-1`个数字的组合数就是这`A[j]`贡献的. 要注意的是, `A[i]`右边可能有多个比`A[i]`小的重复数字, 这些数字只贡献一次.

```cpp
class Solution {
private:
    map<int, int> m;
    long long fac(int num) {
        long long n = 1;
        while (num > 0) {
            n *= num;
            num--;
        }
        return n;
    }
    
    long generateNum() {
        long long num = 1;
        for (auto it = m.begin(); it != m.end(); ++it) {
            num *= fac(it->second);
        }
        return num;
    }
public:
    /**
     * @param A an integer array
     * @return a long integer
     */
    long long permutationIndexII(vector<int>& A) {
        int N = A.size();
        if (N == 0) return 0;
        m.clear();
        long long index = 1;
        for (int num : A) {
            if (m.find(num) != m.end()) {
                ++m[num];
            } else {
                m[num] = 1;
            }
        }
        for (int i = 0; i < N; ++i) {
            set<int> s;
            for (int j = i + 1; j < N; ++j) {
                if (A[j] < A[i] && s.find(A[j]) == s.end()) {
                    m[A[j]]--;
                    index += fac(N - i - 1) / generateNum();
                    s.insert(A[j]);
                    m[A[j]]++;
                }
            }
            m[A[i]]--;
            if (m[A[i]] == 0) m.erase(A[i]);
        }
        return index;
    }
};
```

时间复杂度: `O(n^2)` (至少. `fac`和`generateNum`的复杂度取决于输入.)

空间复杂度: `O(n)`