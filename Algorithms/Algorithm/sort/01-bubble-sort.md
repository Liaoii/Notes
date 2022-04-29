## 1. 冒泡排序

第一种写法：

```java
public static void bubbleSort1(int[] arr) {
    for (int i = 0; i < arr.length - 1; i++) {
        for (int j = 0; j < arr.length - 1 - i; j++) {
            System.out.println(i + "  " + j);
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
}
```

对第一种写法进行优化，如果某一次排序没有进行交换则表示已经有序：

```java
public static void bubbleSort(int[] arr) {
    boolean swapped = true;
    for (int i = 0; i < arr.length - 1; i++) {
        if (!swapped) break;
        swapped = false;
        for (int j = 0; j < arr.length - 1 - i; j++) {
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
                swapped = true;
            }
        }
    }
}
```

对第二种写法进一步进行优化：

```java
public static void bubbleSort(int[] arr) {
    boolean swapped = true;
    int lastUnsortedElement = arr.length - 1;
    int swappedIndex = -1;
    while (swapped) {
        swapped = false;
        for (int i = 0; i < lastUnsortedElement; i++) {
            if (arr[i] > arr[i + 1]) {
                int temp = arr[i];
                arr[i] = arr[i + 1];
                arr[i + 1] = temp;
                swapped = true;
                swappedIndex = i;
            }
        }
        lastUnsortedElement = swappedIndex;
    }
}
```

