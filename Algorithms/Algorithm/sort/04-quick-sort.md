## 4. 快速排序

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

**简洁版快速排序：**

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
