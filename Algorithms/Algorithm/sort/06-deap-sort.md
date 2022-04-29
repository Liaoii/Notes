## 6. 堆排序

```java
class Deap {
    private int[] data;
    private int count;

    public Deap() {
        data = new int[1000];
        count = 0;
    }

    public void push(int x) {
        data[count++] = x;
        shift_up(count - 1);
    }

    public void pop() {
        if (size() == 0) return;
        data[0] = data[count - 1];
        count--;
        shift_down(0);
    }

    private void shift_up(int index) {
        while (index > 0 && data[(index - 1) / 2] < data[index]) {
            int temp = data[(index - 1) / 2];
            data[(index - 1) / 2] = data[index];
            data[index] = temp;
            index = (index - 1) / 2;
        }
    }

    private void shift_down(int index) {
        int n = count - 1;
        while (index * 2 + 1 <= n) {
            int t = index;
            if (data[t] < data[index * 2 + 1]) t = index * 2 + 1;
            if (index * 2 + 2 <= n && data[t] < data[index * 2 + 2]) t = index * 2 + 2;
            if (t == index) break;
            int temp = data[t];
            data[t] = data[index];
            data[index] = temp;
            index = t;
        }
    }

    public int top() {
        return data[0];
    }

    public int size() {
        return count;
    }
}
```

