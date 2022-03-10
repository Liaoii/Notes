# 排序（Sort）

#### [88. 合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)

不开辟新的空间，并使用归并排序的思想：

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int index1 = m - 1, index2 = n - 1;
        int lastIndex = index1 + index2 + 1;
        while (index1 >= 0 || index2 >= 0) {
            if (index1 == -1 || (index2 >= 0 && nums2[index2] >= nums1[index1])) {
                nums1[lastIndex--] = nums2[index2--];
            } else {
                nums1[lastIndex--] = nums1[index1--];
            }
        }
    }
}
```

先合并在快速排序：

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        for (int i = 0; i < n; i++) {
            nums1[m + i] = nums2[i];
        }
        quickSort(nums1, 0, nums1.length - 1);
    }

    private void quickSort(int[] nums, int start, int end) {
        if (start >= end) return;
        int middle = partition(nums, start, end);
        quickSort(nums, start, middle - 1);
        quickSort(nums, middle + 1, end);
    }

    private int partition(int[] nums, int start, int end) {
        int pivot = nums[start];
        int left = start + 1, right = end;
        while (left < right) {
            while (left < right && nums[left] <= pivot) left++;
            if (left != right) {
                int temp = nums[left];
                nums[left] = nums[right];
                nums[right] = temp;
                right--;
            }
        }
        if (left == right && nums[right] > pivot) right--;
        if (start != right) {
            int temp = nums[right];
            nums[right] = nums[start];
            nums[start] = temp;
        }
        return right;
    }
}
```

先合并再进行归并排序：

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        for (int i = 0; i < n; i++) {
            nums1[m + i] = nums2[i];
        }
        int[] temp = new int[nums1.length];
        mergeSort(nums1, 0, nums1.length - 1, temp);
    }

    private void mergeSort(int[] nums, int start, int end, int[] temp) {
        if (start >= end) return;
        int middle = (start + end) >> 1;
        mergeSort(nums, start, middle, temp);
        mergeSort(nums, middle + 1, end, temp);
        int start1 = start, start2 = middle + 1, tempIndex = start;
        while (start1 <= middle || start2 <= end) {
            if (start2 > end || (start1 <= middle && nums[start1] <= nums[start2])) {
                temp[tempIndex++] = nums[start1++];
            } else {
                temp[tempIndex++] = nums[start2++];
            }
        }
        while (start <= end) nums[start] = temp[start++];
    }
}
```

#### [169. 多数元素](https://leetcode-cn.com/problems/majority-element/)

先使用快速排序进行排序，然后取中间值：

```java
class Solution {
    public int majorityElement(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        return nums[nums.length / 2];
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
        nums[right] = base;
        quickSort(nums, start, right - 1);
        quickSort(nums, right + 1, end);
    }
}
```

先使用归并排序进行排序，然后取中间值：

```java
class Solution {
    public int majorityElement(int[] nums) {
        mergeSort(nums, 0, nums.length - 1);
        return nums[nums.length / 2];
    }

    private void mergeSort(int[] nums, int start, int end) {
        if (start >= end) return;
        int middle = (start + end) >> 1;
        mergeSort(nums, start, middle);
        mergeSort(nums, middle + 1, end);
        int start1 = start, start2 = middle + 1, tempIndex = 0;
        int[] temp = new int[end - start + 1];
        while (start1 <= middle || start2 <= end) {
            if (start2 > end || (start1 <= middle && nums[start1] <= nums[start2])) {
                temp[tempIndex++] = nums[start1++];
            } else {
                temp[tempIndex++] = nums[start2++];
            }
        }
        for (int i = start; i <= end; i++) nums[i] = temp[i - start];
    }
}
```

排序的简洁解法：

```java
class Solution {
    public int majorityElement(int[] nums) {
        Arrays.sort(nums);
        return nums[nums.length >> 1];
    }
}
```

使用哈希表记录每个数字的个数：

```java
class Solution {
    public int majorityElement(int[] nums) {
        int length = nums.length;
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : nums) {
            int count = map.getOrDefault(num, 0) + 1;
            if (count > length >> 1) return num;
            map.put(num, count);
        }
        return -1;
    }
}
```

#### [217. 存在重复元素](https://leetcode-cn.com/problems/contains-duplicate/)

使用哈希表进行计数：

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : nums) {
            int count = map.getOrDefault(num, 0) + 1;
            if (count > 1) return true;
            map.put(num, count);
        }
        return false;
    }
}
```
