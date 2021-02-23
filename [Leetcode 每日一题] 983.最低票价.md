> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/minimum-cost-for-tickets/)

## 题目描述

在一个火车旅行很受欢迎的国度，你提前一年计划了一些火车旅行。在接下来的一年里，你要旅行的日子将以一个名为  days  的数组给出。每一项是一个从  1  到  365  的整数。

火车票有三种不同的销售方式：

1. 一张为期一天的通行证售价为 costs[0] 美元；
2. 一张为期七天的通行证售价为 costs[1] 美元；
3. 一张为期三十天的通行证售价为 costs[2] 美元。

通行证允许数天无限制的旅行。 例如，如果我们在第 2 天获得一张为期 7 天的通行证，那么我们可以连着旅行 7 天：第 2 天、第 3 天、第 4 天、第 5 天、第 6 天、第 7 天和第 8 天。

返回你想要完成在给定的列表  days  中列出的每一天的旅行所需要的最低消费。

## 题目样例

### 示例 1

#### 输入

days = [1,4,6,7,8,20], costs = [2,7,15]

#### 输出

11

#### 解释

例如，这里有一种购买通行证的方法，可以让你完成你的旅行计划：

1. 在第 1 天，你花了 costs[0] = \$2 买了一张为期 1 天的通行证，它将在第 1 天生效。
2. 在第 3 天，你花了 costs[1] = \$7 买了一张为期 7 天的通行证，它将在第 3, 4, ..., 9 天生效。
3. 在第 20 天，你花了 costs[0] = \$2 买了一张为期 1 天的通行证，它将在第 20 天生效。

你总共花了 \$11，并完成了你计划的每一天旅行。

### 示例 2

#### 输入

days = [1,2,3,4,5,6,7,8,9,10,30,31], costs = [2,7,15]

#### 输出

17

#### 解释

例如，这里有一种购买通行证的方法，可以让你完成你的旅行计划：

1. 在第 1 天，你花了 costs[2] = \$15 买了一张为期 30 天的通行证，它将在第 1, 2, ..., 30 天生效。
2. 在第 31 天，你花了 costs[0] = \$2 买了一张为期 1 天的通行证，它将在第 31 天生效。

你总共花了 \$17，并完成了你计划的每一天旅行。

## 题目思考

1. 对于每一个旅行日期, 都有三种方案可选: 1 天/7 天/30 天
2. 具体选哪种方案最优, 则要考虑前面的日期, 因为前面日期可能买了多日票, 覆盖到当前日期的话, 当前日期就无需额外买了
3. 这种对之前结果有依赖的问题大部分都可以采用**动态规划**来解决

## 解决方案

### 思路

- 动态规划
- dp\[i\]表示第 i 天的最低票价
- 那么 dp\[i\] = min(dp\[i-1\]+costs[0], dp\[j\] + costs[1], dp\[k\] + costs[2]) (j 和 k 分别是 i 之前的最接近 i 的日期, 且与 i 的时间间隔大于等于 7 和 30 的(表示不在同一区间))
- 结果就是最后一个 dp 值咯
- (代码里有更加详细的注释)

### 复杂度

- 时间复杂度 O(N): 需要遍历每一天, 然后乘上最大常系数 30
- 空间复杂度 O(N): 需要申请一个长度和日期相同的 dp 数组

### 代码

#### Python 3

```python
class Solution:
    def mincostTickets(self, days: List[int], costs: List[int]) -> int:
        dp = []
        for i in range(len(days)):
            # 当前日期买1天票的情况
            mncost = costs[0] if i == 0 else dp[-1] + costs[0]
            # 当前日期买了7天或30天票的情况
            # 需要找到之前的不在当前时间窗口的日期
            for ci, duration in ((1, 7), (2, 30)):
                # ci表示当前duration在costs数组的下标
                for j in range(i)[::-1]:
                    if days[i] - days[j] >= duration:
                        # 意味着在第j个日期和当前日期不在同一个时间窗口内, 如果当前日期想买这个时间窗口票的话, 必须在那个日期基础上加上当前时间窗口的价格
                        mncost = min(mncost, dp[j] + costs[ci])
                        break
                else:
                    # 说明前面的所有日期和当前日期都在同一时间窗口内, 直接买一张票就能全部覆盖
                    mncost = min(mncost, costs[ci])
            dp.append(mncost)
        return dp[-1]
```

#### C++

```cpp
class Solution {
public:
    int mincostTickets(vector<int>& days, vector<int>& costs) {
        int num_days = days.size();
        vector<int> dp(num_days, 0);
        unordered_map<int, int> costs_map = {{1, 7}, {2, 30}};

        for (int i = 0; i < num_days; ++i) {
            dp[i] = i > 0 ? (dp[i-1] + costs[0]) : costs[0];

            for (auto& entry : costs_map) {
                bool in_range = true;
                auto index = entry.first;
                auto duration = entry.second;
                for (int j = i-1; j >= 0; --j) {
                    if (days[i] - days[j] >= duration) {
                        dp[i] = min(dp[i], dp[j] + costs[index]);
                        in_range = false;
                        break;
                    }
                }
                if (in_range) {
                    dp[i] = min(dp[i], costs[index]);
                }
            }
        }

        return dp[num_days-1];
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
