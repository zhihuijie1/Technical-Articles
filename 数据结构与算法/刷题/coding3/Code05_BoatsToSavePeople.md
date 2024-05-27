> 给你一个数组`people`，其中`people[i]`是`ith`人的重量，以及一个**无限多的船**，其中每艘船可承载的最大重量为`limit`。每艘船最多同时运载两个人，条件是这些人的重量总和最多为 "限制"。
>
> 返回运载每个给定人员的最少船只数



> 思路：这种配对最优解问题，需要解决的问题的本质是：**配对的匹配度最高（最合适的两个数进行匹配）**
>
> ​			1：**即第三大的数与第三小的数匹配，第二大与第二小的数匹配  ------ 左右指针法**
>
> ​			2：**从中间撕开，分成左右两份，然后从中间向两侧进行扩散匹配 ------ 中间指针法**
>
> ​			      从中间撕开的方法的好处是：可以一次匹配一批数，而左右指针法只能一次匹配一个数。
>
> ​				  比如：1 1 1 2 3 | 6 6 7 7 8       limit = 10
>
> ​				  3可以与6 6 7 7 进行匹配，那么3前面的2 1 1 1 都可以与6 6 7 7 进行匹配，与此同时还是匹配的最优解，匹配度				  最高   3 - 6  ， 2 - 6 ，1 - 7 ， 1 - 7
>
> 



> 本题思路 : 采用中间指针法，撕开的标准是：limit / 2 

![](D:/%E4%BD%A0%E5%A5%BDJava/23-17137576167631.png)







```java
public class Code05_BoatsToSavePeople {
    public int numRescueBoats(int[] people, int limit) {
        if (people == null || people.length == 0 || limit <= 0) {
            return 0;
        }
        Arrays.sort(people);
        int N = people.length;
        if (people[N - 1] > limit) {
            return -1;
        }
        int lessR = -1;
        for (int i = N - 1; i >= 0; i--) {
            if (people[i] <= limit >> 1) {
                lessR = i;
                break;
            }
        }
        if (lessR == -1) {
            return N;
        }
        int L = lessR;
        int R = L + 1;
        int noUsed = 0;
        while (L >= 0) {
            int solved = 0;
            while (R < N && people[L] + people[R] <= limit) {
                R++;
                solved++;
            }
            if (solved == 0) {
                noUsed++;
                L--;
            } else {
                L = Math.max(-1, L - solved);
            }
        }
        int used = lessR - noUsed + 1;
        int moreUnsolved = N - lessR - 1 - used;
        return used + ((noUsed + 1) >> 1) + moreUnsolved;
    }
}
```

























