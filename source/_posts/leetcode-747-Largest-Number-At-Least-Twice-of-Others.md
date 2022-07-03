---
title: leetcode-747-Largest-Number-At-Least-Twice-of-Others
date: 2019-06-16 21:13:54
tags:
  - leetcode
  - C++
---

In a given integer array nums, there is always exactly one largest element.

Find whether the largest element in the array is at least twice as much as every other number in the array.

If it is, return the index of the largest element, otherwise return -1.

Example 1:

```
Input: nums = [3, 6, 1, 0]
Output: 1
Explanation: 6 is the largest integer, and for every other number in the array x,
6 is more than twice as big as x.  The index of value 6 is 1, so we return 1.
```

Example 2:

```
Input: nums = [1, 2, 3, 4]
Output: -1
Explanation: 4 isn't at least as big as twice the value of 3, so we return -1.
```

Note:

1. nums will have a length in the range [1, 50].
2. Every nums[i] will be an integer in the range [0, 99].

思路先找到最大的值和最大值的下标，之后将数组排序，比较最大值是否为第二大值的两倍。

```C++
class Solution {
public:
    int dominantIndex(vector<int>& nums) {
        if(nums.size() < 2) return 0;

        int result = 0;
        int temp_data = nums[0];

        for(int i = 0; i < nums.size(); i++)
        {
            if(nums[i] > temp_data)
            {
                temp_data = nums[i];
                result = i;
            }
        }

        sort(nums.begin(),nums.end());

        if((nums[nums.size() - 2] * 2) > temp_data)
        {
            return -1;
        }

        return result;
    }
};
```
