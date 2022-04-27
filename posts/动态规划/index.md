# 动态规划


## 动态规划

### [5\. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

Difficulty: **中等**


给你一个字符串 `s`，找到 `s` 中最长的回文子串。

**示例 1：**

```
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```

**示例 2：**

```
输入：s = "cbbd"
输出："bb"
```

**示例 3：**

```
输入：s = "a"
输出："a"
```

**示例 4：**

```
输入：s = "ac"
输出："a"
```

**提示：**

*   `1 <= s.length <= 1000`
*   `s` 仅由数字和英文字母（大写和/或小写）组成

#### Solution

分析：

```
首先如何判定此题用动态规划呢？因为很明显，回文串去掉外层的两个字符，剩下的仍然是回文串，所以可以使用动态规划 。而动态规划有三个问题需要考虑：
1. 状态的定义：stat[i][j]表示string[i:j]范围的字符串是否为回文串。
2. 状态转移方程：stat[i][j] = stat[i + 1][j - 1] && s[i] == s[j]; j < s.length;
                            i == j ,true， i + 1 = j,  s[i] == s[j]
3. 计算路径：
   循环应该从何处开始计算，此题因为所有的长的回文串都需要先计算短的回文串，所以应当按照长度来进行循环，先计算长度为1的回文串，而后在慢慢变大，长度最大为s.length()
```



Language: ****

```Java
class Solution {
    public static String longestPalindrome(String s) {
        boolean [][] stat = new boolean[s.length()][s.length()];
        String result = null;
        int maxLength = 1,pre = 0,end = 0;
        for (int i = 0;i < stat.length;i++) {
            stat[i][i] = true;
        }
        for (int len = 1; len <= s.length(); len++) {
            for (int i = 0;i < s.length(); i++) {
                int j = i + len - 1;
                if (j >= s.length()) {
                    break;
                }
                if (i == j) {
                    stat[i][j] = true;
                } else if (i + 1 == j) {
                    stat[i][j] = s.charAt(i) == s.charAt(j);
                } else {
                    stat[i][j] = stat[i+1][j-1] && (s.charAt(i) == s.charAt(j));
                }
                if (stat[i][j] && j - i + 1 > maxLength) {
                    pre = i;
                    end = j;
                    maxLength = j - i + 1;
                }
            }
        }
        result = s.substring(pre,end + 1);
        return result;
    }
}
```

### [53\. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

Difficulty: **简单**


给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**示例 1：**

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

**示例 2：**

```
输入：nums = [1]
输出：1
```

**示例 3：**

```
输入：nums = [0]
输出：0
```

**示例 4：**

```
输入：nums = [-1]
输出：-1
```

**示例 5：**

```
输入：nums = [-100000]
输出：-100000
```

**提示：**

*   `1 <= nums.length <= 3 * 10<sup>4</sup>`
*   `-10<sup>5</sup> <= nums[i] <= 10<sup>5</sup>`

**进阶：**如果你已经实现复杂度为 `O(n)` 的解法，尝试使用更为精妙的 **分治法** 求解。

#### Solution

思路：

``` markdown
为什么这一题想到用动态规划做？  最优最大等问题一般都可以考虑用dp
状态定义： 一开始我是想用二元dp，但是发现行不通，因为你根本没有办法递归，然后就没有思路了，但是既然我都想到这个问题了，为什么没有去切换一下思路，换一种状态定义解决这个问题呢？
```



Language: ****

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int pre = 0,maxSum = Integer.MIN_VALUE;
        for (int num : nums) {
            pre = Math.max(pre + num,num);
            maxSum = Math.max(pre,maxSum);
        }
        return maxSum;
    }
}
```

### [64\. 最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)

Difficulty: **中等**


给定一个包含非负整数的 `_m_ x _n_` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

**说明：**每次只能向下或者向右移动一步。

**示例 1：**

![](https://assets.leetcode.com/uploads/2020/11/05/minpath.jpg)

```
输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。
```

**示例 2：**

```
输入：grid = [[1,2,3],[4,5,6]]
输出：12
```

**提示：**

*   `m == grid.length`
*   `n == grid[i].length`
*   `1 <= m, n <= 200`
*   `0 <= grid[i][j] <= 100`


#### Solution

Language: ****

```Java
class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length,n = grid[0].length;
        int [][] dp = new int[m][n];
        for (int i = 0;i < m;i++) {
            for (int j = 0;j < n;j++) {
                if (i == 0 && j == 0) {
                    dp[i][j] = grid[i][j];
                } else if (i == 0) {
                    dp[i][j] = grid[i][j] + dp[i][j - 1];
                } else if (j == 0) {
                    dp[i][j] = grid[i][j] + dp[i - 1][j];
                } else {
                    dp[i][j] = Math.min(dp[i - 1][j],dp[i][j - 1]) + grid[i][j];
                }
            }
        }
        return dp[m - 1][n - 1];
    }
}
```


