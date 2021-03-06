> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)

> 今天我们来做一道经典题目, 这道题思路不难, 但优化的过程还挺有意思的, 最后 python 可以四行代码搞定, 大家也可以在看代码之前想想怎么写~

## 题目描述

给定一个数字，我们按照如下规则把它翻译为字符串：0 翻译成 “a” ，1 翻译成 “b”，……，11 翻译成 “l”，……，25 翻译成 “z”。一个数字可能有多个翻译。请编程实现一个函数，用来计算一个数字有多少种不同的翻译方法。

- 0 <= num < 2^31

## 题目样例

### 示例 1

#### 输入

12258

#### 输出

5

### 示例 2

#### 输入

s = ""

#### 输出

12258 有 5 种不同的翻译，分别是"bccfi", "bwfi", "bczi", "mcfi"和"mzi"

## 题目思考

1. 根据数字的不同, 可能有不同方案, 例如当前是 2, 可能是单独 1 个 2, 也可能是和前面数字组成的 12 和 22
2. 利用这个发现, 你想到了什么思路?

## 解决方案

### 思路

- 根据题目描述, 它很像一道动态规划经典题: [70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/), 也是 1 个数字可以有多种方案
- 不同的是这里可以组成两位数的数字是有限制的, 不能像爬楼梯那样无脑相加
- 所以我们可以定义 dp 数组, dp[i]表示从左到右遍历到第 i 位数字时可以翻译成的字符串数目
- 固定第 i 位数字后, 观察其前一位数字, 可以得到如下转移方程:
  - 如果当前数字和前一位数字(如果存在的话)组成的两位数在 10 到 25 之间, 那么当前数字既可以独立使用, 也可以和之前数字合用, 所以 `dp[i] = dp[i-2](使用2个字符) + dp[i-1](使用1个字符)`
  - 如果当前数字无法和前一位数字组成有效的两位数, 意味着当前数字只能独立使用, 所以 `dp[i] = dp[i-1]`
- 观察上述方程, 我们发现整个 dp 数组只用到了 i-2 和 i-1, 所以我们可以只使用两个变量, 定义上一个和当前的 dp 值, 这样既节省了空间, 又精简了代码
- 注意需要先把输入的数字转换成字符串, 方便每一位的处理
- 注意初始化值都为 1, 因为至少可以转换成 1 个字符串

### 复杂度

- 时间复杂度 O(M): M 是数字的位数, 只需要遍历一遍数字的每一位即可
- 空间复杂度 O(1): 只使用了几个变量

### 代码

#### Python 3

```python
class Solution:
    def translateNum(self, num: int) -> int:
        s, pre, cur = str(num), 1, 1
        for i in range(1, len(s)):
            # 将s[i-1]和s[i]组成的字符串视为整体, 判断其值能否位于10和25之间, 能的话就加上前面两个的值的和, 否则就只能加上前一个的值了
            pre, cur = cur, ((pre + cur) if 10 <= int(s[i - 1:i + 1]) <= 25 else cur)
        return cur
```

#### C++

```cpp
class Solution {
public:
    int translateNum(int num) {
        int pre = 1, cur = 1;
        string s = to_string(num);
        for (int i = 1; i < s.size(); ++i) {
            int value = stoi(s.substr(i-1, 2));
            int temp = pre;
            pre = cur;
            if (value >= 10 && value <= 25) {
                cur = temp + cur;
            }
        }
        return cur;
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
