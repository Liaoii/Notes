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

