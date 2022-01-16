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
