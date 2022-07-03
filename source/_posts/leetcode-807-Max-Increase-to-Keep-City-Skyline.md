---
title: leetcode-807-Max-Increase-to-Keep-City-Skyline
tags:
  - leetcode
---

In a 2 dimensional array grid, each value grid[i][j] represents the height of a building located there. We are allowed to increase the height of any number of buildings, by any amount (the amounts can be different for different buildings). Height 0 is considered to be a building as well.

<!--more-->

At the end, the "skyline" when viewed from all four directions of the grid, i.e. top, bottom, left, and right, must be the same as the skyline of the original grid. A city's skyline is the outer contour of the rectangles formed by all the buildings when viewed from a distance. See the following example.

What is the maximum total sum that the height of the buildings can be increased?

```
Example:
Input: grid = [[3,0,8,4],[2,4,5,7],[9,2,6,3],[0,3,1,0]]
Output: 35
Explanation:
The grid is:
[ [3, 0, 8, 4],
  [2, 4, 5, 7],
  [9, 2, 6, 3],
  [0, 3, 1, 0] ]

The skyline viewed from top or bottom is: [9, 4, 8, 7]
The skyline viewed from left or right is: [8, 7, 9, 3]

The grid after increasing the height of buildings without affecting skylines is:

gridNew = [ [8, 4, 8, 7],
            [7, 4, 7, 7],
            [9, 4, 8, 7],
            [3, 3, 3, 3] ]
```

Notes:

* `1 < grid.length = grid[0].length <= 50`.
* All heights `grid[i][j]` are in the range `[0, 100]`.
* All buildings in `grid[i][j]` occupy the entire grid cell: that is, they are a `1 x 1 x grid[i][j]` rectangular prism.

这一题是计算最大的建筑容量，具体解题思路为先取出每一行每一列的最大值 `top`, `left`。 然后新构成的矩阵的每一个值 `new[i][j]`为`top[j]`和`left[i]`中较小的值。最后计算两个矩阵中所有元素的差即可。具体解法如下。

```
class Solution {
public:
    int maxIncreaseKeepingSkyline(vector<vector<int>>& grid) {
        int sum_p = 0, sum_a = 0;
        vector<int> top,left;

        top = grid[0];

        for(int i = 0; i < grid.size(); i++)
        {
            left.push_back(grid[i][0]);
            for(int j = 0; j < grid[0].size(); j++)
            {
                if(top[j] < grid[i][j])
                {
                    top[j] = grid[i][j];
                }

                if(left[i] < grid[i][j])
                {
                    left[i] = grid[i][j];
                }
                sum_p += grid[i][j];
            }
        }

        for(int i = 0; i < grid.size(); i++)
        {
            int temp_t = left[i];
            for(int j = 0; j < grid[0].size(); j++)
            {
                sum_a += (temp_t < top[j]?temp_t:top[j]);
            }
        }

        return (sum_a-sum_p);
    }
};
```
