---
title: leetcode-283-Move-Zeroes
date: 2019-06-16 16:46:49
tags:
---

Given an array nums, write a function to move all 0's to the end of it while maintaining the relative order of the non-zero elements.

<!--more-->

Example:

```
Input: [0,1,0,3,12]
Output: [1,3,12,0,0]
```

Note:

You must do this in-place without making a copy of the array.
Minimize the total number of operations.

我的解决思路如下：
便利向量nums， 统计有多少0值，将0值数删除，最后补0

```C++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int count = 0;

        for(int i = 0;i < nums.size(); i++)
        {
            if(nums[i] == 0)
            {
                nums.erase(nums.begin()+i);
                i--;
                count++;
            }
        }

        for(int i  = 0; i < count; i++)
        {
            nums.push_back(0);
        }
    }
};
```
