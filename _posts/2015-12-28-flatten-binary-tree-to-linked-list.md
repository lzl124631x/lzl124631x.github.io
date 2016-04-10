---
layout: post
title:  "[OJ] Flatten Binary Tree to Linked List"
date:   2015-12-28 00:00:00 +0800
tags: OJ
---

[114. Flatten Binary Tree to Linked List (Medium)](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)

[453. Flatten Binary Tree to Linked List (Easy)](http://www.lintcode.com/en/problem/flatten-binary-tree-to-linked-list/)

# 解法1: 用stack.

```cpp
class Solution {
public:
    void flatten(TreeNode *root) {
    	if(!root) return;
    	TreeNode *pnode = NULL;
    	stack<TreeNode*> s;
    	s.push(root);
    	while(!s.empty()){
    		root = s.top(); s.pop();
    		if(pnode){
    			pnode->left = NULL;
    			pnode->right = root;
    		}
    		if(root->right) s.push(root->right);
    		if(root->left) s.push(root->left);
    		pnode = root;
    	}
    	pnode->left = pnode->right = NULL;
    }
};
```

# 解法2: 递归

```cpp
class Solution {
private:
    void _flatten(TreeNode *root, TreeNode **ph, TreeNode **pt) {
        if (!root) {
            *ph = *pt = NULL;
            return;
        }
        TreeNode *h1, *t1, *h2, *t2;
        _flatten(root->left, &h1, &t1);
        _flatten(root->right, &h2, &t2);
        root->left = NULL;
        *ph = *pt = root;
        if (h1) {
            root->right = h1;
            *pt = t1;
            if (h2) {
                t1->right = h2;
                *pt = t2;
            }
        } else if (h2) {
            root->right = h2;
            *pt = t2;
        }
    }
public:
    void flatten(TreeNode *root) {
        TreeNode *h, *t;
        _flatten(root, &h, &t);
    }
};
```

`ph`其实一定指向`root`, 所以可以省略. 简化为一下代码:

```cpp
class Solution {
private:
    void _flatten(TreeNode *root, TreeNode **ptail) {
        if (!root) {
            *ptail = NULL;
            return;
        }
        TreeNode *t1, *t2;
        _flatten(root->left, &t1);
        _flatten(root->right, &t2);
        if (t1) {
            t1->right = root->right;
            root->right = root->left;
            root->left = NULL;
        }
        *ptail = t2 ? t2 : (t1 ? t1 : root);
    }
public:
    void flatten(TreeNode *root) {
        TreeNode *t;
        _flatten(root, &t);
    }
};
```

# 解法3: `@Netario36`

假设`flatten`函数已经可以以**前序遍历**完成flatten的任务, 且`flatten(root->left)`和`flatten(root->right)`已完成, 那么接下来要做的操作就是:

记`flatten(root->left)`后该链表的最后一个节点是`tail`.

那么

```cpp
tail->right = root->right;
root->right = tail;
root->left = NULL;
```

就可以把`root`节点, `root->left`的flatten子树, 和`root->right`的flatten子树串起来.

这代码看起来简洁, 但是把左子树处理完并插入到`root`和`root->right`之间后, 要处理插入前的`root->right`子树需要递归`N`次, 其中`N`是`root->left`子树节点数. 也就是说, 有很多不必要的递归.

```cpp
//@Netario36
class Solution {
private:
    TreeNode *tail;
public:
    void flatten(TreeNode *root) {
        if (!root) return;
        if (root->left) {
            flatten(root->left);
            tail->right = root->right;
            root->right = root->left;
            root->left = NULL;
        }
        tail = root;
        flatten(root->right);
    }
};
```

基于`@Netario36`的版本我写了一个前序遍历结构更明显, 没有无用的递归的版本.

```cpp
class Solution {
private:
    TreeNode *tail;
public:
    void flatten(TreeNode *root) {
        if (!root) return;
        tail = root;
        if (root->left) {
            flatten(root->left);
            tail->right = root->right;
            root->right = root->left;
            root->left = NULL;
        }
        flatten(tail->right);
    }
};
```

# 解法4: `@versavitality`

参数`rightRoot`是`root`的右兄弟节点, 返回值为`root`子树与`rightRoot`子树flatten之后的根节点.

`TreeNode *right = _flatten(root->right, rightRoot);`将`root->right`子树与`rightRoot`子树flatten到一起.

`root->right = _flatten(root->left, right);`将`root->left`子树与上一步的结果flatten到一起.

```cpp
// @versavitality
class Solution {
private:
    TreeNode* _flatten(TreeNode *root, TreeNode *rightRoot) {
        if (!root) return rightRoot;
        TreeNode *right = _flatten(root->right, rightRoot);
        root->right = _flatten(root->left, right);
        root->left = NULL;
        return root;
    }
public:
    void flatten(TreeNode *root) {
        _flatten(root, NULL);
    }
};
```

蛋疼的博客园, 有`@`字符会翻译成mailto链接...又不知道怎么取消, 只能用\`\`包起来了.