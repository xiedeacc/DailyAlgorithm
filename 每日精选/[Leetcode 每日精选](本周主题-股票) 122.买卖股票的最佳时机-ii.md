> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

> 今天我们继续来做股票问题, 这道题和昨天那道共同组成了这个系列的最基础的部分. 我会把这个系列汇总起来, 大家在我的公众号"每日精选算法题"中的聊天框中回复 **股票** 就能看到了~

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

给定一个数组，它的第  i 个元素是一支给定股票第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

## 题目样例

### 示例 1

#### 输入

[7,1,5,3,6,4]

#### 输出

7

#### 解释

- 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
- 随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。

### 示例 2

#### 输入

[1,2,3,4,5]

#### 输出

4

#### 解释

- 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
- 注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
- 因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。

## 题目思考

1. 相比昨天的题目, 这个题可以无限次买卖, 我们还能再次利用贪心法吗?

## 解决方案

### 思路

- 可以买卖无限次, 那我们就尽可能地在每次能产生收益的时候都进行买卖. 如果说昨天那道题只能买卖一次就像是长线交易一样, 那今天的题目就类似极限的短线交易, 哈哈
- 同样是贪心思路, 我们每次遇到下一个数比当前数大的时候, 我们就进行一次交易, 并把收益累加到最终结果中, 这样结果即为所求
- 注意这个过程并不违反题目规定 "不能同时参与多笔交易", 因为我们会在下一次买入前立刻卖出. 例如对于[1,2,3]这种情况, 我们在 1 的时候买入, 2 的时候卖出, 并重新买入(因为 3 比 2 大), 然后在 3 的时候再卖出, 总的最大收益就是 2

### 复杂度

- 时间复杂度 O(N): 需要遍历整个数组一遍
- 空间复杂度 O(1): 只需要维护常数个变量

### 代码

#### Python 3

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        res = 0
        for i in range(1, len(prices)):
            if prices[i] > prices[i - 1]:
                res += prices[i] - prices[i - 1]
        return res
```

#### C++

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int res = 0;
        for (int i = 1; i < prices.size(); ++i) {
            if (prices[i] > prices[i - 1]) {
                res += prices[i] - prices[i - 1];
            }
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
