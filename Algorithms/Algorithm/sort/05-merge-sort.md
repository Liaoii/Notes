## 5. 归并排序

使用临时空间进行归并排序：

```java
public class MergeSort {

    public static void mergeSort(int[] arr) {
        if (arr.length == 0) return;
        int[] result = new int[arr.length];
        mergeSort(arr, 0, arr.length - 1, result);
    }

    private static void mergeSort(int[] arr, int start, int end, int[] result) {
        if (start == end) return;
        int middle = (start + end) >> 1;
        mergeSort(arr, start, middle, result);
        mergeSort(arr, middle + 1, end, result);
        merge(arr, start, end, result);
    }

    private static void merge(int[] arr, int start, int end, int[] result) {
        int middle = (start + end) >> 1;
        int index1 = start;
        int index2 = middle + 1;
        int resultIndex = start;
        while (index1 <= middle && index2 <= end) {
            if (arr[index1] <= arr[index2]) {
                result[resultIndex++] = arr[index1++];
            } else {
                result[resultIndex++] = arr[index2++];
            }
        }
        while (index1 <= middle) {
            result[resultIndex++] = arr[index1++];
        }
        while (index2 <= end) {
            result[resultIndex++] = arr[index2++];
        }
        while (start <= end) arr[start] = result[start++];
    }
}
```

不使用临时空间的归并排序：

```java
public class MergeSort {

    public static void mergeSort(int[] arr) {
        if (arr.length == 0) return;
        mergeSort(arr, 0, arr.length - 1);
    }

    private static void mergeSort(int[] arr, int start, int end) {
        if (start == end) return;
        int middle = (start + end) >> 1;
        mergeSort(arr, start, middle);
        mergeSort(arr, middle + 1, end);

        merge(arr, start, end);
    }

    private static void merge(int[] arr, int start, int end) {
        int middle = (start + end) >> 1;
        int index1 = start;
        int index2 = middle + 1;
        while (index1 <= middle && index2 <= end) {
            if (arr[index1] <= arr[index2]) {
                index1++;
            } else {
                int value = arr[index2];
                int index = index2;
                while (index > index1) {
                    arr[index] = arr[index - 1];
                    index--;
                }
                arr[index] = value;
                index1++;
                index2++;
                middle++;
            }
        }
    }
}
```

另一种不使用临时空间的方法：

```java
public class MergeSort {

    public static void mergeSort(int[] arr) {
        if (arr.length == 0) return;
        mergeSort(arr, 0, arr.length - 1);
    }

    private static void mergeSort(int[] arr, int start, int end) {
        if (start == end) return;
        int middle = (start + end) >> 1;
        mergeSort(arr, start, middle);
        mergeSort(arr, middle + 1, end);
        merge(arr, start, end);
    }

    private static void merge(int[] arr, int start, int end) {
        int middle = (start + end) >> 1;
        int start2 = middle + 1;
        int index1 = start;

        while (index1 <= middle && start2 <= end) {
            if (arr[index1] > arr[start2]) {
                int temp = arr[index1];
                arr[index1] = arr[start2];
                arr[start2] = temp;
                if (start2 != end) {
                    int value = arr[start2];
                    int index = start2;
                    while (index < end && arr[index + 1] < value) {
                        arr[index] = arr[index + 1];
                        index++;
                    }
                    arr[index] = value;
                }
            }
            index1++;
        }
    }
}
```

**简洁的归并排序：**

```java
public class MergeSort {

    public static void mergeSort(int[] arr) {
        if (arr.length == 0) return;
        mergeSort(arr, 0, arr.length - 1);
    }

    private static void mergeSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int middle = (start + end) >> 1;
        mergeSort(arr, start, middle);
        mergeSort(arr, middle + 1, end);
        int[] temp = new int[end - start + 1];
        int k = 0, p1 = start, p2 = middle + 1;
        while (p1 <= middle || p2 <= end) {
            if ((p2 > end) || (p1 <= middle && arr[p1] <= arr[p2])) {
                temp[k++] = arr[p1++];
            } else {
                temp[k++] = arr[p2++];
            }
        }
        for (int i = start; i <= end; i++) arr[i] = temp[i - start];
    }
}
```

