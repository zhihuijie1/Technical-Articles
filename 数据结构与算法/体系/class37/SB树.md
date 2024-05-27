





## 四种违规情况

LL  LR  RL  RR

LL型与LR型

<img src="D:/%E4%BD%A0%E5%A5%BDJava/11-17108469453361.png" style="zoom:25%;" />

RL型与RR型

<img src="D:/%E4%BD%A0%E5%A5%BDJava/12-17108470569903.png" style="zoom:25%;" />



## 如何进行树的调整



<img src="D:/%E4%BD%A0%E5%A5%BDJava/13-17108482721235.png" style="zoom:25%;" />

只要左旋 | 右旋 之后，左孩子|有孩子发生改变的节点要进行递归操作，重新进行检验与调整。

> 之前，C是D，E的叔叔，但是D这棵树的size大于C，所以你这个叔叔不中用啊。右旋之后，D变成了C的叔叔。
>
> F，G变成了E的新竞争对手，即A这颗树的左孩子有可能有问题，所以A这个节点需要重新检查。现在以A为头节点的树调
>
> 好了，但是E,C成了D的新竞争对手，即B这颗树的左孩子有可能有问题，所以要对B这个节点进行重新的检查。



当涉及到调整时，可能会出现层层递归的现象发生。例如A要调整，向下触探2层调整，调整完之后，可能C又需要调整，然后继续向下初探两层调整 ......一直到低。所以这个调整十分的恶心。但是时间复杂度仍然是logN



插入节点的时候，就从下向上讲影响到的节点进行调整，



当删除节点的时候，不调整，直接删除就行，只有插入节点的时候进行调整。因为调整非常的恶心。

这也导致一个问题的发生，就是如果一个过程只有删除删除删除。可能会删成棒状，可能会导致时间复杂度变成 **log(add时调整好的最大层数)**

<img src="D:/%E4%BD%A0%E5%A5%BDJava/14-17108506090488.png" style="zoom:25%;" />

这也是SB树的爽点，删除的时候，我不调了，只有add时才进行调整。因为add一次就会将树调平。因为会有左右节点层层递

归。但是AVL树如果删除的时候，我不调了，当删成棒状结构的时候，你add之后，只会对add所在的分支进行调平。	











代码：

```java
package algorithmbasic.basicsets.class37;

public class sizeBalanceTree {
    public static class SBTNode<K extends Comparable<K>, V> {
        public K key;
        public V value;
        public int size;
        public SBTNode<K, V> left;
        public SBTNode<K, V> right;

        public SBTNode(K key, V value) {
            this.key = key;
            this.value = value;
            size = 0;
        }
    }

    public static class SizeBalancedTreeMap<K extends Comparable<K>, V> {
        public SBTNode<K, V> add(SBTNode<K, V> cur, K key, V value) {
            if (cur == null) {
                return new SBTNode<>(key, value);
            }
            if (cur.key.compareTo(key) < 0) {
                cur.right = add(cur.right, key, value);
            } else {
                cur.left = add(cur.left, key, value);
            }
            cur.size++;
            return mainTain(cur);
        }

        public SBTNode<K, V> delete(SBTNode<K, V> cur, K key) {
            if (cur.key.compareTo(key) < 0) {
                cur.right = delete(cur.right, key);
            } else if (cur.key.compareTo(key) > 0) {
                cur.left = delete(cur.left, key);
            } else {
                if (cur.left == null && cur.right == null) {
                    return null;
                } else if (cur.left == null) {
                    return cur.right;
                } else if (cur.right == null) {
                    return cur.left;
                } else {
                    // --------------------- begin - hardThinking---------------------
                    SBTNode<K, V> des = cur.right;
                    SBTNode<K, V> pre = null;
                    while (des.left != null) {
                        pre = des;
                        pre.size--;
                        des = des.left;
                    }
                    if (pre != null) {
                        pre.left = des.right;
                        des.right = cur.right;
                    }
                    des.left = cur.left;
                    des.size = cur.left.size + (des.right == null ? 0 : des.right.size) + 1;
                    cur = des;
                    // --------------------- end ---------------------
                }
            }
            return cur;
        }

        public SBTNode<K, V> mainTain(SBTNode<K, V> cur) {
            if (cur == null) {
                return null;
            }
            int leftSzie = cur.left == null ? 0 : cur.left.size;
            int leftLeftSize = cur.left != null && cur.left.left != null ? cur.left.left.size : 0;
            int leftRightSize = cur.left != null && cur.left.right != null ? cur.left.right.size : 0;
            int rightSzie = cur.right == null ? 0 : cur.right.size;
            int rightLeftSize = cur.right != null && cur.right.left != null ? cur.right.left.size : 0;
            int rightRightSize = cur.right != null && cur.right.right != null ? cur.right.right.size : 0;

            if (leftLeftSize > rightSzie) {// LL
                cur = rightRotate(cur);
                cur.left = mainTain(cur.left);
                cur = mainTain(cur);
            } else if (leftRightSize > rightSzie) {// LR
                cur.left = leftRotate(cur.left);
                cur = rightRotate(cur);
                cur.left = mainTain(cur.left);
                cur.right = mainTain(cur.right);
                cur = mainTain(cur);
            } else if (rightLeftSize > leftSzie) {// RL
                cur.right = rightRotate(cur.right);
                cur = leftRotate(cur);
                cur.left = mainTain(cur.left);
                cur.right = mainTain(cur.right);
                cur = mainTain(cur);
            } else if (rightRightSize > leftSzie) {// RR
                cur = leftRotate(cur);
                cur.left = mainTain(cur.left);
                cur = mainTain(cur);
            }
            return cur;
        }

        public SBTNode<K, V> leftRotate(SBTNode<K, V> cur) {
            if (cur == null) {
                return null;
            }
            SBTNode<K, V> right = cur.right;
            cur.right = right.left;
            right.left = cur;
            right.size = cur.size;//cur之前是整棵树的头节点，size是整棵树的size
            cur.size = (cur.right == null ? 0 : cur.right.size) + (cur.left == null ? 0 : cur.left.size) + 1;
            return right;
        }

        public SBTNode<K, V> rightRotate(SBTNode<K, V> cur) {
            if (cur == null) {
                return null;
            }
            SBTNode<K, V> left = cur.left;
            cur.left = left.right;
            left.right = cur;
            left.size = cur.size;//cur之前是整棵树的头节点，size是整棵树的size
            cur.size = (cur.left == null ? 0 : cur.left.size) + (cur.right == null ? 0 : cur.right.size) + 1;
            return left;
        }
    }
}

```



































