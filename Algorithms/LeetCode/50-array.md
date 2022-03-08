# 数组（Array）

## [11. 盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

暴力解法，会超时：

```java
class Solution {
    public int maxArea(int[] height) {
        int ans = 0;
        for (int i = 0; i < height.length; i++) {
            for (int j = i + 1; j < height.length; j++) {
                ans = Math.max(ans, Math.min(height[i], height[j]) * (j - i));
            }
        }
        return ans;
    }
}
```

使用双指针进行求解：

```java
class Solution {
    public int maxArea(int[] height) {
        int ans = 0;
        int left = 0, right = height.length - 1;
        while (left < right) {
            ans = Math.max(ans, Math.min(height[left], height[right]) * (right - left));
            if (height[left] <= height[right]) {
                left++;
            } else {
                right--;
            }
        }
        return ans;
    }
}
```

## [724. 寻找数组的中心下标](https://leetcode-cn.com/problems/find-pivot-index/)

```java
class Solution {
    public int pivotIndex(int[] nums) {
        int ans = -1;
        for (int i = 0; i < nums.length; i++) {
            int lSum = sum(nums, 0, i - 1);
            int rSum = sum(nums, i + 1, nums.length - 1);
            if (lSum == rSum) {
                ans = i;
                break;
            }

        }
        return ans;
    }

    private int sum(int[] nums, int start, int end) {
        int ans = 0;
        for (int i = start; i <= end; i++) {
            ans += nums[i];
        }
        return ans;
    }
}
```