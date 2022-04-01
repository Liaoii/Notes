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

**简介版快速排序：**

```java
class QuickSort {

    public static void quickSort(int[] arr) {
        quickSort(arr, 0, arr.length - 1);
    }

    private void quickSort(int[] nums, int start, int end) {
        if (start >= end) return;
        int left = start, right = end, base = nums[start];
        while (left < right) {
            while (left < right && nums[right] >= base) right--;
            if (left < right) nums[left++] = nums[right];
            while (left < right && nums[left] < base) left++;
            if (left < right) nums[right--] = nums[left];
        }
        nums[left] = base;
        quickSort(nums, start, left - 1);
        quickSort(nums, left + 1, end);
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

## 8. 基数排序

