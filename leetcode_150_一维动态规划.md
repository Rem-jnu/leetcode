### 1、爬楼梯，

有n阶楼梯，每一次只能爬一阶或者两阶，爬到楼顶有多少种爬法

dp[i] :表示爬到第i阶楼梯的方法数

第i阶楼梯可由i-1阶楼梯爬一步上来，也可在i-2阶楼梯爬两步上来

dp[i] = dp[i-1]+dp[i-2]

```java
class Solution {
    public int climbStairs(int n) {
        if (n <= 2) {
            return n;
        }
        int[] dp = new int[n + 1];
        dp[1] = 1;
        dp[2] = 2;
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
}
```

### 2、打家劫舍

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你 **不触动警报装置的情况下** ，一夜之内能够偷窃到的最高金额。

dp[i]定义：第0-i个房屋偷到的最大钱数目

dp[i] = max(dp[i - 1], dp[i - 2] + num[i]) , 分为偷第i家和不偷第i家

最终结果返回dp[n - 1]

```java
class Solution {
    public int rob(int[] nums) {
		// nums数组表示每个房间内的钱
        int n = nums.length;
        if (n == 1) {
            return nums[0];
        }
        if (n == 2) {
            return Math.max(nums[0], nums[1]);
        }
        int[] dp = new int[n];
        // dp[i]的含义：到第i个房屋为止，偷到的钱的最大值
        dp[0] = nums[0];
        dp[1] = Math.max(nums[0], nums[1]);
        for (int i = 2; i < n; i++) {
            dp[i] = Math.max(dp[i - 2] + nums[i], dp[i - 1]);
        }
        return dp[n - 1];
    }
}
```





### 3、单词拆分

给你一个字符串 `s` 和一个字符串列表 `wordDict` 作为字典。如果可以利用字典中出现的一个或多个单词拼接出 `s` 则返回 `true`。

**注意：**不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用

第一次做没有想出来

dp[i]表示下标为0到i-1的字符串是否可以拼出，当我们计算dp[i]的时候，dp[0]-dp[i-1]我们已经知道，

j的范围为0到j-1，dp[i]是否可以拼出来，以j作为中介位置，若dp[j]为true，而且j到i-1的字符串在字典集合里面。那么dp[i]为true

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        char[] chars = s.toCharArray();
        int n = chars.length;
        boolean[] dp = new boolean[n + 1];
        // dp[i]的含义：下标为0~i-1的字符串是否可以由字典中组成
        dp[0] = true;
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                dp[i] = dp[j] && check(chars, j, i - 1, wordDict);
                if (dp[i]) {
                    break;
                }
            }
        }
        return dp[n];


        
    }

    // 判断字符数组中的一部分组成的字符串是否在字典中
    public boolean check(char[] chars, int start, int end, List<String> wordDict) {
        StringBuilder sb = new StringBuilder();
        for (int i = start; i <= end; i++) {
            sb.append(chars[i]);
        }
        if (wordDict.contains(sb.toString())) {
            return true;
        }
        return false;
    }
}
```

### 4、零钱兑换

给你一个整数数组 `coins` ，表示不同面额的硬币；以及一个整数 `amount` ，表示总金额。

计算并返回可以凑成总金额所需的 **最少的硬币个数** 。如果没有任何一种硬币组合能组成总金额，返回 `-1` 。

你可以认为每种硬币的数量是无限的。



dp[i]表示总金额为i所需最少硬币数量，思路跟上面差不多

当计算dp[i]的时候,dp[0]到dp[i - 1]已经知道了，枚举所有的硬币金额d，如果dp[i - d] != -1，说明i-d这个金额再加上d

就等于金额i了，所以我们需要计算dp[i-d]的最小值，

dp[i]=-1表示无法组成金额i，最终返回dp[amount]

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        dp[0] = 0;
        for (int i = 1; i <= amount; i++) {
            int minv = Integer.MAX_VALUE;
            for (int d : coins) {
                if (i - d >= 0 && dp[i - d] != -1) {
                    minv = Math.min(minv, dp[i - d]);
                }
            }
            if (minv == Integer.MAX_VALUE) {
                dp[i] = -1;
            }else {
                dp[i] = minv + 1;
            }
            
        }

        return dp[amount];
    }
}
```

### 5、 最长递增子序列

给你一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。

**子序列** 是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7]` 是数组 `[0,3,1,6,2,2,7]` 的子序列

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n];
        // dp[i]表示以下标i结尾的最长递增子序列长度;

        dp[0] = 1;
        for (int i = 1; i < n; i++) {
            int maxn = Integer.MIN_VALUE;
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i]) {
                    maxn = Math.max(maxn, dp[j]);
                }
            }
            if (maxn == Integer.MIN_VALUE) {
                dp[i] = 1;
            }else {
                dp[i] = maxn + 1;
            }
            
        }
        int res = Integer.MIN_VALUE;
        for (int d : dp) {
            res = Math.max(res, d);
        }
        return res;
    }
}
```

总结：主要是明确dp[i]的含义，还有怎么选取返回值