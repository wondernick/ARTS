## ARTS1



### Algorithm

Leetcode -102  Binary Tree Level Order Traversal

本题的要求是给一个二叉树，返回一个按层分布的二维数组，没一层的node‘s value 都在数组相应的层中

两个思路： 1.广度优先搜索 2.深度优先搜索

先上深度优先搜索代码

```markdown
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
    vector<vector<int>> levelOrder(TreeNode* root) {
        
        vector<vector<int>> res;
        
        subcal(root,res,0);
        
        return res;
        
    }
    
    void subcal(TreeNode* root, vector<vector<int>>& trans, int row){
    
        if(!root) return;
        if(trans.size() == row) trans.push_back({});
        trans[row].push_back(root->val);
        
        if(root->left){
            subcal(root->left,trans,row+1);
        } 
        if(root->right){
            subcal(root->right,trans,row+1);
        }
    }
};
```

借助一个辅助函数，将根节点，数组，及level 传入，依次递归。



再上广度优先搜索代码

```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        
        vector<vector<int>> result;
        
        if (!root){
            return result;
        }
        
        queue<TreeNode*> ql;
        
        ql.push(root);
            
        while (ql.size() != 0) {
            int level = ql.size();
            vector<int> trans;
            for (int i = 0; i < level; i++){
                TreeNode *temp = ql.front();
                ql.pop();
                trans.push_back(temp->val);
                if(temp->left)
                    ql.push(temp->left);
                if(temp->right)
                    ql.push(temp->right);
            }
            result.push_back(trans);
        }    
        return result;
    }
};
```

利用一个 queue，将每一层的node暂存进去， queue 的每一次 push（）都是为下一次遍历准备数据。每一次遍历都准备好了本层的所有节点，那么我们把这一层的所有子节点暂存到queue中，就可以为下一层遍历做好准备。

### Review

https://medium.com/flawless-app-stories/the-only-viable-ios-architecture-c42f7b4c845d

这篇文章的主旨是为了告诉我们，MVC并不是一种设计模式，而是一种设计思想，我们以往对MVC的抱怨是我们没有理解好MVC的本质，导致我们把MVC作为一种模版做coding，引来了诸多耦合性和健壮性方面的问题。

如果我们把MVC作为一种指导思想，那么我们就会知道要不停的解耦，不停的拆分，而不是简单的把代码分布在三个模块中。



### TIp

掌握了利用runtime进行函数hook，并把对应系统方法的实现替换为我们自己编写的代码，从而实现一些普遍性的操作。



### Share

无痕埋点对于诸多应用来说都是非常有用的，以我接触的产品埋点来说，多数埋点对于产品了解用户行为和用户习惯都没起到太大作用，那么我们埋这么多点的意义就是为了帮产品完成他们的KPI，一个中型app，埋点数可能会累积到500+以上，如果手动埋点，程序员的工作量和后期维护时间可想而知。

通过无痕埋点，将各个基础类在生命周期内的用户行为地毯式的搜集起来，在集中式的管理类中统一做处理，能够极大程度的节省程序员的工作量及后期的维护成本。



### ARTS 第一篇，内容略显粗糙，希望后面会越来越精良。

