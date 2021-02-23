> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

> 今天我们继续来做股票问题, 在做股票系列的两道困难题(III 和 IV)之前, 我们先来做包含"冷冻期"或"手续费"的这两道中等题, 让难度跨度没那么大, 今天就先来做包含冷冻期的这道吧. 大家在我的公众号"每日精选算法题"中的聊天框中回复 **股票** 就能看到了当前已经更新的股票系列了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

给定一个整数数组，其中第  i  个元素代表了第  i  天的股票价格 。​

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

## 题目样例

### 示例

#### 输入

[1,2,3,0,2]

#### 输出

3

#### 解释

- 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]

## 题目思考

1. 相比昨天的无限买卖的题目, 这道题多了个冷冻期的限制, 我们还能再次利用贪心法吗?
2. 如果不能利用贪心法, 还有什么其他思路吗?

## 解决方案

### 思路

- **分析**
  - 由于引入了冷冻期, 我们不能像之前的无限买卖那样每次都加相邻的正收益了, 因为两次买卖中间至少要隔一天, 而应该在哪些天买入和卖出, 就不能像贪心法分析的那样简单了
  - 观察冷冻期的概念, 虽然当前天不能基于前一天的结果来计算, 但可以使用更早的结果. 例如我们第 i 天要卖出, 那么上一次卖出的时间最晚得为 **i-3**, 这样冷却一天后, 第 i-1 天再次买入, 第 i 天才能卖出. 这就意味着我们可以使用动态规划的思路, 找到转移方程就能解决这个问题
- **推导**
  - 设 dp[i]表示**在第 i 天卖出**时能获得的最大收益, 注意可能第 i 天是一个低位, 这种情况下 dp[i]的结果可能很小, 所以我们最终结果应该是所有 dp 值中的最大值
  - 我们仍然需要维护 mnprice, 表示当前 i 天之前的最低价格. 但是这个最低价格**不能**和之前一样代表`[0:i-1]`的最低价格了, 因为在这个区间内我们可能已经有很多次买卖, mnprice 可能已经被用过了, 不能再被重复利用. 所以它应该是`[j:i-1]`之间的最低价格, j 是上次卖出的日期
  - 至于如何确定 j, 我们需要额外引入一个变量 mxdp, 表示在 i-3 天及之前的最大 dp 值 (为什么是 i-3? 可以参考上面的分析). 如果当前的 dp[i-3]比之前的 mxdp 大的话, 我们就更新 mxdp 和 mnprice(此时新的 mnprice 只能为 prices[i-1] 了)
  - 根据上面分析, 第 i 天卖出的最大收益为 `dp[i] = max(mxdp + prices[i] - mnprice)`. 由于 prices[i]是固定的, 所以我们只需要求 `max(mxdp - mnprice)`. 可以将它们视为一个整体, 再引入一个变量 mxdiff 来求截止到 i 天的最大 `mxdp - mnprice` 即可, 这样 dp[i] 就等于 `prices[i] + mxdiff`. (因为不一定 i-3 天卖出就是之前所能得到的最大收益, 有可能更早卖出才是)
- **总结**
  - 根据上面的分析和推导, 可以得出转移方程就是`dp[i] = max(mxdp + prices[i] - mnprice) = prices[i] + mxdiff`, 这个就是最最主要的转移方程, 也是整个股票系列的核心, 在接下来的股票问题中还会多次被用到. 大家如果有什么疑问的话也可以通过文章开头的联系方式跟我交流.
  - 下面的代码中对每一步有详细的注释, 帮助大家理解~

### 复杂度

- 时间复杂度 O(N): 需要遍历整个数组一遍
- 空间复杂度 O(N): dp 数组的长度, 注意 dp 数组可以只保留 4 个元素, 即到 i-3 天的 dp 值, 这样可以把空间复杂度降为 O(1), 感兴趣的同学可以自行尝试

### 代码

#### Python 3

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        # dp[i]表示第i天卖出股票能获得的最大收益
        n = len(prices)
        dp = [0] * n
        # mnprice - 上一次卖出之后到当前的最低价格
        # mxdp - i-3天及之前所能获得的最高收益
        # mxdiff - 最大的mxdp-mnprice
        mnprice, mxdiff, mxdp = float('inf'), -float('inf'), 0
        res = 0
        for i in range(1, n):
            # 更新最低价格
            mnprice = min(mnprice, prices[i - 1])
            if i - 3 >= 0 and dp[i - 3] > mxdp:
                # 当前dp[i-3]比mxdp更大, 更新它
                mxdp = dp[i - 3]
                # 同时mnprice也要被更新, 因为上一次卖出是i-3, 现在可能值只能是prices[i-1]了
                mnprice = prices[i - 1]
            # 更新mxdp - mnprice这个整体的最大值
            mxdiff = max(mxdiff, mxdp - mnprice)
            # 当前dp就是prices[i] + mxdiff
            dp[i] = prices[i] + mxdiff
            # 更新最终结果
            res = max(res, dp[i])
        return res
```

#### C++

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int res = 0;
        vector<int> dp(prices.size(), 0);
        int minPrice = INT_MAX, maxDiff = INT_MIN, maxdp = 0;

        for (int i = 1; i < prices.size(); ++i) {
            minPrice = min(minPrice, prices[i - 1]);
            if (i - 3 >= 0 && dp[i - 3] > maxdp) {
                maxdp = dp[i - 3];
                minPrice = prices[i - 1];
            }
            maxDiff = max(maxDiff, maxdp - minPrice);
            dp[i] = prices[i] + maxDiff;
            res = max(res, dp[i]);
        }

        return res;
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
