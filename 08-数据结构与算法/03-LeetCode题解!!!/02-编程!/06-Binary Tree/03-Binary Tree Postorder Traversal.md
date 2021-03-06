# Binary Tree Postorder Traversal

## Question

- leetcode: Binary Tree Postorder Traversal | LeetCode OJ
- lintcode: (68) Binary Tree Postorder Traversal

```
Problem Statement

Given a binary tree, return the postorder traversal of its nodes' values.

Example
Given binary tree {1,#,2,3},
   1
    \
     2
    /
   3
return [3,2,1].

Challenge
Can you do it without recursion?
```

## 题解1 - 递归

首先使用递归便于理解。

## C++ - Traversal
    
    /**
     * Definition of TreeNode:
     * class TreeNode {
     * public:
     *     int val;
     *     TreeNode *left, *right;
     *     TreeNode(int val) {
     *         this->val = val;
     *         this->left = this->right = NULL;
     *     }
     * }
     */
    class Solution {
        /**
         * @param root: The root of binary tree.
         * @return: Postorder in vector which contains node values.
         */
    public:
        vector<int> postorderTraversal(TreeNode *root) {
            vector<int> result;
    
            traverse(root, result);
    
            return result;
        }
    
    private:
        void traverse(TreeNode *root, vector<int> &ret) {
            if (root == NULL) {
                return;
            }
    
            traverse(root->left, ret);
            traverse(root->right, ret);
            ret.push_back(root->val);
        }
    };

## Java - Divide and Conquer

    /**
     * Definition for a binary tree node.
     * public class TreeNode {
     *     int val;
     *     TreeNode left;
     *     TreeNode right;
     *     TreeNode(int x) { val = x; }
     * }
     */
    public class Solution {
        public List<Integer> postorderTraversal(TreeNode root) {
            List<Integer> result = new ArrayList<Integer>();
            if (root != null) {
                List<Integer> left = postorderTraversal(root.left);
                result.addAll(left);
                List<Integer> right = postorderTraversal(root.right);
                result.addAll(right);
                result.add(root.val);
            }
    
            return result;
        }
    }

## 源码分析

    递归版的太简单了，没啥好说的，注意入栈顺序。

## 复杂度分析

    时间复杂度近似为 O(n).

## 题解2 - 迭代

    使用递归写后序遍历那是相当的简单，我们来个不使用递归的迭代版。
    整体思路仍然为「左右根」，那么怎么才能知道什么时候该访问根节点呢？
    问题即转化为如何保证左右子节点一定先被访问到？
    由于入栈之后左右节点已无法区分，因此需要区分左右子节点是否被访问过(加入到最终返回结果中)。
    除了有左右节点的情况，根节点也可能没有任何子节点，此时也可直接将其值加入到最终返回结果中。

## C++
    
    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Solution {
    public:
        vector<int> postorderTraversal(TreeNode* root) {
            vector<int> result;
            if (root == NULL) return result;
    
            TreeNode *prev = NULL;
            stack<TreeNode *> s;
            s.push(root);
            while (!s.empty()) {
                TreeNode *curr = s.top();
                bool noChild = false;
                if (curr->left == NULL && curr->right == NULL) {
                    noChild = true;
                }
                bool childVisited = false;
                if (prev != NULL && (curr->left == prev || curr->right == prev)) {
                    childVisited = true;
                }
    
                // traverse
                if (noChild || childVisited) {
                    result.push_back(curr->val);
                    s.pop();
                    prev = curr;
                } else {
                    if (curr->right != NULL) s.push(curr->right);
                    if (curr->left != NULL) s.push(curr->left);
                }
            }
    
            return result;
        }
    };

## Java

    /**
     * Definition for a binary tree node.
     * public class TreeNode {
     *     int val;
     *     TreeNode left;
     *     TreeNode right;
     *     TreeNode(int x) { val = x; }
     * }
     */
    public class Solution {
        public List<Integer> postorderTraversal(TreeNode root) {
            List<Integer> result = new ArrayList<Integer>();
            if (root == null) return result;
    
            Deque<TreeNode> stack = new ArrayDeque<TreeNode>();
            stack.push(root);
            TreeNode prev = null;
            while (!stack.isEmpty()) {
                TreeNode curr = stack.peek();
                boolean noChild = (curr.left == null && curr.right == null);
                boolean childVisited = false;
                if (prev != null && (curr.left == prev || curr.right == prev)) {
                    childVisited = true;
                }
    
                if (noChild || childVisited) {
                    result.add(curr.val);
                    stack.pop();
                    prev = curr;
                } else {
                    if (curr.right != null) stack.push(curr.right);
                    if (curr.left != null) stack.push(curr.left);
                }
            }
    
            return result;
        }
    }

## 源码分析

    遍历顺序为『左右根』，判断根节点是否应该从栈中剔除有两种条件，一为无子节点，二为子节点已遍历过。
    判断子节点是否遍历过需要排除prev == null 的情况，因为 prev 初始化为 null.
    将递归写成迭代的难点在于如何在迭代中体现递归本质及边界条件的确立，可使用简单示例和纸上画出栈调用图辅助分析。

## 复杂度分析

    最坏情况下栈内存储所有节点，空间复杂度近似为 O(n), 每个节点遍历两次或以上，时间复杂度近似为 O(n).

## 题解3 - 反转先序遍历

    要想得到『左右根』的后序遍历结果，我们发现只需将『根右左』的结果转置即可，
    而先序遍历通常为『根左右』，故改变『左右』的顺序即可，所以如此一来后序遍历的非递归实现起来就非常简单了。

## C++

    /**
     * Definition of TreeNode:
     * class TreeNode {
     * public:
     *     int val;
     *     TreeNode *left, *right;
     *     TreeNode(int val) {
     *         this->val = val;
     *         this->left = this->right = NULL;
     *     }
     * }
     */
    class Solution {
        /**
         * @param root: The root of binary tree.
         * @return: Postorder in vector which contains node values.
         */
    public:
        vector<int> postorderTraversal(TreeNode *root) {
            vector<int> result;
            if (root == NULL) return result;
    
            stack<TreeNode*> s;
            s.push(root);
            while (!s.empty()) {
                TreeNode *node = s.top();
                s.pop();
                result.push_back(node->val);
                // root, right, left => left, right, root
                if (node->left != NULL) s.push(node->left);
                if (node->right != NULL) s.push(node->right);
            }
            // reverse
            std::reverse(result.begin(), result.end());
            return result;
        }
    };

## Java

    /**
     * Definition of TreeNode:
     * public class TreeNode {
     *     public int val;
     *     public TreeNode left, right;
     *     public TreeNode(int val) {
     *         this.val = val;
     *         this.left = this.right = null;
     *     }
     * }
     */
    public class Solution {
        /**
         * @param root: The root of binary tree.
         * @return: Postorder in ArrayList which contains node values.
         */
        public ArrayList<Integer> postorderTraversal(TreeNode root) {
            ArrayList<Integer> result = new ArrayList<Integer>();
            if (root == null) return result;
    
            Deque<TreeNode> stack = new ArrayDeque<TreeNode>();
            stack.push(root);
            while (!stack.isEmpty()) {
                TreeNode node = stack.pop();
                result.add(node.val);
                if (node.left != null) stack.push(node.left);
                if (node.right != null) stack.push(node.right);
            }
            Collections.reverse(result);
    
            return result;
        }
    }

## 源码分析

    注意入栈的顺序和最后转置即可。

## 复杂度分析

    同先序遍历。