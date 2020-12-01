---
title: Leetcode二叉树问题
categories: 编程相关
tags:
- 编程相关

---

### 1.1 [二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

#### 1.1.1 题目描述

给定一个二叉树，返回它的 *前序* 遍历。

实例 1：

```bash
输入: [1,null,2,3]  
   1
    \
     2
    /
   3 

输出: [1,2,3]
```

#### 1.1.2 思路解析

􏰅􏴝􏳝􏱭 􏰅􏴝􏳝􏱭用栈或者􏰅􏴝􏳝􏱭 Morris 􏴡􏴢􏰥遍历。

#### 1.1.3 代码

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> results;
        stack<const TreeNode*> s;
        // 边界条件判断
        if(root != nullptr) {
            s.push(root);
        }
      	// root->right->left
        while(!s.empty()) {
            const TreeNode* p = s.top();
            s.pop();
            results.push_back(p->val);
            if (p->right != nullptr) {
                s.push(p->right);
            }
            if (p->left != nullptr) {
                s.push(p->left);
            }
        }
        return results;
    }
};
```

### 1.2[二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

#### 1.2.1 题目描述

给定一个二叉树，返回它的 *中序* 遍历。

实例 1：

```bash
输入: [1,null,2,3]
   1
    \
     2
    /
   3

输出: [1,3,2]
```

#### 1.1.2 思路解析

􏰅􏴝􏳝􏱭 􏰅􏴝􏳝􏱭用栈或者􏰅􏴝􏳝􏱭 Morris 􏴡􏴢􏰥遍历。

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        stack<const TreeNode *> s;
        vector<int> result;
        const TreeNode* p = root;
        while(!s.empty() || p != nullptr) {
            if (p != nullptr) {
                s.push(p);
                p = p->left;
            }
            else {
                p = s.top();
                result.push_back(p->val);
                s.pop();
                p = p->right;
            }
        }
        return result;
    }
};
```

### 1.3[ 二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

#### 1.3.1 题目描述

给定一个二叉树，返回它的 *后序* 遍历。

实例 1：

```bash
输入: [1,null,2,3]  
   1
    \
     2
    /
   3 

输出: [3,2,1]
```

#### 1.3.2 思路解析

􏰅􏴝􏳝􏱭 􏰅􏴝􏳝􏱭用栈或者􏰅􏴝􏳝􏱭 Morris 􏴡􏴢􏰥遍历。

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        stack<const TreeNode*> s;
        vector<int> result;
        const TreeNode* p = root;
        const TreeNode* q = nullptr;
        do{
            while(p != nullptr) {
                s.push(p);
                p = p->left;
            }
            q = nullptr;
            while(!s.empty()){
                p = s.top();
                s.pop();
              	// 如果右子树被访问过，载入根节点
                if (p->right == q) {
                    result.push_back(p->val);
                    q = p;
                }
              	// 否则迭代右子树
                else {
                    s.push(p);
                    p = p->right;
                    break;
                }
            }
        }while(!s.empty());
        return result;
    }
};
```

## 1.4 [二叉树的层次遍历 II](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

#### 1.4.1 题目描述

给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

例如：
给定二叉树 `[3,9,20,null,null,15,7]`

```python
    3
   / \
  9  20
    /  \
   15   7
```

返回其自底向上的层次遍历为：

```python
[
  [15,7],
  [9,20],
  [3]
]
```

#### 1.4.2 题目分析

#### 1.4.3 代码

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> result;
        traverse(root, 1, result);
        std::reverse(result.begin(), result.end());
        return result;
    }

    void traverse(TreeNode* root, size_t level, vector<vector<int>>& result) {
        if (!root) {
            return;
        }
        if (result.size() < level) {
            result.push_back(vector<int> ());
        }
        result[level-1].push_back(root->val);
        traverse(root->left, level+1, result);
        traverse(root->right, level+1, result);
    }
};
```

