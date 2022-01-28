# 堆（Heap）

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

## [2099. 找到和最大的长度为 K 的子序列](https://leetcode-cn.com/problems/find-subsequence-of-length-k-with-the-largest-sum/)



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