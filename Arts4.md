## ARTS4



### Algorithm

Leetcode -39. Combination Sum

本题是给定一个正数数组，再给定一个 target值，求在这个数组中能够相加得target的所有组合。

此题的解题思路：一看这个问题肯定要用到递归，问题在于是用原函数进行递归，还是再造函数进行递归。 这套题我采用了再造递归函数，在递归函数中进行情况判断，这里在递归函数中新引入了三个新参数， 一个是作为输出结果的二维整数数组，一个累积元素的传递数组，另一个是后续判断的起始下标。target每次递归减去当前下标的值，如果target为0了，说明正好减没，此时可以将传递数组作为一个解放到结果数组中，如果为负，说明这个数值路径是不通，需要剪枝舍弃。 如果为正，说明还有迭代的空间，继续进行后续递归。

```markdown
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        
        vector<vector<int>> result;
        vector<int> out;
        combinationSumRecu(candidates,result,out, 0, target);
        return result;
    }
    
    void combinationSumRecu(vector<int>& candidates, vector<vector<int>>& result, vector<int> out,int start,int target){
        if(target < 0)
            return;
        if(target == 0){
            result.push_back(out);
            return;
        }
        
        for (int i = start ; i < candidates.size(); i++){
            out.push_back(candidates[i]);
            combinationSumRecu(candidates,result,out,i, target - candidates[i]);
            out.pop_back();
        }
    }
};
```

借助一个辅助函数，将根左右括号的剩余数量，排列的字符串，结果数组传入，依次递归。



### Review

https://time.geekbang.org/column/article/995?utm_source=pinpaizhuanqu&utm_medium=geektime&utm_campaign=guanwang&utm_term=guanwang&utm_content=0511

个人时间管理主题，收获有两点：

对产品的紧急需求，可以不说不，但要有条件的说是；

开会不是讨论问题，开会是讨论方案。 

### TIp

学习了 IOS pod库的打包方式，将项目中的单一功能模块进行了抽离，并封装成库。 由于是 swift 和OC 混编，导致出现了一些兼容性问题，后续在同事 的帮助下得以解决，将大部分OC代码用swift 代替。

体会，并不是所有的功能模块都适合组件化，以我此次封装的库为例，完全是为了满足 B项目的一个小业务点，如果我封装成pod，就无法依赖 B和A 部分基础组建，很多轮子要重新造。像这种对轮子有依赖，且功能唯一性不强的模块其实不适宜封装成库，通过pod 进行引入。



### Share

金字塔原理  1～3 章

1.文章的序言很重要，能否引起读者的兴趣关键就在于序言的内容，序言最好能够通过讲故事的方式把文章要表达的主旨展现给读者。

2.序言一般可以按照 ： 背景 -> 冲突-> 疑问 ->解决方案 的结构进行组织

3.文章内容是将主题至上而下进行的拆解，各内容的横向关系可以是演绎，可以归纳，一般来说归纳更好，读者接受度更高。





### ARTS 第四篇，medium的技术文章收获不大，现阶段不适合我，故每周转至陈浩的课程收获整理。

