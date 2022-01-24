# 栈（Stack）

## [20. 有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

很久很久之前的代码：

```java
class Solution {
    public boolean isValid(String s) {
        LinkedList<String> stack = new LinkedList<>();
        while (s.length() > 0) {
            String head = s.substring(0, 1);
            String last = stack.peekLast();
            if (("}".equals(head) && "{".equals(last)) ||
                    ("]".equals(head) && "[".equals(last)) ||
                    (")".equals(head) && "(".equals(last))) {
                stack.pollLast();
            } else {
                stack.add(head);
            }
            s = s.substring(1, s.length());
        }
        if (stack.size() == 0) {
            return true;
        }
        return false;
    }
}
```

使用栈：

```java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            switch (c) {
                case '(':
                case '{':
                case '[':
                    stack.push(c);
                    break;
                case ')':
                    if (stack.isEmpty() || stack.peek() != '(') return false;
                    stack.pop();
                    break;
                case '}':
                    if (stack.isEmpty() || stack.peek() != '{') return false;
                    stack.pop();
                    break;
                case ']':
                    if (stack.isEmpty() || stack.peek() != '[') return false;
                    stack.pop();
                    break;
            }
        }
        return stack.isEmpty();
    }
}
```

## [94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

使用递归实现：

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        getChild(root, res);
        return res;
    }
    private void getChild(TreeNode root, List<Integer> res) {
        if (root.left != null) getChild(root.left, res);
        res.add(root.val);
        if (root.right != null) getChild(root.right, res);
    }
}
```

使用双栈实现：

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        Stack<TreeNode> tStack = new Stack<>();
        tStack.add(root);
        Stack<Integer> iStack = new Stack<>();
        iStack.add(0);
        while (!iStack.isEmpty()) {
            int step = iStack.pop();
            switch (step) {
                case 0: {
                    iStack.push(1);
                    if (tStack.peek().left != null) {
                        tStack.push(tStack.peek().left);
                        iStack.push(0);
                    }
                }
                break;
                case 1: {
                    iStack.push(3);
                    iStack.push(2);
                    res.add(tStack.peek().val);
                }
                break;
                case 2: {
                    if (tStack.peek().right != null) {
                        tStack.push(tStack.peek().right);
                        iStack.push(0);
                    }
                }
                break;
                case 3: {
                    tStack.pop();
                }
                break;
            }
        }
        return res;
    }
}
```

## [144. 二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

使用递归：

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        getChild(root, res);
        return res;
    }
    private void getChild(TreeNode root, List<Integer> res) {
        res.add(root.val);
        if (root.left != null) getChild(root.left, res);
        if (root.right != null) getChild(root.right, res);
    }
}
```

使用双栈实现：

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        Stack<TreeNode> tStack = new Stack<>();
        tStack.push(root);
        Stack<Integer> iStack = new Stack<>();
        iStack.push(0);
        while (!iStack.isEmpty()) {
            int step = iStack.pop();
            switch (step) {
                case 0: {
                    res.add(tStack.peek().val);
                    iStack.push(3);
                    iStack.push(1);
                }
                break;
                case 1: {
                    iStack.push(2);
                    if (tStack.peek().left != null) {
                        tStack.push(tStack.peek().left);
                        iStack.push(0);
                    }
                }
                break;
                case 2: {
                    if (tStack.peek().right != null) {
                        tStack.push(tStack.peek().right);
                        iStack.push(0);
                    }
                }
                break;
                case 3: {
                    tStack.pop();
                }
                break;
            }
        }
        return res;
    }
}
```

## [145. 二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

使用递归实现：

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        getChild(root, res);
        return res;
    }

    private void getChild(TreeNode root, List<Integer> res) {
        if (root.left != null) getChild(root.left, res);
        if (root.right != null) getChild(root.right, res);
        res.add(root.val);
    }
}
```

使用双栈实现：

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        Stack<TreeNode> tStack = new Stack<>();
        tStack.push(root);
        Stack<Integer> iStack = new Stack<>();
        iStack.push(0);
        while (!tStack.isEmpty()) {
            int status = iStack.pop();
            switch (status) {
                case 0: {
                    iStack.push(1);
                    if (tStack.peek().left != null) {
                        tStack.push(tStack.peek().left);
                        iStack.push(0);
                    }
                }
                break;
                case 1: {
                    iStack.push(2);
                    if (tStack.peek().right != null) {
                        tStack.push(tStack.peek().right);
                        iStack.push(0);
                    }
                }
                break;
                case 2: {
                    res.add(tStack.pop().val);
                }
            }
        }
        return res;
    }
}
```

## [155. 最小栈](https://leetcode-cn.com/problems/min-stack/)

```java
class MinStack {

    private Stack<Integer> stack;

    public MinStack() {
        stack = new Stack<>();
    }

    public void push(int val) {
        stack.push(val);
    }

    public void pop() {
        stack.pop();
    }

    public int top() {
        return stack.peek();
    }

    public int getMin() {
        int min = stack.peek();
        for (Iterator<Integer> it = stack.iterator(); it.hasNext(); ) {
            min = Math.min(min, it.next());
        }
        return min;
    }
}
```

使用辅助栈：

```java
class MinStack {

    Stack<Integer> stack, mStack;

    public MinStack() {
        stack = new Stack<>();
        mStack = new Stack<>();
    }

    public void push(int val) {
        stack.push(val);
        int min = mStack.isEmpty() ? val : Math.min(mStack.peek(), val);
        mStack.push(min);
    }

    public void pop() {
        stack.pop();
        mStack.pop();
    }

    public int top() {
        return stack.peek();
    }

    public int getMin() {
        return mStack.peek();
    }
}
```

## [225. 用队列实现栈](https://leetcode-cn.com/problems/implement-stack-using-queues/)

这是什么狗屁实现：

```java
class MyStack {

    private List<Integer> nodes;

    public MyStack() {
        nodes = new ArrayList<>();
    }
    
    public void push(int x) {
        nodes.add(x);
    }
    
    public int pop() {
        int result = -1;
        if(nodes.size() > 0) {
            result = nodes.get(nodes.size() -1);
            nodes.remove(nodes.size() - 1);
        }
        return result;
    }
    
    public int top() {
        if(nodes.size() > 0) {
            return nodes.get(nodes.size() - 1);
        }
        return -1;
    }
    
    public boolean empty() {
        return nodes.size() == 0;
    }
}
```

使用双栈实现：

```java
class MyStack {

    Queue<Integer> q1, q2;

    public MyStack() {
        q1 = new LinkedList<>();
        q2 = new LinkedList<>();
    }

    public void push(int x) {
        if (q1.isEmpty()) {
            q1.offer(x);
            while (!q2.isEmpty()) {
                q1.offer(q2.poll());
            }
        } else {
            q2.offer(x);
            while (!q1.isEmpty()) {
                q2.offer(q1.poll());
            }
        }
    }

    public int pop() {
        if (!q1.isEmpty()) return q1.poll();
        else return q2.poll();
    }

    public int top() {
        if (!q1.isEmpty()) return q1.peek();
        else return q2.peek();
    }

    public boolean empty() {
        return q1.isEmpty() && q2.isEmpty();
    }
}
```

使用单栈实现：

```java
class MyStack {

    Queue<Integer> queue;

    public MyStack() {
        queue = new LinkedList<>();
    }

    public void push(int x) {
        int len = queue.size();
        queue.offer(x);
        while (len-- > 0) {
            queue.offer(queue.poll());
        }
    }

    public int pop() {
        return queue.poll();
    }

    public int top() {
        return queue.peek();
    }

    public boolean empty() {
        return queue.isEmpty();
    }
}
```

## [227. 基本计算器 II](https://leetcode-cn.com/problems/basic-calculator-ii/)

使用优先级实现（超时）：

```java
class Solution {
    public int calculate(String s) {
        char[] ch = s.toCharArray();
        return calculate(ch, 0, ch.length - 1);
    }
    public int calculate(char[] ch, int left, int right) {
        int op = -1, pri = 10000 - 1, cur_pri = 0, temp = 0;
        for (int i = left; i <= right; i++) {
            cur_pri = 10000;
            switch (ch[i]) {
                case '+':
                case '-':
                    cur_pri = 1 + temp;
                    break;
                case '*':
                case '/':
                    cur_pri = 2 + temp;
                    break;
                case '(':
                    temp += 100;
                    break;
                case ')':
                    temp -= 100;
                    break;
            }
            if (cur_pri <= pri) {
                pri = cur_pri;
                op = i;
            }

        }
        if (op == -1) {
            int num = 0;
            for (int j = left; j <= right; j++) {
                if (ch[j] < '0' || ch[j] > '9') continue;
                num = num * 10 + (ch[j] - '0');
            }
            return num;
        }
        int a = calculate(ch, left, op - 1);
        int b = calculate(ch, op + 1, right);
        switch (ch[op]) {
            case '+':
                return a + b;
            case '-':
                return a - b;
            case '*':
                return a * b;
            case '/':
                return a / b;
        }
        return 0;
    }
}
```

使用栈实现：

```java
class Solution {
    public int calculate(String s) {
        s += '@';
        char[] ch = s.toCharArray();
        Stack<Integer> numberStack = new Stack<>();
        Stack<Character> symbolStack = new Stack<>();
        for (int i = 0, num = 0; i < ch.length; i++) {
            char c = ch[i];
            if (c == ' ') continue;
            if (c - '0' >= 0 && c - '9' <= 0) {
                num = num * 10 + (c - '0');
                continue;
            }
            numberStack.push(num);
            num = 0;
            while (!symbolStack.empty() && level(c) <= level(symbolStack.peek())) {
                int b = numberStack.pop();
                int a = numberStack.pop();
                numberStack.push(calc(a, symbolStack.pop(), b));
            }
            symbolStack.push(c);
        }
        return numberStack.pop();
    }

    private int calc(int a, char symbol, int b) {
        switch (symbol) {
            case '+':
                return a + b;
            case '-':
                return a - b;
            case '*':
                return a * b;
            case '/':
                return a / b;
        }
        return -1;
    }

    private int level(char c) {
        switch (c) {
            case '@':
                return -1;
            case '+':
            case '-':
                return 1;
            case '*':
            case '/':
                return 2;
        }
        return 0;
    }
}
```

## [232. 用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

```java
class MyQueue {

    private Stack<Integer> stack1;
    private Stack<Integer> stack2;

    public MyQueue() {
        stack1 = new Stack<>();
        stack2 = new Stack<>();
    }

    public void push(int x) {
        stack1.push(x);

    }

    public int pop() {
        if (stack2.isEmpty()) {
            while (!stack1.isEmpty()) {
                stack2.push(stack1.pop());
            }
        }
        return stack2.pop();
    }

    public int peek() {
        if (stack2.isEmpty()) {
            while (!stack1.isEmpty()) {
                stack2.push(stack1.pop());
            }
        }
        return stack2.peek();
    }

    public boolean empty() {
        return stack1.isEmpty() && stack2.isEmpty();
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

## [331. 验证二叉树的前序序列化](https://leetcode-cn.com/problems/verify-preorder-serialization-of-a-binary-tree/)

```java
class Solution {
    public boolean isValidSerialization(String preorder) {
        String[] split = preorder.split(",");
        List<String> list = new ArrayList<>();
        for (String s : split) {
            list.add(s);
            while (list.size() >= 3 && "#".equals(list.get(list.size() - 1)) && "#".equals(list.get(list.size() - 2)) && !"#".equals(list.get(list.size() - 3))) {
                list.remove(list.size() - 1);
                list.remove(list.size() - 1);
                list.set(list.size() - 1, "#");
            }
        }
        return list.size() == 1 && "#".equals(list.get(0));
    }
}
```

## [341. 扁平化嵌套列表迭代器](https://leetcode-cn.com/problems/flatten-nested-list-iterator/)

```java
public class NestedIterator implements Iterator<Integer> {

    private List<Integer> list;

    public NestedIterator(List<NestedInteger> nestedList) {
        list = new ArrayList<>();
        getList(nestedList, list);
    }

    private void getList(List<NestedInteger> nestedList, List<Integer> list) {
        for (NestedInteger n : nestedList) {
            if (n.isInteger()) {
                list.add(n.getInteger());
            } else {
                getList(n.getList(), list);
            }
        }
    }

    @Override
    public Integer next() {
        return list.remove(0);
    }

    @Override
    public boolean hasNext() {
        return list.size() > 0;
    }
}
```

## [636. 函数的独占时间](https://leetcode-cn.com/problems/exclusive-time-of-functions/)

```java
class Solution {
    public int[] exclusiveTime(int n, List<String> logs) {
        int[] ans = new int[n];
        int lastIndex = 0;
        Stack<Integer> iStack = new Stack<>();
        for (int i = 0; i < logs.size(); i++) {
            String s = logs.get(i);
            String[] split = s.split(":");
            int left = Integer.parseInt(split[0]);
            int right = Integer.parseInt(split[2]);
            if ("start".equals(split[1])) {
                if (!iStack.empty()) {
                    ans[iStack.peek()] += (right - lastIndex);
                }
                iStack.push(left);
                lastIndex = right;
            } else {
                ans[left] += right - lastIndex + 1;
                iStack.pop();
                lastIndex = right + 1;
            }
        }
        return ans;
    }
}
```

## [682. 棒球比赛](https://leetcode-cn.com/problems/baseball-game/)

```java
class Solution {
    public int calPoints(String[] ops) {
        Stack<Integer> stack = new Stack<>();
        for (String s : ops) {
            if ("+".equals(s)) {
                int top = stack.pop();
                int sum = top + stack.peek();
                stack.push(top);
                stack.push(sum);
            } else if ("D".equals(s)) {
                stack.push(stack.peek() * 2);
            } else if ("C".equals(s)) {
                stack.pop();
            } else {
                stack.push(Integer.parseInt(s));
            }
        }
        int sum = 0;
        while (!stack.isEmpty()) {
            sum += stack.pop();
        }
        return sum;
    }
}
```

## [844. 比较含退格的字符串](https://leetcode-cn.com/problems/backspace-string-compare/)

```java
class Solution {
    public boolean backspaceCompare(String s, String t) {
        Stack<Character> sStack = new Stack<>();
        Stack<Character> tStack = new Stack<>();
        transform(s, sStack);
        transform(t, tStack);
        if (sStack.size() != tStack.size()) return false;
        while (!sStack.isEmpty()) {
            if (sStack.pop() != tStack.pop()) return false;
        }
        return true;
    }

    private void transform(String t, Stack<Character> tStack) {
        for (int i = 0; i < t.length(); i++) {
            char c = t.charAt(i);
            if (c == '#') {
                if (!tStack.isEmpty()) tStack.pop();
            } else {
                tStack.push(c);
            }
        }
    }
}
```

## [946. 验证栈序列](https://leetcode-cn.com/problems/validate-stack-sequences/)

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Stack<Integer> stack = new Stack<>();
        for (int i = 0, j = 0; i < popped.length; i++) {
            while (j < pushed.length && (stack.isEmpty() || stack.peek() != popped[i])) {
                stack.push(pushed[j]);
                j++;
            }
            if (stack.pop() != popped[i]) return false;
        }
        return true;
    }
}
```

## [1021. 删除最外层的括号](https://leetcode-cn.com/problems/remove-outermost-parentheses/)

```java
class Solution {
    public String removeOuterParentheses(String s) {
        String result = "";
        for (int i = 0, pre = 0, count = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(') count++;
            else count--;
            if (count != 0) continue;
            result += s.substring(pre + 1, i);
            pre = i + 1;
        }
        return result;
    }
}
```

优化：

```java
class Solution {
    public String removeOuterParentheses(String s) {
        StringBuilder sb = new StringBuilder();
        int count = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') {
                count++;
                if (count > 1) sb.append(c);
            } else {
                count--;
                if (count >= 1) sb.append(c);
            }
        }
        return sb.toString();
    }
}
```

## [1249. 移除无效的括号](https://leetcode-cn.com/problems/minimum-remove-to-make-valid-parentheses/)

```java
class Solution {
    public StringBuilder remove(String s, char l, char r) {
        StringBuilder sb = new StringBuilder();
        int count = 0;
        char[] chars = s.toCharArray();
        for (int i = 0; i < chars.length; i++) {
            if (chars[i] == l) {
                count++;
            } else if (chars[i] == r) {
                if (count == 0) continue;
                count--;
            }
            sb.append(chars[i]);
        }
        return sb;
    }

    public String minRemoveToMakeValid(String s) {
        StringBuilder sb = remove(s, '(', ')');
        sb = remove(sb.reverse().toString(), ')', '(');
        return sb.reverse().toString();
    }
}
```

使用栈的解决方式：

```java
class Solution {
    public String minRemoveToMakeValid(String s) {
        Stack<Integer> stack = new Stack<>();
        Set<Integer> set = new HashSet<>();
        StringBuilder sb = new StringBuilder();
        char[] chars = s.toCharArray();
        for (int i = 0; i < chars.length; i++) {
            char c = chars[i];
            if (c == '(') {
                stack.push(i);
            } else if (c == ')') {
                if (stack.isEmpty()) set.add(i);
                else stack.pop();
            }
        }
        while (!stack.isEmpty()) set.add(stack.pop());
        for (int i = 0; i < chars.length; i++) {
            if (!set.contains(i)) sb.append(chars[i]);
        }
        return sb.toString();
    }
}
```

使用计数器优化：

```java
class Solution {
    public String minRemoveToMakeValid(String s) {
        StringBuilder sb = new StringBuilder();
        int count = 0, b = 0;
        char[] chars = s.toCharArray();
        for (int i = 0; i < chars.length; i++) {
            if (chars[i] == '(') {
                count++;
                b++;
            } else if (chars[i] == ')') {
                if (count == 0) continue;
                count--;
            }
            sb.append(chars[i]);
        }
        b = b - count;
        chars = sb.toString().toCharArray();
        sb = new StringBuilder();
        for (int i = 0; i < chars.length; i++) {
            if (chars[i] == '(') {
                b--;
                if (b < 0) continue;
            }
            sb.append(chars[i]);
        }
        return sb.toString();
    }
}
```

## [1614. 括号的最大嵌套深度](https://leetcode-cn.com/problems/maximum-nesting-depth-of-the-parentheses/)

使用递归实现：

```java
class Solution {
    public int maxDepth(String s) {
        char[] ch = s.toCharArray();
        Stack<Character> stack = new Stack<>();
        int max = 0;
        for (char c : ch) {
            if (c == '(') {
                stack.push(c);
                max = Math.max(max, stack.size());
            } else if (c == ')') {
                stack.pop();
            }
        }
        return max;
    }
}
```

使用计数器进行优化：

```java
class Solution {
    public int maxDepth(String s) {
        char[] ch = s.toCharArray();
        int max = 0;
        for (int i = 0, count = 0; i < ch.length; i++) {
            if (ch[i] == '(') {
                count++;
                max = Math.max(max, count);
            } else if (ch[i] == ')') {
                count--;
            }
        }
        return max;
    }
}
```

## [1700. 无法吃午餐的学生数量](https://leetcode-cn.com/problems/number-of-students-unable-to-eat-lunch/)

```java
class Solution {
    public int countStudents(int[] students, int[] sandwiches) {
        int[] stuCount = new int[2];
        for (int i = 0; i < students.length; i++) {
            stuCount[students[i]]++;
        }
        for (int i = 0; i < sandwiches.length; i++) {
            if (stuCount[sandwiches[i]] == 0) {
                return stuCount[2 - sandwiches[i] - 1];
            } else {
                stuCount[sandwiches[i]]--;
            }
        }
        return 0;
    }
}
```

## [面试题 02.06. 回文链表](https://leetcode-cn.com/problems/palindrome-linked-list-lcci/)

```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        List<Integer> list = new ArrayList<>();
        while (head != null) {
            list.add(head.val);
            head = head.next;
        }
        for (int i = 0, j = list.size() - 1; i <= j; i++, j--) {
            if (!list.get(i).equals(list.get(j))) return false;
        }
        return true;
    }
}
```

使用递归实现：

```java
class Solution {
    private ListNode pre;

    public boolean recursion(ListNode head) {
        if (head.next != null) {
            if (!recursion(head.next)) return false;
        }
        if (pre.val != head.val) return false;
        pre = pre.next;
        return true;
    }

    public boolean isPalindrome(ListNode head) {
        if (head == null) return true;
        pre = head;
        return recursion(head);
    }

}
```

## [面试题 03.02. 栈的最小值](https://leetcode-cn.com/problems/min-stack-lcci/)

```java
class MinStack {

    private Stack<Integer> iStack, mStack;

    public MinStack() {
        iStack = new Stack<>();
        mStack = new Stack<>();
    }

    public void push(int x) {
        iStack.push(x);
        if (mStack.empty()) mStack.push(x);
        else mStack.push(Math.min(mStack.peek(), x));
    }

    public void pop() {
        iStack.pop();
        mStack.pop();
    }

    public int top() {
        return iStack.peek();
    }

    public int getMin() {
        return mStack.peek();
    }
}
```

## [面试题 03.04. 化栈为队](https://leetcode-cn.com/problems/implement-queue-using-stacks-lcci/)

```java
class MyQueue {

    private Stack<Integer> stack1, stack2;

    /**
     * Initialize your data structure here.
     */
    public MyQueue() {
        stack1 = new Stack<>();
        stack2 = new Stack<>();
    }

    /**
     * Push element x to the back of queue.
     */
    public void push(int x) {
        stack2.push(x);
    }

    /**
     * Removes the element from in front of queue and returns that element.
     */
    public int pop() {
        transfer();
        return stack1.pop();
    }

    private void transfer() {
        if (!stack1.isEmpty()) return;
        while (!stack2.isEmpty()) {
            stack1.push(stack2.pop());
        }
    }

    /**
     * Get the front element.
     */
    public int peek() {
        transfer();
        return stack1.peek();
    }

    /**
     * Returns whether the queue is empty.
     */
    public boolean empty() {
        return stack1.isEmpty() && stack2.isEmpty();
    }
}
```