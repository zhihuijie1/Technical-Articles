# IsPalindromeList

**给定一个单链表的头节点head，请判断该链表是否为回文结构**

# 思路：



```java

// 1：方法一：采用容器的暴力方法，主要是采用一个栈结构。将链表中的数据一个一个的压入栈中，然后一个一个的弹出。这样就
//    做到了将链表逆序过来。然后一个一个的比对。
// 2：方法二: 先找到链表的中点位置，然后将中点位置后面的链表进行倒序连接。然后两边的指针向中间缩，一个一个的比对。
//    最后再把指针的顺序弄回去。
```

# 注意点：



```java

// 1：注意点1：将中点后面的链表反过来，对应图1。
	Node n1 = slow;
        Node n2 = n1.next;
        Node p = null;
        slow.next = null;
        while (n2 != null) {
            p = n2.next;
            n2.next = n1;
            n1 = n2;
            n2 = p;
        }
// 注意这里的p是非常的重要的，他一直记录着n2的下一个位置。下面的写法是错误的，因为n2进入到了死循环。
	Node n1 = slow;
        Node n2 = n1.next;
        slow.next = null;
        while (n2 != null) {
            n2.next = n1;
            n1 = n2;
            n2 = n2.next;
        }
```

图一：

<img src="D:/%E4%BD%A0%E5%A5%BDJava/1140.png" style="zoom: 50%;" />

```java
// 2：注意点2：
        Node n3 = head; // left first
        n2 = n1; // right first
        Boolean ans = true;
        while (n2 != null && n3 != null) {
            if (n3.val != n2.val) {
                ans = false;
                break;
            }
            n2 = n2.next; // from right to mid
            n3 = n3.next; // from left to mid
        }
//      当时链表的长度是奇数时，n3与n2都走向null就停止，当链表的长度时偶数时，n2走向null才停止。
```

图二：

<img src="D:/%E4%BD%A0%E5%A5%BDJava/1141.png" style="zoom:50%;" />



```java
// 注意点3：往回反的时候，也是三个指针一块走。主要原因时中间的指针会变方向。

        n2 = n1.next;
        n1.next = null;
        while (n2 != null) { // ------------------注意点3
            n3 = n2.next;
            n2.next = n1;
            n1 = n2;
            n2 = n3;
        }
```

<img src="D:/%E4%BD%A0%E5%A5%BDJava/1142.png" style="zoom:50%;" />

# 完整代码



```java
package algorithmbasic.class10;

import java.util.Stack;

// 给定一个单链表的头节点head，请判断该链表是否为回文结构
public class IsPalindromeList {

    public static class Node {
        public int val;
        public Node next;

        public Node(int val) {
            this.val = val;
        }
    }

    // 方法一：采用容器的暴力方法
    // 空间复杂度：O(n)
    public static boolean isPalindrome1(Node head) {
        Stack<Integer> stack = new Stack<>();
        Node cur = head;
        // 遍历第一遍，将链表中的数据放入栈中。
        while (cur != null) {
            stack.push(cur.val);
            cur = cur.next;
        }
        // 第二次遍历，将栈中的数据一个一个的弹出来，进行比对。
        cur = head;
        while (cur != null) {
            if (cur.val != stack.pop()) {
                return false;
            }
            cur = cur.next;
        }
        return true;
    }

    // 方法二：
    // 空间复杂度：O(1)
    public static boolean isPalindrome2(Node head) {
        if (head == null || head.next == null) {
            return true;
        }
        // 快慢指针法找中点。
        Node slow = head;
        Node fast = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        // slow:中点
        // 将中点后面的链表反过来。-------------注意点1
        Node n1 = slow;
        Node n2 = n1.next;
        Node p = null;
        slow.next = null;
        while (n2 != null) {
            p = n2.next;
            n2.next = n1;
            n1 = n2;
            n2 = p;
        }

        // 1 -> 2 -> 3 <- 2 <- 1
        // 1 -> 2 -> 2 <- 1
        Node n3 = head; // left first
        n2 = n1; // right first
        Boolean ans = true;
        while (n2 != null && n3 != null) { // ------------------注意点2
            if (n3.val != n2.val) {
                ans = false;
                break;
            }
            n2 = n2.next; // from right to mid
            n3 = n3.next; // from left to mid
        }
        // n1: right first
        // n2: right second
        // n3: right third
        n2 = n1.next;
        n1.next = null;
        while (n2 != null) { // ------------------注意点3
            n3 = n2.next;
            n2.next = n1;
            n1 = n2;
            n2 = n3;
        }
        return ans;
    }
```



# 对数器



```java
public static void printLinkedList(Node node) {
        System.out.print("Linked List: ");
        while (node != null) {
            System.out.print(node.val + " ");
            node = node.next;
        }
        System.out.println();
    }

    public static void main(String[] args) {

        Node head = null;
        printLinkedList(head);
        System.out.print(isPalindrome1(head) + " | ");
        System.out.print(isPalindrome2(head) + " | ");
        //System.out.println(isPalindrome3(head) + " | ");
        printLinkedList(head);
        System.out.println("=========================");

        head = new Node(1);
        printLinkedList(head);
        System.out.print(isPalindrome1(head) + " | ");
        System.out.print(isPalindrome2(head) + " | ");
        //System.out.println(isPalindrome3(head) + " | ");
        printLinkedList(head);
        System.out.println("=========================");

        head = new Node(1);
        head.next = new Node(2);
        printLinkedList(head);
        System.out.print(isPalindrome1(head) + " | ");
        System.out.print(isPalindrome2(head) + " | ");
        //System.out.println(isPalindrome3(head) + " | ");
        printLinkedList(head);
        System.out.println("=========================");

        head = new Node(1);
        head.next = new Node(1);
        printLinkedList(head);
        System.out.print(isPalindrome1(head) + " | ");
        System.out.print(isPalindrome2(head) + " | ");
        //System.out.println(isPalindrome3(head) + " | ");
        printLinkedList(head);
        System.out.println("=========================");

        head = new Node(1);
        head.next = new Node(2);
        head.next.next = new Node(3);
        printLinkedList(head);
        System.out.print(isPalindrome1(head) + " | ");
        Boolean a = isPalindrome2(head);
        System.out.print(a + " | ");
        //System.out.println(isPalindrome3(head) + " | ");
        printLinkedList(head);
        System.out.println("=========================");

        head = new Node(1);
        head.next = new Node(2);
        head.next.next = new Node(1);
        printLinkedList(head);
        System.out.print(isPalindrome1(head) + " | ");
        System.out.print(isPalindrome2(head) + " | ");
        //System.out.println(isPalindrome3(head) + " | ");
        printLinkedList(head);
        System.out.println("=========================");

        head = new Node(1);
        head.next = new Node(2);
        head.next.next = new Node(3);
        head.next.next.next = new Node(1);
        printLinkedList(head);
        System.out.print(isPalindrome1(head) + " | ");
        System.out.print(isPalindrome2(head) + " | ");
        //System.out.println(isPalindrome3(head) + " | ");
        printLinkedList(head);
        System.out.println("=========================");

        head = new Node(1);
        head.next = new Node(2);
        head.next.next = new Node(2);
        head.next.next.next = new Node(1);
        printLinkedList(head);
        System.out.print(isPalindrome1(head) + " | ");
        System.out.print(isPalindrome2(head) + " | ");
        //System.out.println(isPalindrome3(head) + " | ");
        printLinkedList(head);
        System.out.println("=========================");

        head = new Node(1);
        head.next = new Node(2);
        head.next.next = new Node(3);
        head.next.next.next = new Node(2);
        head.next.next.next.next = new Node(1);
        printLinkedList(head);
        System.out.print(isPalindrome1(head) + " | ");
        System.out.print(isPalindrome2(head) + " | ");
        //System.out.println(isPalindrome3(head) + " | ");
        printLinkedList(head);
        System.out.println("=========================");
    }
```

