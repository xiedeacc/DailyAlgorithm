> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/regions-cut-by-slashes/)

> 今天我们再来做一道并查集的变种问题. 个人觉得这道题非常有趣, 特别是它的思考过程; 而且除了并查集之外还可以用其他的做法, 可以帮助大家扩展思维. 大家在我的公众号"每日精选算法题"中的聊天框中回复 **并查集** 就能看到该系列当前已经更新的文章了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

在由 1 x 1 方格组成的 N x N 网格  grid 中，每个 1 x 1  方块由 /、\ 或空格构成。这些字符会将方块划分为一些共边的区域。

（请注意，反斜杠字符是转义的，因此 \ 用 `\\`  表示。）。

返回区域的数目。

## 题目样例

### 示例 1

#### 输入

```
[
" /",
"/ "
]
```

#### 输出

2

#### 解释

- 2x2 网格如下:

![示例图](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/15/1.png)

### 示例 2

#### 输入

```
[
" /",
" "
]
```

#### 输出

1

#### 解释

- 2x2 网格如下:

![示例图](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/15/2.png)

### 示例 3

#### 输入

```
[
"\\/",
"/\\"
]
```

#### 输出

4

#### 解释

- 回想一下，因为 \ 字符是转义的，所以 `\\/` 表示 \/，而 `/\\` 表示 /\。）
- 2x2 网格如下：

![示例图](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/15/3.png)

## 题目思考

1. 每个格子只有 3 种情况, 我们可以做哪些预处理?
2. 如何应用并查集的思想?
3. 除了并查集外, 还有别的思路吗?

## 解决方案

### 方案 1

#### 思路

- **分析**
  - 观察题目, 不管是正斜杠还是反斜杠, 都会把一个格子分成两块, 不同的只是块的方位. 那么我们完全可以利用这个条件, 将一个格子根据其两个对角线进行分割, 分成上下左右四块 (参考示例 3 的图), 这样每一块都只存在于斜杠的一边, 方便进行处理.
  - 接下来思考划分的区域个数, 很明显如果两个格子有连通的部分, 它们就同属一个区域.
  - 结合上面的分析, 针对每个格子的上下左右每一块, 我们都能够根据当前格子的状态(空白/正斜杠/反斜杠)找到其对应的连通的块. 这个连通的块可能是同一个格子里, 也可能是相邻格子. 以正斜杠为例, 具体连通关系如下 (其他两种情况可以用同样方法分析):
    - 处于上方的块: 和当前格子左块相连, 以及上面一个格子的下块相连
    - 处于下方的块: 和当前格子右块相连, 以及下面一个格子的上块相连
    - 处于左方的块: 和当前格子上块相连, 以及左面一个格子的右块相连
    - 处于右方的块: 和当前格子下块相连, 以及右面一个格子的左块相连
  - 找到所有连通关系之后, 我们就可以应用并查集的思路了: 将连通的块合并起来, 最后将所有块的祖先加入集合中, 集合数目即为最终区域个数
  - 注意这里并查集也是需要做一些修改的, 需要从一维改为三维,利用三元组(行号,列号,块的方向)来区分不同的块
- **实现**
  - 预处理部分: 这里代码用了表驱动法进行预处理, 通俗点说就是用多维字典存所有的连通关系, 需要的时候查表即可. 这样做的好处是后面的循环合并的逻辑会变得非常简单, 不需要各种 if 判断, 避免了代码的冗余. 下面代码对这一部分进行了详细的注释, 大家可以结合代码和注释来理解
  - 并查集部分: 将传统的并查集改造成三元组即可

#### 复杂度

- 时间复杂度 O(N^2logN): 总共需要遍历`4*4*N*N`次, 并查集中存`4*N*N`个元素, 每次合并和查询操作需要 `O(log(4*N*N)) = O(log(N))` 的时间, 省略掉常系数, 总时间复杂度就是 O(N^2logN)
- 空间复杂度 O(N^2): pre 字典中存 `4*N*N` 个元素

#### 代码

```python
class Solution:
    def regionsBySlashes(self, grid: List[str]) -> int:
        # 由于是斜杠, 所以可以将每个格子根据两个对角线划分为四个块, 分别是上下左右(可以参考示例3的图的四个块)
        # 方法1: 并查集
        up, down, left, right = 0, 1, 2, 3
        relations = {
            ' ': {
                # 表示当前格子是' '的情况下, 4个块各自可以连通的部分, 正斜杠和反斜杠的情况类似
                # 元组中的前两个值表示和当前的行和列的差值, 如果都是0表示在同一个格子里, 而(-1,0)表示在上面一个格子里, 即(r-1,c)
                # 对于相邻格子而言, 所有的up都可以与上一个格子的down相连, left和左边格子的right相连, 以此类推
                # 而对于同一格子内的四个块, 如果是空格的话表示互相连通, 正斜杠的话说明left和up连通, 反斜杠的话说明up和right连通, 以此类推就可以得出这个连通关系字典了
                up: [(0, 0, down), (0, 0, left), (0, 0, right), (-1, 0, down)],
                down: [(0, 0, up), (0, 0, left), (0, 0, right), (1, 0, up)],
                left: [(0, 0, up), (0, 0, down), (0, 0, right),
                       (0, -1, right)],
                right: [(0, 0, up), (0, 0, down), (0, 0, left), (0, 1, left)],
            },
            '/': {
                up: [(0, 0, left), (-1, 0, down)],
                down: [(0, 0, right), (1, 0, up)],
                left: [(0, 0, up), (0, -1, right)],
                right: [(0, 0, down), (0, 1, left)],
            },
            '\\': {
                up: [(0, 0, right), (-1, 0, down)],
                down: [(0, 0, left), (1, 0, up)],
                left: [(0, 0, down), (0, -1, right)],
                right: [(0, 0, up), (0, 1, left)],
            }
        }
        n = len(grid)
        # 并查集部分, 进行了一些改动保存三维状态: r,c,d, 来代表每个块 (r-行号, c-列号, d-方向)
        pre = {}

        def find(r, c, d):
            if (r, c, d) not in pre:
                pre[r, c, d] = (r, c, d)
            elif pre[r, c, d] != (r, c, d):
                pr, pc, pb = pre[r, c, d]
                pre[r, c, d] = find(pr, pc, pb)
            return pre[r, c, d]

        def union(r1, c1, d1, r2, c2, d2):
            pre[find(r1, c1, d1)] = find(r2, c2, d2)

        for r in range(n):
            for c in range(n):
                char = grid[r][c]
                for d in (up, down, left, right):
                    # 遍历当前格子的四个块, 用d表示上下左右
                    for neighbor in relations[char][d]:
                        # dr和dc表示邻居格子与当前格子的r和c的delta值, 可选范围为-1/0/1
                        dr, dc, dd = neighbor
                        # rr和cc是实际的邻居rc, 需要判断是否是有效值
                        rr, cc = r + dr, c + dc
                        if 0 <= rr < n and 0 <= cc < n:
                            union(r, c, d, rr, cc, dd)
        regions = set()
        for r in range(n):
            for c in range(n):
                for d in (up, down, left, right):
                    # 将每个块的祖先放在集合中, 相同祖先的块即表示这些块之间相互连通
                    # 最后集合的元素个数即为结果
                    regions.add(find(r, c, d))
        return len(regions)
```

### 方案 2

#### 思路

- **分析**
  - 回顾方案 1, 我们是将所有连通的块进行合并来求区域个数的
  - 但这个题作为图类型的题目, 是不是可以尝试一些经典图算法呢?
  - 答案也是肯定的, 这里我们采用 BFS 来进行分析: 在得到连通关系后, 像经典的 BFS 算法一样, 这里我们也需要一个 visit 集合来避免重复遍历. 然后开始遍历所有的块, 从一个不在 visit 集合的块出发, BFS 遍历其所有连通的块, 然后将它们加到 visit 集合中, 重复这一过程直到循环结束. 最终结果就是调用 BFS 的次数, 因为每次调用都代表找到了一个不在已有的任何区域的一个块.
- **实现**
  - 预处理部分: 跟方案 1 一样
  - BFS 部分: 将 BFS 操作单独提取出来, 这里存的也是三元组, 唯一确定一个块
  - 下面代码中对 BFS 部分有详细注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N^2): 因为有 visit 集合的存在, 每个块不会被重复遍历, 所以最终就是把每个块都遍历一遍就能得出结果, 忽略常系数后时间复杂度就是 O(N^2), 相比并查集的方案会快一些
- 空间复杂度 O(N^2): visit 集合中存 `4*N*N` 个元素

#### 代码

```python
class Solution:
    def regionsBySlashes(self, grid: List[str]) -> int:
        # 方法2: BFS
        # 同样方法预处理连通关系
        up, down, left, right = 0, 1, 2, 3
        relations = {
            ' ': {
                up: [(0, 0, down), (0, 0, left), (0, 0, right), (-1, 0, down)],
                down: [(0, 0, up), (0, 0, left), (0, 0, right), (1, 0, up)],
                left: [(0, 0, up), (0, 0, down), (0, 0, right),
                       (0, -1, right)],
                right: [(0, 0, up), (0, 0, down), (0, 0, left), (0, 1, left)],
            },
            '/': {
                up: [(0, 0, left), (-1, 0, down)],
                down: [(0, 0, right), (1, 0, up)],
                left: [(0, 0, up), (0, -1, right)],
                right: [(0, 0, down), (0, 1, left)],
            },
            '\\': {
                up: [(0, 0, right), (-1, 0, down)],
                down: [(0, 0, left), (1, 0, up)],
                left: [(0, 0, down), (0, -1, right)],
                right: [(0, 0, up), (0, 1, left)],
            }
        }
        n = len(grid)
        # visit 集合v判断当前块是否已经被访问过
        v = set()
        res = 0

        def bfs(r, c, d):
            q = [(r, c, d)]
            for x in q:
                r, c, d = x
                char = grid[r][c]
                for nex in relations[char][d]:
                    # 找到当前块的邻居, 如果其有效且尚未被访问, 将其加到q以及v集合中
                    dr, dc, dd = nex
                    # rr和cc是实际的邻居rc, 需要判断是否是有效值
                    rr, cc = r + dr, c + dc
                    if 0 <= rr < n and 0 <= cc < n and (rr, cc, dd) not in v:
                        # 找到有效邻居了, 加入q和v
                        q.append((rr, cc, dd))
                        v.add((rr, cc, dd))

        for r in range(n):
            for c in range(n):
                for d in range(4):
                    if (r, c, d) not in v:
                        # 找到一个还不属于任何区域的块, 以它为起点BFS, 同时res+1
                        res += 1
                        bfs(r, c, d)
        return res
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
