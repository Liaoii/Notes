# 堆（Heap）

## [215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> queue = new PriorityQueue<>();
        for (int i : nums) queue.offer(i);
        while (queue.size() > k) queue.poll();
        return queue.peek();
    }
}
```

简单的优化：

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> queue = new PriorityQueue<>();
        for (int i : nums) {
            queue.offer(i);
            if (queue.size() > k) queue.poll();
        }
        return queue.peek();
    }
}
```

## [264. 丑数 II](https://leetcode-cn.com/problems/ugly-number-ii/)

```java
class Solution {
    public int nthUglyNumber(int n) {
        PriorityQueue<Long> queue = new PriorityQueue<>();
        long ans = 0;
        queue.offer(1L);
        while (n-- > 0) {
            ans = queue.poll();
            if (ans % 5 == 0) {
                queue.offer(ans * 5);
            } else if (ans % 3 == 0) {
                queue.offer(ans * 5);
                queue.offer(ans * 3);
            } else {
                queue.offer(ans * 5);
                queue.offer(ans * 3);
                queue.offer(ans * 2);
            }
        }
        return (int) ans;
    }
}
```

## [295. 数据流的中位数](https://leetcode-cn.com/problems/find-median-from-data-stream/)

```java
class MedianFinder {

    private PriorityQueue<Integer> max, min;

    public MedianFinder() {
        max = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        });
        min = new PriorityQueue<>();
    }

    public void addNum(int num) {
        if (max.isEmpty() || num < max.peek()) max.offer(num);
        else min.offer(num);
        if (min.size() > max.size()) max.offer(min.poll());
        if (max.size() == min.size() + 2) min.offer(max.poll());
    }

    public double findMedian() {
        int size = max.size() + min.size();
        if (size % 2 == 1) {
            return max.peek();
        } else {
            return (max.peek() + min.peek()) * 1.0 / 2;
        }
    }
}
```

## [313. 超级丑数](https://leetcode-cn.com/problems/super-ugly-number/)

```java
class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        int[] p = new int[primes.length];
        List<Integer> data = new ArrayList<>();
        data.add(1);
        int ans = 1;
        while (data.size() != n) {
            ans = primes[0] * data.get(p[0]);
            for (int i = 1; i < primes.length; i++) {
                ans = Math.min(ans, primes[i] * data.get(p[i]));
            }
            for (int i = 0; i < primes.length; i++) {
                if (primes[i] * data.get(p[i]) == ans) p[i]++;
            }
            data.add(ans);
        }
        return ans;
    }
}
```

## [373. 查找和最小的 K 对数字](https://leetcode-cn.com/problems/find-k-pairs-with-smallest-sums/)

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        PriorityQueue<List<Integer>> queue = new PriorityQueue<>(new Comparator<List<Integer>>() {
            @Override
            public int compare(List<Integer> o1, List<Integer> o2) {
                return (o2.get(0) + o2.get(1)) - (o1.get(0) + o1.get(1));
            }
        });
        for (int i : nums1) {
            for (int j : nums2) {
                List<Integer> temp = new ArrayList<>();
                temp.add(i);
                temp.add(j);
                if (queue.size() < k || queue.peek().get(0) + queue.peek().get(1) > i + j) {
                    queue.offer(temp);
                    if (queue.size() > k) queue.poll();
                } else {
                    break;
                }
            }
        }
        List<List<Integer>> ans = new ArrayList<>();
        while (!queue.isEmpty()) {
            ans.add(queue.poll());
        }
        return ans;
    }
}
```

## [506. 相对名次](https://leetcode-cn.com/problems/relative-ranks/)

```java
class Solution {
    public String[] findRelativeRanks(int[] score) {
        String[] ans = new String[score.length];
        PriorityQueue<Integer> pq = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        });
        for (int i : score) pq.offer(i);
        HashMap<Integer, String> map = new HashMap<>();
        for (int i = 1; i <= score.length; i++) {
            if (i == 1) {
                map.put(pq.poll(), "Gold Medal");
                continue;
            }
            if (i == 2) {
                map.put(pq.poll(), "Silver Medal");
                continue;
            }
            if (i == 3) {
                map.put(pq.poll(), "Bronze Medal");
                continue;
            }
            map.put(pq.poll(), i + "");
        }
        for (int i = 0; i < score.length; i++) {
            ans[i] = map.get(score[i]);
        }
        return ans;
    }
}
```

## [692. 前K个高频单词](https://leetcode-cn.com/problems/top-k-frequent-words/)

```java
class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> map = new HashMap<>();
        for (String s : words)
            map.put(s, map.getOrDefault(s, 0) + 1);
        PriorityQueue<Map.Entry<String, Integer>> queue = new PriorityQueue<>(new Comparator<Map.Entry<String, Integer>>() {
            @Override
            public int compare(Map.Entry<String, Integer> o1, Map.Entry<String, Integer> o2) {
                if (!o1.getValue().equals(o2.getValue())) {
                    return o2.getValue() - o1.getValue();
                } else {
                    return o1.getKey().compareTo(o2.getKey());
                }
            }
        });
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            queue.offer(entry);
        }
        List<String> ans = new ArrayList<>();
        while (k-- > 0) {
            ans.add(queue.poll().getKey());
        }
        return ans;
    }
}
```

## [703. 数据流中的第 K 大元素](https://leetcode-cn.com/problems/kth-largest-element-in-a-stream/)

```java
class KthLargest {

    private PriorityQueue<Integer> queue;
    private int len;

    public KthLargest(int k, int[] nums) {
        queue = new PriorityQueue<>();
        len = k;
        for (int i : nums) {
            if (queue.size() < k) {
                queue.offer(i);
            } else {
                if (queue.peek() < i) {
                    queue.poll();
                    queue.offer(i);
                }
            }
        }
    }

    public int add(int val) {
        if (queue.size() < len) {
            queue.offer(val);
        } else {
            if (queue.peek() < val) {
                queue.poll();
                queue.offer(val);
            }
        }
        return queue.peek();
    }
}
```

更简洁的代码：

```java
class KthLargest {
    private PriorityQueue<Integer> queue;
    int len;

    public KthLargest(int k, int[] nums) {
        queue = new PriorityQueue<>();
        len = k;
        for (int i : nums) queue.offer(i);
    }

    public int add(int val) {
        queue.offer(val);
        while (queue.size() > len) queue.poll();
        return queue.peek();
    }
}
```



## [1046. 最后一块石头的重量](https://leetcode-cn.com/problems/last-stone-weight/)

```java
class Solution {
    public int lastStoneWeight(int[] stones) {
        PriorityQueue<Integer> queue = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        });
        for (int i : stones) queue.offer(i);
        while (queue.size() > 1) {
            int y = queue.poll();
            int x = queue.poll();
            if (x != y) {
                queue.offer(y - x);
            }
        }
        return queue.isEmpty() ? 0 : queue.peek();
    }
}
```

## [1337. 矩阵中战斗力最弱的 K 行](https://leetcode-cn.com/problems/the-k-weakest-rows-in-a-matrix/)

使用哈希表进行额外存储：

```java
class Solution {
    public int[] kWeakestRows(int[][] mat, int k) {
        List<Integer> list = new ArrayList<>();
        Map<Integer, Integer> map = new HashMap<>();
        PriorityQueue<Integer> queue = new PriorityQueue<>();
        for (int i = 0; i < mat.length; i++) {
            int[] temp = mat[i];
            int l = 0, r = temp.length - 1, pos = -1;
            while (l <= r) {
                int m = (l + r) / 2;
                if (temp[m] == 0) {
                    r = m - 1;
                } else {
                    pos = m;
                    l = m + 1;
                }
            }
            list.add((pos + 1) * 100 + i);
            map.put((pos + 1) * 100 + i, i);
        }
        for (Integer i : list) {
            queue.offer(i);
        }
        int[] ans = new int[k];
        for (int i = 0; i < k; i++) ans[i] = map.get(queue.poll());
        return ans;
    }
}
```

优化堆的实现：

```java
class Solution {
    public int[] kWeakestRows(int[][] mat, int k) {
        List<Integer> list = new ArrayList<>();
        Map<Integer, Integer> map = new HashMap<>();
        PriorityQueue<int[]> queue = new PriorityQueue<>(new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return o1[1] - o2[1];
            }
        });
        for (int i = 0; i < mat.length; i++) {
            int[] temp = mat[i];
            int l = 0, r = temp.length - 1, pos = -1;
            while (l <= r) {
                int m = (l + r) / 2;
                if (temp[m] == 0) {
                    r = m - 1;
                } else {
                    pos = m;
                    l = m + 1;
                }
            }
            queue.offer(new int[]{i, (pos + 1) * 100 + i});
        }
        int[] ans = new int[k];
        for (int i = 0; i < k; i++) ans[i] = queue.poll()[0];
        return ans;
    }
}
```

## [1464. 数组中两元素的最大乘积](https://leetcode-cn.com/problems/maximum-product-of-two-elements-in-an-array/)

```java
class Solution {
    public int maxProduct(int[] nums) {
        PriorityQueue<Integer> queue = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        });
        for (int i : nums) queue.add(i);
        return (queue.poll() - 1) * (queue.poll() - 1);
    }
}
```

## [1753. 移除石子的最大得分](https://leetcode-cn.com/problems/maximum-score-from-removing-stones/)

```java
class Solution {
    public int maximumScore(int a, int b, int c) {
        int temp;
        if (a > b) {
            temp = a;
            a = b;
            b = temp;
        }
        if (a > c) {
            temp = a;
            a = c;
            c = temp;
        }
        if (b > c) {
            temp = b;
            b = c;
            c = temp;
        }
        int ans = 0;
        int count = Math.min(a, c - b);
        a -= count;
        ans += count;
        if (a > 0) {
            if (a % 2 == 1) a -= 1;
            b -= a / 2;
            ans += a;
        }
        ans += b;
        return ans;
    }
}
```

## [2099. 找到和最大的长度为 K 的子序列](https://leetcode-cn.com/problems/find-subsequence-of-length-k-with-the-largest-sum/)

```java
class Solution {
    public int[] maxSubsequence(int[] nums, int k) {
        int[] ind = new int[k];
        int[] ans = new int[k];
        PriorityQueue<int[]> queue = new PriorityQueue<>(new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return o2[1] - o1[1];
            }
        });
        for (int i = 0; i < nums.length; i++) queue.offer(new int[]{i, nums[i]});
        for (int i = 0; i < k; i++) ind[i] = queue.poll()[0];
        Arrays.sort(ind);
        for (int i = 0; i < k; i++) {
            ans[i] = nums[ind[i]];
        }
        return ans;
    }
}
```

## [剑指 Offer 40. 最小的k个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)

使用大根堆求解：

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        int[] ans = new int[k];
        if (k == 0) return ans;
        PriorityQueue<Integer> queue = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        });
        for (int j : arr) {
            queue.offer(j);
            if (queue.size() > k) queue.poll();
        }
        for (int i = 0; i < k; i++) {
            ans[i] = queue.poll();
        }
        return ans;
    }
}
```

使用排序求解：

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        int[] ans = new int[k];
        Arrays.sort(arr);
        System.arraycopy(arr, 0, ans, 0, k);
        return ans;
    }
}
```

## [剑指 Offer II 059. 数据流的第 K 大数值](https://leetcode-cn.com/problems/jBjn9C/)

```java
class KthLargest {
    PriorityQueue<Integer> queue;
    int len;

    public KthLargest(int k, int[] nums) {
        queue = new PriorityQueue<>();
        len = k;
        for (int i = 0; i < nums.length; i++) {
            if (queue.size() < k) {
                queue.offer(nums[i]);
            } else {
                if (nums[i] > queue.peek()) {
                    queue.poll();
                    queue.offer(nums[i]);
                }
            }
        }
    }

    public int add(int val) {
        if (queue.size() < len) {
            queue.offer(val);
        } else {
            if (val > queue.peek()) {
                queue.poll();
                queue.offer(val);
            }
        }
        return queue.peek();
    }
}
```

## [面试题 17.20. 连续中值](https://leetcode-cn.com/problems/continuous-median-lcci/)

```java
class MedianFinder {

    private PriorityQueue<Integer> max, min;

    public MedianFinder() {
        max = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        });
        min = new PriorityQueue<>();
    }

    public void addNum(int num) {
        if (max.isEmpty() || num < max.peek()) max.offer(num);
        else min.offer(num);
        if (min.size() > max.size()) max.offer(min.poll());
        if (max.size() == min.size() + 2) min.offer(max.poll());
    }

    public double findMedian() {
        int size = max.size() + min.size();
        if (size % 2 == 1) {
            return max.peek();
        } else {
            return (max.peek() + min.peek()) * 1.0 / 2;
        }
    }
}
```

