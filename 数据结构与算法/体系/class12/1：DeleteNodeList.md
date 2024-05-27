

```java
//注意1：
//给你一个头节点和一个要删除的节点，其返回值必须是node类型，因为要是删除的是头节点，必须返回的是新头。 
```

![](D:/%E4%BD%A0%E5%A5%BDJava/1151.png)

```java
    // 1: 给你一个头节点以及要删除的节点，其返回值必须是Node类型
    //    因为：如果要删除的节点是头节点，必须返回一个新头。 ----------------------------注意1
    public static Node deleteNode(Node head , Node note) {
        if(head == null || note == null) {
            return null;
        }
        if(head == note) {
            return head.next;
        }
        Node cur = head;
        while (cur.next != null && cur.next != note) {
            cur = cur.next;
        }
        if(cur.next == note) {
            cur.next = cur.next.next;
        }
        return head;
    }
```







```java
//注意2：
//当给的节点是头节点时，没有问题可以操作，但是要是删除的节点时最后一个节点时，就会出错。因为要删除最后一个节点，就是
//让倒数第二个节点指向null，但是我们找不到倒数第二个。将node制空，即note == null是不起作用的。因为note只是一个
//“引用的拷贝”。null是系统上一个独立的区域。比如1->2->null。如果你要删掉2。你需要找到1，然后将1.next指向这个区域。不是说你把2变成null，你变不成。你就是变成了，1的next也没有指向公认的null的区域。
```

![](D:/%E4%BD%A0%E5%A5%BDJava/1152.png)

```java

    // 2:一种看似高效其实搞笑的节点删除方式
    //   只给你要删除的节点，然后让你删除该节点。
    //   采用拷贝的方法，把后一个拷贝到前一个。
    // 1 2 3 4 5 6 7
    public static void deleteNode2(Node note) {
        if(note == null) {
            return;
        }
        // 最后一个节点
        if(note.next == null) {
            note = null; // 做不到。 ------------------------------------------注意2
        }else {
            note.value = note.next.value;
            note.next = note.next.next;
        }
    }
```

