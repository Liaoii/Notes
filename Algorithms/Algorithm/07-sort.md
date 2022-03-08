# 排序（Sort）

## 1.冒泡排序

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

## 2.选择排序

```java
public static void selectionSort(int[] nums) {
    int minIndex;
    for (int i = 0; i < nums.length; i++) {
        minIndex = i;
        for (int j = i + 1; j < nums.length; j++) {
            if (nums[j] < nums[minIndex]) {
                minIndex = j;
            }
        }
        if (minIndex != i) {
            int temp = nums[i];
            nums[i] = nums[minIndex];
            nums[minIndex] = temp;
        }
    }
}
```

二元选择排序，在一轮排序中同时排好最小值和最大值：

```java
public static void selectionSort2(int[] nums) {
    int minIndex, maxIndex;
    for (int i = 0; i < nums.length / 2; i++) {
        minIndex = i;
        maxIndex = i;
        for (int j = i + 1; j < nums.length - i; j++) {
            if (nums[j] < nums[minIndex]) minIndex = j;
            if (nums[j] > nums[maxIndex]) maxIndex = j;
        }
        if (minIndex == maxIndex) break;
        int temp = nums[i];
        nums[i] = nums[minIndex];
        nums[minIndex] = temp;
        if (maxIndex == i) maxIndex = minIndex;
        int lastIndex = nums.length - i - 1;
        temp = nums[maxIndex];
        nums[maxIndex] = nums[lastIndex];
        nums[lastIndex] = temp;
    }
}
```

## 3.插入排序

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

## 4.快速排序

简单的快速排序：

```java
class QuickSort {

    public static void quickSort(int[] arr) {
        quickSort(arr, 0, arr.length - 1);
    }

    public static void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int middle = partition(arr, start, end);
        quickSort(arr, start, middle - 1);
        quickSort(arr, middle + 1, end);
    }

    private static int partition(int[] arr, int start, int end) {
        int pivot = arr[start];
        int left = start + 1;
        int right = end;
        while (left < right) {
            while (left < right && arr[left] <= pivot) left++;
            if (left != right) {
                int temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;
                right--;
            }
        }
        if (left == right && arr[right] > pivot) right--;
        if (right != start) {
            int temp = arr[start];
            arr[start] = arr[right];
            arr[right] = temp;
        }
        return right;
    }
}
```

双指针分区算法：

```java
class QuickSort {

    public static void quickSort(int[] arr) {
        quickSort(arr, 0, arr.length - 1);
    }

    public static void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int middle = partition(arr, start, end);
        quickSort(arr, start, middle - 1);
        quickSort(arr, middle + 1, end);
    }

    private static int partition(int[] arr, int start, int end) {
        int pivot = arr[start];
        int left = start + 1;
        int right = end;
        while (left < right) {
            while (left < right && arr[left] <= pivot) left++;
            while (left < right && arr[right] >= pivot) right--;
            if (left < right) {
                int temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;
                left++;
                right--;
            }
        }
        if (left == right && arr[right] > pivot) right--;
        if (right != start) {
            int temp = arr[start];
            arr[start] = arr[right];
            arr[right] = temp;
        }
        return right;
    }
}
```

## 5.归并排序

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

简洁的归并排序：

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
