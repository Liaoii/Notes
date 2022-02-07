# 链表（Linked List）

## [2. 两数相加*](https://leetcode-cn.com/problems/add-two-numbers/)

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

## [19. 删除链表的倒数第N个结点*](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

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

使用栈实现：

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode pre = new ListNode(0, head), p = pre;
        Stack<ListNode> stack = new Stack<>();
        while (p != null) {
            stack.push(p);
            p = p.next;
        }
        while (n-- > 0) stack.pop();
        p = stack.peek();
        p.next = p.next.next;
        return pre.next;
    }
}
```

2022-02-07双指针解法：

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode pre = new ListNode(0, head), indN = pre, p = pre;
        while (n-- > 0) indN = indN.next;
        while (indN.next != null) {
            p = p.next;
            indN = indN.next;
        }
        p.next = p.next.next;
        return pre.next;
    }
}
```

## [21. 合并两个有序链表*](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

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

2022-02-07优化上一个解法：

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        if (list1 == null && list2 == null) return null;
        ListNode ans = new ListNode(), p = ans;
        while (true) {
            if (list1 == null) {
                p.next = list2;
                break;
            }
            if (list2 == null) {
                p.next = list1;
                break;
            }
            if (list1.val <= list2.val) {
                p.next = list1;
                list1 = list1.next;
            } else {
                p.next = list2;
                list2 = list2.next;
            }
            p = p.next;
        }
        return ans.next;
    }
}
```

使用递归求解：

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        if (list1 == null) {
            return list2;
        } else if (list2 == null) {
            return list1;
        } else if (list1.val < list2.val) {
            list1.next = mergeTwoLists(list1.next, list2);
            return list1;
        } else {
            list2.next = mergeTwoLists(list1, list2.next);
            return list2;
        }
    }
}
```

## [23. 合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        PriorityQueue<ListNode> pq = new PriorityQueue<>(new Comparator<ListNode>() {
            @Override
            public int compare(ListNode o1, ListNode o2) {
                return o1.val - o2.val;
            }
        });
        for (ListNode node : lists) {
            if (node != null) pq.offer(node);
        }
        ListNode pre = new ListNode(), p = pre;
        while (!pq.isEmpty()) {
            ListNode temp = pq.poll();
            p.next = new ListNode(temp.val);
            p = p.next;
            if (temp.next != null) pq.offer(temp.next);
        }
        return pre.next;
    }
}
```

2022-02-07优化优先队列解法，避免重新开创空间：

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        PriorityQueue<ListNode> pq = new PriorityQueue<>(new Comparator<ListNode>() {
            @Override
            public int compare(ListNode o1, ListNode o2) {
                return o1.val - o2.val;
            }
        });
        for (ListNode node : lists) {
            if (node != null) pq.offer(node);
        }
        ListNode pre = new ListNode(), p = pre;
        while (!pq.isEmpty()) {
            ListNode temp = pq.poll();
            p.next = temp;
            p = p.next;
            if (temp.next != null) pq.offer(temp.next);
        }
        return pre.next;
    }
}
```



## [24. 两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

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

    public ListNode swapPairs(ListNode head) {
        ListNode pre = new ListNode();
        pre.next = head;
        ListNode p = pre, q = pre.next;
        while ((p.next = reverse(q, 2)) != q) {
            p = q;
            q = p.next;
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

## [86. 分隔链表](https://leetcode-cn.com/problems/partition-list/)

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        if (head == null || head.next == null) return head;
        ListNode pre = new ListNode(0, head), p = pre;
        ListNode right = new ListNode(), r = right;
        while (p.next != null) {
            if (p.next.val >= x) {
                right.next = p.next;
                p.next = p.next.next;
                right = right.next;
                right.next = null;
            } else {
                p = p.next;
            }
        }
        p.next = r.next;
        return pre.next;
    }
}
```

新建两个新链表：

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        ListNode r1 = new ListNode(), p1 = r1, r2 = new ListNode(), p2 = r2, p = head, q;
        while (p != null) {
            q = p.next;
            if (p.val < x) {
                p.next = p1.next;
                p1.next = p;
                p1 = p;
            } else {
                p.next = p2.next;
                p2.next = p;
                p2 = p;
            }
            p = q;
        }
        p1.next = r2.next;
        return r1.next;
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

## [109. 有序链表转换二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/)

```java
class Solution {
    public TreeNode sortedListToBST(ListNode head) {
        int len = 0;
        ListNode pre = head;
        while (pre != null) {
            len++;
            pre = pre.next;
        }
        return createTree(head, 1, len);
    }

    public TreeNode createTree(ListNode head, int left, int right) {
        if (left == right) {
            return new TreeNode(head.val);
        } else if (left > right) {
            return null;
        }
        int mid = (left + right) / 2, m = mid;
        ListNode p = head;
        while (--m > 0) p = p.next;
        TreeNode ans = new TreeNode(p.val);
        ans.left = createTree(head, left, mid - 1);
        ans.right = createTree(p.next, 1, right - mid);
        return ans;
    }
}
```

## [114. 二叉树展开为链表](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

```java
class Solution {
    public void flatten(TreeNode root) {
        List<TreeNode> list = new ArrayList<>();
        getChild(root, list);
        for (int i = 1; i < list.size(); i++) {
            TreeNode pre = list.get(i - 1);
            TreeNode cur = list.get(i);
            pre.left = null;
            pre.right = cur;
        }
    }

    private void getChild(TreeNode root, List<TreeNode> list) {
        if (root == null) return;
        list.add(root);
        getChild(root.left, list);
        getChild(root.right, list);
    }
}
```

## [138. 复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

使用额外哈希表：

```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;
        Node pre = new Node(0), h = new Node(0), r = pre;
        h.next = head;
        int index = -1;
        HashMap<Node, Integer> nodeMap = new HashMap<>();
        HashMap<Integer, Node> map = new HashMap<>();
        while (h.next != null) {
            h = h.next;
            index++;
            Node temp = new Node(h.val);
            map.put(index, temp);
            nodeMap.put(h, index);
            r.next = temp;
            r = temp;
        }
        r = pre.next;
        while (head != null) {
            if (head.random != null) {
                r.random = map.get(nodeMap.get(head.random));
            }
            head = head.next;
            r = r.next;
        }
        return pre.next;
    }
}
```

拷贝每个节点：

```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;
        Node h = head;
        while (h != null) {
            Node copy = new Node(h.val);
            copy.next = h.next;
            h.next = copy;
            h = copy.next;
        }
        Node r = head;
        while (r != null && r.next != null) {
            if (r.random != null)
                r.next.random = r.random.next;
            r = r.next.next;
        }
        Node q = head.next, p = head;
        while (p != null) {
            Node temp = p.next;
            p.next = p.next.next;
            temp.next = (temp.next != null) ? temp.next.next : null;
            p = p.next;
        }
        return q;
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

```
class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null) return false;
        ListNode slow = head, fast = head;
        do {
            slow = slow.next;
            fast = fast.next == null ? null : fast.next.next;
        } while (fast != null && fast != slow);
        return fast != null;
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

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null) return null;
        ListNode slow = head, fast = head;
        do {
            slow = slow.next;
            fast = fast.next == null ? null : fast.next.next;
        } while (fast != null && fast != slow);
        if (fast == null) return null;
        slow = head;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }
        return slow;
    }
}
```

## [143. 重排链表](https://leetcode-cn.com/problems/reorder-list/)



## [147. 对链表进行插入排序](https://leetcode-cn.com/problems/insertion-sort-list/)

```java
class Solution {
    public ListNode insertionSortList(ListNode head) {
        if (head == null) return null;
        ListNode pre = new ListNode(0, head), temp;
        while (head.next != null) {
            if (head.next.val < head.val) {
                temp = head.next;
                head.next = head.next.next;
                ListNode p = pre;
                while (p.next != null) {
                    if (temp.val < p.next.val) {
                        temp.next = p.next;
                        p.next = temp;
                        break;
                    } else {
                        p = p.next;
                    }
                }
            } else {
                head = head.next;
            }
        }
        return pre.next;
    }
}
```

## [160. 相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

第一次解法：

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        HashMap<ListNode, Object> map = new HashMap<>();
        while (headA != null) {
            if (map.containsKey(headA)) {
                return headA;
            } else {
                map.put(headA, null);
                headA = headA.next;
            }
        }
        while (headB != null) {
            if (map.containsKey(headB)) {
                return headB;
            } else {
                map.put(headB, null);
                headB = headB.next;
            }
        }
        return null;
    }
}
```

优化第一次的解法：

```java
class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        HashSet<ListNode> set = new HashSet<>();
        ListNode temp = headA;
        while (temp != null) {
            set.add(temp);
            temp = temp.next;
        }
        temp = headB;
        while (temp != null) {
            if (set.contains(temp)) {
                return temp;
            }
            temp = temp.next;
        }
        return null;
    }
}
```

双指针解法：

```java
class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode tempA = headA;
        ListNode tempB = headB;
        while (tempA != tempB) {
            tempA = tempA == null ? headB : tempA.next;
            tempB = tempB == null ? headA : tempB.next;
        }
        return tempA;
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

## [203. 移除链表元素](https://leetcode-cn.com/problems/remove-linked-list-elements/)

```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        ListNode pre = new ListNode(0, head), p = pre;
        while (pre.next != null) {
            if (pre.next.val == val) {
                pre.next = pre.next.next;
            } else {
                pre = pre.next;
            }
        }
        return p.next;
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

## [234. 回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)

```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        if (head.next == null) return true;
        int index = 0;
        HashMap<Integer, ListNode> map = new HashMap<>();
        while (head != null) {
            map.put(++index, head);
            head = head.next;
        }
        for (int i = 1; i <= index / 2; i++) {
            int j = index - i + 1;
            if (map.get(i).val != map.get(j).val) return false;
        }
        return true;
    }
}
```

## [237. 删除链表中的节点](https://leetcode-cn.com/problems/delete-node-in-a-linked-list/)

```java
class Solution {
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```

## [328. 奇偶链表](https://leetcode-cn.com/problems/odd-even-linked-list/)

第一次代码：

```java
class Solution {
    public static ListNode oddEvenList(ListNode head) {
        if (head == null) return null;
        ListNode pre = new ListNode(0, head), p = pre;
        ListNode h = new ListNode(), tail = h;
        while (pre != null && pre.next != null) {
            h.next = pre.next;
            h = h.next;
            pre.next = pre.next.next;
            pre = pre.next;
        }
        h.next = p.next;
        return tail.next;
    }
}
```

优化后的解法：

```java
class Solution {
    public static ListNode oddEvenList(ListNode head) {
        if (head == null) return null;
        ListNode p = head, q = head.next, qHead = q;
        while (q != null && q.next != null) {
            p.next = q.next;
            p = p.next;
            q.next = p.next;
            q = q.next;
        }
        p.next = qHead;
        return head;
    }
}
```

## [382. 链表随机节点](https://leetcode-cn.com/problems/linked-list-random-node/)

```java
class Solution {

    ListNode head;
    Random random;

    public Solution(ListNode head) {
        this.head = head;
        random = new Random();
    }

    public int getRandom() {
        int result = 0;
        ListNode p = head;
        int count = 0;
        while (p != null) {
            count++;
            int r = random.nextInt(count) + 1;
            if (r == count) {
                result = p.val;
            }
            p = p.next;
        }
        return result;
    }
}
```

## [876. 链表的中间结点](https://leetcode-cn.com/problems/middle-of-the-linked-list/)

```java
class Solution {
    public ListNode middleNode(ListNode head) {
        ListNode p = head;
        int length = 0;
        while (head != null) {
            length++;
            head = head.next;
        }
        int mid = length / 2 + 1;
        while (--mid > 0) p = p.next;
        return p;
    }
}
```

## [1290. 二进制链表转整数](https://leetcode-cn.com/problems/convert-binary-number-in-a-linked-list-to-integer/)

常规解法：

```java
class Solution {
    public int getDecimalValue(ListNode head) {
        String binaryStr = "";
        while (head != null) {
            binaryStr += head.val;
            head = head.next;
        }
        return Integer.valueOf(binaryStr, 2);
    }
}
```

数学运算解法：

```java
class Solution {
    public int getDecimalValue(ListNode head) {
        ListNode pre = head;
        int length = 0;
        int result = 0;
        while (head != null) {
            length++;
            head = head.next;
        }
        while (pre != null) {
            length--;
            if (pre.val == 1) {
                result += Math.pow(2, length);
            }
            pre = pre.next;
        }
        return result;
    }
}
```

## [1669. 合并两个链表](https://leetcode-cn.com/problems/merge-in-between-linked-lists/)

```java
class Solution {
    public ListNode mergeInBetween(ListNode list1, int a, int b, ListNode list2) {
        ListNode temp1 = list2;
        while (temp1.next != null) {
            temp1 = temp1.next;
        }
        ListNode temp2 = list1;
        while (b-- > 0) {
            temp2 = temp2.next;
        }
        temp1.next = temp2.next;
        ListNode temp3 = list1;
        while (--a > 0) {
            temp3 = temp3.next;
        }
        temp3.next = list2;
        return list1;
    }
}
```

## [1721. 交换链表中的节点](https://leetcode-cn.com/problems/swapping-nodes-in-a-linked-list/)

```java
class Solution {
    public ListNode swapNodes(ListNode head, int k) {
        ListNode h = head;
        int length = 0;
        while (h != null) {
            length++;
            h = h.next;
        }
        int j = length - k + 1;
        if (k == j) return head;
        ListNode p = head, q = head;
        while (--k > 0) {
            p = p.next;
        }
        while (--j > 0) {
            q = q.next;
        }
        int temp = p.val;
        p.val = q.val;
        q.val = temp;
        return head;
    }
}
```

## [面试题 02.07. 链表相交](https://leetcode-cn.com/problems/intersection-of-two-linked-lists-lcci/)

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode tempA = headA, tempB = headB;
        while (tempA != tempB) {
            tempA = tempA == null ? headB : tempA.next;
            tempB = tempB == null ? headA : tempB.next;
        }
        return tempA;
    }
}
```

