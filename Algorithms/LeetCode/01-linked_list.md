# 链表（Linked List）

## [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

常规解法：

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {

        ListNode headNode = new ListNode();
        ListNode currentNode = headNode;

        int flag = 0;
        while(l1 != null || l2 != null) {
            int val1 = l1 != null ? l1.val : 0;
            int val2 = l2 != null ? l2.val : 0;
            int sum = val1 + val2 + flag;
            ListNode node = new ListNode(sum % 10);
            flag = sum / 10;
            currentNode.next = node;
            currentNode = node;
            if(l1 != null) l1 = l1.next;
            if(l2 != null) l2 = l2.next;
        }
        if(flag == 1) {
            ListNode node = new ListNode(1);
            currentNode.next = node;
        }
        return headNode.next;
    }
}
```

## [19. 删除链表的倒数第N个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

> 常规解法：
>
> ①添加虚拟头结点
>
> ②获取整个链表的长度
>
> ③通过链表的长度和N定位要删除节点的上一个节点
>
> ③删除倒数第N个节点

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode pre = new ListNode();
        pre.next = head;
        int length = 1;
        while (head.next != null) {
            head = head.next;
            length++;
        }
        ListNode temp = pre;
        for (int i = 0; i < length - n; i++) {
            temp = temp.next;
        }
        ListNode next = temp.next != null ? temp.next.next : null;
        temp.next = next;
        return pre.next;
    }
}
```

> 只遍历一次：
>
> ①添加虚拟头节点
>
> ②使用两个指针（p、q）指向虚拟头节点
>
> ③将q指针向后移动N
>
> ④将p和q同步向后移动，直到q指针为空
>
> ⑤此时的p指针指向需要删除节点的上一个节点，进行删除操作

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode pre = new ListNode();
        pre.next = head;
        ListNode q = pre, p = pre;
        for (int i = 0; i < n + 1; i++) {
            q = q.next;
        }
        while (q != null) {
            p = p.next;
            q = q.next;
        }
        p.next = p.next != null ? p.next.next : null;
        return pre.next;
    }
}
```

## [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

常规解法：

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        if (list1 == null && list2 == null) return null;
        ListNode pre = new ListNode();
        ListNode current = pre;

        while (list1 != null || list2 != null) {
            if (list1 == null) {
                current.next = list2;
                break;
            }
            if (list2 == null) {
                current.next = list1;
                break;
            }
            int val1 = list1.val;
            int val2 = list2.val;
            ListNode node;
            if (val1 <= val2) {
                node = new ListNode(val1);
                list1 = list1.next;
            } else {
                node = new ListNode(val2);
                list2 = list2.next;
            }
            current.next = node;
            current = current.next;
        }
        return pre.next;
    }
}
```

