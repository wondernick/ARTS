## ARTS1



### Algorithm

Leetcode -22. Generate Parentheses

本题的要求是给定一个数值n，返回所有正确表达n个左右括号的情况。

此题的解题思路：任何排序情况下，如果出现右括号大于左括号的情况就需要进行剪枝，如果左右括号出现的次数都已经等于n的情况，说明排列已完成，可以将此排列放入到结果数组中。

```markdown
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        
        vector<string> result;
        
        generate(n,n,"",result);
        return result;
        
    }
    
    void generate(int left, int right, string out,vector<string> &result){
        
        if (left > right)
            return;
        if (left == 0 && right == 0)
            result.push_back(out);
        
        if (left > 0)
            generate(left - 1,right,out + "(", result);
        if (right > 0)
            generate(left,right - 1,out + ")",result);
    }
    
};
```

借助一个辅助函数，将根左右括号的剩余数量，排列的字符串，结果数组传入，依次递归。



### Review

https://medium.com/flawless-app-stories/from-hobbyist-to-professional-ios-developer-liskov-substitution-principle-f98d6e1e6b19

这篇文章向我们推荐了一种 软件设计的原则： Liskov substitution principle， 简单来说就是子类不能修改基类的特征方法，导致 子类丧失了基础特性。这样会对处理子类的程序员造成误导。 如何规避这种情况呢， swift 的 协议可以很好的解决，把 基类的特征方法，放置于协议中，基类准守这样的协议，



### TIp

安装了ffmeg，利用ffmpeg对mac进行录屏，看了下 ffmpeg的文档，功能强大，从04年到现今，经久不衰，各种被封装成音视频产品。 感慨一下 法布里斯的牛掰和雷神的付出。



### Share

https://time.geekbang.org/column/article/285?utm_source=pinpaizhuanqu&utm_medium=geektime&utm_campaign=guanwang&utm_term=guanwang&utm_content=0511

###### 从Equalfax信息泄漏看数据安全

学到了几点数据安全方面的知识点：

1.应该引导用户设置复杂的密码

2.公司的内部网络绝不可暴露到外部

3.要对系统进行及时的升级

4.安全日志不要外泄，安全日志中携带的信息容易成为黑客攻击的发起点

5.用户的金融信息要即时销毁，不可保存于硬盘中

6.要对用户密码进行散列，比较推荐的是用 SHA-2 256 ，而不要用MD5（可被认为碰撞）

7.把敏感信息放到安全级别较高的区域，这部分数据做到只如不出，只能在安全区域内进行操作

8.被加密的数据和加密的密钥交给不同的人管理，形成互相牵制，互相审计

9.一旦安全信息泄漏，需要做好通告，监控外部访问流量，超过访问流量后要进行限流







### ARTS 第三篇，medium的技术文章深度普遍不高，但是行文很好，收获有限，慢慢积累吧。

