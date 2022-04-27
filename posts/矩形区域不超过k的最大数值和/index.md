# 矩形区域不超过k的最大数值和_363


### [363\. 矩形区域不超过 K 的最大数值和](https://leetcode-cn.com/problems/max-sum-of-rectangle-no-larger-than-k/)

Difficulty: **困难**


给你一个 `m x n` 的矩阵 `matrix` 和一个整数 `k` ，找出并返回矩阵内部矩形区域的不超过 `k` 的最大数值和。

题目数据保证总会存在一个数值和不超过 `k` 的矩形区域。

**示例 1：**

![](https://assets.leetcode.com/uploads/2021/03/18/sum-grid.jpg)

```
输入：matrix = [[1,0,1],[0,-2,3]], k = 2
输出：2
解释：蓝色边框圈出来的矩形区域 [[0, 1], [-2, 3]] 的数值和是 2，且 2 是不超过 k 的最大数字（k = 2）。
```

**示例 2：**

```
输入：matrix = [[2,2,-1]], k = 3
输出：3
```

**提示：**

*   `m == matrix.length`
*   `n == matrix[i].length`
*   `1 <= m, n <= 100`
*   `-100 <= matrix[i][j] <= 100`
*   `-10<sup>5</sup> <= k <= 10<sup>5</sup>`

**进阶：**如果行数远大于列数，该如何设计解决方案？


#### Solution

Language: **java**

**思路1：**

`利用区间db的思路去做，也就是先定义一个状态sums[i][j][rlen][clen]，表示以i，j为矩阵左顶点，rlen行，clen列的矩阵的数值和。然后就是去思考如何做一个正确的状态转换方程，在这里面需要一起思考如何去进行循环以便于计算当前状态转换时前一个状态已经被计算过了。`

**代码1：**

```java
class Solution {
    public int maxSumSubmatrix(int[][] matrix, int k) {
        int m = matrix.length, n = matrix[0].length;
        int[][][][] sums = new int[2][n][n + 1][m + 1];

        int res = Integer.MIN_VALUE;
        int flag = 0;
        for (int i = m - 1;i >= 0;i--) {
            for (int j = n - 1;j >= 0;j--) {
                for (int rlen = 1;rlen + j <= n;rlen++) {
                    for (int clen = 1;clen + i <= m;clen++) {
                        if (rlen == 1 && clen == 1) {
                            sums[flag][j][rlen][clen] = matrix[i][j];
                        } else if (rlen == 1) {
                            sums[flag][j][rlen][clen] = sums[(flag + 1) % 2][j][rlen][clen - 1] + matrix[i][j];
                        } else if (clen == 1) {
                            sums[flag][j][rlen][clen] = sums[flag][j + 1][rlen - 1][clen] + matrix[i][j];
                        } else {
                            sums[flag][j][rlen][clen] = sums[flag][j][1][clen] + sums[flag][j + 1][rlen - 1][clen];
                        }
                        if (sums[flag][j][rlen][clen] > res && sums[flag][j][rlen][clen] <= k) {
                            res = sums[flag][j][rlen][clen];
                        }
                    }
                }
            }
            flag = (flag + 1) % 2;
            sums[flag] = new int[n][n + 1][m + 1];
        }
        return res;
    }
}
```

**官方思路：**

`	枚举i - k 行的矩阵不同列的前缀和，而后题目便变成了在一个一维的前缀和集合中求最接近k的区间和。区间和sum = Sr - Sl;Sr - Sl <= k;此时可以利用TreeSet集合的ceiling函数求Sl，然后用Math.max比较的到最大值。 `

**官方题解：**

```
class Solution {
    public int maxSumSubmatrix(int[][] matrix, int k) {
        int ans = Integer.MIN_VALUE;
        int m = matrix.length, n = matrix[0].length;
        for (int i = 0; i < m; ++i) { // 枚举上边界
            int[] sum = new int[n];
            for (int j = i; j < m; ++j) { // 枚举下边界
                for (int c = 0; c < n; ++c) {
                    sum[c] += matrix[j][c]; // 更新每列的元素和
                }
                TreeSet<Integer> sumSet = new TreeSet<Integer>();
                sumSet.add(0);
                int s = 0;
                for (int v : sum) {
                    s += v;
                    Integer ceil = sumSet.ceiling(s - k);
                    if (ceil != null) {
                        ans = Math.max(ans, s - ceil);
                    }
                    sumSet.add(s);
                }
            }
        }
        return ans;
    }
}

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/max-sum-of-rectangle-no-larger-than-k/solution/ju-xing-qu-yu-bu-chao-guo-k-de-zui-da-sh-70q2/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```


