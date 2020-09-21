## ARTS-Week8



### Algorithm

##### \105. Construct Binary Tree from Preorder and Inorder Traversal

给出一个二叉树通过前序遍历形成的数组和中序遍历形成的数组，通过这两个数组，复原二叉树

这个题用python写代码会简化很多，因为python的list可以很方便的截取片段作为下一次递归的参数，而这道题的解题核心就是不断的根据两个两个数组的下标获取左右子树的list片段。上代码：

```c++
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> TreeNode:
        if not preorder:
            return None
        root = TreeNode(preorder[0])
        index = inorder.index(preorder[0])
        root.left = self.buildTree(preorder[1:index+1],inorder[0:index])
        root.right = self.buildTree(preorder[index+1:],inorder[index+1:])
        return root
```

首次进入函数，preorder的第一个元素肯定是根节点， 根据这个元素在中序数组找到分割点，这个分割点在中序数组的左右子树之间，可以认为此分割点的左边为中序的左子树，右边为中序的右子树，再回头看前序遍历，由于前序的顺序是 中-左-右，中序是 左-中-右，每一段所在的子树是固定的 也就是说 前序的 中-左 所在的片段 == 中序的 左中 所在的片段，基于此 我们找到了 中序的 中 也就能够定位到前序的 左 也就是preorder[1:index+1], 前序的右也就是 preorder[index+1：]， 前序和中序左右都取出来后，就可以进入到下一个递归，直到list为空，说明递归终止。

### Review

https://time.geekbang.org/column/article/2711?utm_campaign=guanwang&utm_source=baidu-ad&utm_medium=ppzq-pc&utm_content=title&utm_term=baidu-ad-ppzq-title

##### 函数式编程

函数式编程是一个非常古老的概念，它只关心定义输入数据和输出数据相关的关系，数学表达式里面其实是一种映射，输入的数据和输出的数据关系是什么样的，是用函数来定义的。

函数式编程有一下特征：

1.stateless： 不维护任何状态， 我的理解就是不和函数空间之外的变量发生任何关系

2.immutable： 输入一旦确定，输出必然确定

##### 优势：

1.没有状态就没有伤害 

2.并行执行无伤害

3.Copy-Paste 重构代码无伤害

4.函数执行没有顺序上的问题



##### 劣势：

1.数据复制比较严重。可能会有人顾虑性能问题，但是没有状态就不需要加锁，不加锁就可以玩命的并发，反而会提高性能。



##### 函数式三件套：

Map:按规则依次变化

Reduce：按规则依次组装

Filter：按规则依次筛选



##### 函数式的pipeline模式：

pipeline 借鉴于 Unix Shell 的管道操作--把若干个命令串起来执行，前面命令的输出成为后面命令的输入，如此完成一个流式计算。

以往我们写一个连续操作的程序，可能会在一个函数中进行顺序操作，比如：

一个process()有三个步骤：

1.找出偶数；

2.乘以3；

3.转成字符串返回

传统的程序实现如下：

```python
def process(num):    # filter out non-evens    
    if num % 2 != 0:        
        return    num = num * 3    
    num = 'The Number: %s' % num    
    return num 
nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 
for num in nums:    print process(num)   
# 输出：# None
# The Number: 6
# None
# The Number: 12
# None
# The Number: 18
# None
# The Number: 24
# None
# The Number: 30
```

我们拆解一下，将三个“子需求”写成函数：

```python
def even_filter(nums): 
    for num in nums: 
        if num % 2 == 0: 
            yield num
def multiply_by_three(nums):
    for num in nums:
        yield num * 3
def convert_to_string(nums): 
    for num in nums: 
        yield 'The Number: %s' % num
```

然后，我们将这三个函数串起来：

```python

nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
pipeline = convert_to_string(multiply_by_three(even_filter(nums)))
for num in pipeline:
    print num
# 输出：
# The Number: 6
# The Number: 12
# The Number: 18
# The Number: 24
# The Number: 30
```

这样就通过链式命令将一系列函数通过pipeline 的模式变现出来。

再改装一下，通过三件套进行进一步的演进。

```python

def even_filter(nums):
    return filter(lambda x: x%2==0, nums)
 
def multiply_by_three(nums):
    return map(lambda x: x*3, nums)
 
def convert_to_string(nums):
    return map(lambda x: 'The Number: %s' % x,  nums)
 
nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
pipeline = convert_to_string(
               multiply_by_three(
                   even_filter(nums)
               )
            )
for num in pipeline:
    print num
```



### TIp

这两周学习了python基础，为之后的数据处理做准备。python的切片和类型转换相较于C++有着较大的便捷性，当然性能上肯定赶不上C++/C，同样的程序逻辑，python的耗时大概是C++的四倍左右。



#### Share

https://medium.com/better-programming/why-flutter-isnt-the-next-big-thing-e268488521f4

#### Why Flutter Isn't the Next Big Thing

最近团队的小伙伴在做flutter demo项目，特意找了一些关于flutter的文章来看，在medium中找到一篇对flutter反向视角的文章，读完后感觉挺有参考意义的。

作者的题目很吸引人，给人感觉是要极度唱衰Flutter，事实上文章内容也确实也从几个视角来说明Flutter无法成为主流：

1. It‘s not RN & It's not native：flutter 既不是类前端的RN 也不是 移动端本地开发，他是有自己的独立环境和生态的技术，这会导致不论是前端和原生开发都会有一定的学习成本，大家都需要一个过程去熟悉语法，开发人群的平滑过度会存在问题。
2. You Still Have to Write Most of Your App Twice： 无论如何，你都需要在安卓和IOS 两个平台分别调试，测试。
3. Support Is Negligile： Flutter 的技术生态还不能称为健全，各个主流技术论坛中Flutter的主题讨论都很少，相应的开发人员解决问题的资源也相对有像
4. Flutter’s LifeTime ： 作者表示，依谷歌的尿性，一旦他们认为 flutter的投资回报不佳，他们可能会弃坑。 谷歌在发布Flutter的同时也发布了 JetPack，很明显，谷歌并没有把宝全压在flutter上。
5. 最后这名国外作者认为，Flutter目前适用于小型验证项目，对于大型团队和应用还是要慎重

基于作者观点，说下我的看法，flutter作为一项跨平台的技术实现，已经在一些小型互联网公司得到应用，主要目的是为了较少人工成本和试水新技术，快速迭代，小团队开发。 但是针对一些对交互效果和用户体验有高标准要求大平台来说，目前阶段flutter还是比较鸡肋。 对于移动端开发者来说，能够通过一些小demo熟悉一下语言特性，在不同的平台上进行过简单调试就可以了，没有必要太过深入。我们还是应该把更多精力放在内功的修炼上，平台的底层原理，数据结构和算法的精进，这些计算机通用技术对我们的提升在长期看来收益会更客观。