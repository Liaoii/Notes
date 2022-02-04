# 快速排序（Quick Sort）

## [912. 排序数组](https://leetcode-cn.com/problems/sort-an-array/)

```java
class Solution {
    public int getMid(int a, int b, int c) {
        if ((c >= a && a >= b) || (b >= a && a >= c)) return a;
        if ((a >= b && b >= c) || (c >= b && b >= a)) return b;
        if ((b >= c && c >= a) || (a >= c && c >= b)) return c;
        return -1;
    }

    public void quick_sort(int[] array, int left, int right) {
        while (right - left > 16) {
            int l = left, r = right, base = getMid(array[left], array[(left + right) / 2], array[right]);
            do {
                while (array[l] < base) l++;
                while (array[r] > base) r--;
                if (l <= r) {
                    int temp = array[l];
                    array[l] = array[r];
                    array[r] = temp;
                    l++;
                    r--;
                }
            } while (l <= r);
            quick_sort(array, l, right);
            right = r;
        }
    }

    public void insert_sort(int[] array, int left, int right) {
        int index = left;
        for (int i = left + 1; i <= right; i++) {
            if (array[i] < array[index]) index = i;
        }
        while (index > left) {
            int temp = array[index];
            array[index] = array[index - 1];
            array[index - 1] = temp;
            index--;
        }
        for (int i = left + 2; i <= right; i++) {
            int j = i;
            while (array[j] < array[j - 1]) {
                int temp = array[j];
                array[j] = array[j - 1];
                array[j - 1] = temp;
                j--;
            }
        }
    }

    public void sort(int[] array, int left, int right) {
        quick_sort(array, left, right);
        insert_sort(array, left, right);
    }

    public int[] sortArray(int[] nums) {
        sort(nums, 0, nums.length - 1);
        return nums;
    }
}
```

## [剑指 Offer 21. 调整数组顺序使奇数位于偶数前面](https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)

```java
class Solution {
    public int[] exchange(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            while (left < nums.length && nums[left] % 2 == 1) left++;
            while (right > 0 && nums[right] % 2 == 0) right--;
            if (left < right) {
                int temp = nums[left];
                nums[left] = nums[right];
                nums[right] = temp;
                left++;
                right--;
            }
        }
        return nums;
    }
}
```
