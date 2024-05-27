

KMP算法的用途是：在一个字符串中找到某一个字串的位置。

时间复杂度是O（N）



## 代码：

```java
package algorithmbasic.basicsets.class28;

public class KMP {
    public static int getIndexOf(String s1, String s2) {
        if (s1 == null || s2 == null || s1.length() < 1 || s2.length() < 1 || s2.length() > s1.length()) {
            return -1;
        }
        char[] str1 = s1.toCharArray();
        char[] str2 = s2.toCharArray();

        int[] next = makeNextArray(str2);

        int x = 0;
        int y = 0;

        while (x < str1.length && y < str2.length) {
            /**
             * 跳出循环分三种情况
             * 1：当 x == str1.length && y != str2.length ==> 没有在str1中找到str2.
             * 2：当 x == str1.length && y == str2.length ==> 在最后的时候正好找到了。.
             * 3：当 x != str1.length && y == str2.length ==> 在前面就找到了.
             */
            if (str1[x] == str2[y]) {
                x++;
                y++;
            } else if (next[y] == -1) { // y == 0 ， 说明x所在的位置没有一个跟他匹配成功的，那就x++,继续向下找吧。
                x++;
            } else {
                y = next[y]; // 直接来到next[y]位置与x位置进行比较。
            }
        }
        return y == str2.length ? x - y : -1;
    }

    private static int[] makeNextArray(char[] str2) {
        if (str2.length < 2) {
            return new int[]{-1};
        }
        int[] next = new int[str2.length];
        next[0] = -1;
        next[1] = 0;
        int i = 2;
        int cn = 0;
        while (i < str2.length) {
            if (str2[cn] == str2[i - 1]) {
                next[i++] = ++cn;
            } else if (cn > 0) { // 说明i-1位置是有最大相同字串的
                cn = next[cn];
            } else { // cn退到最开始的位置都不相等。说明当前i位置压根没有最大相同字串。
                next[i++] = 0;
            }
        }
        return next;
    }
}
```



## **next数组：**

> <img src="D:/%E4%BD%A0%E5%A5%BDJava/1705.jpg" style="zoom: 20%;" />
>
> next数组里面记录的是什么，next数组里面记录的是**当前位置（i）之前**（不包括当前位置）**最大的相同子串的长度**（对两个子串的要求是：第一个子串以0下标开始，第二个子串以**当前位置的前一个位置(i - 1)**结尾，并且子串的长度不可以等于当前位置之前的整个串的长度（**[0 , i-1]**））
>
> next数组的规定：第一个-1，第二个是0.





## **分析一下整个KMP算法的算法流程：**

> ![](D:/%E4%BD%A0%E5%A5%BDJava/1706.jpg)
>
> 开始的时候，i指针与j指针一一对应的分别遍历str1与str2，当不一样的时候，拿到 j 位置所对应的next数组的值z，也就是A部分 == B == C，**j 位置直接指向 z（因为A == B ，B== C 所以 A == C,所以前部分不需要比对，因为一定相等）** ，然后继续往后比对 i 位置与 j 位置的值循环往复。特殊的情况是：当 j 指向0位置的时候，还没有比对上，那就让 i++ 继续比对下一个位置，说明没有一个是合适的。
>
> 
>
> 跳过的中间的部分没有一个是合适的所以就直接舍弃了，直接跳到B部分进行比对。
>
> 假设中间的一部分有等于str2的，那么
>





## **创建next数组的流程**

> <img src="D:/%E4%BD%A0%E5%A5%BDJava/1707.jpg" style="zoom:25%;" />
>
> 当前来到i位置，i位置之前的最大相同子串的长度就是A == B，然后 i++，来到当前的 i 位置，现在我只需要判断
>
> **str2[ j ]** 是否等于 **str2[ i - 1 ]**  即可。如果这两个位置相同，那么就让A，B吞进去就可以了。
>
> <img src="D:/%E4%BD%A0%E5%A5%BDJava/1708.jpg" style="zoom:25%;" />
>
> 如果这两个位置不同，就寻找 j 位置之前的最大相同字串 C ，D 。A中的C，D分别映射着B中的C' ， D‘，（C == D == C' == D' ）。然后 j 指向 next[ j ] 位置。判断此时 str2[ j ] 是否等于 str2[ i - 1] .如果等于的话C , D'吞进去接可以了。



## 时间复杂度分析：

### 创建next数组的时间复杂度分析

> ```java
> 	private static int[] makeNextArray(char[] str2) {
>         if (str2.length < 2) {
>             return new int[]{-1};
>         }
>         int[] next = new int[str2.length];
>         next[0] = -1;
>         next[1] = 0;
>         int i = 2;
>         int cn = 0;
>         while (i < str2.length) {
>             if (str2[cn] == str2[i - 1]) {
>                 next[i++] = ++cn;
>             } else if (cn > 0) { // 说明i-1位置是有最大相同字串的
>                 cn = next[cn];
>             } else { // cn退到最开始的位置都不相等。说明当前i位置压根没有最大相同字串。
>                 next[i++] = 0;
>             }
>         }
>         return next;
>     }
> ```
>
> i 是没有回退的，是一步一步线性向前的。 cn是有回退的增长。所以分析时间复杂度的时候单独分析cn不好分析。
>
> ![](D:/%E4%BD%A0%E5%A5%BDJava/1709.jpg)

### kmp算法时间复杂度分析

> 
>
> ```java
> public static int getIndexOf(String s1, String s2) {
>         if (s1 == null || s2 == null || s1.length() < 1 || s2.length() < 1 || s2.length() > s1.length()) {
>             return -1;
>         }
>         char[] str1 = s1.toCharArray();
>         char[] str2 = s2.toCharArray();
> 
>         int[] next = makeNextArray(str2);
> 
>         int x = 0;
>         int y = 0;
> 
>         while (x < str1.length && y < str2.length) {
>             /**
>              * 跳出循环分三种情况
>              * 1：当 x == str1.length && y != str2.length ==> 没有在str1中找到str2.
>              * 2：当 x == str1.length && y == str2.length ==> 在最后的时候正好找到了。.
>              * 3：当 x != str1.length && y == str2.length ==> 在前面就找到了.
>              */
>             if (str1[x] == str2[y]) {
>                 x++;
>                 y++;
>             } else if (next[y] == -1) { // y == 0 ， 说明x所在的位置没有一个跟他匹配成功的，那就x++,继续向下找吧。
>                 x++;
>             } else {
>                 y = next[y]; // 直接来到next[y]位置与x位置进行比较。
>             }
>         }
>         return y == str2.length ? x - y : -1;
>     }
> ```
>
> x 是没有回退的，是一步一步线性向前的。 y是有回退的增长。所以分析时间复杂度的时候单独分析y不好分析。
>
> 跟上面是同样的分析方法。
