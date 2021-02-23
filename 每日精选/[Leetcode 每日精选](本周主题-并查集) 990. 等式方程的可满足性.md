> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/satisfiability-of-equality-equations/)

> 今天的每日一题是有关并查集的内容, 正好并查集思路简单, 出现频率不低, 变种也有很多, 还是挺有意思的, 这周就来做这个系列的吧. 大家在我的公众号"每日精选算法题"中的聊天框中回复 **并查集** 就能看到该系列当前已经更新的文章了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

给定一个由表示变量之间关系的字符串方程组成的数组，每个字符串方程 equations[i] 的长度为 4，并采用两种不同的形式之一："a==b" 或  "a!=b"。在这里，a 和 b 是小写字母（不一定不同），表示单字母变量名。

只有当可以将整数分配给变量名，以便满足所有给定的方程时才返回  true，否则返回 false。

- 1 <= equations.length <= 500
- equations[i].length == 4
- equations[i][0] 和  equations[i][3]  是小写字母
- equations[i][1] 要么是  '='，要么是  '!'
- equations[i][2]  是  '='

## 题目样例

### 示例 1

#### 输入

["a==b","b!=a"]

#### 输出

false

#### 解释

如果我们指定，a = 1 且 b = 1，那么可以满足第一个方程，但无法满足第二个方程。没有办法分配变量同时满足这两个方程。

### 示例 2

#### 输入

["b==a","a==b"]

#### 输出

true

#### 解释

我们可以指定 a = 1 且 b = 1 以满足满足这两个方程。

## 题目思考

1. 题目乍一看挺复杂的, 但能否换个角度思考: 什么情况下一定不满足等式?
2. 如何利用两个变量相等这一条件?

## 解决方案

### 思路

- **分析**
  - 题目只有两种方程, 等于和不等
  - 我们如果能够汇总出所有变量的相等性, 然后针对不等的方程, 看看对应的变量是否已经存在相等关系, 就知道能否满足了
- **推导**
  - 先遍历一遍方程, 此时只关注**相等**的情况. 我们可以把相等的变量归为一类, 这样可以产生若干个集合, 然后每个集合中有若干个元素, 它们都是相等的
  - 然后再遍历一遍方程, 此时只关注**不等**的情况. 如果不等式两边的变量已经出现在相同集合中了, 那么就会产生矛盾, 一定无法同时满足所有方程. 反之我们总能够指定不同的数到变量, 使得满足所有方程.
- **实现**
  - 根据推导过程, 这个就是经典的[并查集](https://zh.wikipedia.org/wiki/%E5%B9%B6%E6%9F%A5%E9%9B%86)所做的事情, 就是将元素进行分类, 相同的放在同一个集合中. 当然靠暴力模拟也可以做到归类, 但是那样会涉及到耗时巨大的集合求并集和循环判断等, 效率过低; 而并查集可以做到每次操作只需要 O(logN)甚至更短的时间.
  - 并查集的思路很简单, 具体步骤如下:
    1. 首先我们需要定义一个字典 pre, pre[x]表示 x 的祖先, 如果两个元素具有相同祖先, 就表示它们在同一个集合中. 可以把祖先 pre[x] 想象成一个树的根节点, 那么 x 就是树中的一个节点(可能是根节点本身)
    2. 然后定义一个 find 方法, 查找当前元素的祖先, 如果祖先不存在的话就把自身当做祖先. 这里用到了路径压缩的优化, 就是说当发现自己的祖先不是自身的时候, 就尝试把自己的祖先设置为自己的当前祖先的祖先, 从而降低树的高度, 加快之后的查找过程.
    3. 最后定义一个 union 方法, 用于合并两个元素. 这里的思路也很简单, 就是找到各自的祖先, 然后将其中一个的祖先的祖先设置为另外一个祖先即可, 等于就把两个树合并在了一起.
  - 下面的代码中对每个步骤都有注释, 大家可以结合代码和上面的步骤来理解并查集的思路.
  - 注意在求每个节点的祖先时仍然需要调用 find(), 利用路径压缩得到最终最根部的祖先, 而不是直接拿它的 pre 值. 这是因为在 union 结束后 pre 字典里存的不一定是每个节点最根部的祖先: 我们 union 的时候只是把一个节点的祖先指向另一个节点的祖先, 而对于节点 x 本身而言, 其祖先 pre[x] 仍为原来的祖先, 仍需要通过 find 方法路径压缩.
- **总结**
  - 涉及到并查集的问题基本都可以复用这个代码模板, 这个也是整个并查集系列的核心. 不过有时候需要针对题目本身做一些改动, 后面几天我会更新一些使用并查集变种的问题, 希望大家能够从中有所收获, 遇到类似的问题可以举一反三.

### 复杂度

- 时间复杂度 O(N+MlogM): 假设有 N 个方程以及 M 个变量, 需要遍历方程 2 遍, 然后共需要合并 M 个变量, 每次合并操作需要 logM 的时间, 总共就是 O(N+MlogM)
- 空间复杂度 O(M): pre 字典中存 M 个元素

### 代码

#### Python 3

```python
class Solution:
    def equationsPossible(self, equations: List[str]) -> bool:
        # 经典并查集模板
        # 祖先字典
        pre = {}

        def find(x):
            # 找x的祖先
            if x not in pre:
                # 祖先设为本身
                pre[x] = x
            elif pre[x] != x:
                # 路径压缩, 更新当前祖先到更上面的祖先
                pre[x] = find(pre[x])
            return pre[x]

        def union(x, y):
            # 合并x和y到同一集合, 即把x的祖先的祖先指向y的祖先
            pre[find(x)] = find(y)

        for e in equations:
            x, y = e[0], e[3]
            if e[1] == '=':
                if x != y:
                    # 只需要合并变量不等的情况
                    union(x, y)
            else:
                if x == y:
                    # 当前是不等式, 但是两边是同一个变量, 必然不满足条件, 直接返回false
                    return False
        for e in equations:
            x, y = e[0], e[3]
            if e[1] == '!' and find(x) == find(y):
                # 当前是不等式, 但是发现x和y在同一集合中, 即之前判断过x应该等于y, 有矛盾, 直接返回false
                return False
        return True
```

#### C++

```cpp
class Solution {
private:
    unordered_map<char, char> pre;
    char find(char x) {
        if (pre.find(x) == pre.end()) {
            pre[x] = x;
        } else if (pre[x] != x) {
            pre[x] = find(pre[x]);
        }
        return pre[x];
    }

    void Union(char x, char y) {
        pre[find(x)] = find(y);
    }
public:
    bool equationsPossible(vector<string>& equations) {
        for (auto& e : equations) {
            char x = e[0];
            char y = e[3];
            if (e[1] == '=') {
                if (x != y) {
                    Union(x, y);
                }
            } else {
                if (x == y) {
                    return false;
                }
            }
        }

        for (auto& e : equations) {
            char x = e[0];
            char y = e[3];
            if (e[1] == '!' && find(x) == find(y)) {
                return false;
            }
        }

        return true;
    }
};
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的简书](https://www.jianshu.com/u/3a17f1fdfd67)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
