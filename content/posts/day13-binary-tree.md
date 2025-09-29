---
title: "Day13 - Binary Tree"
date: 2025-09-28T16:29:22-07:00
draft: false
ShowToc: true
---

medium把我封了。。。。申诉也没人理我

本来想用wix之类的别的网站，但是我怕它们又莫名其妙封我，干脆自己建了一个小小的博客，反正全是static pages。不过不能用我的大号建repo，因为公司有很多开源的东西用的个人账号，所以同事们都能看到我在做啥（x

甚至还可以评论：Hugo ships with support for Disqus, a third-party service that provides comment and community capabilities to websites via JavaScript.

---

今天复习二叉树！
- **完全二叉树**：除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层*最左边*的若干位置
- **二叉搜索树**： `leftChild.val < curr.val < rightChild.val`
- **平衡二叉搜索树**：`|leftChild.height - rightChild.height| <= 1` for every node


二叉树的存储方式：
- **链式存储**：通过指针把分布在各个地址的节点串联一起
- **顺序存储**：用数组来存储。如果父节点的数组下标是 i，那么它的左孩子就是 `i * 2 + 1`，右孩子就是 `i * 2 + 2`。

二叉树的遍历方法：
- **DFS**：preorder, postorder, inorder
- **BFS**：level order

---

144, 145, 94 (basic DFS)

102, 107, 199, 637, 429, 515, 116, 117, 104, 111 (BFS)

***

## DFS

### [144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)
preorder: 第⼀个是根节点的值，接着是左⼦树，最后是右⼦树

#### Recursion

```java
class Solution {
    LinkedList<Integer> res = new LinkedList<>();
    public List<Integer> preorderTraversal(TreeNode root) {
        traverse(root);
        return res;
    }

    private void traverse(TreeNode root) {
        if (root == null) {
            return;
        }
        res.add(root.val);
        traverse(root.left);
        traverse(root.right);
    }
}
```

#### Iteration
用stack，右孩子先入栈，左孩子再入栈，这样左孩子可以先出栈

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        LinkedList<Integer> res = new LinkedList<>();
        Stack<TreeNode> stack = new Stack<>();

        if (root == null) {
            return res;
        }
        stack.add(root);

        while (!stack.empty()) {
            TreeNode curr = stack.pop();
            res.add(curr.val);
            if (curr.right!=null) stack.add(curr.right);
            if (curr.left!=null) stack.add(curr.left);
        }
        return res;
    }
}
```

### [145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)

#### Recursion

postorder: 先是左⼦树，接着是右⼦树，最后是根节点的值

```java
class Solution {
    LinkedList<Integer> res = new LinkedList<>();
    public List<Integer> postorderTraversal(TreeNode root) {
        traverse(root);
        return res;
    }

    private void traverse(TreeNode curr) {
        if (curr == null) {
            return;
        }
        traverse(curr.left);
        traverse(curr.right);
        res.add(curr.val);
    }
}
```

#### Iteration 1

用stack，和preorder差不多，唯一区别是我们最后要反转res。这样的话入栈顺序是：先左再右，这样右孩子先出栈，res里的顺序就是中-右-左，反转之后就是左-右-中了。

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        LinkedList<Integer> res = new LinkedList<>();
        Stack<TreeNode> stack = new Stack<>();

        if (root == null) {
            return res;
        }
        stack.add (root);

        while (!stack.empty()) {
            TreeNode curr = stack.pop();
            res.add(curr.val);

            if (curr.left!=null) stack.add(curr.left);
            if (curr.right!=null) stack.add(curr.right);
        }

        Collections.reverse(res);
        return res;
    }
}
```

#### Iteration 2

同样是stack，怎么做到最后处理root？

遇到root的时候：把它放回堆里再用null标记，再先右后左放进堆里，这样左孩子会先出栈，然后是右孩子，最后才是根。


```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        LinkedList<Integer> res = new LinkedList<>();
        Stack<TreeNode> stack = new Stack<>();

        if (root == null) {
            return res;
        }
        stack.add (root);

        while (!stack.empty()) {
            TreeNode curr = stack.pop();
            if (curr != null) {
                stack.push(curr);
                stack.push(null);
                if (curr.right != null) stack.push(curr.right);
                if (curr.left != null) stack.push(curr.left);
            } else {
                curr = stack.pop();
                res.add(curr.val);
            }
        }
        return res;
    }
}
```

### [94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/description/)

#### Recursion

```java
class Solution {
    LinkedList<Integer> res = new LinkedList<>();
    public List<Integer> inorderTraversal(TreeNode root) {
        traverse(root);
        return res;
    }

    private void traverse(TreeNode curr) {
        if (curr == null) {
            return;
        }
        traverse(curr.left);
        res.add(curr.val);
        traverse(curr.right);
    }
}
```

#### Iteration 1

还是用stack，每次在检查curr之前都先走到最左。

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        LinkedList<Integer> res = new LinkedList<>();
        Stack<TreeNode> stack = new Stack<>();

        TreeNode curr = root;
        while (curr != null || !stack.empty()) {
            // go to leftest
            while (curr != null) {
                stack.add(curr);
                curr = curr.left;
            }
            // root
            curr = stack.pop();
            res.add(curr.val);
            // right
            curr = curr.right;
        }

        return res;
    }
}
```

#### Iteration 2

同样用null标记根，入栈顺序是反过来的右-中-左

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        LinkedList<Integer> res = new LinkedList<>();
        Stack<TreeNode> stack = new Stack<>();

        if (root != null) {
            stack.push(root);
        }

        while (!stack.empty()) {
            TreeNode curr = stack.pop();
            if (curr != null) {
                if (curr.right != null) stack.push(curr.right);
                stack.push(curr);
                stack.push(null);
                if (curr.left != null) stack.push(curr.left);
            } else {
                curr = stack.pop();
                res.add(curr.val);
            }
        }
        return res;
    }
}
```

## BFS

### [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/description/)

BFS的话迭代更简单。

#### Iteration 

两个queue，一个记录node，一个记录level

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new LinkedList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        Queue<Integer> level = new LinkedList<>();

        if (root != null) {
            queue.add(root);
            level.add(0);
        }

        while (!queue.isEmpty()) {
            TreeNode curr = queue.poll();
            int currLevel = level.poll();

            if (res.size() <= currLevel) {
                res.add(new LinkedList<Integer>());
            }
            res.get(currLevel).add(curr.val);
            if (curr.left != null) {
                queue.add(curr.left);
                level.add(currLevel+1);
            }
            if (curr.right != null) {
                queue.add(curr.right);
                level.add(currLevel+1);
            }
        }
        return res;
    }
}
```

更好的方法：我们可以记录每一层的size，用while loop来遍历这一层，这样就不需要两个queue了

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new LinkedList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        if (root != null) {
            queue.add(root);
        }

        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            LinkedList<Integer> level = new LinkedList<>();
            while (levelSize-- > 0) {
                TreeNode curr = queue.poll();
                level.add(curr.val);
                if (curr.left != null) queue.add(curr.left);
                if (curr.right != null) queue.add(curr.right);
            }
            res.add(level);
        }
        return res;
    }
}
```

#### Recursion
其实还是DFS,就是多pass in一个level param，只要确保左在右前面处理就行

```java
class Solution {
    List<List<Integer>> res = new LinkedList<>();
    public List<List<Integer>> levelOrder(TreeNode root) {
        traverse(root, 0);
        return res;
    }

    private void traverse(TreeNode curr, int level) {
        if (curr == null) return;

        while (level >= res.size()) {
            res.add(new LinkedList<Integer>());
        }

        res.get(level).add(curr.val);
        traverse(curr.left, level+1);
        traverse(curr.right, level+1);
    }
}
```


### [107. Binary Tree Level Order Traversal II](https://leetcode.com/problems/binary-tree-level-order-traversal-ii/description/)

用arraylist，每次从最前面添加新level（或者按正常顺序加，最后reverse)

```java
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        if (root != null) {
            queue.add(root);
        }

        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            List<Integer> level = new LinkedList<>();
            while (levelSize-- > 0) {
                TreeNode curr = queue.poll();
                level.add(curr.val);
                if (curr.left != null) queue.add(curr.left);
                if (curr.right != null) queue.add(curr.right);
            }
            res.add(0, level);
        }
        return res;
    }
}
```

### [199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)

BFS做法：每次只加入level的最后一个元素
```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> res = new LinkedList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        if (root != null) {
            queue.add(root);
        }

        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            while (levelSize-- > 0) {
                TreeNode curr = queue.poll();
                if (levelSize == 0) res.add(curr.val);  // 只加level的最后一个元素
                if (curr.left != null) queue.add(curr.left);
                if (curr.right != null) queue.add(curr.right);
            }
        }
        return res;
    }
}
```

DFS做法：如果这一层已经有元素了就不加入。

确保先处理右再处理左。root什么时候处理不重要，因为它和children不在同一个level

因为先右后左，所以这一层加入的第一个元素一定是最右的元素。

```java
class Solution {
    List<Integer> res = new LinkedList<>();
    public List<Integer> rightSideView(TreeNode root) {
        traverse(root,0);
        return res;
    }

    private void traverse(TreeNode curr, int level) {
        if (curr == null) return;
        
        if (level >= res.size()) {  // 这一层加入的第一个元素
            res.add(curr.val);
        }
        traverse(curr.right, level+1);
        traverse(curr.left, level+1);
    }
}
```

### [637. Average of Levels in Binary Tree](https://leetcode.com/problems/average-of-levels-in-binary-tree/)
计算平均数instead of 添加整层的列表

```java
class Solution {
    public List<Double> averageOfLevels(TreeNode root) {
        List<Double> res = new LinkedList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        if (root != null) {
            queue.add(root);
        }

        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            double total = 0.0;    // make sure to use double here，不然会整数溢出
            for (int i = 0; i < levelSize; i++) {
                TreeNode curr = queue.poll();
                total += curr.val;  
                if (curr.left != null) queue.add(curr.left);
                if (curr.right != null) queue.add(curr.right);
            }
            res.add(total / levelSize);
        }
        return res;
    }
}
```

### [429. N-ary Tree Level Order Traversal](https://leetcode.com/problems/n-ary-tree-level-order-traversal/)

遍历所有小孩instead of左孩子和右孩子

```java
class Solution {
    List<List<Integer>> res = new LinkedList<>();
    public List<List<Integer>> levelOrder(Node root) {
        traverse(root, 0);
        return res;
    }

    private void traverse(Node curr, int level) {
        if (curr == null) return;
        while (level >= res.size()) {
            res.add(new LinkedList<Integer>());
        }

        res.get(level).add(curr.val);
        for (Node child : curr.children) {
            traverse(child, level+1);
        }
    }
}
```

### [515. Find Largest Value in Each Tree Row](https://leetcode.com/problems/find-largest-value-in-each-tree-row/)

同637,记录最大值 instead of 算平均值
```java
class Solution {
    public List<Integer> largestValues(TreeNode root) {
        List<Integer> res = new LinkedList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        if (root != null) {
            queue.add(root);
        }

        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            int max = Integer.MIN_VALUE;
            for (int i = 0; i < levelSize; i++) {
                TreeNode curr = queue.poll();
                max = Math.max(max, curr.val);
                if (curr.left != null) queue.add(curr.left);
                if (curr.right != null) queue.add(curr.right);
            }
            res.add(max);
        }
        return res;
    }
}
```

### [116. Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)

queue approach: curr -> queue.peek

```java
class Solution {
    public Node connect(Node root) {
        Queue<Node> queue = new LinkedList<>();
        if (root != null) {
            queue.add(root);
        }

        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            while(levelSize-- > 0) {
                Node curr = queue.poll();
                if (levelSize > 0) {
                    curr.next = queue.peek();
                }
                if (curr.left != null) queue.add(curr.left);
                if (curr.right != null) queue.add(curr.right);
            }
        }
        return root;
    }
}
```

recursion

```java
class Solution {
    public Node connect(Node root) {
       if (root==null) return null;
        helper(root.left, root.right);
        return root; 
    }

    private void helper(Node n1, Node n2) {
        if (n1 == null || n2 == null) return;
        n1.next = n2;

        // 这里的顺序无所谓，但是会重复连接不够efficient
        // 这题的前提是：perfect binary tree！！
        helper(n1.left, n1.right);
        helper(n1.right, n2.left);
        helper(n2.left, n2.right);
    }
}
```

### [117. Populating Next Right Pointers in Each Node II](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/)

跟上题一模一样的代码，但是不能用recursion



### [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

recursion

```java
class Solution {
    Integer max = 0;
    public int maxDepth(TreeNode root) {
        traverse(root, 0);
        return max;
    }
    private void traverse(TreeNode curr, int level) {
        if (curr == null) return;
        max = Math.max(max, level+1);

        traverse(curr.left, level+1);
        traverse(curr.right, level+1);
    }
}
```

iteration的话BFS更合适，因为它自带level数据(用一个counter记录level即可)，DFS的话还得额外用一个queue来记录level

```java
class Solution {
    public int maxDepth(TreeNode root) {
        int level = 0;
        Queue<TreeNode> queue = new LinkedList<>();
        if (root != null) {
            queue.add(root);
        }

        while (!queue.isEmpty()) {
            level++;
            int levelSize = queue.size();
            while(levelSize-- > 0) {
                TreeNode curr = queue.poll();
                if (curr.left != null) queue.add(curr.left);
                if (curr.right != null) queue.add(curr.right);
            }
        }
        return level;
    }
}
```

### [111. Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/)

recursion: minDepth = min(左孩子minDepth，右孩子minDepth)+1

```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) return 0;
        int left = minDepth(root.left);
        int right = minDepth(root.right);
        // left/right could be 0
        if (left == 0 || right == 0) {
            return left + right + 1;
        }
        return Math.min(left, right) + 1;
    }
}
```

iteration: 一旦遇到无孩节点，一定就已经遇到了最小深度，直接返回

```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) return 0;
        Queue<TreeNode> queue =  new LinkedList<>();
        queue.add(root);

        int level = 0;
        while (!queue.isEmpty()) {
            level++;
            int levelSize = queue.size();
            while (levelSize-- > 0) {
                TreeNode curr = queue.poll();
                if (curr.left == null && curr.right == null) {
                    return level;
                }
                if (curr.left != null) queue.add(curr.left);
                if (curr.right != null) queue.add(curr.right);
            }
        }
        return -1; // won't reach here
    }
}
```