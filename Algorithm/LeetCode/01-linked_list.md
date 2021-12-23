# 链表（Linked List）

## [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

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

