# 队列（Queue）

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
        while (!stack1.isEmpty()) {
            stack2.push(stack1.pop());
        }
        stack1.push(x);
        while (!stack2.isEmpty()) {
            stack1.push(stack2.pop());
        }
    }

    public int pop() {
        if (stack1.isEmpty()) return -1;
        return stack1.pop();
    }

    public int peek() {
        return stack1.peek();
    }

    public boolean empty() {
        return stack1.isEmpty();
    }
}
```

效率优化：

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

## [239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

超时：

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int[] result = new int[nums.length - k + 1];
        for (int head = 0, tail = k - 1; tail < nums.length; head++, tail++) {
            int max = getMax(nums, head, tail);
            result[head] = max;
        }
        return result;
    }

    private int getMax(int[] nums, int head, int tail) {
        int max = nums[head];
        for (int i = head; i <= tail; i++) {
            if (nums[i] > max) max = nums[i];
        }
        return max;
    }
}
```

超时优化：

```java

class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int[] result = new int[nums.length - k + 1];
        int max = getMax(nums, 0, k - 1);
        for (int head = 0, tail = k - 1; tail < nums.length; head++, tail++) {
            if (nums[tail] >= max) {
                max = nums[tail];
            } else {
                max = getMax(nums, head, tail);
            }
            result[head] = max;
        }
        return result;
    }

    private int getMax(int[] nums, int head, int tail) {
        int max = nums[head];
        for (int i = head; i <= tail; i++) {
            if (nums[i] > max) max = nums[i];
        }
        return max;
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

## [387. 字符串中的第一个唯一字符](https://leetcode-cn.com/problems/first-unique-character-in-a-string/)

```java
class Solution {
    public int firstUniqChar(String s) {
        HashMap<Character, Integer> map = new HashMap<>();
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (map.get(c) == 1) {
                return i;
            }
        }
        return -1;
    }
}
```

使用数组代替哈希表：

```java
class Solution {
    public int firstUniqChar(String s) {
        int[] count = new int[26];
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            count[c - 'a']++;
        }
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (count[c - 'a'] == 1) return i;
        }
        return -1;
    }
}
```

遍历哈希表方式：

```java
class Solution {
    public int firstUniqChar(String s) {
        Map<Character, Integer> map = new HashMap<>();
        int length = s.length();
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (map.containsKey(c)) {
                map.put(c, -1);
            } else {
                map.put(c, i);
            }
        }
        int index = length;
        for (Map.Entry<Character, Integer> entry : map.entrySet()) {
            if (entry.getValue() != -1 && entry.getValue() < index) {
                index = entry.getValue();
            }
        }
        return index == length ? -1 : index;
    }
}
```

使用队列的方式：

```java
class Solution {
    public int firstUniqChar(String s) {
        int[] count = new int[26];
        Queue<Pair> queue = new LinkedList<>();
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            count[c - 'a']++;
            queue.offer(new Pair(c, i));
        }
        while (!queue.isEmpty() && count[queue.peek().c - 'a'] != 1) {
            queue.poll();
        }
        return queue.isEmpty() ? -1 : queue.peek().index;
    }

    class Pair {
        char c;
        int index;

        Pair(char c, int index) {
            this.c = c;
            this.index = index;
        }
    }
}
```

## [621. 任务调度器](https://leetcode-cn.com/problems/task-scheduler/)

```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] count = new int[26];
        for (int i = 0; i < tasks.length; i++) {
            count[tasks[i] - 'A']++;
        }
        Arrays.sort(count);
        int m = 0;
        for (int i = 25; i >= 0; i--) {
            if (count[i] == count[25]) {
                m++;
            } else {
                break;
            }
        }
        return Math.max(tasks.length, (count[25] - 1) * (n + 1) + m);
    }
}
```

## [622. 设计循环队列](https://leetcode-cn.com/problems/design-circular-queue/)

```java
class MyCircularQueue {

    private int[] array;
    private int head, tail, count;

    public MyCircularQueue(int k) {
        array = new int[k];
        head = 0;
        tail = 0;
        count = 0;
    }

    public boolean enQueue(int value) {
        if (isFull()) return false;
        array[tail] = value;
        tail = (tail + 1) % array.length;
        count++;
        return true;
    }

    public boolean deQueue() {
        if (isEmpty()) return false;
        head = (head + 1) % array.length;
        count--;
        return true;
    }

    public int Front() {
        if (isEmpty()) return -1;
        return array[head];
    }

    public int Rear() {
        if (isEmpty()) return -1;
        return array[(tail - 1 + array.length) % array.length];
    }

    public boolean isEmpty() {
        return count == 0;
    }

    public boolean isFull() {
        return count == array.length;
    }
}
```

## [641. 设计循环双端队列](https://leetcode-cn.com/problems/design-circular-deque/)

```java
class MyCircularDeque {

    private int[] array;
    private int head, tail, count;

    public MyCircularDeque(int k) {
        array = new int[k];
        head = 0;
        tail = 0;
        count = 0;
    }

    public boolean insertFront(int value) {
        if (isFull()) return false;
        head = (head - 1 + array.length) % array.length;
        array[head] = value;
        count++;
        return true;
    }

    public boolean insertLast(int value) {
        if (isFull()) return false;
        array[tail] = value;
        tail = (tail + 1) % array.length;
        count++;
        return true;
    }

    public boolean deleteFront() {
        if (isEmpty()) return false;
        head = (head + 1) % array.length;
        count--;
        return true;
    }

    public boolean deleteLast() {
        if (isEmpty()) return false;
        tail = (tail - 1 + array.length) % array.length;
        count--;
        return true;
    }

    public int getFront() {
        if (isEmpty()) return -1;
        return array[head];
    }

    public int getRear() {
        if (isEmpty()) return -1;
        return array[(tail - 1 + array.length) % array.length];
    }

    public boolean isEmpty() {
        return count == 0;
    }

    public boolean isFull() {
        return count == array.length;
    }
}
```

## [649. Dota2 参议院](https://leetcode-cn.com/problems/dota2-senate/)

```java
class Solution {
    public String predictPartyVictory(String senate) {
        int length = senate.length();
        Queue<Integer> radiant = new LinkedList<>();
        Queue<Integer> dire = new LinkedList<>();
        for (int i = 0; i < length; i++) {
            if (senate.charAt(i) == 'R') radiant.offer(i);
            else dire.offer(i);
        }
        while (!radiant.isEmpty() && !dire.isEmpty()) {
            int r = radiant.poll();
            int d = dire.poll();
            if (r < d) radiant.offer(r + length);
            else dire.offer(d + length);
        }
        return radiant.isEmpty() ? "Dire" : "Radiant";
    }
}
```

## [859. 亲密字符串](https://leetcode-cn.com/problems/buddy-strings/)

```java
class Solution {
    public boolean has_repeat(String s) {
        int[] arr = new int[26];
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            arr[c - 'a'] += 1;
            if (arr[c - 'a'] == 2) return true;
        }
        return false;
    }

    public boolean buddyStrings(String s, String goal) {
        if (s.length() != goal.length()) return false;
        if (s.equals(goal)) return has_repeat(s);
        int i = 0;
        while (s.charAt(i) == goal.charAt(i)) i++;
        int j = i + 1;
        while (j < s.length() && s.charAt(j) == goal.charAt(j)) j++;
        if (j == s.length()) return false;
        if (s.charAt(i) != goal.charAt(j) || s.charAt(j) != goal.charAt(i)) return false;
        j++;
        while (j < s.length()) {
            if (s.charAt(j) != goal.charAt(j)) return false;
            j++;
        }
        return true;
    }
}
```

## [860. 柠檬水找零](https://leetcode-cn.com/problems/lemonade-change/)

```java
class Solution {
    public boolean lemonadeChange(int[] bills) {
        int d5 = 0, d10 = 0;
        for (int i = 0; i < bills.length; i++) {
            if (bills[i] == 5) d5++;
            if (bills[i] == 10) {
                if (d5 == 0) return false;
                d10++;
                d5--;
            }
            if (bills[i] == 20) {
                if (d10 >= 1 && d5 >= 1) {
                    d10--;
                    d5--;
                } else if (d10 == 0 && d5 >= 3) {
                    d5 -= 3;
                } else {
                    return false;
                }
            }
        }
        return true;
    }
}
```

## [933. 最近的请求次数](https://leetcode-cn.com/problems/number-of-recent-calls/)

```java
class RecentCounter {
    private Queue<Integer> queue;

    public RecentCounter() {
        queue = new LinkedList<>();
    }

    public int ping(int t) {
        queue.add(t);
        while (t - queue.peek() > 3000) {
            queue.poll();
        }
        return queue.size();
    }
}
```

## [950. 按递增顺序显示卡牌](https://leetcode-cn.com/problems/reveal-cards-in-increasing-order/)

```java
class Solution {
    public int[] deckRevealedIncreasing(int[] deck) {
        int length = deck.length;
        int[] result = new int[length];
        Deque<Integer> index = new LinkedList<>();
        for (int i = 0; i < length; i++) {
            index.add(i);
        }
        Arrays.sort(deck);
        for (int i = 0; i < deck.length; i++) {
            result[index.pollFirst()] = deck[i];
            if (!index.isEmpty()) index.add(index.pollFirst());
        }
        return result;
    }
}
```

## [969. 煎饼排序](https://leetcode-cn.com/problems/pancake-sorting/)

```java
class Solution {
    public List<Integer> pancakeSort(int[] arr) {
        int[] index = new int[arr.length + 1];
        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < arr.length; i++) {
            index[arr[i]] = i;
        }
        for (int i = arr.length; i >= 1; i--) {
            if (index[i] == i - 1) continue;
            if (index[i] != 0) {
                result.add(index[i] + 1);
                reverse(arr, index[i] + 1, index);
            }
            if (i != 1) {
                result.add(i);
                reverse(arr, i, index);
            }
        }
        return result;
    }

    private void reverse(int[] arr, int n, int[] index) {
        for (int i = 0, j = n - 1; i < j; i++, j--) {
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
            index[arr[i]] = i;
            index[arr[j]] = j;
        }
    }
}
```

```java
class Solution {
    public List<Integer> pancakeSort(int[] arr) {
        int[] index = new int[arr.length];
        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < arr.length; i++) {
            index[arr[i] - 1] = i;
        }
        for (int i = arr.length - 1; i >= 0; i--) {
            if (index[i] == i) continue;
            if (index[i] != 0) {
                result.add(index[i] + 1);
                reverse(arr, index[i] + 1, index);
            }
            if (i != 0) {
                result.add(i + 1);
                reverse(arr, i + 1, index);
            }
        }
        return result;
    }

    private void reverse(int[] arr, int n, int[] index) {
        for (int i = 0, j = n - 1; i < j; i++, j--) {
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
            index[arr[i] - 1] = i;
            index[arr[j] - 1] = j;
        }
    }
}
```

## [1670. 设计前中后队列](https://leetcode-cn.com/problems/design-front-middle-back-queue/)

```java
class Node {
    int val;
    Node next, pre;

    Node() {}

    Node(int val) {
        this.val = val;
    }

    void insert_pre(Node p) {
        p.pre = pre;
        p.next = this;
        if (pre != null) {
            pre.next = p;
        }
        pre = p;
    }

    void insert_next(Node p) {
        p.pre = this;
        p.next = next;
        if (next != null) next.pre = p;
        next = p;
    }

    void delete_pre() {
        if (pre == null) return;
        pre = pre.pre;
        if (pre != null) pre.next = this;
    }

    void delete_next() {
        if (next == null) return;
        next = next.next;
        if (next != null) next.pre = this;
    }
}

class Dequeue {
    Node head = new Node(), tail = new Node();
    int size;

    public Dequeue() {
        head.next = tail;
        tail.pre = head;
        size = 0;
    }

    public void push_back(int val) {
        tail.insert_pre(new Node(val));
        size++;
    }

    public void push_front(int val) {
        head.insert_next(new Node(val));
        size++;
    }

    public int pop_back() {
        if (isEmpty()) return -1;
        int result = tail.pre.val;
        tail.delete_pre();
        size--;
        return result;
    }

    public int pop_front() {
        if (isEmpty()) return -1;
        int result = head.next.val;
        head.delete_next();
        size--;
        return result;
    }

    public boolean isEmpty() {
        return head.next == tail;
    }

    public int front() {
        return head.next.val;
    }

    public int back() {
        return tail.pre.val;
    }

    public int size() {
        return size;
    }
}

class FrontMiddleBackQueue {

    private Dequeue d1, d2;

    public FrontMiddleBackQueue() {
        d1 = new Dequeue();
        d2 = new Dequeue();
    }

    public void pushFront(int val) {
        d1.push_front(val);
        update();
    }

    public void pushMiddle(int val) {
        if (d1.size() > d2.size()) {
            d2.push_front(d1.back());
            d1.pop_back();
        }
        d1.push_back(val);
    }

    public void pushBack(int val) {
        d2.push_back(val);
        update();
    }

    public int popFront() {
        if (isEmpty()) return -1;
        int result = d1.pop_front();
        update();
        return result;
    }

    public int popMiddle() {
        if (isEmpty()) return -1;
        int result = d1.pop_back();
        update();
        return result;
    }

    public int popBack() {
        if (isEmpty()) return -1;
        int result;
        if (d2.isEmpty()) {
            result = d1.pop_back();
        } else {
            result = d2.pop_back();
        }
        update();
        return result;
    }

    public boolean isEmpty() {
        return d1.size == 0;
    }

    public void update() {
        if (d1.size < d2.size) {
            d1.push_back(d2.front());
            d2.pop_front();
        }
        if (d1.size == d2.size + 2) {
            d2.push_front(d1.back());
            d1.pop_back();
        }
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

## [2073. 买票需要的时间](https://leetcode-cn.com/problems/time-needed-to-buy-tickets/)

```java
class Solution {
    public int timeRequiredToBuy(int[] tickets, int k) {
        int target = tickets[k];
        int count = 0;
        for (int i = 0; i < tickets.length; i++) {
            if (i <= k) {
                count += tickets[i] <= target ? tickets[i] : target;
            } else {
                count += tickets[i] <= target - 1 ? tickets[i] : target - 1;
            }
        }
        return count;
    }
}
```

## [面试题 17.09. 第 k 个数](https://leetcode-cn.com/problems/get-kth-magic-number-lcci/)

```java
class Solution {

    public int getKthMagicNumber(int k) {
        int[] array = new int[k];
        array[0] = 1;
        int l3 = 0, l5 = 0, l7 = 0;
        for (int i = 1; i < k; i++) {
            int min = Math.min(3 * array[l3], 5 * array[l5]);
            min = Math.min(min, 7 * array[l7]);
            array[i] = min;
            if (3 * array[l3] == min) l3++;
            if (5 * array[l5] == min) l5++;
            if (7 * array[l7] == min) l7++;
        }
        return array[k - 1];
    }
}
```

