**假设有排成一行的N个位置记为1~N，N一定大于或等于2**
**开始时机器人在其中的M位置上(M一定是1~N中的一个)**
**如果机器人来到1位置，那么下一步只能往右来到2位置；**
**如果机器人来到N位置，那么下一步只能往左来到N-1位置；**
**如果机器人来到中间位置，那么下一步可以往左走或者往右走；**
**规定机器人必须走K步，最终能来到P位置(P也是1~N中的一个)的方法有多少种**
**给定四个参数 N、M、K、P，返回方法数**



![](D:/%E4%BD%A0%E5%A5%BDJava/1398.jpg)



# 暴力解法



```java
//N:一共有N个位置记为1-N
    //start：机器人当前所在的位置
    //aim：目标位置
    //k：一共可以走多少步。
    public static int ways1(int N, int start, int aim, int K) {
        return process1(start, aim, K, N);
    }

    // process方法的解释：现在在cur位置，目标位置是aim，还剩rest步可走，一共有N个位置（1-N）
    // 机器人从当前的cur位置开始走，走rest步之后，到达目标位置aim的方法数一共是多少。
    private static int process1(int cur, int aim, int rest, int N) {
        //下面的四种情况只会命中一个。
        
        //现在还剩0步可走。
        if (rest == 0) {
            return cur == aim ? 1 : 0;
        }
        //如果当前的位置在最左侧，只能往右行走。
        if (cur == 1) {
            return process1(2, aim, rest-1, N);
        }
        //如果当前的位置在最右侧只能往左走。
        if (cur == N) {
            return process1(N - 1, aim, rest-1, N);
        }

        //当前的位置不在边缘，在中间，那既可以向左也可以向右走。
        return process1(cur + 1, aim, rest-1, N) + process1(cur - 1, aim, rest-1, N);
    }
```



暴力递归中有重复解出现的时候才可以优化，如果一个暴力递归在递归的过程中没有重复解出现过那根本就不用优化。

本题是可以优化的，原因如下：

<img src="D:/%E4%BD%A0%E5%A5%BDJava/1399.jpg" style="zoom:33%;" />



现在的位置是7位置，还剩10步可走，然后往下走.............我会发现中间就会出现(7,8)的重复，其实之前出现过的情况以后直接取值就行。所以可以进行优化。



# 优化一：记忆化搜索版本，自顶向下动态规划，递归实现



关键点是分析影响递归结果的因素有几个，本题影响递归结果的因素有两个，一个是cur,另一个是rest。

可以看作是key - value，只不过此时的key有两个影响因素，value就是该情况下的结果。



```java
//进行优化1
    public static int ways2(int N, int start, int aim, int K) {
        //加入一个缓存来存储之前计算过的结果->方便以后遇到一样的情况下直接取结果。
        int[][] dp = new int[N + 1][K + 1];
        //初始化缓存中的数据一上来都是-1.
        for (int i = 0; i <= N; i++) {
            for (int j = 0; j <= K; j++) {
                dp[i][j] = -1;
            }
        }
        return process2(start, K, aim, N, dp);
    }

    //返回的结果是由cur(当前位置),rest(还剩几步可以走)这两个元素决定的。
    //cur 取值范围是:[1,N]
    //rest 取值范围是:[0,K]
    //所以一共有(N * K)种搭配,可以创建一个二维数组来存放结果值。
    private static int process2(int cur, int rest, int aim, int N, int[][] dp) {
        //一进来就判断一下缓存中有无当前情况下的结果。
        //如果缓存中的数据不是-1说明当前情况之前也遇到过，直接返回之前遇到时的处理结果即可。
        if (dp[cur][rest] != -1) {
            return dp[cur][rest];
        }

        //当前情况之前没有遇到过。
        int result = -1;
        //下面的四种情况只会命中一个。
        if (rest == 0) {
            result = cur == aim ? 1 : 0;
        } else if (cur == 1) {
            result = process2(2, rest - 1, aim, N, dp);
        } else if (cur == N) {
            result = process2(N - 1, rest - 1, aim, N, dp);
        } else {
            result = process2(cur - 1, rest - 1, aim, N, dp) + process2(cur + 1, rest - 1, aim, N, dp);
        }

        dp[cur][rest] = result;

        return result;
    }
```

空间换时间

记忆化搜索



# 优化二：依赖版本，是从底到顶的动态规划，迭代实现。



<img src="D:/%E4%BD%A0%E5%A5%BDJava/1400.jpg" style="zoom:25%;" />



根据暴力递归，对暴力递归进行优化。

在暴力递归的过程当中，我发现变化的只有两个元素，一个是start位置，一个是rest剩余可走的步数。

start的取值范围是 [1,N]，rest的取值范围是【0，K】

设计一个二维数组的表格，行 --> N:一共有几个位置。列 --> K：一共有多少步可走。

当我想求（start，k）时，需要递归向下深入，所以我的方法是按以下顺序执行代码。

<img src="D:/%E4%BD%A0%E5%A5%BDJava/1403.jpg" style="zoom: 20%;" />



第一列：只有dp[aim] [0] 的位置是1，第一列的其他位置都是0.   对应：

```java
if (rest == 0) {
	return cur == aim ? 1 : 0;
}
```

之后第一行都需要依赖其左下角的元素。 对应：

```java
if (cur == 1) {
	return process1(2, aim, rest-1, N);
}
```

最后一行需要依赖其左上角的元素。   对应：

```java
if (cur == N) {
	return process1(N - 1, aim, rest-1, N);
}
```

中间的位置需要依赖其左上角以及左下角的元素。 对应：

```java
return process1(cur + 1, aim, rest-1, N) + process1(cur - 1, aim, rest-1, N);
```





之后按照从左到右从上到下的执行顺序执行，当二维数组元素都执行完之后

返回dp[start] [rest]位置的值即可。





```java
	public static int ways3(int N, int start, int aim, int K) {
        int[][] dp = new int[N + 1][K + 1];
        dp[aim][0] = 1;
        for (int i = 1; i <= K; i++) { //列
            
            //注意这里面的执行顺序 -> 由上往下。 
            
            //第一行都需要依赖其左下角的元素
            dp[1][i] = dp[2][i - 1];
            
            //中间的位置需要依赖其左上角以及左下角的元素
            for (int j = 2; j <= N - 1; j++) { //行
                dp[j][i] = dp[j - 1][i - 1] + dp[j + 1][i - 1];
            }

            //最后一行需要依赖其左上角的元素。
            dp[N][i] = dp[N - 1][i - 1];
        }

        return dp[start][K];
    }
```



# 总结



其实暴力递归在dp图中的运算顺序如下所示：

<img src="D:/%E4%BD%A0%E5%A5%BDJava/1404.jpg" style="zoom:25%;" />

记忆搜索法：执行顺序是自顶向下，递归实现，时间复杂度是O（N*K）

依赖版本执行的顺序是自底往上，迭代执行，时间复杂度也是O（N*K）





**写动态规划，最终要的一部是尝试，就是先写出暴力解，然后再对其进行修改。**





