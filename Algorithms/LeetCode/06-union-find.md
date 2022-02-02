# 并查集（Union Find）

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
