> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/friend-circles/)

> 今天继续来做并查集的问题, 这道题仍然比较基础, 而且也是个比较接近现实的问题了. 大家在我的公众号"每日精选算法题"中的聊天框中回复 **并查集** 就能看到该系列当前已经更新的文章了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

班上有  N  名学生。其中有些人是朋友，有些则不是。他们的友谊具有是传递性。如果已知 A 是 B  的朋友，B 是 C  的朋友，那么我们可以认为 A 也是 C  的朋友。所谓的朋友圈，是指所有朋友的集合。

给定一个  N \* N  的矩阵  M，表示班级中学生之间的朋友关系。如果 M[i][j] = 1，表示已知第 i 个和 j 个学生互为朋友关系，否则为不知道。你必须输出所有学生中的已知的朋友圈总数。

- N 在[1,200]的范围内。
- 对于所有学生，有 M[i][i] = 1。
- 如果有 M[i][j] = 1，则有 M[j][i] = 1。

## 题目样例

### 示例 1

#### 输入

[[1,1,0],
[1,1,0],
[0,0,1]]

#### 输出

2

#### 解释

- 已知学生 0 和学生 1 互为朋友，他们在一个朋友圈。
- 第 2 个学生自己在一个朋友圈。所以返回 2。

### 示例 2

#### 输入

[[1,1,0],
[1,1,1],
[0,1,1]]

#### 输出

1

#### 解释

已知学生 0 和学生 1 互为朋友，学生 1 和学生 2 互为朋友，所以学生 0 和学生 2 也是朋友，所以他们三个在一个朋友圈，返回 1。

## 题目思考

1. 朋友圈的个数可以等价于什么呢?

## 解决方案

### 思路

- **分析**
  - 要求朋友圈个数, 我们只需要找到是朋友的学生, 把他们归类在一起即可, 最终朋友圈就是集合的个数
- **推导**
  - 由于朋友关系是相互的, 具有对称性, 所以我们只需要遍历单方朋友关系即可, 即只需要遍历 i=>j(i<=j)的关系
  - 然后对于朋友关系, 将两者 union 起来即可
  - 最后再遍历所有人找他们的祖先 (注意此处仍然需要调用 find 而不是直接拿 pre 字典中存的结果, 具体原因参见上篇文章`[Leetcode 每日精选](本周主题-并查集) 990. 等式方程的可满足性`), 把祖先放到集合里, 最后集合长度即为所求.
- **实现**
  - 下面的代码中对每个步骤都有注释, 方便大家理解

### 复杂度

- 时间复杂度 O(N^2logN): 需要两重循环, 然后每次带有路径压缩的并查集的操作复杂度为 O(logN)
- 空间复杂度 O(N): pre 字典中存 N 个元素

### 代码

#### Python 3

```python
class Solution:
    def findCircleNum(self, M: List[List[int]]) -> int:
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

        n = len(M)
        for i in range(n):
            for j in range(i, n):
                # 由于对称性, M[j][i]一定也是1, 所以此处只需要合并i和j即可, j不需要从头开始
                if M[i][j] == 1:
                    union(i, j)
        cntset = set()
        for i in range(n):
            # 注意这里使用find得到每个节点的最终祖先
            cntset.add(find(i))
        return len(cntset)
```

#### C++

```cpp
class Solution {
public:
    int findCircleNum(vector<vector<int>>& M) {
        unordered_map<int, int> pre;

        function<int(int)> find = [&](int x) {
            if (pre.find(x) == pre.end()) {
                pre[x] = x;
            } else if (pre[x] != x) {
                pre[x] = find(pre[x]);
            }
            return pre[x];
        };

        auto Union = [&](int x, int y) {
            pre[find(x)] = find(y);
        };

        for (int i = 0; i < M.size(); ++i) {
            for (int j = i; j < M.size(); ++j) {
                if (M[i][j]) {
                    Union(i, j);
                }
            }
        }

        unordered_set<int> cntset;
        for (int i = 0; i < M.size(); ++i) {
            cntset.insert(find(i));
        }

        return cntset.size();
    }
};
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
