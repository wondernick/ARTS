## ARTS-Week7



### Algorithm

##### leetcode 51. N-Queens 

求解N皇后排列，并进行输出。

首先想到的就是记忆化递归，通过列、撇、捺 三个 set 存储已被占用的序列，撇的元素为行与列之和，捺的元素为行与列之差， 如果在列 撇 捺 三个set 中均未被占用说明此位置可用，可以占位进行下一轮迭代。上代码



```c++
class Solution {
public:
    
    vector<vector<string>> solveNQueens(int n) {

        vector<vector<string>> res;

        for (int i = 0; i < n; i++) {
            set<int> row;
            set<int> pie;
            set<int> na;
            vector<int> col;
            row.insert(0);
            col.push_back(i);
            pie.insert(i + 0);
            na.insert(i - 0);
            dfs(row, col, pie, na,1,n,res);
        }

        return res;
    }
    void dfs(set<int> row, vector<int> col, set<int>pie, set<int>na, int check, int n, vector<vector<string>> &res) {

        if (check >= n) {
            vector<string> temp;

            for (int j = 0; j < n; j++) {
                string content = "";
                for (int t = 0; t < n; t++) {
                    if (col[j] == t) {
                        content.append("Q");
                    }
                    else {
                        content.append(".");
                    }
                }
                temp.push_back(content);
            }

        res.push_back(temp);
        }
        else {
            for (int k = 0; k < n; k++) {

                vector<int>::iterator iter = find(col.begin(), col.end(), k);
                int pie_c = pie.count(check + k);
                if ((iter == col.end()) && (pie.count(check + k) == 0) && (na.count(k - check) == 0)) {
                    string r('.', n);
                    row.insert(check);
                    col.push_back(k);
                    pie.insert(check + k);
                    na.insert(k - check);

                    dfs(row, col, pie, na, check + 1, n, res);

                    row.erase(check);
                    col.erase(col.begin()+check);
                    pie.erase(check + k);
                    na.erase(k - check);
                }
            }
        }
    }
};
```

##### leetcode NO52  N-Queens II 

 给出一个数组N，求解N皇后排列的最多可能。 这道题最好想的思路就是上面一题的遍历加记忆化，但是这种算法的时间复杂度和空间复杂度有些高，从覃超的课中学到一种通过位运算的方式来判定不同维度的可行性，很是优雅简洁， 算法思路： 首先肯定还是要有DFS，逐层遍历， 通过初始化的三个整数，作为三个维度的判断参数 列、撇、捺。 这个算法最骚的一步是

```
 int bit = (~(col|pie|na)&((1<<N) - 1)); 
```

对列、撇、捺的或再取反， 或是为了得出三个维度均未被占用的位，这个数的 为 0 位表示未被占用， 为1位表示已被占用，再取反，作用是把未被占用的位置一， 已被占用的位置零，((1<<N) - 1)) 表示将0~N位全部置为一，这样可以打掉超出N的位，也就是越界的位。

取出bit后，循环bit位，通过（bit&（-bit））取出 bit 为一的最低为，将最低为放到下一层递归中。最后不要忘了将 bit 的最后一位 一打掉，因为（bit&（-bit））只是取位，并未进行打掉，需要打掉最后一位后进入下一次循环。

```c++
class Solution {
public:
    int count = 0;
    int N;
    int totalNQueens(int n) {
        
        N = n;
        int col, pie ,na = 0;
        
        DFS(1,col,pie,na);
        return count;
    }
    
    void DFS(int row,int col,int pie, int na){
        if(row > N){
            count++;
            return;
        }
            
        int bit = (~(col|pie|na)&((1<<N) - 1));
        while(bit > 0){
            
            int check = bit&(-bit);
            DFS(row+1,col|check, (pie|check)<<1,(na|check)>>1);
            
            bit &= (bit - 1);
        }
    }
    
    
};
```
### Review

https://time.geekbang.org/column/article/1512?utm_campaign=guanwang&utm_source=baidu-ad&utm_medium=ppzq-pc&utm_content=title&utm_term=baidu-ad-ppzq-title

分布式系统的技术栈：

1.加缓存

2.附在均衡

3.异步调用

4.数据镜像

5.数据分区

不是后端开发，但是基于我对后端技术的了解，说说我对这几点的认识， 缓存方面应用最多可能就是 redis了， 负载均衡是 nginx, 异步调用是 mq 和 kafka， 数据镜像是 CDN。 比较粗浅，对很多细节点理解还不到位。

### TIp

学习了 使用commitition 规范 commit message，这个工具可以很好的统一化团队的commit 信息格式， 配合钉钉项目的任务拆分，可以很好的实现项目的颗粒化管理，同时根据commit message 生成的changelog 可以作为后续回顾和复盘的开发过程依据





### ARTS 第七篇，两周一更。

