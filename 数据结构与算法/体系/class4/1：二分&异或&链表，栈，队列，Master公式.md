[TOC]



# @1：对数器：



**想验证什么想测什么就在对数器里测，在对数器里写**



```java
import java.util.Arrays;

public class Duishuqi {
--------------------------------------------------------------------------------------
    //插入排序
    public static void slectSort(int[] array) {
        if(array == null || array.length == 1) {
            return;
        }
        // 0~1
        // 0~2
        // 0~3
        // 0~i
        // 0~n
        for (int i = 1; i < array.length; i++) {
            int j = i-1;
            while(j >= 0 && array[j+1] < array[j]) {
                swap(array,j,j+1);
                j--;
            }
        }
    }
    public static void swap(int[]array,int i,int j) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
--------------------------------------------------------------------------------------
    //系统自带的排序
    public static void comparator(int[] arr) {
        Arrays.sort(arr);
    }
--------------------------------------------------------------------------------------
    public static void main(String[] args) {
        int testTime = 500000; //测试次数
        int maxSize = 100; //随机数组的长度：[0~100]
        int maxValue = 100; //产生的数的大小范围是[-100,100]
        Boolean succeed = true;
        //产生testTime个随机数组进行测试，每次都利用这些随机数组有进行比对
        for (int i = 0; i < testTime; i++) {
            int[] arr1 = generateRandomArray(maxSize, maxValue);
            //产生长度也随机，数值也随机的数组
            int[] arr2 = copyArray(arr1);
            // 利用arr1来测试方法一
            slectSort(arr1);
            // 利用arr2来测试方法二
            comparator(arr2);
            //测试一下两个方法操作后的结果
            if(!isEqual(arr1,arr2)) {
                succeed = false;
            }
        }
        System.out.println(succeed ? "Nice!" : "Fucking fucked!");
    }
--------------------------------------------------------------------------------------
    public static int[] copyArray(int[] arr) {
        if (arr == null) {
            return null;
        }
        int[] res = new int[arr.length];
        for (int i = 0; i < arr.length; i++) {
            res[i] = arr[i];
        }
        return res;
    }
--------------------------------------------------------------------------------------
    public static boolean isEqual(int[] arr1, int[] arr2) {
        if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
            return false;
        }
        if (arr1 == null && arr2 == null) {
            return true;
        }
        if (arr1.length != arr2.length) {
            return false;
        }
        for (int i = 0; i < arr1.length; i++) {
            if (arr1[i] != arr2[i]) {
                return false;
            }
        }
        return true;
    }

--------------------------------------------------------------------------------------
    public static int[] generateRandomArray(int maxSize, int maxValue) {
        /**
         * Math.random() : 等概率产生[0,1)之间的小数
         * Math.random()*N : 等概率产生[0,N)之间的小数
         * (int)Math.random()*(N+1) : 等概率产生[0,N]之间的整数
         */
        int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
        //保证随机产生的数组的长度在[0,maxSize]
        for (int i = 0; i < arr.length; i++) {
            arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
            //[0,100] - [0,100) : 范围是(-100,100]，这样产生的随机数就有正有负，而且范围在 (-100,100]
            //当然这个地方你可以随便写
        }
        return arr;
    }
--------------------------------------------------------------------------------------
}
```



# @2：二分法的详解与扩展

啥时候使用二分：有排他性的东西就使用二分，左右两半有一个有有一个没有，或有一半不确定就使用二分。

二分没必要非得有序



### 1）在一个有序数组中，找某个数是否存在





![](D:/%E4%BD%A0%E5%A5%BDJava/573.png)



**每次对半砍所以时间复杂对是log2的N**

**代码：**

```java
     /*
     * 在一个有序数组中，找某个数是否存在
     */
    public static boolean exist(int[] sortedArr, int num) {
        if(sortedArr == null || sortedArr.length == 0) {
            return false;
        }
        int L = 0;
        int R = sortedArr.length-1;
        int mid = -1;
        while(L <= R) {
            mid = L +((R - L) >> 1); //重点
            if(sortedArr[mid] > num) {
                R = mid - 1;
            }else if(sortedArr[mid] < num) {
                L = mid + 1;
            }else {
                return true;
            }
        }
        return false;
    }
```



- **重点总结：**

- ```java
  mid = L +((R - L) >> 1);
  ```

- **之所以这么写是因为如果用 mid = (L+R) / 2会出现越界的可能**

- **比如：L = 20亿   R = 30亿   L+R == 50亿  整形越界**

- **mid = L +((R - L) / 2); 这样就不会越界，但是除法比加法与位移慢所以采用位移**



### 2）在一个有序数组中，找>=某个数最左侧的位置



### 3）局部最小值问题







# @3：异或运算的性质与扩展



**注意：异或运算就是无进位相加运算** 

1）0^N == N    N^N == 0

2）异或运算满足**交换律和结合率** 但是不满足**分配律**



### 1）不用额外变量交换两个数



![](D:/%E4%BD%A0%E5%A5%BDJava/574.png)

```java
a = a ^ b;
b = a ^ b;
a = a ^ b;
```



```java
public static void swap(int[] arr, int i, int j) { // i与j必须不一样
		arr[i] = arr[i] ^ arr[j];
		arr[j] = arr[i] ^ arr[j];
		arr[i] = arr[i] ^ arr[j];
}
```

**注意：使用这种交换方式的前提条件是两个的数据的内存位置必须不一样，否则就自己把自己刷成0了。**

```java
//a = 甲, b = 甲 (a与b指向同一块内存)
 a = a ^ b;  // a = 甲 ^ 甲 = 0; 把这块内存中的信息改了
 b = a ^ b;  // b = 0 ^ 0;
 a = a ^ b;  // a = 0 ^ 0;
```



### 2）一个数组中有一种数出现了奇数次，其他数都出现了偶数次，怎么找到这一个数

![](D:/%E4%BD%A0%E5%A5%BDJava/575.png)

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashMap;
import java.util.HashSet;

public class demo3 {
--------------------------------------------------------------------------------------
     /**
     * 一个数组中有一种数出现了奇数次，其他数都出现了偶数次，怎么找到这一个数
     */
    public static int find(int[] array) {
        int err = array[0];
        for (int i = 1; i < array.length; i++) {
            err ^= array[i];
        }
        return err;
    }
--------------------------------------------------------------------------------------
    //对照用的方法
    public static int find2(int[] array) {
        HashMap<Integer,Integer> hashMap = new HashMap<>();
        for (int i = 0; i < array.length; i++) {
            if(hashMap.containsKey(array[i])) {
                hashMap.put(array[i],hashMap.get(array[i])+1);
            }else{
                hashMap.put(array[i],1);
            }
        }
        for (int nub : hashMap.keySet()) {
            if(hashMap.get(nub) % 2 != 0) {
                return nub;
            }
        }
        return -1;
    }
--------------------------------------------------------------------------------------
    public static void main(String[] args) {
        int maxLen = 30; //数组中元素的种数
        int maxValue = 100; //随机产生的数的最大取值【-100，100】
        int testTime = 10000;//测试的次数
        for(int i = 0;i < testTime; i++) {
            int[] arr = randomArray(maxLen,maxValue);
            //System.out.println(Arrays.toString(arr));
            //System.out.println();
            int[] arr2 = arrayCopy(arr);
            int nub1 = find(arr);
            int nub2 = find(arr2);
            if(nub1 != nub2) {
                System.out.println("false");
                break;
            }
        }
        System.out.println("true");
    }
--------------------------------------------------------------------------------------
    public static int[] randomArray(int maxLen, int maxValue) {
        ArrayList<Integer> list = new ArrayList<>();
        //真命天子
        int k = (int)(Math.random()*(maxValue+1)); //[0,maxValue]
        int kSize = (int)(Math.random()*(maxValue+1)); // 真命天子出现了多少次 [0,100]之间的奇数次
        while(kSize % 2 == 0) {
            kSize = (int)(Math.random()*(maxValue+1));
        }
        for (int i = 0; i < kSize; i++) {
            list.add(k);
        }
        maxLen--;
        HashSet<Integer> hashSet = new HashSet<>();
        hashSet.add(k);
        while(maxLen != 0) {
            int v = (int)(Math.random()*(maxValue+1)); //[0,maxValue]
            if(hashSet.contains(v)) {
                v = (int)(Math.random()*(maxValue+1));
            }
            hashSet.add(v);
            int Size = (int)(Math.random()*(maxValue+1))+1; // 其他数出现了多少次 [0,100]之间的奇数次
            while(Size % 2 != 0) {
                Size = (int)(Math.random()*(maxValue+1));
            }
            while(Size > 0) {
                list.add(v);
                Size--;
            }
            maxLen--;
        }
        int[] arr = new int[list.size()];
        Integer[] array = list.toArray(new Integer[list.size()]);

        for (int i = 0; i < array.length; i++) {
            int j = (int)(Math.random()*(array.length));
            swap(array,i, j);
        }
        for (int i = 0; i < array.length; i++) {
            arr[i] = array[i];
        }
        return arr;
    }
--------------------------------------------------------------------------------------
    public static void swap(Integer[] array,int i,int j) {
        Integer temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
--------------------------------------------------------------------------------------
    public static int[] arrayCopy(int[] arr) {
        if(arr == null) {
            return null;
        }
        int[] array = new int[arr.length];
        for(int i = 0; i < arr.length; i++) {
            arr[i] = array[i];
        }
        return arr;
    }
}
```



### 3）怎么把一个int类型的数，提取出最右侧的1来



![](D:/%E4%BD%A0%E5%A5%BDJava/576.png)





- **分析：**
- **假设一个整形a的二进制写法：01101110010000，我们想取出最右侧的1，即00000000010000，我们需要将这个1左边都变成0，右边也都变成0（本身就是0）；那我们可以取反**
- **01101110010000**
- **10010001101111（取反后的）**
- **这时候如果进行&运算，那么这个1左边都变成0，但是自己本身也会变成0，所以我们再加1**
- **10010001110000（取反后的，再加1）这样再进行&运算就会得到我们想要的结果。之所以进行加1就是让他再回去，回到取反之前这个1右边的形式**



```java
public class demo4 {
-------------------------------------------------------------------------------------- 
    // 把一个int类型的数，提取出最右侧的1来
    public static void find(int a) {
        int b = a & (~a+1);
        sout(b);
    }
--------------------------------------------------------------------------------------    // 打印出一个整形的二进制形式
    public static void sout(int b) {
        for(int i = 31; i >= 0; i--) {
            System.out.print((b & 1 << i) == 0 ? "0" : "1");
        }
    }
--------------------------------------------------------------------------------------
    public static void main(String[] args) {
        int a = 48;
        find(a);
    }
}
```



### 4）一个数组中有两种数出现了奇数次，其他数都出现了偶数次，怎么找到这两个数



![](D:/%E4%BD%A0%E5%A5%BDJava/577.png)

![](D:/%E4%BD%A0%E5%A5%BDJava/578.png)



**分析：**

**一个数组中，有两种数(a，b)出现了奇数次，剩余的数字出现了偶数次，那么对所有的数进行异或，最后得到的将是a ^ b,而我们想要分别找到这两个数。所以我们对其进行分组（根据差异化，a ^ b二进制形式最右侧1的位置，因为根据位运算相同为0，不同为1，说明a与b在最右侧为1的位置处不同），那我们根据最右侧是否为1这个标准进行分组，一组里面肯定有a一组里面肯定有b，然后分别对两组数据中的数进行异或即可得到答案。**



```java
public static void TimesNum(int[] arr) {
        int err = arr[0];
        for (int i = 1; i < arr.length; i++) {
            err = err ^ arr[i]; // 最后相当于 err = a ^ b
        }
        //寻找err最右边第一个1的位置
        int index = err & (~err+1);
        //之所以会出现1，说明a 与 b在这个位置不一样，出现了差异化，
        // 根据差异化将数组分成两组，一组有a,另一组有b
        int err2 = 0;
        int i = 0;
        while((arr[i] & index) != 0) {
            err2 = err2 ^ arr[i];
            i++;
        }
        System.out.println(err2 + " " + (err2 ^ err));
 }
```

### 5）一个数组种有一种数出现K次，其他数都出现了M次， M > 1,K < M , 找到出现了K次的数，时间复杂度是O（n）,空间复杂度是O（1）

```java
import java.util.Arrays;
import java.util.HashMap;
import java.util.HashSet;

public class demo5 {

    public static int onlyKTimes(int[] array,int k, int m) {
        int[] arr = new int[32];
        int ans = 0;
        for(int i = 0; i < array.length; i++) {
            for (int j = 0; j < 32; j++) {
                if((array[i] & (1 << j)) != 0) {
                    arr[j]++;
                }
            }
        }
        for (int i = 0; i < 32; i++) {
            if(arr[i] % m == k) {
                ans = (1 << i) | ans;
            }
        }
        return ans;
    }
    //对比方法
    public static int onlyTimes2(int[] array,int k, int m) {
        HashMap<Integer,Integer> hashMap = new HashMap<>();
        for (int i = 0; i < array.length; i++) {
            if(hashMap.containsKey(array[i])) {
                hashMap.put(array[i],hashMap.get(array[i])+1);
            }else {
                hashMap.put(array[i],1);
            }
        }
        for (int nub : hashMap.keySet()) {
            if(hashMap.get(nub) == k) {
                return nub;
            }
        }
        return -1;
    }

    // 对数器
    public static void main(String[] args) {
        int testTime = 100;
        int nubKinds = 10; //一个数组中数的种类个数
        int maxValues = 50; //[-100 , 100]
        for (int i = 0; i < testTime; i++) {
            int k = (int)(Math.random()*maxValues+1);
            int m = (int)(Math.random()*maxValues)+10;
            while(m <= k) {
                m = m = (int)(Math.random()*maxValues+10);
            }
            int[] arr1 = randomArray(nubKinds,maxValues,k,m);
            int[] arr2 = copyOf(arr1);

            int a = onlyKTimes(arr1,k,m);
            int b = onlyTimes2(arr2,k,m);
            if(a != b) {
                System.out.println("error");
                System.out.println(Arrays.toString(arr1));
                break;
            }
        }
        System.out.println("true");
    }
    public static int[] randomArray(int nubKinds,int maxValues,int k,int m) {
        HashSet<Integer> set = new HashSet<>();
        int[] arr = new int[k + m*(nubKinds-1)];
        int knub = (int)(Math.random()*(maxValues+1)); //真命天子
        set.add(knub);
        int i = 0;
        for (i = 0; i < k; i++) {
            arr[i] = knub;
        }
        nubKinds--;
        while(nubKinds > 0) {
            int nub =(int)(Math.random()*(maxValues+1));
            while(set.contains(nub)) {
                nub =(int)(Math.random()*(maxValues+1));
            }
            set.add(nub);
            for (int j = 0; j < m; j++) {
                arr[i] = nub;
                i++;
            }
            nubKinds--;
        }
        for (int j = 0; j < arr.length; j++) {
            int y = (int)(Math.random()*arr.length);
            swap(arr,j,y);
        }
        return arr;
    }
    public static void swap(int[] array,int i,int j) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
    public static int[] copyOf(int[] arr) {
        if(arr == null) {
            return null;
        }
        int[] array = new int[arr.length];
        for (int i = 0; i < arr.length; i++) {
            array[i] = arr[i];
        }
        return array;
    }

}

```

# @4：单双链表、栈和队列、递归和Master公式、哈希表和有序表的使用和性能



### 1）：用双链表实现栈与队列



```java
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;
import java.util.Stack;

public class demo2 {
    /**
     * 链表实现栈与队列
     * 以及其对数器的实现
     */
    public static class Node<T> {
        public T value;
        public demo2.Node<T> last;
        public demo2.Node<T> next;

        public Node(T data) {
            value = data;
        }
    }

    /**
     * 用双向链表实现一个队列既可以头插也可以尾插，既可以头出也可以尾出
     */
    public static class DoubleEndsQueue<T> {
        public Node<T> head;
        public Node<T> tail;
        // 从头部进行插入
        public void addFromHead(T value){
            Node<T> node = new Node<>(value);
            if(isEmpty()) {
                head = node;
                tail = node;
            }else {
                head.last = node;
                node.next = head;
                head = node;
            }
        }
        //从尾部进行插入
        public void addFromBottom(T value){
            Node<T> node = new Node<>(value);
            if(isEmpty()) {
                head = node;
                tail = node;
            }else {
                node.last = tail;
                tail.next = head;
                tail = node;
            }
        }
        // 从头部进行删除（重点注意一下这个）
        // 保证删除完的节点与tail和head没有丝毫联系
        public T popFromHead(){
            if(isEmpty()) {
                return null;
            }
            Node<T> cur = head; // cur保存要删除的节点
            if(tail == head) {
                tail = null;
                head = null;
            }else {
                head = head.next;
                head.last = null;
                cur.next = null;
            }
            return cur.value;
        }
        //从尾部开始删除节点
        public T popFromBottom(){
            if(isEmpty()) {
                return null;
            }
            Node<T> cur = tail;
            if(head == tail) {
                head = null;
                tail = null;
            }else {
                tail = tail.last;
                tail.next = null;
                cur.last = null;
            }
            return cur.value;
        }

        public boolean isEmpty(){
            if(head == null) {
                return true;
            }
            return false;
        }
    }

    /**
     * 用队列来实现栈的功能
     */
    public static class MyStack<T> {
        private DoubleEndsQueue<T> queue;
        public MyStack(){
            queue = new DoubleEndsQueue<T>();
        }
        public void push(T value) {
            queue.addFromHead(value);
        }
        public T pop() {
            return queue.popFromHead();
        }
        public boolean isEmpty(){
            return queue.isEmpty();
        }
    }

    /**
     * 用双向链表实现队列
     */

    public static class MyQueue<T> {
        private DoubleEndsQueue<T> queue;
        public MyQueue() {
            queue = new DoubleEndsQueue<>();
        }
        public void push (T value) {
            queue.addFromHead(value);
        }
        public T poll() {
            return queue.popFromBottom();
        }
        public boolean isEmpty() {
            return queue.isEmpty();
        }
    }
    /**
    对数器
    */
    public static void main(String[] args) {
        int testTimes = 10000; //测试次数
        int oneTimeTestNum = (int)((Math.random()*51)+50); // [50,100] 一次测试的要产生的数目
        int maxValue = 100; // 每个数的最大取值
        for (int i = 0; i < testTimes; i++) {
            MyStack<Integer> myStack = new MyStack<>();
            MyQueue<Integer> myQueue = new MyQueue<>();
            Stack<Integer> stack = new Stack<>();
            Queue<Integer> queue = new LinkedList<>();
            for (int j = 0; j < oneTimeTestNum; j++) {
                int value = (int)(Math.random()*maxValue); // 产生的数的范围：[0,99]
                if(myStack.isEmpty() && stack.isEmpty()) {
                    stack.push(value);
                    myStack.push(value);
                }else {
                    if(Math.random() < 0.5) {
                        stack.push(value);
                        myStack.push(value);
                    }else {
                        int a1 = stack.pop();
                        int a2 = myStack.pop();
                        if(!isEmpty(a1,a2)) {
                            System.out.println("栈有误");
                            break;
                        }
                    }
                }
                int numq = (int) (Math.random() * value);
                if (queue.isEmpty()) {
                    myQueue.push(numq);
                    queue.offer(numq);
                } else {
                    if (Math.random() < 0.5) {
                        myQueue.push(numq);
                        queue.offer(numq);
                    } else {
                        if (!isEmpty(myQueue.poll(), queue.poll())) {
                            System.out.println("队列有误");
                            break;
                        }
                    }
                }
            }
        }
        System.out.println("正确");
    }
    public static boolean isEmpty(Integer o1,Integer o2) {
        if(o1 == null && o2 == null) {
            return true;
        }
        if(o1 == null || o2 == null) {
            return false;
        }
        return o1.equals(o2);
    }

}

```



### 2)：在链表中删除指定值的节点



```java
public class demo1 {
    /**
     * 在链表中删除指定值的节点
     */
    public static class Node {
        public int value;
        public Node next;

        public Node(int data) {
            this.value = data;
        }
    }
    public static Node removeValue(Node head, int num) {
        //如果第一个值就与num一样，那头节点就往后移动。直到头节点指向的第一个的值不是num
        while(head.value == num) {
            head = head.next;
        }
        //两个指针，pre：慢 cur：快 cur走过的地方不是num，那pre就跟上cur，
        // 如果cur指向的地方是num，那pre.next = cur.next;
        Node pre = head;
        Node cur = head;
        while(cur != null) {
            if(cur.value == num) {
                pre.next = cur.next;
            }else{
                pre = cur;
            }
            cur = cur.next;
        }
        return head;
    }
}

```



### 3)：用环形数组实现栈和队列  



**左神版 :**

![](D:/%E4%BD%A0%E5%A5%BDJava/588.png)

```java

public class demo3 {
    public static class MyQueue {
        int[] arr;
        int end = 0; // 用来放数的位置
        int begin = 0; // 用来取数的位置
        int size = 0; // 记录数组中数据元素的个数
        public MyQueue(int limit) {
            arr = new int[limit];
        }
        public  void push(int val) {
            if(size == arr.length) {
                throw new RunningtimeException("数组满了");
            }
            arr[end] = val;
            size++;
            end++;
            if(end >= arr.length) {
                end = 0;
            }
        }
        public int poll() {
            if(size == 0) {
                throw new EmptyQueueExpection("空队列异常");
            }
            int ans = arr[begin];
            size--;
            begin++;
            if(begin >= arr.length) {
                begin = 0;
            }
            return ans;
        }
    }
}
```



**比特版：**



```java
package demo;

import java.util.Arrays;

//循环队列
class MyCircularQueue {
    private int[] elem;
    private int front; // 下标
    private int rear;

    public MyCircularQueue(int k) {
        this.elem = new int[k];
    }
    //入栈
    /**
     * 思路：rear是队尾，去寻找要插入的空间，注意rear = (rear+1) % elem.length
     * 按这个公式去走才可以成环
     * 当栈满的时候就不可以再入栈了，而判断栈满的方法有2个：一个是用计数的方法，另一个是浪费一个空间
     * 当然你也可以扩容*/

    public boolean enQueue(int value) {
        //栈满
        if(this.isFull()){
            //扩容
            Arrays.copyOfRange(elem , rear ,rear + elem.length / 2);
            //你也可以之间return false;
            //return false;
        }
        elem[rear] = value;
        rear = (rear+1) % elem.length;
        return true;
    }


    //出栈
    public boolean deQueue() {
        if(this.isEmpty()){
            return false;
        }
        this.front = (front + 1) % elem.length;
        return true;
    }


    //瞄一眼首元素
    public int Front() {
        if(this.isEmpty()){
            return -1;
        }
        return elem[front];
    }
    
    public int Rear() {
        if(this.isEmpty()){
            return -1;
        }
        if(rear == 0){ //这个地方注意一下
            return elem[elem.length - 1];
        }
        return elem[rear - 1];
    }
    
    public boolean isEmpty() {
        if(rear == front){
            return true;
        }
        return false;
    }
    
    public boolean isFull() {
        if((rear+1) % elem.length == front){
            return true;
        }
        return false;
    }
}
```



### 4)：实现有getMin功能的栈，时间复杂度O(1)



我们找到栈中的最小元素，不管是压入元素还是弹出元素，都可以快速的找到栈中的最小元素。

普通的方法是遍历栈然后找到其中的最小元素，每次压入元素与弹出元素都需要遍历栈

**但是这里采用了一个比较新的方法，其时间复杂度是O(1)**

<img src="D:/%E4%BD%A0%E5%A5%BDJava/589.png" style="zoom:50%;" />

**分析：**

左边是常规的数据栈，右边是最小栈。

最小栈保证其栈顶的元素一定是最小的，而且保证最小栈的高度与数据栈的高度相等。

刚开始压入元素的时候，两个栈都是空的，所以两个栈中都压入数据，当栈不为空的时候，再往栈中压入数据，数据栈直接压入，**但是最小栈要先判断一下，如果待压入的数据大于最小栈的栈顶元素，那么继续压入当前最小栈的栈顶元素，保证与数据栈高度一致。如果待压入的数据小于最小栈的栈顶元素。那么就在最小栈中压入这个数据。** **（这样操作的目的是：与数据栈栈顶元素对齐的最小栈的栈顶元素一定是当前数据栈中的最小元素）**



```java
public static class MyStack1 {
        Stack<Integer> stack1; //不同的栈
        Stack<Integer> stack2; //单独设计的栈

        public MyStack1() {
            stack1 = new Stack<>();
            stack2 = new Stack<>();
        }

        //保证stack1与stack2的高度一样
        public void push(int newNum) {
            if (stack1.isEmpty()) {
                stack1.push(newNum);
                stack2.push(newNum);
            } else {
                stack1.push(newNum);
                if (newNum < stack2.peek()) {
                    stack2.push(newNum);
                } else {
                    stack2.push(stack2.peek());
                }
            }
        }

        public int pop() {
            if (stack1.isEmpty()) {
                throw new EmptyStackException();
            }
            stack2.pop();
            return stack1.pop();
        }

        public int getmin() {
            if (stack1.isEmpty()) {
                throw new EmptyStackException();
            }
            return stack2.peek();
        }
    }
```



### 5)：两个栈实现队列



![](D:/%E4%BD%A0%E5%A5%BDJava/590.png)

- **分析 : **
- **注意：当stackPop栈中有数据的时候就别把stackPush中的数据导入到stackPop中，以免数据的顺序发生错误。**
- **在stackPush中放数据，在stackPop中弹数据，只有stackPop中的数据全弹完了，stackPop为空了再将stackPush中的数据导入到stackPop中，而且是将stackPush中的数据全部导完才可以，以免顺序发生错误**



```java
public class demo6 {
    /**
     * 两个栈实现队列
     */
    public static class  TwoStackQueue {
        Stack<Integer> stackPush;
        Stack<Integer> stackPop;

        public TwoStackQueue() {
            stackPush = new Stack<>();
            stackPop = new Stack<>();
        }

        public void offer(int value) {
            stackPush.push(value);
        }
        public int poll() {
            if(stackPop.isEmpty()) {
                stackPushToPop();
            }
            return stackPop.pop();
        }
        public void stackPushToPop() {
            while(!stackPush.isEmpty()) {
                stackPop.push(stackPush.pop());
            }
        }
        public int peek() {
            if(stackPop.isEmpty()) {
                stackPushToPop();
            }
            return stackPop.peek();
        }
    }
}
```



### 6)：两个队列实现栈



**两个队列来回倒，queue队列一致有数，helper队列一直没数，就是一个辅助**



```java
import java.util.LinkedList;
import java.util.Queue;
import java.util.Stack;

public class demo1 {
    /**
     * 两个队列实现栈
     */
    public static class TwoQueueStack<T> {
        Queue<T> queue;
        Queue<T> helper;

        public TwoQueueStack () {
            queue = new LinkedList<>();
            helper = new LinkedList<>();
        }
--------------------------------------------------------------------------------------
        public void push(T value) {
            queue.offer(value);
        }
--------------------------------------------------------------------------------------
        public T poll() {
            if(queue.isEmpty()) {
                throw new EmptyStackException("空栈异常");
            }else {
                while(queue.size() > 1) {
                    helper.offer(queue.poll());
                }
                T ans = queue.poll();
                Queue<T> temp = queue;
                queue = helper;
                helper = temp;
                return ans;
            }
        }
--------------------------------------------------------------------------------------
        public T peek() {
            if(queue.isEmpty()) {
                throw new EmptyStackException("空栈异常");
            }else {
                while(queue.size() > 1) {
                    helper.offer(queue.poll());
                }
                T ans = queue.peek();
                helper.offer(queue.poll());
                Queue<T> temp = queue;
                queue = helper;
                helper = temp;
                return ans;
            }
        }
        public boolean isEmpty() {
            return queue.isEmpty();
        }
    }
--------------------------------------------------------------------------------------     // 对数器：
    public static void main(String[] args) {
        int testTime = 100000;
        int maxValue = 10000;
        TwoQueueStack<Integer> twoQueueStack = new TwoQueueStack<>();
        Stack<Integer> stack = new Stack<>();
        for (int i = 0; i < testTime; i++) {
            if(!twoQueueStack.isEmpty() && !stack.isEmpty()) {
                double a = Math.random();
                if(a < 0.25) {
                    int num = (int)(Math.random() * maxValue); //[0,999]
                    stack.push(num);
                    twoQueueStack.push(num);
                }else if(a < 0.5){
                    if(!twoQueueStack.poll().equals(stack.pop())) {
                        System.out.println("2:错误");
                        break;
                    }
                }else if(a < 0.75) {
                    if(!twoQueueStack.peek().equals(stack.peek())) {
                        System.out.println("3:错误");
                        break;
                    }
                }else {
                    if(twoQueueStack.isEmpty() != stack.isEmpty()) {
                        System.out.println("4:错误");
                        break;
                    }
                }
            }else {
                if(twoQueueStack.isEmpty() && stack.isEmpty()) {
                    int num = (int)(Math.random() * maxValue); //[0,999]
                    twoQueueStack.push(num);
                    stack.push(num);
                }else {
                    System.out.println("1:错误");
                    break;
                }
            }
        }
        System.out.println("正确");
    }
}
```



总结：

**== 的作用：**
　　基本类型：比较的就是值是否相同
　　引用类型：比较的就是地址值是否相同
**equals 的作用:**
　　引用类型：默认情况下，比较的是地址值。
注：不过，我们可以根据情况自己重写该方法。一般重写都是自动生成，比较对象的成员变量值是否相同

对数器中  twoQueueStack.peek().equals(stack.peek()) 要用equals来比较，因为我们想比较的是值而非地址，Integer中已经重写了equals方法，所以直接调用即可。



### 7：用递归行为得到数组中的最大值，并用master公式来估计时间复杂度



**用master公式来估计递归的时间复杂度，因为特殊一点的递归时间复杂度不好求，所以引进了Master公式**



**master公式只适用于子问题规模一致的递归。**



![](D:/%E4%BD%A0%E5%A5%BDJava/592.png)



- **注意master公式最后加的O(N^d)是整个语句中除了递归的语句外，其他语句的时间复杂度。而且必须满足是N^d 的形式，如果是logN的形式是不正确的。**
- **N/b：意思是指这个大问题可以均等的分成几个小问题，子问题的规模必须是一致的。a：指均分了几个。**



**举个例子：**

![](D:/%E4%BD%A0%E5%A5%BDJava/593.png)

![](D:/%E4%BD%A0%E5%A5%BDJava/594.png)



![](D:/%E4%BD%A0%E5%A5%BDJava/595.png)



**用递归行为得到数组中的最大值 ：**



```java
public class demo2 {
    /**
     * 用递归行为得到数组中的最大值
     */
    public static int getMax(int[] arr) {
        return process(arr, 0, arr.length-1);
    }

    public static int process(int[] arr, int L, int R) {
        if(L <= R) {
            return arr[L];
        }
        int mid = L+ (R - L) >> 2;
        int leftMax = process(arr, L, mid);
        int rightMax = process(arr, mid+1, R);
        return Math.max(leftMax, rightMax);
    }
}
```



### 8)：哈希表和有序表使用的code展示/hash表中自带的原生类型是按值传递的，自己创建的对象是按地址传递的



**1: 哈希表的增删查改的时间复杂度都是O(1)，这是他最牛的地方。**

2 ：**== 的作用：**
　　基本类型：比较的就是值是否相同
　　引用类型：比较的就是地址值是否相同
**equals 的作用:**
　　引用类型：默认情况下，比较的是地址值。
注：不过，我们可以根据情况自己重写该方法。一般重写都是自动生成，比较对象的成员变量值是否相同



**在hashMap中原生的自带的Integer,String ,Character类等都是按值传递，自己写的对象是按地址传递。所以一大坨的字符串都要存在hashMap中，而一大坨的Student对象则只是保存在hashMap中一个地址，仅占8个字节。也正因为hashMap具有这一特性，所以hashMap中没有重复出值（原生类型的值）**

**例如：String类型的a = "小白"，b = "小白" 如果把a放在hashMap中，因为是安值传递的，hashMap中认为有了”小白“这一字符串了，所以当你hashMap.containsKey(b)时返回的是true.**

**而自己创建的对象是按地址传递的，Student student1 = new Student(100)  ;  Student student2 = new Student(100); 如果把student1放在hashMap中，hashMap中存的是student1的地址，显现hashMap.containsKey(student2)返回的是false**



```java
import java.util.HashMap;
import java.util.HashSet;

public class demo3 {

    static class Student {
        int value;
        public Student(int val) {
            this.value = val;
        }
    }

    public static void main(String[] args) {
        HashMap<String, String> hashMap = new HashMap<>();
        Integer b = 190000;
        Integer a = 190000;
        String abs = new String("你好我是小白");
        String abd = new String("你好我是小白");
        System.out.println(a == b); //比较的是地址
        System.out.println(a.equals(b)); 
        //比较的是值得大小，Integer中有重写equals
        System.out.println(abs == abd);//比较的是地址
        System.out.println(abs.equals(abd));
        //比较的是值得大小，String中有重写equals
        System.out.println("--------------------");

        hashMap.put(abs,"小白");
        System.out.println(hashMap.containsKey(abd));

        System.out.println("--------------------");

        HashMap<Student,String> hashMap2 = new HashMap<>();
        Student student1 = new Student(100);
        Student student2 = new Student(100);
        hashMap2.put(student1,"小白");
        System.out.println(hashMap2.containsKey(student2));
    }
}

/**
false
true
false
true
--------------------
true
--------------------
false
*/
```



```java
        String[] arr1 = new String[]{"小白","小红"};
        String[] arr2 = new String[]{"小白","小红"};
        HashMap<String[],Integer> hashMap3 = new HashMap<>();
        hashMap3.put(arr1,1);
        System.out.println(hashMap3.containsKey(arr2));
        /**
        false
        */
```



**treeMap：也叫有序表，底层由红黑树实现，增删改擦查的时间复杂度是o(logN)**

**treemap中的功能treeMap都能实现，并且还多了介个功能比如得到表中的最小值最大值等。**

**同样在treeMAP中原生的自带的Integer,String ,Character类等都是按值传递，自己写的对象是按地址传递**

**自己写的对象要冲重写比较器，因为treeMap中涉及比较。**









