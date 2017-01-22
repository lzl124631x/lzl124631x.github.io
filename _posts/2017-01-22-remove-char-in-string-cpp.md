---
layout: post
title:  "C++从string中删除所有的某个特定字符"
date:   2017-01-22 21:00:00 +0800
tags: C++
---

C++中要从`string`中删除所有某个特定字符, 可用如下代码

```cpp
str.erase(std::remove(str.begin(), str.end(), 'a'), str.end());
```

其中, `remove`来自`<algorithm>`, 它的签名是

```cpp
template <class ForwardIterator, class T>
  ForwardIterator remove (ForwardIterator first, ForwardIterator last, const T& val);
```

作用: 在容器中, 删除`[first, last)`之间的所有值等于`val`的值.

删除方法: 将某个等于`val`的值用下一个不等于`val`的值覆盖.

返回值: 一个迭代器 (记作`newEnd`), 该迭代器指向最后一个未被删除元素的下一个元素, 即相当于容器新的`end`.

所以, 运行完`remove`后, 容器的`[first, newEnd)`内的元素即为所有未被删除的元素, `[newEnd, end)`之间的元素已经没用了.

这样, 再运行`str.erase(newEnd, str.end())`即可清空`[newEnd, end)`之间的元素.

---

[`std::remove_if`](http://www.cplusplus.com/reference/algorithm/remove_if/)与`remove`类似, 只是它接受的第三个参数是个函数.

```cpp
// remove_if example
#include <iostream>     // std::cout
#include <algorithm>    // std::remove_if

bool IsOdd (int i) { return ((i%2)==1); }

int main () {
  int myints[] = {1,2,3,4,5,6,7,8,9};            // 1 2 3 4 5 6 7 8 9

  // bounds of range:
  int* pbegin = myints;                          // ^
  int* pend = myints+sizeof(myints)/sizeof(int); // ^                 ^

  pend = std::remove_if (pbegin, pend, IsOdd);   // 2 4 6 8 ? ? ? ? ?
                                                 // ^       ^
  std::cout << "the range contains:";
  for (int* p=pbegin; p!=pend; ++p)
    std::cout << ' ' << *p;
  std::cout << '\n';

  return 0;
}
```

Output:

```cpp
myvector contains: 10 30 30 10 10 0 0 0
```

还有个[`std::remove_copy`](http://www.cplusplus.com/reference/algorithm/remove_copy/), 签名:

```cpp
template <class InputIterator, class OutputIterator, class T>
  OutputIterator remove_copy (InputIterator first, InputIterator last,
                              OutputIterator result, const T& val);
```

它会把`[first, last)`之间不等于`val`的元素都向从`result`开始的容器拷贝.


```cpp
// remove_copy example
#include <iostream>     // std::cout
#include <algorithm>    // std::remove_copy
#include <vector>       // std::vector

int main () {
  int myints[] = {10,20,30,30,20,10,10,20};               // 10 20 30 30 20 10 10 20
  std::vector<int> myvector (8);

  std::remove_copy (myints,myints+8,myvector.begin(),20); // 10 30 30 10 10 0 0 0

  std::cout << "myvector contains:";
  for (std::vector<int>::iterator it=myvector.begin(); it!=myvector.end(); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}
```

Output:

```cpp
myvector contains: 10 30 30 10 10 0 0 0
```
