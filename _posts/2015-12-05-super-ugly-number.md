---
layout: post
title:  "[OJ] Super Ugly Number"
date:   2015-12-05 00:00:00 +0800
tags: OJ
---

[Super Ugly Number](https://leetcode.com/problems/super-ugly-number/)

最后WA没做出来.

```cpp
typedef long long int64;
#define MAXBOUND 10000000
class Solution {
public:
    int nthSuperUglyNumber(int n, vector<int>& primes) {
        bitset<MAXBOUND> bs;
        int64 bound = MAXBOUND;
        vector<int> v;
        bs.set(1);
        for (int i = 0; i < primes.size(); ++i) {
            bs.set(primes[i]);
        }
        for (int64 i = 1; i < bound; ++i) {
            if (!bs[i]) continue;
            --n;
            bs.set(i);
            v.push_back(i);
            if (n <= 0) return i;
            for (int j = 0; j < v.size(); ++j) {
                int64 val = v[j];
                if (val >= bound / i) break;
                bs.set(i * val);
            }
        }
        return -1;
    }
};
```

最后一个case过不了:

> Input: 100000
[7,19,29,37,41,47,53,59,61,79,83,89,101,103,109,127,131,137,139,157,167,179,181,199,211,229,233,239,241,251]
Output: -1
Expected: 1092889481

bitset没法开到`10^9`这么大.

---

```cpp
// @zjh08177
int nthSuperUglyNumber(int n, vector<int>& primes) {
        vector<int> index(primes.size(), 0), ugly(n, INT_MAX);
        ugly[0]=1;
        for(int i=1; i<n; i++){
            for(int j=0; j<primes.size(); j++) ugly[i]=min(ugly[i],ugly[index[j]]*primes[j]);
            for(int j=0; j<primes.size(); j++) index[j]+=(ugly[i]==ugly[index[j]]*primes[j]);
        }
        return ugly[n-1];
}
```

思路:

假设`primes=[2,3]`且已经计算出了前`n`个SUN, 即`SUN[0]`到`SUN[n-1]`, 那么`SUN[n]`是多少?

在`0`到`n-1`中找到一个最小的`a`使得`SUN[a]*2>SUN[n-1]`. 同样地在`0`到`n-1`中找到一个最小的`b`使得`SUN[b]*3>SUN[n-1]`. 下一个SUN一定是`SUN[a]*2`和`SUN[b]*3`中较小的那个. 假设`SUN[a]*2`较小, 那么`SUN[n]`就是`SUN[a]*2`.

再想一步, `SUN[n+1]`是多少?

一定是`SUN[a+1]*2`和`SUN[b]*3`中较小的那一个.

至此可以看出规律, 创建长度为`n`的数组`ugly`记录前`n`个SUN. 创建长度为`k`的index数组, 其中记录着意义如上面`a`和`b`的index值.
初始时, `ugly[0]=1, ugly[其他]=INT_MAX`. index中元素都为`0`. 接下来从`ugly[1]`更新到`ugly[n-1]`, 更新`ugly[i] (i=1~n-1)`时:

找到`ugly[index[j]] * primes[j] (j=0~k-1)`中最小的那个(对应的下标`j`记做`jmin`), 写入到`ugly[i]`, 然后`index[jmin]++`.

最后, `ugly[n-1]`就是所求.

时间复杂度`O(nk)`.

空间复杂度`O(n+k)`.