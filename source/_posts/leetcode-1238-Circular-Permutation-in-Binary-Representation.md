---
title: leetcode-1238-Circular-Permutation-in-Binary-Representation
tags:
  - leetcode
  - dfs
  - 格雷姆数
date: 2019-11-09 23:15:12
---


Given 2 integers `n` and `start`. Your task is return any permutation `p` of `(0,1,2.....,2^n -1)` such that :

<!--more-->

  - `p[0]` = `start`
  - `p[i]` and `p[i+1]` differ by only one bit in their binary representation.
  - `p[0]` and `p[2^n -1]` must also differ by only one bit in their binary representation.

Example 1:
```
Input: n = 2, start = 3
Output: [3,2,0,1]
Explanation: The binary representation of the permutation is (11,10,00,01).
All the adjacent element differ by one bit. Another valid permutation is [3,1,0,2]
```

Example 2:
```
Input: n = 3, start = 2
Output: [2,6,7,5,4,0,1,3]
Explanation: The binary representation of the permutation is (010,110,111,101,100,000,001,011).
```

Constraints:
  - `1 <= n <= 16`
  - `0 <= start < 2 ^ n`

题目所求序列是一个 `格雷码` 序列, 相邻的二进制只有一位不同(有效位数), 首尾同样只有一位不同题目虽然给出了开始位置, 但其实就是一个循环, 找到一个循环序列即可.

**格雷码生成公式**
格雷码生成公式: [i] = i^(i>>1) 自己与自己左移一位相异或

| i | i | i >> 1 | [i] | [i] |
|---|---|--------|-----|-----|
| 0 | 000 | 000 |	000 | 0 |
| 1 |	001 |	000 |	001 |	1 |
| 2 |	010 |	001 |	011 |	3 |
| 3 |	011 |	001 |	010 |	2 |
| 4 |	100 |	010 |	110 |	6 |
| 5 |	101 |	010 |	111 |	7 |
| 6 |	110 |	011 |	101 |	5 |
| 7 |	111 |	011 |	100 |	4 |



题解：
```
class Solution {
public:
    vector<int> circularPermutation(int n, int start) {
        int size = 1 << n;
        vector<int> arr;

        for(int i = 0; i < size; i++)
        {
            arr.push_back(i ^ (i >> 1));
        }

        vector<int> ans;
        for(int i = 0; i < size; i++)
        {
            if(arr[i] == start)
            {
                for(int j = 0; j < size; j++)
                {
                    ans.push_back(arr[(i+j)%size]);
                }
                return ans;
            }
        }

        return ans;
    }
};
```

参考资料：
https://www.cnblogs.com/slowbirdoflsh/p/11823498.html
