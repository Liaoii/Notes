# 快速排序（Quick Sort）

## [75. 颜色分类](https://leetcode-cn.com/problems/sort-colors/)

```java
class Solution {
    public void sortColors(int[] nums) {
        three_partition(nums, 0, nums.length - 1);
    }

    public void three_partition(int[] array, int left, int right) {
        if (left >= right) return;
        int l = -1, r = right + 1, i = left;
        while (i < r) {
            if (array[i] == 1) {
                i++;
            } else if (array[i] < 1) {
                l++;
                int temp = array[l];
                array[l] = array[i];
                array[i] = temp;
                i++;
            } else if (array[i] > 1) {
                r--;
                int temp = array[r];
                array[r] = array[i];
                array[i] = temp;
            }
        }
    }
}
```

## [148. 排序链表](https://leetcode-cn.com/problems/sort-list/)

```java
class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null) return null;
        int left = head.val, right = head.val;
        double mid;
        ListNode p = head, q, h1 = null, h2 = null;
        while (p != null) {
            left = Math.min(left, p.val);
            right = Math.max(right, p.val);
            p = p.next;
        }
        if (left == right) return head;
        mid = (left + right) / 2.0;
        p = head;
        while (p != null) {
            q = p.next;
            if (p.val <= mid) {
                p.next = h1;
                h1 = p;
            } else {
                p.next = h2;
                h2 = p;
            }
            p = q;
        }
        h1 = sortList(h1);
        h2 = sortList(h2);
        p = h1;
        while (p.next != null) p = p.next;
        p.next = h2;
        return h1;
    }
}
```

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
