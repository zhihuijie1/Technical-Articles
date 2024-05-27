**输入正数数组costs、正数数组profits、正数K和正数M**
**costs[i]表示i号项目的花费**
**profits[i]表示i号项目在扣除花费之后还能挣到的钱(利润)**
**K表示你只能串行的最多做k个项目**
**M表示你初始的资金**
**说明：每做完一个项目，马上获得的收益，可以支持你去做下一个项目，不能并行的做项目。**
**输出：最后获得的最大钱数**



**注意：（1）只能串行，不可以并行。（2）一个项目做一次之后不可以做第二次。**

![1221](D:/%E4%BD%A0%E5%A5%BDJava/1221.png)

我们要的肯定是：**成本即小于W利润又是最高的。**注意陈本小于W即可，不需要成本最低利润最高。

举个例字：W == 10     【1，5】【9，20】【6，16】这里面【1，5】成本最低是1但是利润不高。最后W = 10 + 5 == 15.

但是【9，20】是最好的，最后W = 10+20 = 30。

**创建一个小根堆（根据成本）再创建一个大根堆（根据利润），在小根堆中，只要是成本低于W的，我们就弹出来放入大根堆中。此时大根堆中堆定元素就是：成本即小于W利润又是最大的。所以这个是我们的首选。**

![](D:/%E4%BD%A0%E5%A5%BDJava/1222.png)





![](D:/%E4%BD%A0%E5%A5%BDJava/1223.png)

​	

```java
package algorithmbasic.class15;

import java.util.Comparator;
import java.util.PriorityQueue;

public class IPO {
    // 最多K个项目
    // W是初始资金
    // Profits[] Capital[] 一定等长
    // 返回最终最大的资金

    public static int findMaximizedCapital(int K, int W, int[] Profits, int[] Capital) {
        //小根堆(根据成本排序)
        PriorityQueue<Program> minCostQ = new PriorityQueue<>(new MinCostComparator());
        //大根堆(根据利润排序)
        PriorityQueue<Program> maxProfitQ = new PriorityQueue<>(new MaxProfitComparator());
        //将利润与成本封装成一个一个的项目。
        for (int i = 0; i < Profits.length; i++) {
            minCostQ.add(new Program(Profits[i], Capital[i]));
        }
        for (int i = 0; i < K; i++) {
            while (!minCostQ.isEmpty() && minCostQ.peek().Capital < W) { //!minCostQ.isEmpty()别忘了写
                maxProfitQ.add(minCostQ.poll());
            }
            if(maxProfitQ.isEmpty()) {
                return W;
            }
            W += maxProfitQ.poll().Profit;//小心空指针异常。
        }
        return W;
    }

    public static class Program {
        int Profit;//利润
        int Capital;//成本

        public Program(int profit, int capital) {
            Profit = profit;
            Capital = capital;
        }
    }

    public static class MinCostComparator implements Comparator<Program> {
        @Override
        public int compare(Program o1, Program o2) {
            return o1.Capital - o2.Capital;
        }
    }

    public static class MaxProfitComparator implements Comparator<Program> {
        @Override
        public int compare(Program o1, Program o2) {
            return o2.Profit - o1.Profit;
        }
    }
}
```



**注意比较器最好是固定不变的，最好不要向自己定义的比较类里面传参数。因为随着自己穿的参数的变化，比较器的比较方法也会发生变化，这样使用自己定义的比较类的容器还需要重建，非常的麻烦。**