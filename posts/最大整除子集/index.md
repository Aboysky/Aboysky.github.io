# 最大整除子集


### [368\. 最大整除子集](https://leetcode-cn.com/problems/largest-divisible-subset/)

#### 题目

Difficulty: **中等**

给你一个由 **无重复** 正整数组成的集合 `nums` ，请你找出并返回其中最大的整除子集 `answer` ，子集中每一元素对 `(answer[i], answer[j])` 都应当满足：

*   `answer[i] % answer[j] == 0` ，或
*   `answer[j] % answer[i] == 0`

如果存在多个有效解子集，返回其中任何一个均可。

**示例 1：**

```
输入：nums = [1,2,3]
输出：[1,2]
解释：[1,3] 也会被视为正确答案。
```

**示例 2：**

```
输入：nums = [1,2,4,8]
输出：[1,2,4,8]
```

**提示：**

*   `1 <= nums.length <= 1000`
*   `1 <= nums[i] <= 2 * 10<sup>9</sup>`
*   `nums` 中的所有整数 **互不相同**


#### Solution

Language: **Java**

##### 思路：

- `最开始看到题目的时候第一时间想到的回溯分析，因为回溯对于这类选择问题绝对是可以做的，因为他是一个暴力匹配，所以后续就在想有没有什么很好的剪枝方法，但是并没有找到好的剪枝方法。`
- `由于回溯的时间复杂度太高，只能考虑使用动态规划，而使用动态规划第一个就是要考虑定义一个什么状态，此题是叫我们求最长的整除子集，按照一般经验，此时我们应该定义dp[i]表示为在nums中前i位的最长整除子集长度。定义了状态后考虑状态转移函数，dp[i] = Math.max(dp[0 - j] + 1,dp[i]),而dp[0-j]表示nums[i] % nums[j] == 0时j的dp; (i >= 1)，思考一下，如何才能保证nums[i] % nums[j] == 0时就可以保证nums[i]可以添加进入前面的集合？答案是将nums数组排序，因为后面的肯定比前面的大，也就是每次都是和子集的最大元素比较，如果当前元素能够整除子集的最大元素，则一定可以加入这个子集。在转移方程确定后，我们需要考虑的是如何将这个最长的整除子集返回，也就是如何拿到这个整除子集。可以考虑倒推法。也就是我们维护一个变量maxSize(==dp[i])和maxVal(==nums[i])，而后从后往前遍历dp数组，当dp[i] == maxSize时，如果maxVal % nums[i] == 0，那么表明maxSize和maxVal所记录的最大整除子集的最后一个就是这一个。而后将这个添加进入res，此后将maxSize--;maxVal = nums[i],循环下去，则能够找到所有的节点。`

##### 代码：

```Java
class Solution {
    public List<Integer> largestDivisibleSubset(int[] nums) {
        Arrays.sort(nums);
        int len = nums.length;
        int[] dp = new int[len];
        Arrays.fill(dp,1);
        int maxSize = 1;
        int maxVal = 0;
        for (int i = 1;i < len;i++) {
            for (int j = 0;j < i;j++) {
                if (nums[i] % nums[j] == 0) {
                    dp[i] = Math.max(dp[i],dp[j] + 1);
                }
            }
            if (dp[i] > maxSize) {
                maxSize = dp[i];
                maxVal = nums[i];
            }
        }
        List<Integer> res = new ArrayList<>();
        for (int i = len - 1;i >= 0;i--) {
            if (dp[i] == maxSize && maxVal % nums[i] == 0){
                res.add(nums[i]);
                maxSize--;
                maxVal = nums[i];
            } 
        }
        return res;
    }

}
```
