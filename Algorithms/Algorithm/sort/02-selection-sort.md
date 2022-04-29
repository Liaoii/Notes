## 2. 选择排序

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

