# 二叉树（Binary-Tree）

## [100. 相同的树](https://leetcode-cn.com/problems/same-tree/)

```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) return true;
        if ((p == null && q != null) || (p != null && q == null)) return false;
        if (p.val != q.val) return false;
        if (!isSameTree(p.left, q.left)) return false;
        if (!isSameTree(p.right, q.right)) return false;
        return true;
    }
}
```

## [101. 对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return compare(root.left, root.right);
    }

    private boolean compare(TreeNode left, TreeNode right) {
        if (left == null && right == null) return true;
        if (left == null || right == null) return false;
        if (left.val != right.val) return false;
        if (!compare(left.left, right.right)) return false;
        return compare(left.right, right.left);
    }
}
```

## [103. 二叉树的锯齿形层序遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

```java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<>();
        getNext(root, 0, ans);
        for (int i = 1; i < ans.size(); i += 2) {
            reverse(ans.get(i));
        }
        return ans;
    }

    public void reverse(List<Integer> list) {
        for (int i = 0, j = list.size() - 1; i < j; i++, j--) {
            int temp = list.get(i);
            list.set(i, list.get(j));
            list.set(j, temp);
        }
    }

    public void getNext(TreeNode root, int k, List<List<Integer>> ans) {
        if (root == null) return;
        if (k == ans.size()) ans.add(new ArrayList<>());
        ans.get(k).add(root.val);
        getNext(root.left, k + 1, ans);
        getNext(root.right, k + 1, ans);
    }
}
```

## [104. 二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

```java
class Solution {
    public int maxDepth(TreeNode root) {
        return getDepth(root, 0);
    }

    public int getDepth(TreeNode root, int k) {
        if (root == null) return k;
        int l = getDepth(root.left, k + 1);
        int r = getDepth(root.right, k + 1);
        return Math.max(l, r);
    }
}
```

## [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

```java
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder.length == 0) return null;
        int pos = 0;
        while (inorder[pos] != preorder[0]) pos++;
        int[] lPre = new int[pos], rPre = new int[preorder.length - pos - 1], lIn = new int[pos], rIn = new int[preorder.length - pos - 1];
        for (int i = 0; i < pos; i++) {
            lPre[i] = preorder[i + 1];
            lIn[i] = inorder[i];
        }
        for (int i = pos + 1; i < preorder.length; i++) {
            rPre[i - pos - 1] = preorder[i];
            rIn[i - pos - 1] = inorder[i];
        }
        TreeNode node = new TreeNode(preorder[0]);
        node.left = buildTree(lPre, lIn);
        node.right = buildTree(rPre, rIn);
        return node;
    }
}
```

## [107. 二叉树的层序遍历 II](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

```java
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<>();
        getNext(root, 0, ans);
        for (int i = 0, j = ans.size() - 1; i < j; i++, j--) {
            List<Integer> temp = ans.get(i);
            ans.set(i, ans.get(j));
            ans.set(j, temp);
        }
        return ans;
    }

    public void getNext(TreeNode root, int k, List<List<Integer>> ans) {
        if (root == null) return;
        if (k == ans.size()) {
            ans.add(new ArrayList<>());
        }
        ans.get(k).add(root.val);
        getNext(root.left, k + 1, ans);
        getNext(root.right, k + 1, ans);
    }
}
```

## [108. 将有序数组转换为二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return createNode(nums, 0, nums.length - 1);
    }

    public TreeNode createNode(int[] nums, int left, int right) {
        int mid = (right - left) / 2 + left;
        TreeNode node = new TreeNode(nums[mid]);
        if (mid - left > 0) node.left = createNode(nums, left, mid - 1);
        if (right - mid > 0) node.right = createNode(nums, mid + 1, right);
        return node;
    }
}
```

## [110. 平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)

```java
class Solution {
    public int getHeight(TreeNode root) {
        if (root == null) return 0;
        int left = getHeight(root.left);
        int right = getHeight(root.right);
        if (left == -2 || right == -2) return -2;
        if (Math.abs(left - right) > 1) return -2;
        return Math.max(left, right) + 1;
    }

    public boolean isBalanced(TreeNode root) {
        return getHeight(root) >= 0;
    }
}
```

## [111. 二叉树的最小深度](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

```java
class Solution {
    public int minDepth(TreeNode root) {
        return getMinDepth(root, 0);
    }

    public int getMinDepth(TreeNode root, int k) {
        if (root == null) return k;
        if (root.left == null) return getMinDepth(root.right, k + 1);
        if (root.right == null) return getMinDepth(root.left, k + 1);
        return Math.min(getMinDepth(root.right, k + 1), getMinDepth(root.left, k + 1));
    }
}
```

## [112. 路径总和](https://leetcode-cn.com/problems/path-sum/)

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) return false;
        if (root.left == null && root.right == null) return root.val == targetSum;
        if (root.left != null && hasPathSum(root.left, targetSum - root.val)) return true;
        if (root.right != null && hasPathSum(root.right, targetSum - root.val)) return true;
        return false;
    }
}
```

## [144. 二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) return res;
        getChild(root, res);
        return res;
    }

    private void getChild(TreeNode root, List<Integer> res) {
        res.add(root.val);
        if (root.left != null) getChild(root.left, res);
        if (root.right != null) getChild(root.right, res);
    }
}
```

## [222. 完全二叉树的节点个数](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

```java
class Solution {
    public int countNodes(TreeNode root) {
        if (root == null) return 0;
        return countNodes(root.left) + countNodes(root.right) + 1;
    }
}
```

## [226. 翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return null;
        TreeNode temp = root.left;
        root.left = root.right;
        root.right = temp;
        invertTree(root.left);
        invertTree(root.right);
        return root;
    }
}
```

## [235. 二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root.val > p.val && root.val < q.val) return root;
        if (root.val < p.val && root.val > q.val) return root;
        if (root.val == p.val) return p;
        if (root.val == q.val) return q;
        if (root.val < p.val) return lowestCommonAncestor(root.right, p, q);
        return lowestCommonAncestor(root.left, p, q);
    }
}
```

## [257. 二叉树的所有路径](https://leetcode-cn.com/problems/binary-tree-paths/)

```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> ans = new ArrayList<>();
        StringBuilder sb = new StringBuilder();
        sb.append(root.val);
        getPath(root, sb, ans);
        return ans;
    }

    public void getPath(TreeNode root, StringBuilder sb, List<String> ans) {
        if (root.left == null && root.right == null) {
            ans.add(sb.toString());
        }
        if (root.left != null) {
            StringBuilder temp = new StringBuilder();
            temp.append(sb.toString());
            temp.append("->").append(root.left.val);
            getPath(root.left, temp, ans);
        }
        if (root.right != null) {
            StringBuilder temp = new StringBuilder();
            temp.append(sb.toString());
            temp.append("->").append(root.right.val);
            getPath(root.right, temp, ans);
        }
    }
}
```

## [404. 左叶子之和](https://leetcode-cn.com/problems/sum-of-left-leaves/)

```java
class Solution {
    private int sum = 0;

    public int sumOfLeftLeaves(TreeNode root) {
        if (root == null) return 0;
        if (root.left != null && root.left.left == null && root.left.right == null) sum += root.left.val;
        sumOfLeftLeaves(root.left);
        sumOfLeftLeaves(root.right);
        return sum;
    }
}
```

## [501. 二叉搜索树中的众数](https://leetcode-cn.com/problems/find-mode-in-binary-search-tree/)

使用哈希表进行实现：

```java
class Solution {
    public int[] findMode(TreeNode root) {
        Map<Integer, Integer> map = new HashMap<>();
        getChild(root, map);
        Integer max = null;
        for (int i : map.keySet()) {
            max = max == null ? map.get(i) : Math.max(max, map.get(i));
        }
        List<Integer> ans = new ArrayList<>();
        for (int i : map.keySet()) {
            if (max.equals(map.get(i))) ans.add(i);
        }
        int[] mode = new int[ans.size()];
        for (int i = 0; i < ans.size(); i++) {
            mode[i] = ans.get(i);
        }
        return mode;
    }

    public void getChild(TreeNode root, Map<Integer, Integer> map) {
        if (root == null) return;
        getChild(root.left, map);
        map.put(root.val, map.getOrDefault(root.val, 0) + 1);
        getChild(root.right, map);
    }
}
```

不使用额外空间：

```java
class Solution {
    int base, count, maxCount;
    List<Integer> ans = new ArrayList<>();

    public int[] findMode(TreeNode root) {
        getChild(root);
        int[] res = new int[ans.size()];
        for (int i = 0; i < ans.size(); i++)
            res[i] = ans.get(i);
        return res;
    }

    public void getChild(TreeNode root) {
        if (root == null) return;
        getChild(root.left);
        if (root.val == base) {
            count++;
        } else {
            base = root.val;
            count = 1;
        }
        if (count == maxCount) ans.add(root.val);
        if (count > maxCount) {
            maxCount = count;
            ans.clear();
            ans.add(root.val);
        }
        getChild(root.right);
    }
}
```

Morris中序遍历：

## [530. 二叉搜索树的最小绝对差](https://leetcode-cn.com/problems/minimum-absolute-difference-in-bst/)

```java
class Solution {
    public int getMinimumDifference(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        inOrder(root, list);
        int ans = 100001;
        for (int i = 1; i < list.size(); i++) {
            ans = Math.min(ans, Math.abs(list.get(i) - list.get(i - 1)));
        }
        return ans;
    }

    private void inOrder(TreeNode root, List<Integer> list) {
        if (root == null) return;
        inOrder(root.left, list);
        list.add(root.val);
        inOrder(root.right, list);
    }
}
```

不使用额外存储：

```java
class Solution {
    int cur = 100001;
    int min = 100001;

    public int getMinimumDifference(TreeNode root) {
        inOrder(root);
        return min;
    }

    private void inOrder(TreeNode root) {
        if (root == null) return;
        inOrder(root.left);
        min = Math.min(min, Math.abs(root.val - cur));
        cur = root.val;
        inOrder(root.right);
    }
}
```

上一种解法的优化：

```java
class Solution {
    int cur;
    int min;

    public int getMinimumDifference(TreeNode root) {
        cur = -1;
        min = Integer.MAX_VALUE;
        inOrder(root);
        return min;
    }

    private void inOrder(TreeNode root) {
        if (root == null) return;
        inOrder(root.left);
        if (cur != -1) {
            min = Math.min(min, Math.abs(cur - root.val));
        }
        cur = root.val;
        inOrder(root.right);
    }
}
```

## [543. 二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

```java
class Solution {
    int diameter = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        getDiameter(root);
        return diameter;
    }

    public int getDiameter(TreeNode root) {
        if (root.left == null && root.right == null) return 1;
        int left = root.left == null ? 0 : getDiameter(root.left);
        int right = root.right == null ? 0 : getDiameter(root.right);
        diameter = Math.max(diameter, left + right);
        return Math.max(left, right) + 1;
    }
}
```

## [589. N 叉树的前序遍历](https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/)

```java
class Solution {
    public List<Integer> preorder(Node root) {
        List<Integer> ans = new ArrayList<>();
        getChild(root, ans);
        return ans;
    }

    public void getChild(Node root, List<Integer> ans) {
        if (root == null) return;
        ans.add(root.val);
        for (Node n : root.children) {
            getChild(n, ans);
        }
    }
}
```

## [662. 二叉树最大宽度](https://leetcode-cn.com/problems/maximum-width-of-binary-tree/)

```java
class Solution {
    class Serial {
        TreeNode node;
        int serial;
        Serial(TreeNode node, int serial) {
            this.node = node;
            this.serial = serial;
        }
    }

    public int widthOfBinaryTree(TreeNode root) {
        Queue<Serial> queue = new LinkedList<>();
        queue.offer(new Serial(root, 0));
        int ans = 0;
        while (!queue.isEmpty()) {
            int count = queue.size();
            int left = queue.peek().serial, right = queue.peek().serial;
            for (int i = 0; i < count; i++) {
                TreeNode temp = queue.peek().node;
                int ind = queue.peek().serial;
                right = ind;
                if (temp.left != null) queue.offer(new Serial(temp.left, (ind - left) * 2));
                if (temp.right != null) queue.offer(new Serial(temp.right, (ind - left) * 2 + 1));
                queue.poll();
            }
            ans = Math.max(ans, right - left + 1);
        }
        return ans;
    }
}
```

## [968. 监控二叉树](https://leetcode-cn.com/problems/binary-tree-cameras/)

动态规划：

```java
class Solution {
    public int minCameraCover(TreeNode root) {
        int[][] dp = new int[2][2];
        getDp(root, dp);
        return Math.min(dp[0][1], dp[0][0]);
    }

    private void getDp(TreeNode root, int[][] dp) {
        if (root == null) {
            dp[0][0] = 0;
            dp[0][1] = 10000;
            dp[1][0] = 0;
            dp[1][1] = 10000;
            return;
        }
        if (root.left == null && root.right == null) {
            dp[0][0] = 10000;
            dp[0][1] = 1;
            dp[1][0] = 0;
            dp[1][1] = 1;
            return;
        }
        int[][] l = new int[2][2];
        int[][] r = new int[2][2];
        getDp(root.left, l);
        getDp(root.right, r);
        dp[0][0] = Math.min(Math.min(l[0][1] + r[0][0], l[0][0] + r[0][1]), l[0][1] + r[0][1]);
        dp[0][1] = Math.min(Math.min(l[1][0] + r[1][0], l[1][1] + r[1][1]), Math.min(l[1][0] + r[1][1], l[1][1] + r[1][0])) + 1;
        dp[1][0] = Math.min(dp[0][0], l[0][0] + r[0][0]);
        dp[1][1] = dp[0][1];
    }
}
```

## [剑指 Offer 26. 树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)

```java
class Solution {
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        if (A == null || B == null) return false;
        if (A.val == B.val && isMatch(A, B)) return true;
        return isSubStructure(A.left, B) || isSubStructure(A.right, B);
    }

    public boolean isMatch(TreeNode A, TreeNode B) {
        if (B == null) return true;
        if (A == null) return false;
        if (A.val != B.val) return false;
        return isMatch(A.left, B.left) && isMatch(A.right, B.right);
    }
}
```

## [剑指 Offer 32 - II. 从上到下打印二叉树 II](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

```
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<>();
        print(root, 0, ans);
        return ans;
    }

    private void print(TreeNode root, int i, List<List<Integer>> ans) {
        if (root == null) return;
        if (i == ans.size()) {
            List<Integer> list = new ArrayList<>();
            ans.add(list);
        }
        ans.get(i).add(root.val);
        print(root.left, i + 1, ans);
        print(root.right, i + 1, ans);
    }
}
```

## [剑指 Offer 54. 二叉搜索树的第k大节点](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)

```java
class Solution {
    public int kthLargest(TreeNode root, int k) {
        int rCount = getCount(root.right);
        if (k <= rCount) return kthLargest(root.right, k);
        if (k == rCount + 1) return root.val;
        return kthLargest(root.left, k - rCount - 1);
    }

    public int getCount(TreeNode root) {
        if (root == null) return 0;
        return getCount(root.left) + getCount(root.right) + 1;
    }
}
```

