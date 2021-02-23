> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/surrounded-regions/)

> 今天我们来做本周并查集系列的最后一题, 这道题跟图有关, 同样有多种思路. 大家在我的公众号"每日精选算法题"中的聊天框中回复 **并查集** 就能看到该系列当前已经更新的文章了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

给定一个二维的矩阵，包含 'X' 和 'O'（字母 O）。

找到所有被 'X' 围绕的区域，并将这些区域里所有的 'O' 用 'X' 填充。

## 题目样例

### 示例

#### 输入

```
X X X X
X O O X
X X O X
X O X X
```

#### 输出

运行你的函数后，矩阵变为：

```
X X X X
X X X X
X X X X
X O X X
```

#### 解释

被围绕的区间不会存在于边界上，换句话说，任何边界上的  'O'  都不会被填充为  'X'。 任何不在边界上，或不与边界上的  'O'  相连的  'O'  最终都会被填充为  'X'。如果两个元素在水平或垂直方向相邻，则称它们是“相连”的。

## 题目思考

1. 这个问题如何转化成更简单的问题?
2. 如何应用并查集的思想?
3. 除了并查集外, 还有别的思路吗?

## 解决方案

### 方案 1

#### 思路

- **分析**
  - 根据题意, 被 X 包围的区域, 就是不和边界的 O 相连的区域
  - 那么我们可以利用这一条件, 将所有边界的 O 以及连通的 O 都合并在同一集合, 这就能用到并查集的思路了
  - 然后再次遍历矩阵, 将不在该集合的 O 转换成 X 即可
- **实现**
  - 将传统的并查集改造成二元组即可, 存储行列下标.
  - 具体步骤如下:
    - 注意我们需要事先规定一个不存在于矩阵的点, 作为边界 O 的共同祖先. 使用不存在的点是为了保险起见, 因为如果是已经存在的点, 则可能会因为其本身是一个被包围的 O 而导致合并范围错误扩大
    - 然后合并的时候都和这个点进行合并即可保证它们在同一集合中
    - 最后循环判断的时候只需要对比当前 O 和这个自定义的点的祖先是否相等, 不相等的话说明不在同一集合, 变成 X 即可
  - 下面代码中对每一步都有详细注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(RClog(RC))`: 总共需要遍历`RC`次, 并查集中存`RC`数量级的元素, 每次合并和查询操作需要 `O(log(RC))` 的时间, 总时间复杂度就是 `O(RClog(RC))`
- 空间复杂度 `O(RC)`: pre 字典中存`RC`数量级的元素

#### 代码

```python
class Solution:
    def solve(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        # 方法1: 并查集
        # 先将边界的O归为一类, 可以指定一个不存在的点作为这些边界O字符的共同祖先
        # 然后遍历非边界部分, 尝试连通
        # 最后判断每个O字符是否和边界O字符处于同一集合即可(使用find比较其祖先是否相等)
        if not board:
            return
        rows, cols = len(board), len(board[0])
        # 核心并查集部分, 稍作改动存(r,c)二元组
        pre = {}

        def find(r, c):
            if (r, c) not in pre:
                pre[r, c] = (r, c)
            elif (r, c) != pre[r, c]:
                pr, pc = pre[r, c]
                pre[r, c] = find(pr, pc)
            return pre[r, c]

        def union(r1, c1, r2, c2):
            pr, pc = find(r1, c1)
            pre[pr, pc] = find(r2, c2)

        # 将边界O字符归为一类, 即和同一个不存在的点进行union
        root = (rows, cols)
        for r in range(rows):
            for c in (0, cols-1):
                if board[r][c] == 'O':
                    # *表示参数展开, 即把root元组展开后的参数依次传入方法中, 下同
                    union(r, c, *root)
        for c in range(cols):
            for r in (0, rows-1):
                if board[r][c] == 'O':
                    union(r, c, *root)

        # 遍历其他字符, 尝试连通
        for r in range(1, rows-1):
            for c in range(1, cols-1):
                if board[r][c] == 'O':
                    for (rr, cc) in ((r-1, c), (r+1, c), (r, c-1), (r, c+1)):
                        # 找四个方向的有效O邻居, 合并它们
                        if 0 <= rr < rows and 0 <= cc < cols and board[rr][cc] == 'O':
                            union(r, c, rr, cc)
        for r in range(rows):
            for c in range(cols):
                # 只把没在边界O字符所在集合的O改成X
                # 特别注意这里不能直接和root比较! 因为在路径压缩或者union的时候可能会更改最终的祖先, 所以也需要find(root)找到最终统一的那个祖先
                if board[r][c] == 'O' and find(r, c) != find(*root):
                    board[r][c] = 'X'
```

### 方案 2

#### 思路

- **分析**
  - 回顾方案 1, 我们是将所有连通的块进行合并来求区域个数的
  - 但我们也完全可以从边界 O 出发, BFS 得到所有连通的 O 即可, 这样相比而言效率更高
  - 我们同样需要一个 visit 集合来存储边界或与边界连通的 O, 一是为了避免重复遍历, 二是最后循环的时候可以根据当前的 O 是否在该集合中判断其是否是连通 O
- **实现**
  - 经典的 BFS 算法, 注意存储的是行列二元组, 以及初始化的 q 中存所有边界 O 的行列下标, 遍历邻居的时候只考虑有效的 O 邻居
  - 下面代码中对 BFS 部分有详细注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(RC): 因为有 visit 集合的存在, 每个点不会被重复遍历, 所以最终就是把每个点都遍历一遍就能得出结果, 忽略常系数后时间复杂度就是 O(RC), 相比并查集的方案会快一些
- 空间复杂度 O(RC): visit 集合中存 `RC` 个元素

#### 代码

```python
class Solution:
    def solve(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        # 方法2: 经典BFS, 以所有边界的O为起点, BFS遍历, 需要visit集合来存储已经遍历过的元素
        # 之后循环就只改变不在visit集合的O, 也即不和边界O连通的O
        if not board:
            return
        q = []
        rows, cols = len(board), len(board[0])
        # 将所有边界O放到q中
        for r in range(rows):
            for c in (0, cols-1):
                if board[r][c] == 'O':
                    q.append((r, c))
        for c in range(cols):
            for r in (0, rows-1):
                if board[r][c] == 'O':
                    q.append((r, c))
        # visit 集合v判断当前字符是否被访问过
        v = set(q)
        for x in q:
            r, c = x
            for rr, cc in ((r+1, c), (r-1, c), (r, c+1), (r, c-1)):
                if 0 <= rr < rows and 0 <= cc < cols and board[rr][cc] == 'O' and (rr, cc) not in v:
                    # 找到有效邻居了, 加入q和v中
                    v.add((rr, cc))
                    q.append((rr, cc))
        for r in range(rows):
            for c in range(cols):
                # 只改变不在visit集合的字符(意味着没有和边界O连通)
                if board[r][c] == 'O' and (r, c) not in v:
                    board[r][c] = 'X'
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
