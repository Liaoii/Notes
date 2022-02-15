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

2022-02-07，双指针解法：

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

2022-02-07，优化上一个解法：

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

使用递归求解（+）：

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

## [23. 合并K个升序链表*](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

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

2022-02-07，逐个合并每个链表：

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode ans = null;
        for (ListNode node : lists) {
            ans = mergeTwoLists(ans, node);
        }
        return ans;
    }

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

2022-02-07，使用分治法求解（+）：

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        return merge(lists, 0, lists.length - 1);
    }

    public ListNode merge(ListNode[] lists, int start, int end) {
        if (start == end) return lists[start];
        if (start > end) return null;
        int mid = (start + end) / 2;
        return mergeTwoLists(merge(lists, start, mid), merge(lists, mid + 1, end));
    }

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

## [24. 两两交换链表中的节点*](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

K个一组翻转链表（+）：

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

2022-02-07，迭代解法：

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode pre = new ListNode(0, head), p = pre;
        while (p.next != null && p.next.next != null) {
            ListNode first = p.next;
            ListNode second = first.next;
            p.next = second;
            first.next = second.next;
            second.next = first;
            p = first;
        }
        return pre.next;
    }
}
```

2022-02-07，递归解法（+）：

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode tail = head.next;
        head.next = swapPairs(tail.next);
        tail.next = head;
        return tail;
    }
}
```

## [25. K 个一组翻转链表（+）](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

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

## [61. 旋转链表*](https://leetcode-cn.com/problems/rotate-list/)

先将链表连接成环，再进行处理：

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

2022-02-08，常规解法：

```java
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null) return null;
        int len = 1;
        ListNode tail = head, front = head, kst = head;
        while (tail.next != null) {
            tail = tail.next;
            len++;
        }
        k = k % len;
        if (k == 0) return head;
        while (k-- > 0) front = front.next;
        while (front.next != null) {
            front = front.next;
            kst = kst.next;
        }
        ListNode ans = kst.next;
        kst.next = null;
        tail.next = head;
        return ans;
    }
}
```

## [82. 删除排序链表中的重复元素 II*](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

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

2022-02-08，使用哈希表进行求解：

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) return null;
        ListNode pre = new ListNode(0, head), p = pre;
        HashMap<Integer, Integer> map = new HashMap<>();
        while (p.next != null) {
            p = p.next;
            map.put(p.val, map.getOrDefault(p.val, 0) + 1);
        }
        p = pre;
        while (p.next != null) {
            if (map.get(p.next.val) > 1) {
                p.next = p.next.next;
            } else {
                p = p.next;
            }
        }
        return pre.next;
    }
}
```

2022-02-08，使用迭代求解：

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode pre = new ListNode(0, head), p = pre;
        while (p != null) {
            if (p.next != null && p.next.next != null && p.next.val == p.next.next.val) {
                int val = p.next.val;
                while (p.next != null && p.next.val == val) {
                    p.next = p.next.next;
                }
            } else {
                p = p.next;
            }
        }
        return pre.next;
    }
}
```

## [83. 删除排序链表中的重复元素*](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

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

## [86. 分隔链表*](https://leetcode-cn.com/problems/partition-list/)

将大于x值的链表分离出来，添加到原链表的尾部：

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

2022-02-09，对上面新建两个链表的解法进行优化：

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        ListNode left = new ListNode(), lPre = left, right = new ListNode(), rPre = right;
        while (head != null) {
            if (head.val >= x) {
                right.next = head;
                right = right.next;
            } else {
                left.next = head;
                left = left.next;
            }
            head = head.next;
        }
        right.next = null;
        left.next = rPre.next;
        return lPre.next;
    }
}
```

2022-02-08，单链表解法：

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        if (head == null || head.next == null) return head;
        int len = 0;
        ListNode pre = new ListNode(0, head), tail = pre, p = pre;
        while (tail.next != null) {
            tail = tail.next;
            len++;
        }
        while (p.next != null && p.next.next != null && len > 0) {
            if (p.next.val >= x) {
                ListNode temp = p.next;
                p.next = temp.next;
                temp.next = tail.next;
                tail.next = temp;
                tail = tail.next;
            } else {
                p = p.next;
            }
            len--;
        }
        return pre.next;
    }
}
```

**是否可以使用快排的思想来求解？**

## [92. 反转链表 II*](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

递归解法：

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

2022-02-09，对上面解法的优化，使其更易理解：

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        ListNode pre = new ListNode(0, head), p = pre;
        int n = right - left + 1;
        while (--left > 0) p = p.next;
        p.next = reverseN(p.next, n);
        return pre.next;
    }

    public ListNode reverseN(ListNode head, int n) {
        if (n == 1) return head;
        ListNode p = reverseN(head.next, n - 1);
        ListNode temp = head.next;
        head.next = head.next.next;
        temp.next = head;
        return p;
    }
}
```

2022-02-09，常规解法：

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        /**
         * pre 虚拟头节点
         * lPre left的前一个节点
         * l 第left个节点
         * rPre right的前一个节点
         * r 第right个节点
         */
        ListNode pre = new ListNode(0, head), lPre = pre, l, rPre, r;
        int count = right - left;
        while (--left > 0) lPre = lPre.next;
        l = lPre.next;
        rPre = l;
        while (count-- > 0) rPre = rPre.next;
        r = rPre.next;
        rPre.next = null;
        reverseList(l);
        lPre.next = rPre;
        l.next = r;
        return pre.next;
    }

    public ListNode reverseList(ListNode head) {
        ListNode ans = null;
        while (head != null) {
            ListNode ne = head.next;
            head.next = ans;
            ans = head;
            head = ne;
        }
        return ans;
    }
}
```

2022-02-09，一次遍历实现：

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        ListNode pre = new ListNode(0, head), p = pre;
        int count = right - left;
        while (--left > 0) p = p.next;
        ListNode le = p.next;
        while (count-- > 0) {
            ListNode ne = le.next;
            le.next = le.next.next;
            ne.next = p.next;
            p.next = ne;
        }
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

## [116. 填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

```java
class Solution {
    public static Node connect(Node root) {
        if (root == null) return null;
        Queue<Node> stack = new LinkedList<>();
        stack.offer(root);
        while (true) {
            int size = stack.size();
            if (size == 0) break;
            Node last = null;
            while (size-- > 0) {
                Node cur = stack.poll();
                if (cur.left != null) stack.offer(cur.left);
                if (cur.right != null) stack.offer(cur.right);
                if (last != null) last.next = cur;
                last = cur;
            }
        }
        return root;
    }
}
```

## [117. 填充每个节点的下一个右侧节点指针 II](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/)

```java
class Solution {
    public Node connect(Node root) {
        if (root == null) return null;
        Queue<Node> stack = new LinkedList<>();
        stack.offer(root);
        while (true) {
            int size = stack.size();
            if (size == 0) break;
            Node last = null;
            while (size-- > 0) {
                Node cur = stack.poll();
                if (cur.left != null) stack.offer(cur.left);
                if (cur.right != null) stack.offer(cur.right);
                if (last != null) last.next = cur;
                last = cur;
            }
        }
        return root;
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

2022-02-10，使用递归实现：

```java
class Solution {

    ListNode pre;

    public void reorderList(ListNode head) {
        pre = head;
        reOrder(head);
    }

    public void reOrder(ListNode head) {
        if (head.next == null) return;
        reOrder(head.next);
        if (pre.next != null && pre.next.next != null) {
            head.next.next = pre.next;
            pre.next = head.next;
            head.next = null;
            pre = pre.next.next;
        }
    }
}
```

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

## [206. 反转链表*](https://leetcode-cn.com/problems/reverse-linked-list/)

迭代解法：

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

2022-02-09，优化递归解法：

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode ans = reverseList(head.next);
        ListNode temp = head.next;
        head.next = head.next.next;
        temp.next = head;
        return ans;
    }
}
```

官方迭代解法，省去了特判：

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode ans = null;
        while (head != null) {
            ListNode ne = head.next;
            head.next = ans;
            ans = head;
            head = ne;
        }
        return ans;
    }
}
```

官方递归解法，相当简洁：

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode ans = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return ans;
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

## [430. 扁平化多级双向链表](https://leetcode-cn.com/problems/flatten-a-multilevel-doubly-linked-list/)

```java
class Solution {
    public Node flatten(Node head) {
        getChild(head);
        return head;
    }

    public Node getChild(Node head) {
        while (head != null) {
            if (head.child != null) {
                Node ne = head.next;
                head.next = head.child;
                head.child.prev = head;
                Node childTail = getChild(head.child);
                head.child = null;
                if (ne != null) {
                    childTail.next = ne;
                    ne.prev = childTail;
                }
            } else {
                if (head.next != null) head = head.next;
                else break;
            }
        }
        return head;
    }
}
```

## [445. 两数相加 II](https://leetcode-cn.com/problems/add-two-numbers-ii/)

先翻转两个链表，再进行求解：

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        l1 = reverse(l1);
        l2 = reverse(l2);
        ListNode ans = null;
        int flag = 0;
        while (l1 != null || l2 != null) {
            int v1 = l1 == null ? 0 : l1.val;
            int v2 = l2 == null ? 0 : l2.val;
            int v = (v1 + v2 + flag) % 10;
            ListNode pre = new ListNode(v);
            pre.next = ans;
            ans = pre;
            flag = (v1 + v2 + flag) / 10;
            if (l1 != null) l1 = l1.next;
            if (l2 != null) l2 = l2.next;
        }
        if (flag == 1) {
            ListNode pre = new ListNode(1);
            pre.next = ans;
            ans = pre;
        }
        return ans;
    }

    public ListNode reverse(ListNode head) {
        ListNode pre = null;
        while (head != null) {
            ListNode next = head.next;
            head.next = pre;
            pre = head;
            head = next;
        }
        return pre;
    }
}
```

## [705. 设计哈希集合](https://leetcode-cn.com/problems/design-hashset/)

```java
class MyHashSet {

    private boolean[] bool = new boolean[1000001];

    public MyHashSet() {}

    public void add(int key) {
        bool[key] = true;
    }

    public void remove(int key) {
        bool[key] = false;
    }

    public boolean contains(int key) {
        return bool[key] == true;
    }
}
```

## [725. 分隔链表](https://leetcode-cn.com/problems/split-linked-list-in-parts/)

```java
class Solution {
    public ListNode[] splitListToParts(ListNode head, int k) {
        ListNode[] ans = new ListNode[k];
        ListNode p = head;
        int count = 0;
        while (p != null) {
            count++;
            p = p.next;
        }
        p = head;
        for (int i = 0; i < k; i++) {
            int c = count / (k - i) + (count % (k - i) > 0 ? 1 : 0);
            count -= c;
            ListNode temp = p;
            while (p != null && --c > 0) {
                p = p.next;
            }
            ListNode next = p == null ? null : p.next;
            if (p != null) p.next = null;
            p = next;
            ans[i] = temp;
        }
        return ans;
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

## [剑指 Offer 52. 两个链表的第一个公共节点](https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)

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

## [剑指 Offer II 021. 删除链表的倒数第 n 个结点](https://leetcode-cn.com/problems/SLwz0R/)

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode ans = new ListNode(0, head), back = ans, front = ans;
        while (n-- > 0) {
            front = front.next;
        }
        while (front.next != null) {
            front = front.next;
            back = back.next;
        }
        back.next = back.next.next;
        return ans.next;
    }
}
```

## [剑指 Offer II 022. 链表中环的入口节点](https://leetcode-cn.com/problems/c32eOV/)

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null) return null;
        ListNode slow = head, fast = head;
        do {
            slow = slow.next;
            fast = fast.next == null ? null : fast.next.next;
        } while (slow != fast && fast != null);
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

## [剑指 Offer II 023. 两个链表的第一个重合节点](https://leetcode-cn.com/problems/3u1WK4/)

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

## [剑指 Offer II 024. 反转链表](https://leetcode-cn.com/problems/UHnkqh/)

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode ans = null;
        while (head != null) {
            ListNode ne = head.next;
            head.next = ans;
            ans = head;
            head = ne;
        }
        return ans;
    }
}
```

## [剑指 Offer II 025. 链表中的两数相加](https://leetcode-cn.com/problems/lMSNwu/)

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode ans = new ListNode();
        l1 = reverse(l1);
        l2 = reverse(l2);
        int carry = 0;
        while (l1 != null || l2 != null) {
            int val1 = l1 == null ? 0 : l1.val;
            int val2 = l2 == null ? 0 : l2.val;
            int val = (val1 + val2 + carry) % 10;
            ListNode temp = new ListNode(val);
            temp.next = ans.next;
            ans.next = temp;
            carry = (val1 + val2 + carry) / 10;
            if (l1 != null) l1 = l1.next;
            if (l2 != null) l2 = l2.next;
        }
        if (carry > 0) {
            ListNode temp = new ListNode(carry);
            temp.next = ans.next;
            ans.next = temp;
        }
        return ans.next;
    }

    public ListNode reverse(ListNode head) {
        ListNode ans = null;
        while (head != null) {
            ListNode ne = head.next;
            head.next = ans;
            ans = head;
            head = ne;
        }
        return ans;
    }
}
```

## [剑指 Offer II 026. 重排链表](https://leetcode-cn.com/problems/LGjMqU/)

```java
class Solution {
    private ListNode pre;

    public void reorderList(ListNode head) {
        if (head == null || head.next == null) return;
        pre = head;
        reorder(head);
    }

    private void reorder(ListNode head) {
        if (head.next == null) return;
        reorder(head.next);
        if (pre.next != null && pre.next.next != null) {
            head.next.next = pre.next;
            pre.next = head.next;
            pre = pre.next.next;
            head.next = null;
        }
    }
}
```

## [剑指 Offer II 027. 回文链表](https://leetcode-cn.com/problems/aMhZSa/)

```java
class Solution {
    private ListNode pre;

    public boolean isPalindrome(ListNode head) {
        pre = head;
        return isPal(head);
    }

    public boolean isPal(ListNode head) {
        if (head == null) return true;
        if (!isPal(head.next)) {
            return false;
        }
        if (head.val != pre.val) return false;
        pre = pre.next;
        return true;
    }
}
```

## [剑指 Offer II 028. 展平多级双向链表](https://leetcode-cn.com/problems/Qv1Da2/)

```java
class Solution {
    public Node flatten(Node head) {
        getChild(head);
        return head;
    }

    private Node getChild(Node head) {
        while (head != null) {
            if (head.child != null) {
                Node ne = head.next;
                head.next = head.child;
                head.child.prev = head;
                Node childTail = getChild(head.child);
                head.child = null;
                if (ne != null) {
                    childTail.next = ne;
                    ne.prev = childTail;
                }
            } else {
                if (head.next != null) head = head.next;
                else break;
            }
        }
        return head;
    }
}
```

## [剑指 Offer II 077. 链表排序](https://leetcode-cn.com/problems/7WHec2/)

```java
class Solution {
    public ListNode sortList(ListNode head) {
        PriorityQueue<ListNode> pq = new PriorityQueue<>(Comparator.comparingInt(o -> o.val));
        while (head != null) {
            pq.offer(head);
            head = head.next;
        }
        ListNode pre = new ListNode(), p = pre;
        while (!pq.isEmpty()) {
            p.next = pq.poll();
            p = p.next;
        }
        p.next = null;
        return pre.next;
    }
}
```

## [剑指 Offer II 078. 合并排序链表](https://leetcode-cn.com/problems/vvXgSW/)

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        PriorityQueue<ListNode> pq = new PriorityQueue<>(Comparator.comparingInt(o -> o.val));
        ListNode ans = new ListNode(), p = ans;
        for (ListNode node : lists) {
            if (node != null) pq.offer(node);
        }
        while (!pq.isEmpty()) {
            ListNode temp = pq.poll();
            p.next = temp;
            p = p.next;
            if (temp.next != null) pq.offer(temp.next);
        }
        return ans.next;
    }
}
```

## [面试题 02.01. 移除重复节点](https://leetcode-cn.com/problems/remove-duplicate-node-lcci/)

```java
class Solution {
    public ListNode removeDuplicateNodes(ListNode head) {
        if (head == null) return null;
        ListNode ans = head;
        Set<Integer> set = new HashSet<>();
        set.add(head.val);
        while (head.next != null) {
            if (set.contains(head.next.val)) {
                head.next = head.next.next;
            } else {
                set.add(head.next.val);
                head = head.next;
            }
        }
        return ans;
    }
}
```

## [面试题 02.02. 返回倒数第 k 个节点](https://leetcode-cn.com/problems/kth-node-from-end-of-list-lcci/)

```java
class Solution {
    public int kthToLast(ListNode head, int k) {
        ListNode kst = head, ans = head;
        for (int i = 0; i < k; i++) {
            kst = kst.next;
        }
        while (kst != null) {
            kst = kst.next;
            ans = ans.next;
        }
        return ans.val;
    }
}
```

## [面试题 02.03. 删除中间节点](https://leetcode-cn.com/problems/delete-middle-node-lcci/)

```java
class Solution {
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```

## [面试题 02.04. 分割链表](https://leetcode-cn.com/problems/partition-list-lcci/)

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        if (head == null || head.next == null) return head;
        ListNode pre = new ListNode(0, head), p = pre, left = new ListNode(), l = left;
        while (p.next != null) {
            if (p.next.val < x) {
                l.next = p.next;
                l = l.next;
                p.next = p.next.next;
            } else {
                p = p.next;
            }
        }
        l.next = pre.next;
        return left.next;
    }
}
```

## [面试题 02.05. 链表求和](https://leetcode-cn.com/problems/sum-lists-lcci/)

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode pre = new ListNode(), p = pre;
        int carry = 0;
        while (l1 != null || l2 != null) {
            int val1 = l1 == null ? 0 : l1.val;
            int val2 = l2 == null ? 0 : l2.val;
            int num = (val1 + val2 + carry) % 10;
            p.next = new ListNode(num);
            p = p.next;
            carry = (val1 + val2 + carry) / 10;
            if (l1 != null) l1 = l1.next;
            if (l2 != null) l2 = l2.next;
        }
        if (carry > 0) {
            p.next = new ListNode(carry);
        }
        return pre.next;
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

## [面试题 02.08. 环路检测](https://leetcode-cn.com/problems/linked-list-cycle-lcci/)

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null) return null;
        ListNode slow = head, fast = head;
        do {
            slow = slow.next;
            fast = fast.next == null ? null : fast.next.next;
        } while (fast != null && slow != fast);
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

## [面试题 03.03. 堆盘子](https://leetcode-cn.com/problems/stack-of-plates-lcci/)

```java
class StackOfPlates {

    private List<Stack<Integer>> list;
    private int cap;

    public StackOfPlates(int cap) {
        list = new ArrayList<>();
        this.cap = cap;
    }

    public void push(int val) {
        if (cap <= 0) return;
        if (list.size() == 0 || list.get(list.size() - 1).size() == cap) list.add(new Stack<>());
        list.get(list.size() - 1).push(val);
    }

    public int pop() {
        if (list.size() == 0) return -1;
        int ans = list.get(list.size() - 1).pop();
        if (list.get(list.size() - 1).size() == 0) {
            list.remove(list.size() - 1);
        }
        return ans;
    }

    public int popAt(int index) {
        if (index < 0 || index >= list.size() || list.size() == 0) return -1;
        int ans = list.get(index).pop();
        if (list.get(index).size() == 0) {
            list.remove(index);
        }
        return ans;
    }
}
```

## [面试题 04.03. 特定深度节点链表](https://leetcode-cn.com/problems/list-of-depth-lcci/)

```java
class Solution {
    public ListNode[] listOfDepth(TreeNode tree) {
        Queue<TreeNode> queue = new LinkedList<>();
        List<ListNode> ans = new ArrayList<>();
        queue.offer(tree);
        while (!queue.isEmpty()) {
            int size = queue.size();
            ListNode pre = new ListNode(), p = pre;
            while (size-- > 0) {
                TreeNode t = queue.poll();
                if (t.left != null) queue.offer(t.left);
                if (t.right != null) queue.offer(t.right);
                p.next = new ListNode(t.val);
                p = p.next;
            }
            ans.add(pre.next);
        }
        ListNode[] res = new ListNode[ans.size()];
        for (int i = 0; i < ans.size(); i++) {
            res[i] = ans.get(i);
        }
        return res;
    }
}
```

## [面试题 17.12. BiNode](https://leetcode-cn.com/problems/binode-lcci/)

```java
class Solution {
    public TreeNode convertBiNode(TreeNode root) {
        if (root == null) return null;
        List<TreeNode> list = new ArrayList<>();
        getChild(root, list);
        for (int i = 1; i < list.size(); i++) {
            TreeNode pre = list.get(i - 1);
            TreeNode next = list.get(i);
            pre.left = null;
            pre.right = next;
            next.left = null;
            next.right = null;
        }
        return list.get(0);
    }

    public void getChild(TreeNode root, List<TreeNode> list) {
        if (root.left != null) getChild(root.left, list);
        list.add(root);
        if (root.right != null) getChild(root.right, list);
    }
}
```

**如何使用递归求解？**
