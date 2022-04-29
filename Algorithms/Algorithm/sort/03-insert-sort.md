## 3. 插入排序

交换法插入排序：

```java
public static void insertSort(int[] arr) {
    for (int i = 1; i < arr.length; i++) {
        int j = i;
        while (j >= 1 && arr[j] < arr[j - 1]) {
            int temp = arr[j];
            arr[j] = arr[j - 1];
            arr[j - 1] = temp;
            j--;
        }
    }
}
```

移动法插入排序：

```java
public static void insertSort2(int[] arr) {
    for (int i = 1; i < arr.length; i++) {
        int currentNumber = arr[i];
        int j = i - 1;
        while (j >= 0 && currentNumber < arr[j]) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = currentNumber;
    }
}
```

