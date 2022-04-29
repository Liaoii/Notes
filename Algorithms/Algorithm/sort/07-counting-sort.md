## 7. 计数排序

```java
public static void countingSort1(int[] arr) {
    int[] counting = new int[10];
    for (int element : arr) {
        counting[element]++;
    }
    int index = 0;
    for (int i = 0; i < 10; i++) {
        while (counting[i] != 0) {
            arr[index++] = i;
            counting[i]--;
        }
    }
}

public static void countingSort2(int[] arr) {
    int[] counting = new int[10];
    HashMap<Integer, Queue<Integer>> records = new HashMap<>();

    for (int element : arr) {
        counting[element]++;
        if (!records.containsKey(element)) {
            records.put(element, new LinkedList<>());
        }
        records.get(element).offer(element);
    }
    int index = 0;
    for (int i = 0; i < 10; i++) {
        while (counting[i] != 0) {
            arr[index++] = records.get(i).poll();
            counting[i]--;
        }
    }
}

public static void countingSort3(int[] arr) {
    int[] counting = new int[10];

    for (int element : arr) {
        counting[element]++;
    }
    int preCounts = 0;
    for (int i = 0; i < counting.length; i++) {
        int temp = counting[i];
        counting[i] = preCounts;
        preCounts += temp;
    }
    int[] result = new int[arr.length];
    for (int element : arr) {
        int index = counting[element];
        result[index] = element;
        counting[element]++;
    }
    for (int i = 0; i < arr.length; i++) {
        arr[i] = result[i];
    }
}

public static void countingSort4(int[] arr) {
    if (arr == null || arr.length <= 1) return;
    int max = arr[0];
    int min = arr[0];
    for (int i = 1; i < arr.length; i++) {
        if (arr[i] > max) max = arr[i];
        else if (arr[i] < min) min = arr[i];
    }
    int range = max - min + 1;
    int[] counting = new int[range];
    for (int element : arr) {
        counting[element - min]++;
    }
    int preCounts = 0;
    for (int i = 0; i < range; i++) {
        preCounts += counting[i];
        counting[i] = preCounts - counting[i];
    }
    int[] result = new int[arr.length];
    for (int element : arr) {
        result[counting[element - min]] = element;
        counting[element - min]++;
    }
    for (int i = 0; i < arr.length; i++) {
        arr[i] = result[i];
    }
}

public static void countingSort5(int[] arr) {
    if (arr == null || arr.length <= 1) return;
    int max = arr[0];
    int min = arr[0];
    for (int i = 1; i < arr.length; i++) {
        if (arr[i] > max) max = arr[i];
        else if (arr[i] < min) min = arr[i];
    }
    int range = max - min + 1;
    int[] counting = new int[range];
    for (int element : arr) {
        counting[element - min]++;
    }
    counting[0]--;
    for (int i = 1; i < range; i++) {
        counting[i] += counting[i - 1];
    }
    int[] result = new int[arr.length];
    for (int i = arr.length - 1; i >= 0; i--) {
        result[counting[arr[i] - min]] = arr[i];
        counting[arr[i] - min]--;
    }
    for (int i = 0; i < arr.length; i++) {
        arr[i] = result[i];
    }
}
```
