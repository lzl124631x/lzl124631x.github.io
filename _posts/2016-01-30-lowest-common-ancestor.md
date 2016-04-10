---
layout: post
title:  "[OJ] Lowest Common Ancestor"
date:   2016-01-30 00:00:00 +0800
tags: OJ
---

[LintCode 88. Lowest Common Ancestor (Medium)](http://www.lintcode.com/en/problem/lowest-common-ancestor/)

[LeetCode 236. Lowest Common Ancestor of a Binary Tree (Medium)](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

今天写了三种解法, 都比较长.

# 解法1 前序递归DFS, 计数

这个解法的判断比较啰嗦, 但好处就是, 能够根据当前情况选择是否继续向下遍历, 不做无用的搜索. 越早地找到两个节点, 程序就会越早结束.

```cpp
class Solution {
private:
    TreeNode *lca;
    TreeNode *tA, *tB;
    int findLCA(TreeNode *root) {
        if (!root) return 0;
        int cnt = 0;
        if (root->val == tA->val) ++cnt;
        if (root->val == tB->val) ++cnt;
        if (cnt == 2) {
            lca = root;
            return 2;
        }
        int leftCnt = findLCA(root->left);
        if (leftCnt == 2) return 2;
        cnt += leftCnt;
        if (cnt == 2) {
            lca = root;
        } else {
            int rightCnt = findLCA(root->right);
            if (rightCnt == 2) return 2;
            cnt += rightCnt;
            if (cnt == 2) {
                lca = root;
            }
        }
        return cnt;
    }
public:
    TreeNode *lowestCommonAncestor(TreeNode *root, TreeNode *A, TreeNode *B) {
        if (!root || !A || !B) return NULL;
        lca = NULL;
        tA = A, tB = B;
        findLCA(root);
        return lca;
    }
};
```

时间复杂度: `O(n)`

空间复杂度: `O(logn)` (考虑到递归的堆栈消耗)

# 解法2 后序非递归DFS

写了递归的, 自然就想写个非递归的.

思路是:

1. 在碰到第一个节点的时候让`LCA_PTR`指向当前节点, 真正的LCA一定是`*LCA_PTR`本身或者上游节点.
2. 每次**路过**`*LCA_PTR`的父节点的时候, `LCA_PTR`上移指向父节点. (前序和中序非递归遍历中, **路过**就是访问; 后序非递归遍历中, **路过**不一定是访问)
3. 当碰到第二个节点的时候, `LCA_PTR`停留的地方就是真正的LCA.

但是写着写着才注意到, 步骤2决定了必须要用后序遍历才行. 因为前序和中序遍历中, 找到目标节点时, 父节点的遍历可能已经结束了.

要注意的是步骤2中的**路过**, 即
```
if (lca && root->left == lca || root->right == lca) {
    lca = root;
}
```

应该紧随`root = s.top()`, 而不是放在"访问当前节点"的地方.

```
class Solution {
public:
    TreeNode *lowestCommonAncestor(TreeNode *root, TreeNode *A, TreeNode *B) {
        if (!root || !A || !B) return NULL;
        TreeNode *lca = NULL;
        stack<TreeNode*> s;
        TreeNode *prev = NULL;
        int cnt = 0;
        while (root || !s.empty()) {
            while (root) {
                s.push(root);
                root = root->left;
            }
            root = s.top();
            if (lca && root->left == lca || root->right == lca) {
                lca = root;
            }
            if (!root->right || root->right == prev) {
                s.pop();
                if (root->val == A->val) {
                    ++cnt;
                    if (cnt == 1) lca = root;
                }
                if (root->val == B->val) {
                    ++cnt;
                    if (cnt == 1) lca = root;
                }
                if (cnt == 2) return lca;
                prev = root;
                root = NULL;
            } else {
                root = root->right;
            }
        }
        return NULL;
    }
};
```

时间复杂度: `O(n)`

空间复杂度: `O(n)`

# 解法3 单链表的交点

当每个节点有指向父节点的指针时可以用这种方法. 题中的`TreeNode`结构没有`parent`指针, 用`map`构造就好了.

```cpp
class Solution {
private:
    map<TreeNode*, TreeNode*> parents;
    
    void buildParents(TreeNode *root) {
        if (root->left) {
            parents[root->left] = root;
            buildParents(root->left);
        }
        if (root->right) {
            parents[root->right] = root;
            buildParents(root->right);
        }
    }
    
    int depth(TreeNode *node) {
        int d = 0;
        while (node) {
            node = parents[node];
            ++d;
        }
        return d;
    }
public:
    TreeNode *lowestCommonAncestor(TreeNode *root, TreeNode *A, TreeNode *B) {
        if (!root || !A || !B) return NULL;
        parents.clear();
        parents[root] = NULL;
        buildParents(root);

        int da = depth(A), db = depth(B);
        if (da < db) {
            swap(da, db);
            swap(A, B);
        }
        while (da > db) {
            A = parents[A];
            da--;
        }
        while (da && A != B) {
            A = parents[A];
            B = parents[B];
            da--;
        }
        return A;
    }
};
```

时间复杂度: `O(n)`

空间复杂度: `O(n)`

# 解法4 双堆栈

回顾了一下LeetCode上半年前写的代码, 发现当时思路还蛮清晰. 思路类似解法2, 但是多用一个堆栈表示从`root`到LCA的路径, 好处就是代码更清晰一些, 而且中序遍历即可.

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        stack<TreeNode*> s;
        stack<TreeNode*> path;
        bool foundFirst = false;
        TreeNode *lca = NULL;
        while (root || !s.empty()) {
            while (root) {
                if (!foundFirst) {
                    path.push(root);
                }
                s.push(root);
                root = root->left;
            }
            root = s.top();
            if (!path.empty() && root == path.top()) {
                lca = root;
                path.pop();
            }
            s.pop();
            if (root == p || root == q) {
                if (foundFirst) {
                    return lca;
                }
                foundFirst = true;
            }
            root = root->right;
        }
        return NULL;
    }
};
```

时间复杂度: `O(n)`

空间复杂度: `O(n)`

# 解法5 前序递归DFS, 指针

这代码应该是当初看了LeetCode Discuss中的解答写出来的. 相对于解法1, 代码很简洁, 用指针的回传表达是否找到目标节点. 要说缺陷, 就是扫描的点比解法1多一些.

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || root == p || root == q) return root;
        TreeNode *left = lowestCommonAncestor(root->left, p, q), *right = lowestCommonAncestor(root->right, p, q);
        return left && right ? root : (left ? left : right);
    }
};
```

时间复杂度: `O(n)`

空间复杂度: `O(logn)`