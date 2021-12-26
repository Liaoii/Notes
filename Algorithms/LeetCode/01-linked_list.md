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

## [25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

```java
class Solution {
    public ListNode reverseN(ListNode head, int n) {
        if (n == 1) return head;
        ListNode tail = head.next;
        ListNode p = reverseN(head.next, n - 1);
        head.next = tail.next;
        tail.next = head;
        return p;
    }

    public ListNode reverse(ListNode head, int n) {
        ListNode p = head;
        int count = n;
        while (--n > 0 && p != null) p = p.next;
        if (p == null) return head;
        return reverseN(head, count);
    }

    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode pre = new ListNode();
        pre.next = head;
        ListNode p = pre, q = pre.next;
        while ((p.next = reverse(q, k)) != q) {
            p = q;
            q = p.next;
        }
        return pre.next;
    }
}
```

## [61. 旋转链表](https://leetcode-cn.com/problems/rotate-list/)

```java
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null) return null;
        ListNode p = head;
        int length = 1;
        while (p.next != null) {
            p = p.next;
            length++;
        }
        p.next = head;
        k = k % length;
        k = length - k;
        while (k-- > 0) p = p.next;
        head = p.next;
        p.next = null;
        return head;
    }
}
```

## [82. 删除排序链表中的重复元素 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode pre = new ListNode(0, head), p = pre;
        while (p.next != null) {
            if (p.next.next != null && p.next.val == p.next.next.val) {
                ListNode q = p.next.next;
                while (q != null && q.val == p.next.val) q = q.next;
                p.next = q;
            } else {
                p = p.next;
            }
        }
        return pre.next;
    }
}
```

## [83. 删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode temp = head;
        while (temp != null && temp.next != null) {
            if (temp.val == temp.next.val) {
                temp.next = temp.next.next;
            } else {
                temp = temp.next;
            }
        }
        return head;
    }
}
```

## [92. 反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

```java
class Solution {
    public ListNode reverseN(ListNode head, int n) {
        if (n == 1) return head;
        ListNode tail = head.next;
        ListNode p = reverseN(head.next, n - 1);
        head.next = tail.next;
        tail.next = head;
        return p;
    }

    public ListNode reverseBetween(ListNode head, int left, int right) {
        ListNode pre = new ListNode();
        pre.next = head;
        ListNode start = pre;
        int count = right - left + 1;
        while (--left > 0) {
            start = start.next;
        }
        ListNode p = reverseN(start.next, count);
        start.next = p;
        return pre.next;
    }
}
```

## [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

利用HashMap额外存储区的解法：

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        HashMap<ListNode, Object> hashtable = new HashMap<>();
        while (head != null) {
            if (hashtable.containsKey(head)) {
                return true;
            }
            hashtable.put(head, null);
            head = head.next;
        }
        return false;
    }
}
```

使用快慢指针解法：

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null) return false;
        ListNode slow = head;
        ListNode fast = head.next;
        while (fast != null && fast.next != null && slow != fast) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return fast != null && fast.next != null;
    }
}
```

## [142. 环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

使用快慢指针解法：

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null) return null;
        ListNode slow = head;
        ListNode fast = head.next;
        while (fast != null && fast.next != null && slow != fast) {
            slow = slow.next;
            fast = fast.next.next;
        }
        if (fast == null || fast.next == null) return null;
        slow = head.next;
        fast = head.next.next;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next.next;
        }
        slow = head;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }
        return slow;
    }
}
```

简便写法：

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null) return null;
        ListNode slow = head;
        ListNode fast = head;
        if (fast.next == null) return null;
        do {
            slow = slow.next;
            fast = fast.next.next;
        } while (fast != null && fast.next != null && slow != fast);
        if (fast == null || fast.next == null) return null;
        slow = head;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }
        return slow;
    }
}
```

## [202. 快乐数](https://leetcode-cn.com/problems/happy-number/)

```java
class Solution {
    public int getNext(int n) {
        int result = 0;
        while (n > 0) {
            result += (n % 10) * (n % 10);
            n = n / 10;
        }
        return result;
    }

    public boolean isHappy(int n) {
        int slow = n, fast = n;
        do {
            slow = getNext(slow);
            fast = getNext(getNext(fast));
        } while (slow != fast && fast != 1);
        return fast == 1;
    }
}
```

## [206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

常规解法：

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null) return null;
        ListNode pre = null, current = head, p = head.next;
        while (current != null) {
            current.next = pre;
            pre = current;
            current = p;
            if (p != null) p = p.next;
        }
        return pre;
    }
}
```

使用递归解法：

```java
class Solution {
    public static ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode tail = head.next;
        ListNode p = reverseList(head.next);
        head.next = null;
        tail.next = head;
        return p;
    }
}
```

