---
categories:
  - JAVA总结
  - JAVA（2020--2023）
---
# 链表整理

单向链表节点
```java
@Data
@AllArgsConstructor
class Node{
    private int val;
    private Node nextNode;
}
```

0.建链表（尾插、头插）

```java
   //头插
        Node head = new Node(-1, null);
        Node first = head;
        for(int i = 0; i<10; i++){
            Node node = new Node(i, null);
            head.nextNode = node;
            head = node;
        }
        while(first != null){
            System.out.print(first.getVal() + " ");
            first = first.nextNode;
        }

        //尾插
        Node head1 = new Node(-1, null);
        Node first1 = head1;
        Node first2 = head1;
        for(int i = 0; i<10; i++) {
            while (head1.nextNode != null) {
                head1 = head1.nextNode;
            }
            head1.nextNode = new Node(i, null);
        }
        System.out.println("\n///////////////////////");
        while(first1 != null){
            System.out.print(first1.getVal() + " ");
            first1 = first1.nextNode;
        }
```

1.反转链表

快慢指针，快指针比慢指针快1，局部反转。注意别循环溢出。

```java
private void 反转链表(Node head){
    Node pre = null;
    System.out.println("反转链表:");
    print(head);
    while (head != null){
        Node temp = new Node(head.getVal(), null);
        temp.nextNode = pre; //反转
        pre = temp;
        // 链表next
        head = head.nextNode;
    }
    print(pre);
}
```

2.使用快慢指针来找到链表的中点

```java
 while (fast!=null && fast.next!=null){
            //变化fast和slow的值
            fast=fast.next.next;   //快指针走两步
            slow=slow.next;        //满指针走一步
        }
```

3.单链表-快慢指针（是否有环，环入口）

```java
   //判断是否相遇
            if(fast.equals(slow)){
                return true;
            }
 //临时指针+1 与慢指针相遇，则是入口 ==入口还需要理论证明==
```

4.删除链表的倒数第n个节点

快慢指针，快指针指向null时，慢指针指向倒数第n+1个节点，即可卸载第n个节点。

快指针比慢指针快n+1个。

1 2 3 4 5 6 null

倒数第二个 则找到4（快3个，快指针指向null，慢指针为4）

```java
/**
 * 获取链表倒数第N个节点 前-个节点，用于断开链表
 * @param head
 * @param n
 * @return
 */
private Node tailNNode(Node head, int n){
    Node fast = head;
    Node low = head;
    int i = 1;
    while(fast != null){
        if(i >= n+1){
            low = low.nextNode;
        }
        i++;
        fast = fast.nextNode;
    }
    // System.out.println(low.getVal());
    // if(low.nextNode != null){
    //     low.nextNode = low.nextNode.nextNode;
    // }
    // print(head);
    return low;
}
```



5.判断是否为回文链表

获取中值节点，头半段反转，与后续low指针指向链表比对。

注意：

```java
 /**
     * a b c c b a
     * a b c d c b a
     */
    @Test
    public void 回文链表(){
        //找到中点位置
        Node head = new Node(1, null);
        Node first = head;
        for(int i = 2; i<=10; i++){
            Node node = new Node(i, null);
            head.nextNode = node;
            head = node;
        }
        回文算法(first);
        //前链倒转，与后链比对
    }

    private boolean 回文算法(Node head){
        Node fast = head;
        Node low = head;
        Node pre = new Node(head.getVal(), null);

        while(fast != null && fast.nextNode != null){
            fast = fast.nextNode.nextNode;
            low= low.nextNode;
            // 偶数个,往前一个节点
            // 1 2 3 4 5 ==6== 7 8 9 10 null
            // 1 2 3 4 5 ==6== 7 8 9 10 11 null
            if(fast == null){
                break;
            }

            Node newNode = new Node(low.getVal(), null);
            newNode.nextNode = pre;
            pre = newNode;
        }
        //
        print(pre);
        print(low);
        // 比较两条链是否一致
        while(pre != null && low != null){
            if(pre.getVal() != low.getVal()){
                return false;
            }else{
                pre = pre.nextNode;
                low = low.nextNode;
            }
        }
        if(pre != null && low != null) {
            return false;
        }
        return true;
    }
 private void print(Node head){
        Node first = head;
        while(first!=null) {
            System.out.print(first.getVal() + " ");
            first = first.nextNode;
        }
        System.out.println();
    }
```

参考：

[快慢指针算法实践](https://www.eolink.com/news/post/37606.html)

