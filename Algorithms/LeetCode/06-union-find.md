# 并查集（Union Find）

## [128. 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

```java
class Solution {
    class UnionSet {
        private int[] father, size;

        public UnionSet(int len) {
            father = new int[len];
            size = new int[len];
            for (int i = 0; i < len; i++) {
                father[i] = i;
                size[i] = 1;
            }
        }

        public int get(int x) {
            return father[x] = (father[x] == x ? x : get(father[x]));
        }

        public void merge(int a, int b) {
            size[get(b)] += size[get(a)];
            father[get(a)] = get(b);
        }
    }

    public int longestConsecutive(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        UnionSet set = new UnionSet(nums.length);
        for (int i = 0; i < nums.length; i++) {
            int x = nums[i];
            if (map.containsKey(x)) continue;
            if (map.containsKey(x - 1)) set.merge(i, map.get(x - 1));
            if (map.containsKey(x + 1)) set.merge(i, map.get(x + 1));
            map.put(x, i);
        }
        int ans = 0;
        for (int i = 0; i < nums.length; i++) {
            if (set.get(i) == i && set.size[i] > ans) ans = set.size[i];
        }
        return ans;
    }
}
```

## [200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

```java
class Solution {
    class UnionSet {
        private int[] father;
        public UnionSet(int len) {
            father = new int[len];
            for (int i = 0; i < len; i++) {
                father[i] = i;
            }
        }
        public int get(int x) {
            return father[x] = (father[x] == x ? x : get(father[x]));
        }
        public void merge(int a, int b) {
            father[get(a)] = get(b);
        }
    }

    public int numIslands(char[][] grid) {
        int n = grid.length, m = grid[0].length;
        UnionSet set = new UnionSet(m * n);
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == '0') continue;
                if (i > 0 && grid[i - 1][j] == '1') set.merge(i * m + j, (i - 1) * m + j);
                if (j > 0 && grid[i][j - 1] == '1') set.merge(i * m + j, i * m + j - 1);
            }
        }
        int ans = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == '1' && set.get(i * m + j) == i * m + j) ans++;
            }
        }
        return ans;
    }
}
```

## [547. 省份数量](https://leetcode-cn.com/problems/number-of-provinces/)

```java
class Solution {
    class UnionSet {
        private int[] father;
        public UnionSet(int len) {
            father = new int[len];
            for (int i = 0; i < len; i++) {
                father[i] = i;
            }
        }
        public int get(int x) {
            return father[x] = (father[x] == x ? x : get(father[x]));
        }
        public void merge(int a, int b) {
            father[get(a)] = get(b);
        }
    }

    public int findCircleNum(int[][] isConnected) {
        UnionSet set = new UnionSet(isConnected.length);
        for (int i = 0; i < isConnected.length; i++) {
            for (int j = 0; j < isConnected.length; j++) {
                if (isConnected[i][j] == 1) {
                    set.merge(i, j);
                }
            }
        }
        int ans = 0;
        for (int i = 0; i < isConnected.length; i++) {
            if (set.get(i) == i) ans++;
        }
        return ans;
    }
}
```

## [684. 冗余连接](https://leetcode-cn.com/problems/redundant-connection/)

```java
class Solution {
    class UnionSet {
        private int[] father;

        public UnionSet(int len) {
            father = new int[len + 1];
            for (int i = 0; i <= len; i++) {
                father[i] = i;
            }
        }

        public int get(int x) {
            return father[x] = (father[x] == x ? x : get(father[x]));
        }

        public void merge(int a, int b) {
            father[get(a)] = get(b);
        }
    }

    public int[] findRedundantConnection(int[][] edges) {
        UnionSet set = new UnionSet(edges.length);
        for (int[] temp : edges) {
            if (set.get(temp[0]) == set.get(temp[1])) return temp;
            set.merge(temp[0], temp[1]);
        }
        return null;
    }
}
```

## [947. 移除最多的同行或同列石头](https://leetcode-cn.com/problems/most-stones-removed-with-same-row-or-column/)

```java
class Solution {
    class UnionSet {
        private int[] father;

        public UnionSet(int len) {
            father = new int[len];
            for (int i = 0; i < len; i++) {
                father[i] = i;
            }
        }

        public int get(int x) {
            return father[x] = (father[x] == x ? x : get(father[x]));
        }

        public void merge(int a, int b) {
            father[get(a)] = get(b);
        }
    }

    public int removeStones(int[][] stones) {
        HashMap<Integer, Integer> xMap = new HashMap<>();
        HashMap<Integer, Integer> yMap = new HashMap<>();
        UnionSet u = new UnionSet(stones.length);
        for (int i = 0; i < stones.length; i++) {
            int x = stones[i][0];
            int y = stones[i][1];
            if (xMap.containsKey(x)) u.merge(i, xMap.get(x));
            if (yMap.containsKey(y)) u.merge(i, yMap.get(y));
            xMap.put(x, i);
            yMap.put(y, i);
        }
        int count = 0;
        for (int i = 0; i < stones.length; i++) {
            if (u.get(i) == i) count++;
        }
        return stones.length - count;
    }
}
```

## [990. 等式方程的可满足性](https://leetcode-cn.com/problems/satisfiability-of-equality-equations/)

```java
class Solution {
    class UnionSet {
        private int[] father;

        public UnionSet(int len) {
            father = new int[len];
            for (int i = 0; i < len; i++) {
                father[i] = i;
            }
        }

        public int get(int x) {
            return father[x] = (father[x] == x ? x : get(father[x]));
        }

        public void merge(int a, int b) {
            father[get(a)] = get(b);
        }
    }

    public boolean equationsPossible(String[] equations) {
        UnionSet set = new UnionSet(26);
        for (String s : equations) {
            if (s.charAt(1) == '!') continue;
            int a = s.charAt(0) - 'a';
            int b = s.charAt(3) - 'a';
            set.merge(a, b);
        }
        for (String s : equations) {
            if (s.charAt(1) == '=') continue;
            int a = s.charAt(0) - 'a';
            int b = s.charAt(3) - 'a';
            if (set.get(a) == set.get(b)) return false;
        }
        return true;
    }
}
```

## [1202. 交换字符串中的元素](https://leetcode-cn.com/problems/smallest-string-with-swaps/)

```java
class Solution {
    class UnionSet {
        private int[] father;

        public UnionSet(int len) {
            father = new int[len];
            for (int i = 0; i < len; i++) {
                father[i] = i;
            }
        }

        public int get(int x) {
            return father[x] = (father[x] == x ? x : get(father[x]));
        }

        public void merge(int a, int b) {
            father[get(a)] = get(b);
        }
    }

    public String smallestStringWithSwaps(String s, List<List<Integer>> pairs) {
        UnionSet us = new UnionSet(s.length());
        PriorityQueue<Character>[] ps = new PriorityQueue[s.length()];
        for (List<Integer> list : pairs) {
            int a = list.get(0);
            int b = list.get(1);
            us.merge(a, b);
        }
        for (int i = 0; i < s.length(); i++) {
            if (ps[us.get(i)] == null) {
                ps[us.get(i)] = new PriorityQueue<>();
            }
            ps[us.get(i)].offer(s.charAt(i));
        }
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
            sb.append(ps[us.get(i)].poll());
        }
        return sb.toString();
    }
}
```

## [1319. 连通网络的操作次数](https://leetcode-cn.com/problems/number-of-operations-to-make-network-connected/)

```java
class Solution {
    class UnionSet {
        private int[] father;

        public UnionSet(int len) {
            father = new int[len];
            for (int i = 0; i < len; i++) {
                father[i] = i;
            }
        }

        public int get(int x) {
            return father[x] = (father[x] == x ? x : get(father[x]));
        }

        public void merge(int a, int b) {
            father[get(a)] = get(b);
        }
    }

    public int makeConnected(int n, int[][] connections) {
        if (connections.length < n - 1) return -1;
        UnionSet set = new UnionSet(n);
        for (int[] i : connections) {
            int a = i[0];
            int b = i[1];
            set.merge(a, b);
        }
        int ans = 0;
        for (int i = 0; i < n; i++) {
            if (set.get(i) == i) ans++;
        }
        return ans - 1;
    }
}
```
