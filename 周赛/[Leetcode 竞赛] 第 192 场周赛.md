> [竞赛链接](https://leetcode-cn.com/contest/weekly-contest-192/)

> 新鲜出炉今天的周赛, 上周周末出去玩了所以没有更新, 这周重新恢复 😅

> 这场周赛感觉难度适中, 我的发挥也不错, 最终排到了 23 名, 可能题目也是我比较熟悉的类型吧

# [1470] 重新排列数组

> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/contest/weekly-contest-192/problems/shuffle-the-array/)

## 题目描述

给你一个数组 nums ，数组中有 2n 个元素，按 [x1,x2,...,xn,y1,y2,...,yn] 的格式排列。

请你将数组按 [x1,y1,x2,y2,...,xn,yn] 格式重新排列，返回重排后的数组。

- 1 <= n <= 500
- nums.length == 2n
- 1 <= nums[i] <= 10^3

## 题目样例

### 示例 1

#### 输入

nums = [2,5,1,3,4,7], n = 3

#### 输出

[2,3,5,4,1,7]

#### 解释

由于 x1=2, x2=5, x3=1, y1=3, y2=4, y3=7 ，所以答案为 [2,3,5,4,1,7]

### 示例 2

#### 输入

nums = [1,2,3,4,4,3,2,1], n = 4

#### 输出

[1,4,2,3,3,2,4,1]

### 示例 3

#### 输入

nums = [1,1,2,2], n = 2

#### 输出

[1,2,1,2]

## 题目思考

1. 没啥好说的, 读懂题意后直接模拟吧..

## 解决方案

### 思路

- 遍历数组, 按照需求构造结果数组即可

### 复杂度

- 时间复杂度 O(N): 只需遍历数组一次
- 空间复杂度 O(1) 或 O(N): 构造新的结果数组的话就是 O(N), 如果原地修改就是 O(1)

### 代码

#### Python 3

```python
class Solution:
    def shuffle(self, nums: List[int], n: int) -> List[int]:
        res = []
        for i in range(n):
            res.append(nums[i])
            res.append(nums[i + n])
        return res
```

#### C++

```cpp
class Solution
{
public:
    vector<int> shuffle(vector<int> &nums, int n)
    {
        auto res = vector<int>();
        for (int i = 0; i < n; ++i)
        {
            res.push_back(nums[i]);
            res.push_back(nums[i + n]);
        }
        return res;
    }
};
```

---

# [1471] 数组中的 k 个最强值

> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/contest/weekly-contest-192/problems/the-k-strongest-values-in-an-array/)

## 题目描述

给你一个整数数组 arr 和一个整数 k 。

设 m 为数组的中位数，只要满足下述两个前提之一，就可以判定 arr[i] 的值比 arr[j] 的值更强：

- |arr[i] - m| > |arr[j] - m|
- |arr[i] - m| == |arr[j] - m|，且 arr[i] > arr[j]

请返回由数组中最强的 k 个值组成的列表。答案可以以 任意顺序 返回。

中位数 是一个有序整数列表中处于中间位置的值。形式上，如果列表的长度为 n ，那么中位数就是该有序列表（下标从 0 开始）中位于 ((n - 1) / 2) 的元素。

- 例如 arr = [6, -3, 7, 2, 11]，n = 5：数组排序后得到 arr = [-3, 2, 6, 7, 11] ，数组的中间位置为 m = ((5 - 1) / 2) = 2 ，中位数 arr[m] 的值为 6 。
- 例如 arr = [-7, 22, 17, 3]，n = 4：数组排序后得到 arr = [-7, 3, 17, 22] ，数组的中间位置为 m = ((4 - 1) / 2) = 1 ，中位数 arr[m] 的值为 3 。

- 1 <= arr.length <= 10^5
- -10^5 <= arr[i] <= 10^5
- 1 <= k <= arr.length

## 题目样例

### 示例 1

#### 输入

arr = [1,2,3,4,5], k = 2

#### 输出

[5, 1]

#### 解释

- 中位数为 3，按从强到弱顺序排序后，数组变为 [5,1,4,2,3]。最强的两个元素是 [5, 1]。[1, 5] 也是正确答案。
- 注意，尽管 |5 - 3| == |1 - 3| ，但是 5 比 1 更强，因为 5 > 1 。

### 示例 2

#### 输入

arr = [1,1,3,5,5], k = 2

#### 输出

[5, 5]

#### 解释

中位数为 3, 按从强到弱顺序排序后，数组变为 [5,5,1,1,3]。最强的两个元素是 [5, 5]。

### 示例 3

#### 输入

arr = [6,7,11,7,6,8], k = 5

#### 输出

[11,8,6,6,7]

## 题目思考

1. 如何求最强?
2. 需要求最强的 k 个值, 你想到了哪些方法?

## 解决方案

### 思路

- 首先排序求中位数 (也可以使用基于快排思想的快速选择来求, 找从小到大第`((n - 1) / 2)`个元素, 降到 O(N))
- 然后根据更强的定义, 可以自定义一个比较方法, 将数组再次排序
- 最后取最强的 k 个即可 (也可以使用堆(O(NlogK))/快速选择(O(N))等, 最大 k 的问题大家可以直接参考这道题的[官方题解](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/solution/zui-xiao-de-kge-shu-by-leetcode-solution/), 我觉得写的很好很全了)
- 这道题因为数据规模不大, 我就采用了最基础的排序法, 因为代码量少了很多 😂 感兴趣的同学也可以自己尝试剩下的求最大 k 的做法

### 复杂度

- 时间复杂度: 排序法简单, 代码简洁, 但需要 O(NlogN); 快速选择法速度快, 但代码更长, 需要 O(N)
- 空间复杂度 O(1): 只使用了几个变量

### 代码

#### Python 3

```python
class Solution:
    def getStrongest(self, arr: List[int], k: int) -> List[int]:
        n = len(arr)
        # 先排序求中位数
        arr = sorted(arr)
        mid = arr[(n - 1) // 2]

        def mykey(x):
            # 自定义最强排序的key函数
            # 注意py 3不同于其他语言的传入两个参数的自定义比较函数
            # 此处的意思代表先按照abs升序排列, 相等情况下再按照自身的值的升序排列
            return (abs(x - mid), x)

        # 按照最强排序后取最后的k个即为所求
        arr = sorted(arr, key=mykey)
        return arr[n - k:]
```

#### C++

```cpp
class Solution
{
public:
    vector<int> getStrongest(vector<int> &arr, int k)
    {
        auto n = arr.size();
        sort(arr.begin(), arr.end());
        auto mid = arr[(n - 1) / 2];
        sort(arr.begin(), arr.end(), [&](int x, int y) -> bool { return abs(x - mid) < abs(y - mid) || abs(x - mid) == abs(y - mid) && x < y; });
        return vector<int>(arr.begin() + n - k, arr.end());
    }
};
```

---

# [1472] 设计浏览器历史记录

> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/contest/weekly-contest-192/problems/design-browser-history/)

## 题目描述

你有一个只支持单个标签页的 浏览器 ，最开始你浏览的网页是 homepage ，你可以访问其他的网站 url ，也可以在浏览历史中后退 steps 步或前进 steps 步。

请你实现 BrowserHistory 类：

- BrowserHistory(string homepage) ，用 homepage 初始化浏览器类。
- void visit(string url) 从当前页跳转访问 url 对应的页面 。执行此操作会把浏览历史前进的记录全部删除。
- string back(int steps) 在浏览历史中后退 steps 步。如果你只能在浏览历史中后退至多 x 步且 steps > x ，那么你只后退 x 步。请返回后退 至多 steps 步以后的 url 。
- string forward(int steps) 在浏览历史中前进 steps 步。如果你只能在浏览历史中前进至多 x 步且 steps > x ，那么你只前进 x 步。请返回前进 至多 steps 步以后的 url 。

- 1 <= homepage.length <= 20
- 1 <= url.length <= 20
- 1 <= steps <= 100
- homepage 和 url 都只包含 '.' 或者小写英文字母。
- 最多调用 5000 次 visit， back 和 forward 函数。

## 题目样例

### 示例

#### 输入

- ["BrowserHistory","visit","visit","visit","back","back","forward","visit","forward","back","back"]
- [["leetcode.com"],["google.com"],["facebook.com"],["youtube.com"],[1],[1],[1],["linkedin.com"],[2],[2],[7]]

#### 输出

[null,null,null,null,"facebook.com","google.com","facebook.com",null,"linkedin.com","google.com","leetcode.com"]

#### 解释

- BrowserHistory browserHistory = new BrowserHistory("leetcode.com");
- browserHistory.visit("google.com"); // 你原本在浏览 "leetcode.com" 。访问 "google.com"
- browserHistory.visit("facebook.com"); // 你原本在浏览 "google.com" 。访问 "facebook.com"
- browserHistory.visit("youtube.com"); // 你原本在浏览 "facebook.com" 。访问 "youtube.com"
- browserHistory.back(1); // 你原本在浏览 "youtube.com" ，后退到 "facebook.com" 并返回 "facebook.com"
- browserHistory.back(1); // 你原本在浏览 "facebook.com" ，后退到 "google.com" 并返回 "google.com"
- browserHistory.forward(1); // 你原本在浏览 "google.com" ，前进到 "facebook.com" 并返回 "facebook.com"
- browserHistory.visit("linkedin.com"); // 你原本在浏览 "facebook.com" 。 访问 "linkedin.com"
- browserHistory.forward(2); // 你原本在浏览 "linkedin.com" ，你无法前进任何步数。
- browserHistory.back(2); // 你原本在浏览 "linkedin.com" ，后退两步依次先到 "facebook.com" ，然后到 "google.com" ，并返回 "google.com"
- browserHistory.back(7); // 你原本在浏览 "google.com"， 你只能后退一步到 "leetcode.com" ，并返回 "leetcode.com"

## 题目思考

1. 这道题好贴近实际生活, 根据题目描述, 需要哪些数据结构和变量?

## 解决方案

### 思路

- 这道题特别像设计模式中的支持撤销和重试的命令模式, 我们可以使用相同的思路
- 维护一个列表和一个下标, 列表记录当前按照顺序的所有网页, 下标记录当前正在访问的网页
- visit 的时候需要清空当前下标之后的所有网页, 然后再把当前网页加进去
- back 和 forward 的时候就是移动下标向后或者向前, 注意不能超过列表范围

### 复杂度

- 时间复杂度 O(1): 每种操作的均摊复杂度为 O(1). 显然 back 和 forward 是 O(1); visit 虽然有循环 pop 操作, 但是每个网页只会被 pop 一次, 根据摊还分析, 假设历史上一共访问过 N 个网页, 那么总 pop 次数为 O(N), 分摊下来的每个操作的平均时间复杂度就是 O(1)
- 空间复杂度 O(N): 使用一个列表存储所有网页

### 代码

#### Python 3

```python
class BrowserHistory:
    def __init__(self, homepage: str):
        # 初始化访问主页, 下标为0
        self.q = [homepage]
        self.i = 0

    def visit(self, url: str) -> None:
        # 移除当前网页后面的所有网页
        while self.i < len(self.q) - 1:
            self.q.pop()
        self.q.append(url)
        self.i += 1

    def back(self, steps: int) -> str:
        # 新的下标要大于等于0
        self.i = max(0, self.i - steps)
        return self.q[self.i]

    def forward(self, steps: int) -> str:
        # 新的下标要小于列表长度
        self.i = min(len(self.q) - 1, self.i + steps)
        return self.q[self.i]
```

#### C++

```cpp
class BrowserHistory
{
public:
    BrowserHistory(string homepage)
    {
        q.push_back(homepage);
        i = 0;
    }

    void visit(string url)
    {
        while (i < q.size() - 1)
        {
            q.pop_back();
        }
        q.push_back(url);
        ++i;
    }

    string back(int steps)
    {
        i = max(0, i - steps);
        return q[i];
    }

    string forward(int steps)
    {
        i = min(int(q.size()) - 1, i + steps);
        return q[i];
    }

private:
    vector<string> q;
    int i;
};
```

---

# [1473] 给房子涂色 III

> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/contest/weekly-contest-192/problems/paint-house-iii/)

## 题目描述

在一个小城市里，有 m 个房子排成一排，你需要给每个房子涂上 n 种颜色之一（颜色编号为 1 到 n ）。有的房子去年夏天已经涂过颜色了，所以这些房子不需要被重新涂色。

我们将连续相同颜色尽可能多的房子称为一个街区。（比方说 houses = [1,2,2,3,3,2,1,1] ，它包含 5 个街区 [{1}, {2,2}, {3,3}, {2}, {1,1}] 。）

给你一个数组 houses ，一个 m \* n 的矩阵 cost 和一个整数 target ，其中：

- houses[i]：是第 i 个房子的颜色，0 表示这个房子还没有被涂色。
- cost[i][j]：是将第 i 个房子涂成颜色 j+1 的花费。

请你返回房子涂色方案的最小总花费，使得每个房子都被涂色后，恰好组成 target 个街区。如果没有可用的涂色方案，请返回 -1 。

- m == houses.length == cost.length
- n == cost[i].length
- 1 <= m <= 100
- 1 <= n <= 20
- 1 <= target <= m
- 0 <= houses[i] <= n
- 1 <= cost[i][j] <= 10^4

## 题目样例

### 示例 1

#### 输入

houses = [0,0,0,0,0], cost = [[1,10],[10,1],[10,1],[1,10],[5,1]], m = 5, n = 2, target = 3

#### 输出

9

#### 解释

- 房子涂色方案为 [1,2,2,1,1]
- 此方案包含 target = 3 个街区，分别是 [{1}, {2,2}, {1,1}]。
- 涂色的总花费为 (1 + 1 + 1 + 1 + 5) = 9。

### 示例 2

#### 输入

houses = [0,2,1,2,0], cost = [[1,10],[10,1],[10,1],[1,10],[5,1]], m = 5, n = 2, target = 3

#### 输出

11

#### 解释

- 有的房子已经被涂色了，在此基础上涂色方案为 [2,2,1,2,2]
- 此方案包含 target = 3 个街区，分别是 [{2,2}, {1}, {2,2}]。
- 给第一个和最后一个房子涂色的花费为 (10 + 1) = 11。

### 示例 3

#### 输入

houses = [3,1,2,3], cost = [[1,1,1],[1,1,1],[1,1,1],[1,1,1]], m = 4, n = 3, target = 3

#### 输出

-1

## 题目思考

1. 观察数据规模, 房子/颜色/街区数目都很小, 就算把它们每个都当作一维状态也是可以接受的
2. 所以可以尝试动态规划的思路吗?
3. 需要分为几种情况?

## 解决方案

### 思路

- **分析**
  - 这种有多个变量的问题, 如果其数目规模相乘仍然是合理范围的时候, 我们就可以尝试多维动态规划
- **推导**
  - 设 `dp[i,j,b]` 表示第 i 个房子, 涂成 j 颜色, 组成 b 个 block 的最小花费
  - 那么结果就是 `min(dp[m-1,x,target]) (1<=x<=n)`
  - 根据题目描述, 每个房子的初始状态是有 2 种:
    1. 初始颜色为 0, 那么可以被涂色, 加花费
    2. 初始颜色不为 0, 那么颜色不能变, 也不需要加花费
  - 利用这两个条件, 我们可以得出如下状态转移方程
    1. 如果当前房子 i 的初始颜色为 0, 那么它可以被涂 1~n 的任意颜色, 注意需要考虑当前颜色和前一个房子的颜色是否相等, 相等的话街区 b 就不能+1, 不等才可以. 所以转移方程就是 `dp[i,j,b] = min(dp[i-1,jj,b-1]+cost[i][j], dp[i-1,j,b]+cost[i][j]) (1<=jj<=n 且 jj!=j)`
    2. 如果当前房子 i 的初始颜色不为 0, 那么它只能保持当前颜色了, 且不需要额外花费. 它的转移方程简单一些: `dp[i,houses[i],b] = min(dp[i-1,jj,b-1], dp[i-1,houses[i],b]) (1<=jj<=n 且 jj!=houses[i])`
  - 而初始化只需要判断第 0 个房子的初始颜色, 利用上述转移方程即可
  - 有了初始化和转移方程, 我们就可以循环得到最终要求的结果了
- **实现**
  - 下面代码中对上述转移过程进行了合并, 避免了一些冗余判断, 且每个核心步骤有详细的注释, 帮助大家理解

### 复杂度

- 时间复杂度 O(MMNN): 需要遍历当前房子/颜色(MN), 以及内层的前一个房子的街区/颜色(MN) (如果有更优解欢迎指出~ 比赛的时候我想到的就是上面这个思路)
- 空间复杂度 O(MMN): 三维 DP 数组, 两个维度长度是 M(房子下标, 街区数)

### 代码

#### Python 3

```python
class Solution:
    def minCost(self, houses: List[int], cost: List[List[int]], m: int, n: int,
                target: int) -> int:
        dp = collections.defaultdict(int)
        res = float('inf')
        for i in range(m):
            # paint表示是否需要涂色
            paint = True
            if houses[i] != 0:
                # colors表示当前可选的颜色
                colors = [houses[i]]
                # 已有颜色, 不需要涂色
                paint = False
            else:
                colors = range(1, n + 1)
            for j in colors:
                # 计算涂成当前颜色时的cost, 注意已有颜色的情况下cost就是0
                curcost = (0 if not paint else cost[i][j - 1])
                ii = i - 1
                if i == 0:
                    # 第0号房子, 前面没有房子, 所以街区数一定为1, 花费也就是curcost
                    dp[i, j, 1] = curcost
                    continue
                for jj in range(1, n + 1):
                    for b in range(i + 1):
                        # 街区数一定不会超过当前的房子数, 因为极限情况也是一个房子一个街区
                        if (ii, jj, b) in dp:
                            # 根据前一个房子的颜色计算当前的街区数
                            if j == jj:
                                newb = b
                            else:
                                newb = b + 1
                            # 更新当前dp值
                            if (i, j, newb) not in dp:
                                dp[i, j, newb] = dp[ii, jj, b] + curcost
                            else:
                                dp[i, j, newb] = min(
                                    dp[i, j, newb],
                                    dp[ii, jj, b] + curcost,
                                )
                            if i == m - 1 and target == newb:
                                # 更新最终结果值
                                res = min(res, dp[i, j, newb])
        # 如果最终结果仍为inf的话则说明不存在这样的涂色方案, 返回-1
        return res if res != float('inf') else -1
```

#### C++

```cpp
class Solution
{
public:
    int minCost(vector<int> &houses, vector<vector<int>> &cost, int m, int n, int target)
    {
        // 三维dp数组, 初始化为最大int, 各个维度的长度略大于最大m或n即可
        auto dp = vector<vector<vector<int>>>(102, vector<vector<int>>(22, vector<int>(102, INT_MAX)));
        int res = INT_MAX;
        vector<int> allcolors;
        for (int i = 0; i < m; ++i)
        {
            bool paint = true;
            // 这里改用存起点和终点
            int startcolor = 1;
            int endcolor = n;
            if (houses[i] != 0)
            {
                startcolor = endcolor = houses[i];
                paint = false;
            }
            for (int j = startcolor; j <= endcolor; ++j)
            {
                int curcost = paint ? cost[i][j - 1] : 0;
                int ii = i - 1;
                if (i == 0)
                {
                    dp[i][j][1] = curcost;
                }
                else
                {
                    for (int jj = 1; jj <= n; ++jj)
                    {
                        for (int b = 0; b <= i; ++b)
                        {
                            if (dp[ii][jj][b] != INT_MAX)
                            {
                                int newb = j == jj ? b : b + 1;
                                dp[i][j][newb] = min(dp[i][j][newb], dp[ii][jj][b] + curcost);
                                if (i == m - 1 && target == newb)
                                {
                                    res = min(res, dp[i][j][newb]);
                                }
                            }
                        }
                    }
                }
            }
        }
        return res == INT_MAX ? -1 : res;
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
