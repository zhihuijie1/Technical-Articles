

## AOE问题

> **给定两个数组x和hp，长度都是N。**
>
> **x数组一定是有序的，x[i]表示i号怪兽在x轴上的位置**
>
> **hp数组不要求有序，hp[i]表示i号怪兽的血量为了方便起见，可以认为x数组和hp数组中没有负数。**
>
> **再给定一个正数range，表示如果法师释放技能的范围长度(直径！)被打到的每只怪兽损失1点血量。**
>
> **返回要把所有怪兽血量清空，至少需要释放多少次aoe技能？**
>
> **三个参数：int[] x, int[] hp, int range**
>
> **返回：int 次数**



```java
package algorithmbasic.leetcode.coding1;

import java.util.Arrays;
public class AOE {
    //贪心策略 -- 最左边缘以最优的方式变成0(AOE尽可能往右扩)
    //关键点就是：
    //1) 线段树
    //2) 总是用技能的最左边缘刮死最左侧没死的怪兽
    //3) 重复步骤2
    public static int minAoe(int[] x, int[] hp, int range) {
        int n = x.length;
        int[] cover = new int[n];//i位置是最左侧，cover[i]是range范围影响的最右侧的位置。
        int r = 0;
        for (int i = 0; i < n; i++) {
            while (r < n && x[r] - x[i] <= range) {
                r++;
            }
            cover[i] = r - 1;
        }
        //5 2 1 | 9 8 4| 6 2 4
        //3 5 7
        //0 2 4
        // x[i]:当前最左侧的位置
        // cover[i]:以x[i]位置为最左侧影响到的最右侧位置
        SegmentTree segmentTree = new SegmentTree(hp);
        segmentTree.build(1, n, 1);
        int ans = 0;
        for (int i = 1; i <= n; i++) {
            int leftHP = segmentTree.query(i, i, 1, n, 1);
            if (leftHP > 0) {
                ans += leftHP;
                segmentTree.add(i, cover[i - 1] + 1, -leftHP, 1, n, 1); // -- 注意
            }
        }
        return ans;
    }

    //实现区间内的“增” “查” “改”
    //时间复杂度控制在logN 关键点在于懒数组
    public static class SegmentTree {
        private int N;//传入数组的长度
        private int[] arr;//对传入数组的备份，从下标1开始计数
        private int[] lazy;
        private int[] sum;
        public SegmentTree(int[] hp) {
            N = hp.length;
            arr = new int[N + 1];
            for (int i = 1; i <= N; i++) {
                arr[i] = hp[i - 1];
            }
            lazy = new int[N << 2];
            sum = new int[N << 2];
        }

        //线段树的整个操作都是在操作sum数组(用下标构建的虚拟二叉树)，所以在使用之前一定要进行构建
        public void build(int l, int r, int rt) {
            if (l == r) {
                sum[rt] = arr[l];
                return;
            }
            int mid = (l + r) >> 1;
            build(l, mid, rt << 1);
            build(mid + 1, r, rt << 1 | 1);
            pushUp(rt);
        }

        //在[L,R]范围内(小范围)进行修改
        //[l,r]是整体的大范围
        //rt 根节点
        public void add(int L, int R, int C, int l, int r, int rt) {
            if (L <= l && R >= r) {
                sum[rt] += C * (r - l + 1); //---- 注意
                lazy[rt] += C;
                return;
            }
            int mid = (l + r) >> 1;
            pushDown(rt, mid - l + 1, r - mid);
            if (L <= mid) {
                add(L, R, C, l, mid, rt << 1);
            }
            if (R > mid) {
                add(L, R, C, mid + 1, r, rt << 1 | 1);
            }
            pushUp(rt);
        }

        public void pushDown(int rt, int lnumber, int rnumber) {
            int left = rt << 1;
            int right = rt << 1 | 1;
            lazy[left] += lazy[rt];
            lazy[right] += lazy[rt];
            sum[left] += lnumber * lazy[rt];
            sum[right] += rnumber * lazy[rt];
            lazy[rt] = 0;
        }

        public void pushUp(int rt) {
            sum[rt] = sum[rt << 1] + sum[rt << 1 | 1];
        }

        public int query(int L, int R, int l, int r, int rt) {
            if (L <= l && R >= r) {
                return sum[rt];
            }
            int mid = (l + r) >> 1;
            pushDown(rt, mid - l + 1, r - mid);
            int ans = 0;
            if (L <= mid) {
                ans += query(L, R, l, mid, rt << 1);
            }
            if (R > mid) {
                ans += query(L, R, mid + 1, r, rt << 1 | 1);
            }
            return ans;
        }
    }
}

```



## TargetSum

> **You are given an integer array `nums` and an integer `target`.**
>
> **You want to build an expression out of nums by adding one of the symbols `'+'` and `'-'` before each integer in nums and then concatenate all the integers.**
>
> - **For example, if `nums = [2, 1]`, you can add a `'+'` before `2` and a `'-'` before `1` and concatenate them to build the expression `"+2-1"`.**
>
> **Return the number of different expressions that you can build, which evaluates to `target`.**



```java
package algorithmbasic.leetcode.coding1;

import java.util.HashMap;

//https://leetcode.com/problems/target-sum/description/
public class TargetSum {
    //方法一：暴力递归
    public int findTargetSumWays1(int[] nums, int target) {
        return process(nums, 0, target);
    }

    //当前位置: index
    //当前目标值: target
    //返回:满足目标值的方法数
    private int process(int[] arr, int index, int target) {
        if (index == arr.length && target == 0) {
            return 1;// 没有数就是一种方法
        }
        if (index == arr.length) {
            return 0;
        }
        //index还没有到头
        return process(arr, index + 1, target - arr[index]) + process(arr, index + 1, target + arr[index]);
    }

    //方法二：记忆性优化
    public int findTargetSumWays2(int[] nums, int target) {
        HashMap<Integer, HashMap<Integer, Integer>> map = new HashMap<>();
        return process2(nums, 0, target, map);
    }

    //当前位置: index
    //当前目标值: target
    //返回:满足目标值的方法数
    private int process2(int[] arr, int index, int target, HashMap<Integer, HashMap<Integer, Integer>> dp) {

        //[1,4,6,2,8,11]
        // 0 1 2 3 4 5
        //     |
        //I_A_B
        //当前位置_还剩下的目标值_后边目标值的方法数
        if (dp.containsKey(index) && dp.get(index).containsKey(target)) {
            return dp.get(index).get(target);
        }
        int ans = 0;
        if (index == arr.length && target == 0) {
            ans = 1;
        } else if (index == arr.length) {
            ans = 0;
        } else {
            ans = process2(arr, index + 1, target - arr[index], dp) + process2(arr, index + 1, target + arr[index], dp);
        }
        if (!dp.containsKey(index)) {
            dp.put(index, new HashMap<>());
        }
        dp.get(index).put(target, ans);
        return ans;
    }

    // 优化点一 :
    // 你可以认为arr中都是非负数
    // 因为即便是arr中有负数，比如[3,-4,2]
    // 因为你能在每个数前面用+或者-号
    // 所以[3,-4,2]其实和[3,4,2]达成一样的效果
    // 那么我们就全把arr变成非负数，不会影响结果的
    // 优化点二 :
    // 如果arr都是非负数，并且所有数的累加和是sum
    // 那么如果target>sum，很明显没有任何方法可以达到target，可以直接返回0
    // 优化点三 :
    // arr内部的数组，不管怎么+和-，最终的结果都一定不会改变奇偶性
    // 所以，如果所有数的累加和是sum，
    // 并且与target的奇偶性不一样，没有任何方法可以达到target，可以直接返回0
    // 优化点四 :
    // 比如说给定一个数组, arr = [1, 2, 3, 4, 5] 并且 target = 3
    // 其中一个方案是 : +1 -2 +3 -4 +5 = 3
    // 该方案中取了正的集合为P = {1，3，5}
    // 该方案中取了负的集合为N = {2，4}
    // 所以任何一种方案，都一定有 sum(P) - sum(N) = target
    // 现在我们来处理一下这个等式，把左右两边都加上sum(P) + sum(N)，那么就会变成如下：
    // sum(P) - sum(N) + sum(P) + sum(N) = target + sum(P) + sum(N)
    // 2 * sum(P) = target + 数组所有数的累加和
    // sum(P) = (target + 数组所有数的累加和) / 2
    // 也就是说，任何一个集合，只要累加和是(target + 数组所有数的累加和) / 2
    // 那么就一定对应一种target的方式
    // 也就是说，比如非负数组arr，target = 7, 而所有数累加和是11
    // 求有多少方法组成7，其实就是求有多少种达到累加和(7+11)/2=9的方法
    // 优化点五 :
    // 二维动态规划的空间压缩技巧

    public int findTargetSumWays3(int[] nums, int target) {
        int sum = 0;
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            sum += nums[i];
        }
        //进行奇偶判断 和 target判断
        return target > sum || ((target & 1) ^ (sum & 1)) != 0 ? 0 : subset(nums, (target + sum) >> 1);
    }

    //dp[i][j]
    //i -- [0,i]这个范围
    //i -- 目标值

    //dp[n][s]

/*    private static int subset(int[] nums, int s) {
        int n = nums.length;
        int[][] dp = new int[n + 1][s + 1];
        for (int i = 0; i <= n; i++) {
            dp[i][0] = 1;
        }
        for (int i = 1; i <= s; i++) {
            dp[0][i] = 0;
        }
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j <= s; j++) {
                if (nums[i - 1] <= j) {
                    dp[i][j] = dp[i - 1][j - nums[i - 1]] + dp[i - 1][j];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return dp[n][s];
    }*/
}
```



## Near2Power

> **已知n是正数**
> **返回大于等于，且最接近n的，2的某次方的值**

```java
package algorithmbasic.leetcode.coding1;

public class Near2Power {
    //思路：二进制形式全变成1111...   然后再加1
    public static final int tableSizeFor(int n) {

        //刚上来n就是2的某次方的值
        //1000
        //111

        //11000
        //10111
        //11111
        n -= 1;
        n = n | n >>> 1;
        n = n | n >>> 2;
        n = n | n >>> 4;
        n = n | n >>> 8;
        n = n | n >>> 16;

        //全变成1111.。。

        return n + 1;

    }
}
```



## MinSwapStep



>  **一个数组中只有两种字符'G'和'B'，**
>  **可以让所有的G都放在左侧，所有的B都放在右侧**
>  **或者可以让所有的G都放在右侧，所有的B都放在左侧**
>  **但是只能在相邻字符之间进行交换操作，请问请问至少需要交换几次，**

```java
package algorithmbasic.leetcode.coding1;

public class MinSwapStep {
    public static int minSteps1(String s) {
        int answer1 = answer(s,'G');
        int answer2 = answer(s,'B');
        return Math.min(answer1,answer2);
    }

    private static int answer(String s, char C) {
        char[] str = s.toCharArray();
        int a = 0;
        int b = 0;
        int step = 0;
        for (b = 0; b < str.length; b++) {
            if (str[b] == C) {
                step += b - a;
                a++;
            }
        }
        return step;
    }

    // 为了测试
    public static String randomString(int maxLen) {
        char[] str = new char[(int) (Math.random() * maxLen)];
        for (int i = 0; i < str.length; i++) {
            str[i] = Math.random() < 0.5 ? 'G' : 'B';
        }
        return String.valueOf(str);
    }

    public static int minSteps2(String s) {
        if (s == null || s.equals("")) {
            return 0;
        }
        char[] str = s.toCharArray();
        int step1 = 0;
        int step2 = 0;
        int gi = 0;
        int bi = 0;
        for (int i = 0; i < str.length; i++) {
            if (str[i] == 'G') { // 当前的G，去左边   方案1
                step1 += i - (gi++);
            } else {// 当前的B，去左边   方案2
                step2 += i - (bi++);
            }
        }
        return Math.min(step1, step2);
    }

    public static void main(String[] args) {
        int maxLen = 100;
        int testTime = 1000000;
        System.out.println("测试开始");
        for (int i = 0; i < testTime; i++) {
            String str = randomString(maxLen);
            int ans1 = minSteps1(str);
            int ans2 = minSteps2(str);
            if (ans1 != ans2) {
                System.out.println("Oops!");
            }
        }
        System.out.println("测试结束");
    }
}

```





## LongestIncreasingPath

>     /**
>      * Given an m x n integers matrix, return the length of the longest increasing path in matrix.
>      * From each cell, you can either move in four directions: left, right, up, or down.
>      * You may not move diagonally or move outside the boundary (i.e., wrap-around is not allowed).
>      */

```java
package algorithmbasic.leetcode.coding1;

//https://leetcode.com/problems/longest-increasing-path-in-a-matrix/description/
public class LongestIncreasingPath {
    public int longestIncreasingPath(int[][] matrix) {
        if (matrix == null) {
            return 0;
        }
        int N = matrix.length;
        int M = matrix[0].length;
        int dp[][] = new int[N][M];
        int ans = 0;
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < M; j++) {
                ans = Math.max(ans, process(matrix, i, j, dp));
            }
        }
        return ans;
    }

    //返回值 -- 某个位置为起点，最大递增链的长度
    private static int process(int[][] matrix, int i, int j, int[][] dp) {
        if (dp[i][j] != 0) {
            return dp[i][j];
        }
        int N = matrix.length;
        int M = matrix[0].length;
        //因为条件判断已经提前写好了所以不用单独写
        int up = i > 0 && (matrix[i - 1][j] > matrix[i][j]) ? process(matrix, i - 1, j, dp) : 0;
        int down = i < N - 1 && (matrix[i + 1][j] > matrix[i][j]) ? process(matrix, i + 1, j, dp) : 0;
        int left = j > 0 && (matrix[i][j - 1] > matrix[i][j]) ? process(matrix, i, j - 1, dp) : 0;
        int right = j < M - 1 && (matrix[i][j + 1] > matrix[i][j]) ? process(matrix, i, j + 1, dp) : 0;
        int ans = Math.max(Math.max(up, down), Math.max(left, right)) + 1;
        dp[i][j] = ans;
        return ans;
    }
}

```

