## ARTS2



### Algorithm

Leetcode -111   Minimum Depth of Binary Tree

本题的要求是给一个二叉树，返回这个二叉树的最小层级

三个思路：1递归 2.广度优先搜索 3.深度优先搜索

这道题是我对之前做过的题目的回顾，之前选择的解决思路是借助一个函数进行递归，可以通过很少的代码量把问题解决，代码格式也相对优雅。 近期在回顾学习广度优先搜索和深度优先搜索，所以想再通过广度优先搜索把这道题再从做一遍。

先上递归的代码

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
    int minDepth(TreeNode* root) {
        if(!root) return 0;
        
        return depthOfBranch(root) + 1;
    }
    
    int depthOfBranch(TreeNode* root){
        
        if(!root->left&&!root->right){
            return 0;
        }else if(root->left&&!root->right){
            return 1+depthOfBranch(root->left);
        }else if(root->right&&!root->left){
            return 1+depthOfBranch(root->right);
        }else{
            return min(1+depthOfBranch(root->left),1+depthOfBranch(root->right));
        }
        
    }
    
};
```

借助一个辅助递归函数，如果进入函数的节点没有子树，说明已到达叶子结点，那么累加计数也就停止，如果包含子节点，那么累加 1 并向下递归， 以这种方式分别求出两条子树层级中的最小值，那么就是这棵树的层级最小值。

再上广度优先搜索代码

```
class Solution {
public:
    int minDepth(TreeNode* root) {
        
        int min = 0;
        
        if (!root)
            return min;
        queue<TreeNode*> deque;
        deque.push(root);
        while(!deque.empty()){
            int level = deque.size();
            min = min + 1;
            for (int i = 0 ; i < level ; i++) {
                TreeNode *temp = deque.front();
                deque.pop();
                if (temp->left){                
                    deque.push(temp->left);
                }
                if (temp->right){
                    deque.push(temp->right);
                }
                if (!temp->left&&!temp->right){
                    return min;
                }
            }
        }
        return min;
    }
};
```

利用一个 queue，将每一层的node暂存进去， queue 的每一次 push（）都是为下一次遍历准备数据。每一次遍历都准备好了本层的所有节点，一旦遇到没有左右节点的叶子结点，就返回当前的层数，既最小的层级。

### Review

https://medium.com/flawless-app-stories/make-the-code-testable-in-swift-4f3b1710e16b

Make the code testable in Swift

这篇文章的主旨告诉我们如何将 swift 代码设计的可测试化。

文章开头作者基于一段示例代码，带出第一个问题：在一个比较封闭的网络请求基类中，由于无法访问 数据请求的内部属性 ，也就无法把把它变为完全本地的请求，因为负责网络请求的实例是在 网络请求执行方法中进行的初始化。

这个问题可以依靠依赖注入的方法去攻克

依赖注入有三种形式：

1.方法注入

2.属性注入

3.构造注入

作者最终选择的了构造注入，因为这种方式既不会产生额外的可选属性，也不会对基础方法造成影响。每一个类初始化时都要分配一个网络请求，每一个实例都要有一个初始化的请求属性

为了方便测试请求数据的处理结果，作者选择让请求类遵守一个请求协议，协议内部定义了数据解析的默认实现。

在我看来，作者的工作就是将网络请求类中的属性交由外部注入，基础方法放到协议中进行默认实现，这样在执行单元测试的时候就可将数据文件作为初始值注入到请求类中。



### TIp

利用Fishhook将系统的C函数hook到本地的实现，掌握了，Fishhook的hook原理，掌握了 IOS 应用的加载原理（通过dyld对MachO文件进行加载，对系统的框架进行加载），fishhook通过 _DATA 数据段进行地址修改，以达到hook的目的。



### Share

https://juejin.im/post/5eaaeca25188256d8f6921fd

谈一谈App的架构设计

作者在文章中指出，应用需要从三方面考虑架构：1）代码的可读性 2）代码的可拓展性 3）代码需要的加速框架

1.代码的可读性：团队要有统一的代码规范和设计原则

2.代码的可拓展性：可拓展性可以从以下三个方面思考

1）产品思维， PM后续会有哪些功能安排，产品形态会有哪些变化，为这些可能的改变预留接口。或者更直接的，看一下最出色的竟品有哪些功能，为这些功能留好接口。

2）设计规范， 工程师要建立自己的设计规范，这个规范包含如下要素：大方向上要能够抽象，能普适，有清晰的长远目标， 局部有设计图，有逻辑性，各种边界条件和异常情况要考虑周详。

3）技术选型， 包括 语言 编程方式 第三方库 布局方式，要选择社区活跃的语言，要选择近期维护频繁的第三方或者经典通用的框架。

3.代码需要的加速框架：也就是一些基础工具，如网络模块，音视频模块，基础数据模块，基础控件模块等。这些模块都是构成一个应用必不可少的功能模块。



### ARTS 第二篇，通过公司内部分享，深刻的理解了学习一项技能的最佳途径，就行 掌握- 实践 - 分享 这是最快最有效的学习路径。

