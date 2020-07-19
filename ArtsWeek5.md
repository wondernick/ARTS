## ARTS-Week5



### Algorithm

Leetcode -3. Longest Substring Without Repeating Characters

本题是给定一个字符串，返回最长的无重复字符子串的长度。

此题的解题思路：拿到这个题首先想到的就是滑动窗体，快慢下标，如果在两个下标的区域内没有重复的字符，那么快下标就前进，如果出现了重复的字符，那么慢下标就移动到重复下标的下一位置。 随着下标的移动，计算快慢下标的间距，每次都更新最长的间距。 如此循环下去，直到快下标到达字符串尾部为止，那么此时的最长间距即位最长无重复字符子串的长度。时间复杂度O（n）。

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        if (s.size() <= 1)
            return s.size();

        int left = 0;
        int res = 1;
        
        for (int i = 1;i < s.size(); i++ ){
            int count = i - left;
            string sub = s.substr(left,count);
            if (sub.find(s[i]) != sub.npos){
                left = left + sub.find(s[i]) + 1;
            }else {
                res = max(res, count + 1) ;
            }
        }
        
        return res;
    }
};
```



在网上借鉴了一种使用 unordered_map 的算法，此算法是借用一个 unordered_map存储位置信息，如果在map中能够找到相应字符的key 说明之前就出现过，取出value ，判断此位置是否在慢下标之后，如果是，说明此区域已出现重复字符，需要移动慢下标到重复位置之后，如果否，说明此重复字符不在有效区域内，不予理睬。每次循环都更新最大的快慢下标间距。此方法可以省一些内存，但是处理时间没有上一个算法快。



```c++
    int lengthOfLongestSubstring(string s) {
        if (s.size() <= 1)
                return s.size();

        int left = 0;
        int res = 1;
        unordered_map<int,int> check;

        for (int i = 0;i < s.size(); i++ ){

            if (check.count(s[i])&&check[s[i]]>=left){
                left = check[s[i]] + 1;
            }

            check[s[i]] = i;   
            res = max(res, i - left + 1) ;

        }

        return res;
      }

```
### Review

https://time.geekbang.org/column/article/297?utm_source=pinpaizhuanqu&utm_medium=geektime&utm_campaign=guanwang&utm_term=guanwang&utm_content=0511

如何做好一名技术leader：

成为技术leader 的征兆：

1.帮人解决问题

2.被人依赖

这两点可以作为擢拔 主管的 基本依据（3～5人）

需要的基本素质：

1.赢得他人的信任 （本团队&跨团队）

2.开发的心态 + 倾向性的价值观 （不随风倒会做人）

3.Lead by Example （show code）

4.保持热情和冲劲 （往往热爱生活，兴趣广泛的人会更有激情和冲劲）

5.能够抓住重点，看透事物的本质 （头脑清晰，视野开阔）

具备以上五点可以作为一个单一方向的部门负责人（10～15人）

6.描绘令人激动的方向，提供令人向往的环境（袁绍， 刘备这样的人）

7.甘当铺路石，为他们创造机会（情怀+格局）

6和7属于高阶软素质，需要底蕴和情怀。兼具以上所有条件，可以担当跨部门事业部的负责人，甚至CTO（30～50人）

联系到58沈剑对于技术leader的两点思考：1.能够复制自己 2.能够持续思考10倍提升团队效能的事情。这两点实际指标是当上leader后的考评点。

### TIp

IOS 卡顿监测有两种方式：

1. 监听RunLoop 状态回调 （KCFRunLoopBeforeSources 和 KCFRunLoopAfterWaiting）
2. 子线程ping主线程、

IOS 自旋锁和互斥锁：

1. 互斥锁：当访问被锁住的资源时， 线程会进入休眠等待状态，等待解锁 
2. 自旋锁：当访问被锁住的资源时，线程会以死循环的方式等待解锁，一旦资源被解锁，等待的线程会立即执行（如果使用不当会很浪费CPU）



### Share

https://juejin.im/post/5ef6930fe51d4534a361530a#heading-6

通过对runloop 的监控 打造一款app性能监控系统。





### ARTS 第四篇，medium的技术文章收获不大，现阶段不适合我，故每周转至陈浩的课程收获整理。

