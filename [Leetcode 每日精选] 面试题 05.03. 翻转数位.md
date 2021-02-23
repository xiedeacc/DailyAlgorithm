> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/reverse-bits-lcci/)

> 前几天的题目难度有点大, 那今天我们就来做一道简单题吧 😝 这道题比较简单, 但也有多种解法, 而且如果加上一些进阶限制条件, 比如单次遍历和 O(1)空间之类的, 也值得思考一下如何进行优化

## 题目描述

给定一个 32 位整数 num，你可以将一个数位从 0 变为 1。请编写一个程序，找出你能够获得的最长的一串 1 的长度。

## 题目样例

### 示例 1

#### 输入

num = 1775(0b11011101111)

#### 输出

8

#### 解释

把最后面的那个 0 换成 1 就能得到连续 8 个 1 了

### 示例 2

#### 输入

num = 7(0b0111)

#### 输出

4

#### 解释

把最左边的 0 换成 1

## 题目思考

1. 根据题目描述, 有没有什么简单直接的思路?
2. 如何优化, 能够动态地一次遍历就得到结果, 从而满足进阶限制条件呢?

## 解决方案

### 方案 1

#### 思路

- 最直接的方法, 既然要求翻转后的最长长度, 那是不是可以针对每一位 0, 将其变成 1 后统计新数字的最长连续 1 长度, 看看哪个最长即可
- 注意不要忘了求原来数字的最长长度, 因为题目说的是可以翻转, 意思就是不翻转也行

#### 复杂度

- 时间复杂度 O(32\*32): 总共需要判断 32 位中每一位的 0 翻转后的数字(最差情况), 每次判断需要遍历所有 32 位数, 所以是 O(32\*32)
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

##### Python 3

```python
class Solution:
    def reverseBits(self, num: int) -> int:
        def getLongest1(n):
            res = 0
            curcnt = 0
            for i in range(32):
                if n & 1 << i:
                    # 当前位是1, 连续长度curcnt+1
                    curcnt += 1
                    res = max(res, curcnt)
                else:
                    # 当前位是0, 中断了连续1, curcnt重置为0
                    curcnt = 0
            return res

        res = getLongest1(num)
        for i in range(32):
            if num & (1 << i) == 0:
                # 将第i位的0翻转为1后计算新的数字的最大连续1的长度
                res = max(res, getLongest1(num | (1 << i)))
        return res
```

##### C++

```cpp
class Solution {
public:
    int reverseBits(int num) {
        auto getLongest1 = [](int n) {
            int res = 0;
            int curcnt = 0;
            for(int i = 0; i < 32; ++i) {
                if (n & (1 << i)) {
                    res = max(res, ++curcnt);
                } else {
                    curcnt = 0;
                }
            }
            return res;
        };

        int res = getLongest1(num);
        for (int i = 0; i < 32; ++i) {
            if ((num & (1 << i)) == 0) {
                res = max(res, getLongest1(num | (1 << i)));
            }
        }
        return res;
    }
};
```

### 方案 2

#### 思路

- 回顾方案 1, 对于每一位的 0 都需要翻转下然后统计新的数字的连续 1 长度, 这个工作是否可以通过一次遍历就完成?
- 因为中间可能存在翻转, 所以需要记录上一个连续 1 和当前的连续 1 的长度, 自然就想到了双指针
- 具体做法如下:
  - 使用两个变量 pre 和 cur
  - pre 表示上一个连续 1 的长度, 初始化为 0
  - cur 表示当前连续 1 的长度, 初始化为 0
  - 结果即为最大的 pre+1+cur (1 表示中间从 0 翻转成的那个 1)
  - **需要注意 32 位全为 1 (即-1)的特殊情况**, 这个时候 cur 是 32, pre 是 0, pre+1+cur 就不对了, 所以最终结果 res 需要为 min(32, res)

#### 复杂度

- 时间复杂度 O(32): 只需要遍历一遍每一位即可
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

##### Python 3

```python
class Solution:
    def reverseBits(self, num: int) -> int:
        pre, cur = 0, 0
        # 结果至少为1, 因为即使全为0, 也可以翻转其中一位变成1个1
        res = 1
        for i in range(32):
            if num & (1 << i):
                # 当前位是1, 增加cur的长度, 同时更新结果值
                cur += 1
                res = max(res, pre + cur + 1)
            else:
                # 当前位是0, 不能继续连续下去了, 更新pre和cur
                pre = cur
                cur = 0
        return min(32, res)
```

##### C++

```cpp
class Solution {
public:
    int reverseBits(int num) {
        int res = 1;
        int pre = 0, cur = 0;
        for (int i = 0; i < 32; ++i) {
            if (num & (1 << i)) {
                ++cur;
                res = max(res, pre + cur + 1);
            } else {
                pre = cur;
                cur = 0;
            }
        }
        return min(res, 32);
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
