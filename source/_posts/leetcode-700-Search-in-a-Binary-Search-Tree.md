---
title: leetcode-700-Search-in-a-Binary-Search-Tree
date: 2019-06-13 07:45:41
tags:
  - leetcode
  - C++
---
Given the root node of a binary search tree (BST) and a value. You need to find the node in the BST that the node's value equals the given value. Return the subtree rooted with that node. If such node doesn't exist, you should return NULL.

For example,

<!--more-->

Given the tree:
```
        4
       / \
      2   7
     / \
    1   3
```
And the value to search: 2
You should return this subtree:
```
      2     
     / \   
    1   3
```
In the example above, if we want to search the value 5, since there is no node with value 5, we should return NULL.

Note that an empty tree is represented by NULL, therefore you would see the expected output (serialized tree format) as [], not null.

递归搜素即可
```
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
    TreeNode* searchBST(TreeNode* root, int val) {
        if(root == NULL) return root;

        if(root->val == val) return root;

        TreeNode* result_l = searchBST(root->left,val);
        TreeNode* result_r = searchBST(root->right,val);

        if(result_l == NULL && result_r == NULL)
        {
            return NULL;
        }
        else if(result_l != NULL && result_r == NULL)
        {
            return result_l;
        }
        else
        {
            return result_r;
        }


    }
};
```
