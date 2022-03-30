# 排序（Sort）

#### [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> ans = new ArrayList<>();
        if (nums.length < 3) return ans;
        quickSort(nums, 0, nums.length - 1);
        int len = nums.length;
        for (int i = 0; i < len; i++) {
            if (nums[i] > 0) return ans;
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            int left = i + 1, right = len - 1;
            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];
                if (sum == 0) {
                    List<Integer> list = new ArrayList<>();
                    list.add(nums[i]);
                    list.add(nums[left]);
                    list.add(nums[right]);
                    ans.add(list);
                    while (left < right && nums[left] == nums[left + 1]) left++;
                    while (left < right && nums[right] == nums[right - 1]) right--;
                    left++;
                    right--;
                } else if (sum > 0) {
                    right--;
                } else {
                    left++;
                }
            }
        }
        return ans;
    }

    private void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int left = start, right = end, base = arr[start];
        while (left < right) {
            while (left < right && arr[right] >= base) right--;
            if (left < right) arr[left++] = arr[right];
            while (left < right && arr[left] < base) left++;
            if (left < right) arr[right--] = arr[left];
        }
        arr[left] = base;
        quickSort(arr, start, left - 1);
        quickSort(arr, left + 1, end);
    }
}
```

2022-03-28：

```
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> ans = new ArrayList<>();
        int len = nums.length;
        if (len < 3) return ans;
        Arrays.sort(nums);
        for (int i = 0; i < len; i++) {
            int first = nums[i];
            if (first > 0) return ans;
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            int secondIndex = i + 1, thirdIndex = len - 1;
            while (secondIndex < thirdIndex) {
                int sum = first + nums[secondIndex] + nums[thirdIndex];
                if (sum == 0) {
                    List<Integer> item = new ArrayList<>();
                    item.add(first);
                    item.add(nums[secondIndex]);
                    item.add(nums[thirdIndex]);
                    ans.add(item);
                    while (secondIndex < thirdIndex && nums[secondIndex] == nums[secondIndex + 1]) secondIndex++;
                    while (secondIndex < thirdIndex && nums[thirdIndex] == nums[thirdIndex - 1]) thirdIndex--;
                    secondIndex++;
                    thirdIndex--;
                } else if (sum > 0) {
                    thirdIndex--;
                } else {
                    secondIndex++;
                }
            }
        }
        return ans;
    }
}
```

#### [16. 最接近的三数之和](https://leetcode-cn.com/problems/3sum-closest/)

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int ans = 10000000;
        for (int i = 0; i < nums.length; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            int l = i + 1, r = nums.length - 1;
            while (l < r) {
                int sum = nums[i] + nums[l] + nums[r];
                if (sum == target) return target;
                if (Math.abs(sum - target) < Math.abs(ans - target)) ans = sum;
                if (sum > target) {
                    while (l < r - 1 && nums[r] == nums[r - 1]) r--;
                    r--;
                } else {
                    while (l + 1 < r && nums[l] == nums[l + 1]) l++;
                    l++;
                }
            }
        }
        return ans;
    }
}
```

#### [18. 四数之和](https://leetcode-cn.com/problems/4sum/)

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> ans = new ArrayList<>();
        int len = nums.length;
        if (nums.length < 4) return ans;
        Arrays.sort(nums);
        for (int i = 0; i < len - 3; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            if ((long) nums[i] + nums[i + 1] + nums[i + 2] + nums[i + 3] > target) return ans;
            for (int j = i + 1; j < len - 2; j++) {
                if (j > i + 1 && nums[j] == nums[j - 1]) continue;
                if ((long) nums[i] + nums[j] + nums[j + 1] + nums[j + 2] > target) break;
                int second = nums[j];
                int thirdIndex = j + 1, fourthIndex = len - 1;
                while (thirdIndex < fourthIndex) {
                    int sum = nums[i] + second + nums[thirdIndex] + nums[fourthIndex];
                    if (sum == target) {
                        List<Integer> item = new ArrayList<>();
                        item.add(nums[i]);
                        item.add(second);
                        item.add(nums[thirdIndex]);
                        item.add(nums[fourthIndex]);
                        ans.add(item);
                        while (thirdIndex < fourthIndex && nums[thirdIndex] == nums[thirdIndex + 1]) thirdIndex++;
                        while (thirdIndex < fourthIndex && nums[fourthIndex] == nums[fourthIndex - 1]) fourthIndex--;
                        thirdIndex++;
                        fourthIndex--;
                    } else if (sum > target) {
                        fourthIndex--;
                    } else {
                        thirdIndex++;
                    }
                }
            }
        }
        return ans;
    }
}
```

#### [49. 字母异位词分组](https://leetcode-cn.com/problems/group-anagrams/)

```
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        List<List<String>> ans = new ArrayList<>();
        if (strs == null || strs.length == 0) return ans;
        Map<String, List<String>> map = new HashMap<>();
        for (String str : strs) {
            char[] chars = str.toCharArray();
            Arrays.sort(chars);
            String sortedStr = new String(chars);
            List<String> list = map.getOrDefault(sortedStr, new ArrayList<>());
            list.add(str);
            map.put(sortedStr, list);
        }
        ans.addAll(map.values());
        return ans;
    }
}
```

#### [56. 合并区间](https://leetcode-cn.com/problems/merge-intervals/)

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        List<int[]> list = new ArrayList<>();
        Arrays.sort(intervals, Comparator.comparingInt(o -> o[0]));
        int start = intervals[0][0], end = intervals[0][1];
        for (int[] interval : intervals) {
            if (interval[0] > end) {
                list.add(new int[]{start, end});
                start = interval[0];
            }
            if (interval[1] > end) {
                end = interval[1];
            }
        }
        list.add(new int[]{start, end});
        int[][] ans = new int[list.size()][];
        for (int i = 0; i < list.size(); i++) {
            ans[i] = list.get(i);
        }
        return ans;
    }
}
```

#### [75. 颜色分类](https://leetcode-cn.com/problems/sort-colors/)

```java
class Solution {
    public void sortColors(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
    }

    private void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int left = start, right = end, base = arr[start];
        while (left < right) {
            while (left < right && arr[right] >= base) right--;
            if (left < right) arr[left++] = arr[right];
            while (left < right && arr[left] < base) left++;
            if (left < right) arr[right--] = arr[left];
        }
        arr[left] = base;
        quickSort(arr, start, left - 1);
        quickSort(arr, left + 1, end);
    }
}
```

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

#### [147. 对链表进行插入排序](https://leetcode-cn.com/problems/insertion-sort-list/)



#### [164. 最大间距](https://leetcode-cn.com/problems/maximum-gap/)

```java
class Solution {
    public int maximumGap(int[] nums) {
        if (nums.length < 2) return 0;
        Arrays.sort(nums);
        int ans = 0;
        for (int i = 1; i < nums.length; i++) {
            ans = Math.max(ans, nums[i] - nums[i - 1]);
        }
        return ans;
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

#### [179. 最大数](https://leetcode-cn.com/problems/largest-number/)

使用冒泡排序求解：

```java
class Solution {
    public String largestNumber(int[] nums) {
        for (int i = 0; i < nums.length - 1; i++) {
            for (int j = 0; j < nums.length - 1 - i; j++) {
                if (("" + nums[j] + nums[j + 1]).compareTo("" + nums[j + 1] + nums[j]) < 0) {
                    int temp = nums[j];
                    nums[j] = nums[j + 1];
                    nums[j + 1] = temp;
                }
            }
        }
        if (nums[0] == 0) return "0";
        StringBuilder sb = new StringBuilder();
        for (int num : nums) sb.append(num);
        return sb.toString();
    }
}
```

使用Arrays.sort()求解：

```java
class Solution {
    public String largestNumber(int[] nums) {
        Integer[] copy = new Integer[nums.length];
        for (int i = 0; i < nums.length; i++) copy[i] = nums[i];
        Arrays.sort(copy, ((o1, o2) -> ("" + o2 + o1).compareTo("" + o1 + o2)));
        if (copy[0] == 0) return "0";
        StringBuilder sb = new StringBuilder();
        for (int num : copy) sb.append(num);
        return sb.toString();
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

#### [220. 存在重复元素 III](https://leetcode-cn.com/problems/contains-duplicate-iii/)





#### [242. 有效的字母异位词](https://leetcode-cn.com/problems/valid-anagram/)

使用哈希表进行计数：

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        Map<Character, Integer> map = new HashMap<>();
        char[] chars = s.toCharArray();
        for (char c : chars) {
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        chars = t.toCharArray();
        for (char c : chars) {
            int count = map.getOrDefault(c, 0) - 1;
            if (count < 0) return false;
            map.put(c, count);
        }
        return true;
    }
}
```

先将两个字符串进行快速排序，然后逐个进行比较：

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        char[] sChars = s.toCharArray();
        quickSort(sChars, 0, sChars.length - 1);
        char[] tChars = t.toCharArray();
        quickSort(tChars, 0, tChars.length - 1);
        for (int i = 0; i < sChars.length; i++) {
            if (sChars[i] != tChars[i]) return false;
        }
        return true;
    }

    private void quickSort(char[] chars, int start, int end) {
        if (start >= end) return;
        int left = start, right = end;
        char base = chars[left];
        while (left < right) {
            while (left < right && chars[right] >= base) right--;
            if (left < right) chars[left++] = chars[right];
            while (left < right && chars[left] < base) left++;
            if (left < right) chars[right--] = chars[left];
        }
        chars[left] = base;
        quickSort(chars, start, left - 1);
        quickSort(chars, left + 1, end);
    }
}
```

#### [252. 会议室](https://leetcode-cn.com/problems/meeting-rooms/)

先对数组进行归并排序，然后在遍历一次进行判断：

```java
class Solution {
    public boolean canAttendMeetings(int[][] intervals) {
        if (intervals.length == 0) return true;
        mergeSort(intervals, 0, intervals.length - 1);
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i - 1][1] > intervals[i][0]) return false;
        }
        return true;
    }

    private void mergeSort(int[][] arr, int start, int end) {
        if (start >= end) return;
        int middle = (start + end) >> 1;
        mergeSort(arr, start, middle);
        mergeSort(arr, middle + 1, end);
        int s1 = start, s2 = middle + 1, tempIndex = 0;
        int[][] temp = new int[end - start + 1][];
        while (s1 <= middle || s2 <= end) {
            if (s2 > end || (s1 <= middle && arr[s1][0] <= arr[s2][0])) {
                temp[tempIndex++] = arr[s1++];
            } else {
                temp[tempIndex++] = arr[s2++];
            }
        }
        for (int i = start; i <= end; i++) arr[i] = temp[i - start];
    }
}
```

#### [268. 丢失的数字](https://leetcode-cn.com/problems/missing-number/)

使用快速排序进行排序：

```java
class Solution {
    public int missingNumber(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != i) return i;
        }
        return nums.length;
    }

    private void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int left = start, right = end, base = arr[left];
        while (left < right) {
            while (left < right && arr[right] >= base) right--;
            if (left < right) arr[left++] = arr[right];
            while (left < right && arr[left] < base) left++;
            if (left < right) arr[right--] = arr[left];
        }
        arr[left] = base;
        quickSort(arr, start, left - 1);
        quickSort(arr, left + 1, end);
    }
}
```

使用哈希表进行求解：

```java
class Solution {
    public int missingNumber(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            set.add(num);
        }
        for (int i = 0; i < nums.length; i++) {
            if (!set.contains(i)) return i;
        }
        return nums.length;
    }
}
```

#### [295. 数据流的中位数](https://leetcode-cn.com/problems/find-median-from-data-stream/)



#### [349. 两个数组的交集](https://leetcode-cn.com/problems/intersection-of-two-arrays/)

使用哈希表进行求解：

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums1) set.add(num);
        List<Integer> list = new ArrayList<>();
        for (int num : nums2) {
            if (set.contains(num)) {
                list.add(num);
                set.remove(num);
            }
        }
        int[] ans = new int[list.size()];
        for (int i = 0; i < list.size(); i++) ans[i] = list.get(i);
        return ans;
    }
}
```

使用单边快速排序对两个数组进行排序，然后使用双指针进行判断：

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        quickSort(nums1, 0, nums1.length - 1);
        quickSort(nums2, 0, nums2.length - 1);
        int[] ans = new int[nums1.length + nums2.length];
        int index = 0, index1 = 0, index2 = 0;
        while (index1 < nums1.length && index2 < nums2.length) {
            int num1 = nums1[index1], num2 = nums2[index2];
            if (num1 == num2) {
                if (index == 0 || num1 != ans[index - 1]) ans[index++] = num1;
                index1++;
                index2++;
            } else if (num1 < num2) {
                index1++;
            } else {
                index2++;
            }
        }
        return Arrays.copyOfRange(ans, 0, index);
    }

    private void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int middle = partition(arr, start, end);
        quickSort(arr, start, middle - 1);
        quickSort(arr, middle + 1, end);
    }

    private int partition(int[] arr, int start, int end) {
        int pivot = arr[start];
        int left = start + 1, right = end;
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

#### [350. 两个数组的交集 II](https://leetcode-cn.com/problems/intersection-of-two-arrays-ii/)

使用哈希表进行计数：

```
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<>();
        List<Integer> list = new ArrayList<>();
        for (int num : nums1) map.put(num, map.getOrDefault(num, 0) + 1);
        for (int num : nums2) {
            int count = map.getOrDefault(num, 0);
            if (count > 0) list.add(num);
            map.put(num, count - 1);
        }
        int[] ans = new int[list.size()];
        for (int i = 0; i < list.size(); i++) {
            ans[i] = list.get(i);
        }
        return ans;
    }
}
```

先对两个数组进行排序，然后再使用双指针进行处理：

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        quickSort(nums1, 0, nums1.length - 1);
        quickSort(nums2, 0, nums2.length - 1);
        int[] ans = new int[nums1.length + nums2.length];
        int index = 0, index1 = 0, index2 = 0;
        while (index1 < nums1.length && index2 < nums2.length) {
            int num1 = nums1[index1], num2 = nums2[index2];
            if (num1 == num2) {
                ans[index++] = num1;
                index1++;
                index2++;
            } else if (num1 < num2) {
                index1++;
            } else {
                index2++;
            }
        }
        return Arrays.copyOfRange(ans, 0, index);
    }

    private void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int middle = partition(arr, start, end);
        quickSort(arr, start, middle - 1);
        quickSort(arr, middle + 1, end);
    }

    private int partition(int[] arr, int start, int end) {
        int pivot = arr[start];
        int left = start + 1, right = end;
        while (left < right) {
            while (left < right && arr[left] <= pivot) left++;
            while (left < right && arr[right] > pivot) right--;
            if (left != right) {
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

#### [389. 找不同](https://leetcode-cn.com/problems/find-the-difference/)

先进行排序，然后在进行求解：

```java
class Solution {
    public char findTheDifference(String s, String t) {
        char[] sChars = s.toCharArray();
        char[] tChars = t.toCharArray();
        quickSort(sChars, 0, sChars.length - 1);
        quickSort(tChars, 0, tChars.length - 1);
        for (int i = 0; i < sChars.length; i++) {
            if (tChars[i] != sChars[i]) return tChars[i];
        }
        return tChars[tChars.length - 1];
    }

    private void quickSort(char[] arr, int start, int end) {
        if (start >= end) return;
        int middle = partition(arr, start, end);
        quickSort(arr, start, middle - 1);
        quickSort(arr, middle + 1, end);
    }

    private int partition(char[] arr, int start, int end) {
        char pivot = arr[start];
        int left = start + 1, right = end;
        while (left < right) {
            while (left < right && arr[left] <= pivot) left++;
            if (left != right) {
                char temp = arr[right];
                arr[right] = arr[left];
                arr[left] = temp;
                right--;
            }
        }
        if (left == right && arr[right] > pivot) right--;
        if (right != start) {
            char temp = arr[right];
            arr[right] = arr[start];
            arr[start] = temp;
        }
        return right;
    }
}
```

使用数组进行计数：

```java
class Solution {
    public char findTheDifference(String s, String t) {
        int[] count = new int[26];
        for (char c : s.toCharArray()) {
            count[c - 'a']++;
        }
        for (char c : t.toCharArray()) {
            count[c - 'a']--;
            if (count[c - 'a'] < 0) return c;
        }
        return ' ';
    }
}
```

使用求和的方式进行求解：

```java
class Solution {
    public char findTheDifference(String s, String t) {
        int sum = 0;
        for (char c : t.toCharArray()) {
            sum += c;
        }
        for (char c : s.toCharArray()) {
            sum -= c;
        }
        return (char) sum;
    }
}
```

#### [414. 第三大的数](https://leetcode-cn.com/problems/third-maximum-number/)

先进行排序：

```java
class Solution {
    public int thirdMax(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        int cur = nums[0];
        int st = 2;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] != cur) {
                st--;
                cur = nums[i];
            }
            if (st == 0) return nums[i];
        }
        return nums[0];
    }

    private void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int middle = partition(arr, start, end);
        quickSort(arr, start, middle - 1);
        quickSort(arr, middle + 1, end);
    }

    private int partition(int[] arr, int start, int end) {
        int left = start + 1, right = end, base = arr[start];
        while (left < right) {
            while (left < right && arr[left] >= base) left++;
            if (left != right) {
                int temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;
                right--;
            }
        }
        if (left == right && arr[right] < base) right--;
        if (right != start) {
            int temp = arr[start];
            arr[start] = arr[right];
            arr[right] = temp;
        }
        return right;
    }
}
```

使用有序集合进行求解：

```java
class Solution {
    public int thirdMax(int[] nums) {
        TreeSet<Integer> set = new TreeSet<>();
        for (int num : nums) {
            set.add(num);
            if (set.size() > 3) set.pollFirst();
        }
        return set.size() == 3 ? set.pollFirst() : set.pollLast();
    }
}
```

#### [455. 分发饼干](https://leetcode-cn.com/problems/assign-cookies/)

首先对两个数组进行排序：

```java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        quickSort(g, 0, g.length - 1);
        System.out.println(Arrays.toString(g));
        quickSort(s, 0, s.length - 1);
        int ans = 0;
        int index1 = 0, index2 = 0;
        while (index1 < g.length && index2 < s.length) {
            if (s[index2] >= g[index1]) {
                ans++;
                index1++;
            }
            index2++;
        }
        return ans;
    }

    private void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int middle = partition(arr, start, end);
        quickSort(arr, start, middle - 1);
        quickSort(arr, middle + 1, end);
    }

    private int partition(int[] arr, int start, int end) {
        int left = start + 1, right = end, base = arr[start];
        while (left < right) {
            while (left < right && arr[left] <= base) left++;
            while (left < right && arr[right] > base) right--;
            if (left != right) {
                int temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;
                left++;
                right--;
            }
        }
        if (left == right && arr[right] > base) right--;
        if (right != start) {
            int temp = arr[start];
            arr[start] = arr[right];
            arr[right] = temp;
        }
        return right;
    }
}
```

#### [506. 相对名次](https://leetcode-cn.com/problems/relative-ranks/)

```java
class Solution {
    public String[] findRelativeRanks(int[] score) {
        String[] ans = new String[score.length];
        int[] copy = score.clone();
        Arrays.sort(copy);
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < copy.length; i++) {
            map.put(copy[i], copy.length - i);
        }
        for (int i = 0; i < score.length; i++) {
            int rank = map.get(score[i]);
            if (rank == 1) {
                ans[i] = "Gold Medal";
            } else if (rank == 2) {
                ans[i] = "Silver Medal";
            } else if (rank == 3) {
                ans[i] = "Bronze Medal";
            } else {
                ans[i] = rank + "";
            }
        }
        return ans;
    }
}
```

#### [561. 数组拆分 I](https://leetcode-cn.com/problems/array-partition-i/)

先进行排序：

```java
class Solution {
    public int arrayPairSum(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        int ans = 0;
        for (int i = 0; i < nums.length; i += 2) {
            ans += nums[i];
        }
        return ans;
    }

    private void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int middle = partition(arr, start, end);
        quickSort(arr, start, middle - 1);
        quickSort(arr, middle + 1, end);
    }

    private int partition(int[] arr, int start, int end) {
        int left = start, right = end, base = arr[start];
        while (left < right) {
            while (left < right && arr[left] <= base) left++;
            if (left != right) {
                int temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;
                right--;
            }
        }
        if (left == right && arr[right] > base) right--;
        if (start != right) {
            int temp = arr[start];
            arr[start] = arr[right];
            arr[right] = temp;
        }
        return right;
    }
}
```

#### [594. 最长和谐子序列](https://leetcode-cn.com/problems/longest-harmonious-subsequence/)

先排序后进行处理：

```
class Solution {
    public int findLHS(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        int start = 0;
        int ans = 0;
        for (int i = 0; i < nums.length; i++) {
            while (nums[i] - nums[start] > 1) start++;
            if (nums[i] - nums[start] == 1) ans = Math.max(ans, i - start + 1);
        }
        return ans;
    }

    private void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int middle = partiton(arr, start, end);
        quickSort(arr, start, middle - 1);
        quickSort(arr, middle + 1, end);
    }

    private int partiton(int[] arr, int start, int end) {
        int left = start + 1, right = end, base = arr[start];
        while (left < right) {
            while (left < right && arr[left] <= base) left++;
            while (left < right && arr[right] > base) right--;
            if (left != right) {
                int temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;
                left++;
                right--;
            }
        }
        if (left == right && arr[right] > base) right--;
        if (start != right) {
            int temp = arr[start];
            arr[start] = arr[right];
            arr[right] = temp;
        }
        return right;
    }
}
```

#### [628. 三个数的最大乘积](https://leetcode-cn.com/problems/maximum-product-of-three-numbers/)

```java
class Solution {
    public int maximumProduct(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        int len = nums.length;
        return Math.max(nums[len - 1] * nums[len - 2] * nums[len - 3], nums[0] * nums[1] * nums[len - 1]);
    }

    private void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int left = start, right = end, base = arr[start];
        while (left < right) {
            while (left < right && arr[right] >= base) right--;
            if (left < right) arr[left++] = arr[right];
            while (left < right && arr[left] < base) left++;
            if (left < right) arr[right--] = arr[left];
        }
        arr[left] = base;
        quickSort(arr, start, left - 1);
        quickSort(arr, left + 1, end);
    }
}
```

#### [645. 错误的集合](https://leetcode-cn.com/problems/set-mismatch/)

```java
class Solution {
    public int[] findErrorNums(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        int[] ans = new int[2];
        int prev = 0;
        for (int num : nums) {
            if (num == prev) {
                ans[0] = prev;
            } else if (num - prev > 1) {
                ans[1] = prev + 1;
            }
            prev = num;
        }
        if (nums[nums.length - 1] != nums.length) ans[1] = nums.length;
        return ans;
    }

    private void quickSort(int[] arr, int start, int end) {
        if (start >= end) return;
        int left = start, right = end, base = arr[start];
        while (left < right) {
            while (left < right && arr[right] >= base) right--;
            if (left < right) arr[left++] = arr[right];
            while (left < right && arr[left] < base) left++;
            if (left < right) arr[right--] = arr[left];
        }
        arr[left] = base;
        quickSort(arr, start, left - 1);
        quickSort(arr, left + 1, end);
    }
}
```

#### [720. 词典中最长的单词](https://leetcode-cn.com/problems/longest-word-in-dictionary/)

先按单词的长度从小到大排序：

```java
class Solution {
    public String longestWord(String[] words) {
        Arrays.sort(words, (a, b) -> {
            if (a.length() == b.length()) {
                return b.compareTo(a);
            } else {
                return a.length() - b.length();
            }
        });
        Set<String> set = new HashSet<>();
        set.add("");
        String ans = "";
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            if (set.contains(word.substring(0, word.length() - 1))) {
                ans = word;
                set.add(word);
            }
        }
        return ans;
    }
}
```

#### [747. 至少是其他数字两倍的最大数](https://leetcode-cn.com/problems/largest-number-at-least-twice-of-others/)

拷贝数组并排序：

```java
class Solution {
    public int dominantIndex(int[] nums) {
        if (nums.length == 1) return 0;
        int[] copy = nums.clone();
        Arrays.sort(copy);
        if (copy[copy.length - 1] >= copy[copy.length - 2] * 2) {
            for (int i = 0; i < nums.length; i++) {
                if (nums[i] == copy[copy.length - 1]) return i;
            }
        } else {
            return -1;
        }
        return -1;
    }
}
```

遍历一次数组，找到最大和第二大的数字：

```java
class Solution {
    public int dominantIndex(int[] nums) {
        int maximal = -1, second = -1, maxIndex = -1;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] > maximal) {
                second = maximal;
                maximal = nums[i];
                maxIndex = i;
            } else if (nums[i] > second) {
                second = nums[i];
            }
        }
        return maximal >= second * 2 ? maxIndex : -1;
    }
}
```

#### [888. 公平的糖果交换](https://leetcode-cn.com/problems/fair-candy-swap/)

```java
class Solution {
    public int[] fairCandySwap(int[] aliceSizes, int[] bobSizes) {
        int aliceSum = Arrays.stream(aliceSizes).sum();
        int bobSum = Arrays.stream(bobSizes).sum();
        int diff = (aliceSum - bobSum) >> 1;
        Set<Integer> aliceSet = new HashSet<>();
        for (int a : aliceSizes) aliceSet.add(a);
        int[] ans = new int[2];
        for (int b : bobSizes) {
            if (aliceSet.contains(b + diff)) {
                ans[0] = b + diff;
                ans[1] = b;
                break;
            }
        }
        return ans;
    }
}
```

#### [905. 按奇偶排序数组](https://leetcode-cn.com/problems/sort-array-by-parity/)

使用快速排序的思想求解：

```java
class Solution {
    public int[] sortArrayByParity(int[] nums) {
        sort(nums, 0, nums.length - 1);
        return nums;
    }

    private void sort(int[] arr, int start, int end) {
        if (start >= end) return;
        int left = start, right = end;
        while (left < right) {
            while (left < right && arr[left] % 2 == 0) left++;
            while (left < right && arr[right] % 2 == 1) right--;
            if (left != right) {
                int temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;
                left++;
                right--;
            }
        }
    }
}
```

#### [922. 按奇偶排序数组 II](https://leetcode-cn.com/problems/sort-array-by-parity-ii/)

```java
class Solution {
    public int[] sortArrayByParityII(int[] nums) {
        sort(nums, 0, nums.length - 1);
        for (int i = 1; i < nums.length / 2; i += 2) {
            int temp = nums[i];
            nums[i] = nums[nums.length - i - 1];
            nums[nums.length - i - 1] = temp;
        }
        return nums;
    }

    private void sort(int[] arr, int start, int end) {
        if (start >= end) return;
        int left = start, right = end;
        while (left < right) {
            while (left < right && arr[left] % 2 == 0) left++;
            while (left < right && arr[right] % 2 == 1) right--;
            if (left != right) {
                int temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;
                left++;
                right--;
            }
        }
    }
}
```

#### [976. 三角形的最大周长](https://leetcode-cn.com/problems/largest-perimeter-triangle/)

三重循环：

```java
class Solution {
    public int largestPerimeter(int[] nums) {
        Arrays.sort(nums);
        int len = nums.length;
        for (int i = len - 1; i >= 0; i--) {
            int first = nums[i];
            for (int j = i - 1; j >= 0; j--) {
                int second = nums[j];
                for (int k = j - 1; k >= 0; k--) {
                    int third = nums[k];
                    if (second + third > first) return first + second + third;
                }
            }
        }
        return 0;
    }
}
```

#### [977. 有序数组的平方](https://leetcode-cn.com/problems/squares-of-a-sorted-array/)

直接排序结果数组：

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        for (int i = 0; i < nums.length; i++) nums[i] = nums[i] * nums[i];
        Arrays.sort(nums);
        return nums;
    }
}
```

双指针解法：

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int minIndex = 0;
        for (int i = 0; i < nums.length; i++) {
            if (Math.abs(nums[i]) < Math.abs(nums[minIndex])) minIndex = i;
        }
        int[] ans = new int[nums.length];
        int l = minIndex, r = minIndex + 1, ansIndex = 0;
        while (l >= 0 || r < nums.length) {
            if (l < 0 || (r < nums.length && Math.abs(nums[r]) <= Math.abs(nums[l]))) {
                ans[ansIndex++] = nums[r] * nums[r++];
            } else {
                ans[ansIndex++] = nums[l] * nums[l--];
            }
        }
        return ans;
    }
}
```

#### [1051. 高度检查器](https://leetcode-cn.com/problems/height-checker/)

```java
class Solution {
    public int heightChecker(int[] heights) {
        int[] copy = heights.clone();
        Arrays.sort(copy);
        int ans = 0;
        for (int i = 0; i < heights.length; i++) {
            if (copy[i] != heights[i]) ans++;
        }
        return ans;
    }
}
```

#### [2164. 对奇偶下标分别排序](https://leetcode-cn.com/problems/sort-even-and-odd-indices-independently/)





#### [LCS 02. 完成一半题目](https://leetcode-cn.com/problems/WqXACV/)

```java
class Solution {
    public int halfQuestions(int[] questions) {
        int[] frequency = new int[1001];
        int len = questions.length;
        for (int num : questions) frequency[num]++;
        Arrays.sort(frequency);
        int ans = 0, sum = 0;
        for (int i = frequency.length - 1; i >= 0; i--) {
            if (frequency[i] > 0) {
                sum += frequency[i];
                ans++;
                if (sum >= (len >> 1)) return ans;
            }
        }
        return -1;
    }
}
```

#### [剑指 Offer 03. 数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

先排序，再进行判断：

```
class Solution {
    public int findRepeatNumber(int[] nums) {
        Arrays.sort(nums);
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == nums[i - 1]) return nums[i];
        }
        return -1;
    }
}
```

使用哈希表进行辅助：

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            if (set.contains(num)) {
                return num;
            } else {
                set.add(num);
            }
        }
        return -1;
    }
}
```

#### [剑指 Offer 39. 数组中出现次数超过一半的数字](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/)

```java
class Solution {
    public int majorityElement(int[] nums) {
        Arrays.sort(nums);
        return nums[nums.length >> 1];
    }
}
```

#### [剑指 Offer 40. 最小的k个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        PriorityQueue<Integer> priorityQueue = new PriorityQueue<>(((o1, o2) -> o2 - o1));
        for (int num : arr) {
            priorityQueue.offer(num);
            if (priorityQueue.size() > k) priorityQueue.poll();
        }
        int[] ans = new int[k];
        for (int i = 0; i < k; i++) {
            ans[i] = priorityQueue.poll();
        }
        return ans;
    }
}
```

#### [剑指 Offer 61. 扑克牌中的顺子](https://leetcode-cn.com/problems/bu-ke-pai-zhong-de-shun-zi-lcof/)

```java
class Solution {
    public boolean isStraight(int[] nums) {
        Arrays.sort(nums);
        int zeroCount = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == 0) {
                zeroCount++;
            } else if (i != 0 && nums[i] == nums[i - 1]) {
                return false;
            }
        }
        return nums[4] - nums[zeroCount] <= 4;
    }
}
```

#### [剑指 Offer II 032. 有效的变位词](https://leetcode-cn.com/problems/dKk3P7/)

对每个字母进行计数：

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.equals(t) || s.length() != t.length()) return false;
        char[] sChars = s.toCharArray();
        char[] tChars = t.toCharArray();
        int[] count = new int[26];
        for (char ch : sChars) count[ch - 'a']++;
        for (char ch : tChars) {
            count[ch - 'a']--;
            if (count[ch - 'a'] < 0) return false;
        }
        return true;
    }
}
```

#### [剑指 Offer II 075. 数组相对排序](https://leetcode-cn.com/problems/0H97ZC/)

使用计数排序进行求解：

```java
class Solution {
    public int[] relativeSortArray(int[] arr1, int[] arr2) {
        int max = 0;
        for (int num : arr1) max = Math.max(max, num);
        int[] frequency = new int[max + 1];
        for (int num : arr1) frequency[num]++;
        int[] ans = new int[arr1.length];
        int index = 0;
        for (int num : arr2) {
            for (int i = 0; i < frequency[num]; i++) {
                ans[index++] = num;
            }
            frequency[num] = 0;
        }
        for (int i = 0; i <= max; i++) {
            for (int j = 0; j < frequency[i]; j++) {
                ans[index++] = i;
            }
        }
        return ans;
    }
}
```

#### [面试题 01.01. 判定字符是否唯一](https://leetcode-cn.com/problems/is-unique-lcci/)

使用哈希表进行求解：

```java
class Solution {
    public boolean isUnique(String astr) {
        char[] chars = astr.toCharArray();
        Set<Character> set = new HashSet<>();
        for (char ch : chars) {
            if (set.contains(ch)) {
                return false;
            }
            set.add(ch);
        }
        return true;
    }
}
```

不使用额外的空间求解：

```java
class Solution {
    public boolean isUnique(String astr) {
        char[] chars = astr.toCharArray();
        Arrays.sort(chars);
        for (int i = 1; i < chars.length; i++) {
            if (chars[i] == chars[i - 1]) return false;
        }
        return true;
    }
}
```

#### [面试题 01.02. 判定是否互为字符重排](https://leetcode-cn.com/problems/check-permutation-lcci/)

```java
class Solution {
    public boolean CheckPermutation(String s1, String s2) {
        if (s1.length() != s2.length()) return false;
        char[] s1Chars = s1.toCharArray();
        Arrays.sort(s1Chars);
        char[] s2Chars = s2.toCharArray();
        Arrays.sort(s2Chars);
        for (int i = 0; i < s1Chars.length; i++) {
            if (s1Chars[i] != s2Chars[i]) return false;
        }
        return true;
    }
}
```

#### [面试题 10.01. 合并排序的数组](https://leetcode-cn.com/problems/sorted-merge-lcci/)

```java
class Solution {
    public void merge(int[] A, int m, int[] B, int n) {
        int aIndex = m - 1, bIndex = n - 1, lastIndex = A.length - 1;
        while (aIndex >= 0 || bIndex >= 0) {
            if (aIndex < 0 || (bIndex >= 0 && B[bIndex] >= A[aIndex])) {
                A[lastIndex--] = B[bIndex--];
            } else {
                A[lastIndex--] = A[aIndex--];
            }
        }
    }
}
```

#### [面试题 17.04. 消失的数字](https://leetcode-cn.com/problems/missing-number-lcci/)

```java
class Solution {
    public int missingNumber(int[] nums) {
        Arrays.sort(nums);
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != i) return i;
        }
        return nums.length;
    }
}
```

