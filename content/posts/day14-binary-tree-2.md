---
title: "Day14 - Binary Tree 2"
date: 2025-09-29T21:12:22-07:00
draft: false
ShowToc: true
---

104 111昨天写了

226 101 559 100 572 589 590

---

### [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/description/)

每个节点交换左右

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return root;

        TreeNode temp = root.left;
        root.left = root.right;
        root.right = temp;
        
        invertTree(root.left);
        invertTree(root.right);
        return root;
    }
}
```

也可以BFS，每次poll node都swap它的孩子

### [101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree/description/)

recursion比较简单

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root==null) return true;
        return mirrorTrees(root.left, root.right);
    }

    private boolean mirrorTrees(TreeNode left, TreeNode right) {
        if (left == null && right == null) return true;
        if (left == null || right == null) return false;

        if (left.val != right.val) return false;
        return mirrorTrees(left.left, right.right) && mirrorTrees(left.right, right.left);
    }
}
```

用BFS的话得用两个栈，一个正着放左子树，一个倒着放右子树。

或者用一个deque，思路同上。

### [559. Maximum Depth of N-ary Tree](https://leetcode.com/problems/maximum-depth-of-n-ary-tree/description/)

BFS，每一层counter++

```java
class Solution {
    public int maxDepth(Node root) {
        if (root == null) return 0;
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);
        int depth = 0;

        while (!queue.isEmpty()) {
            depth++;
            int levelSize = queue.size();
            while (levelSize-- > 0) {
                Node curr = queue.poll();
                for (Node child : curr.children) {
                    if (child != null) queue.offer(child);
                }
            }
        }
        return depth;
    }
}
```

DFS
```java
class Solution {
    Integer max = 0;
    public int maxDepth(Node root) {
        traverse(root, 0);
        return max;
    }

    private void traverse(Node node, int level) {
        if (node == null) return;
        max = Math.max(max, level+1);

        for (Node child : node.children) {
            traverse(child, level+1);
        }
    }
}
```

### [100. Same Tree](https://leetcode.com/problems/same-tree/)

随便traverse+比较即可

```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) return true;
        if (p == null || q == null || p.val != q.val) return false;
        return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
    }
}
```

### [572. Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/)

```java
class Solution {
    public boolean isSubtree(TreeNode root, TreeNode subRoot) {
        if (root == null) return false;
        // 一旦 isSameTree(root, subRoot) 就立刻返回 true，不会继续递归左右子树。
        if (isSameTree(root, subRoot)) return true;

        return isSubtree(root.left, subRoot) || isSubtree(root.right, subRoot);
    }

    private boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) return true;
        if (p == null || q == null || p.val != q.val) return false;
        return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
    }
}
```

### [589. N-ary Tree Preorder Traversal](https://leetcode.com/problems/n-ary-tree-preorder-traversal/)

recursion

```java
class Solution {
    List<Integer> res = new LinkedList<>();
    public List<Integer> preorder(Node root) {
        traverse(root);
        return res;
    }

    private void traverse(Node root) {
        if (root == null) return;
        res.add(root.val);
        for (Node child : root.children) {
            traverse(child);
        }
    }
}
```

iteration

```java
class Solution {
    public List<Integer> preorder(Node root) {
        List<Integer> res = new LinkedList<>();
        if (root == null) return res;
        Stack<Node> stack = new Stack<>();
        stack.push(root);

        while (!stack.empty()) {
            Node curr = stack.pop();
            res.add(curr.val);
            for (int i = curr.children.size()-1; i >= 0; i--) {
                Node child = curr.children.get(i);
                if (child != null) stack.push(child);
            }
        }
        return res;
    }
}
```

### [590. N-ary Tree Postorder Traversal](https://leetcode.com/problems/n-ary-tree-postorder-traversal/)

recursion

```java
class Solution {
    List<Integer> res = new LinkedList<>();
    public List<Integer> postorder(Node root) {
        traverse(root);
        return res;
    }

    private void traverse(Node curr) {
        if (curr == null) return;
        for (Node child : curr.children) {
            if (child != null) traverse(child);
        }
        res.add(curr.val);
    }
}
```

iteration

```java
class Solution {
    public List<Integer> postorder(Node root) {
        List<Integer> res = new LinkedList<>();
        if (root == null) return res;
        Stack<Node> stack = new Stack<>();
        stack.push(root);

        while (!stack.empty()) {
            Node curr = stack.pop();
            if (curr != null) {
                stack.push(curr);
                stack.push(null);
                for (int i = curr.children.size()-1; i >= 0; i--) {
                    Node child = curr.children.get(i);
                    if (child != null) stack.push(child);
                }
            } else {
                curr = stack.pop();
                res.add(curr.val);
            }
        }
        
        return res;
    }
}
```