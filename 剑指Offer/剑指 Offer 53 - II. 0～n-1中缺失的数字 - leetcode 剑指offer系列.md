> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

一个长度为 n-1 的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围 0 ～ n-1 之内。在范围 0 ～ n-1 内的 n 个数字中有且只有一个数字不在该数组中，请找出这个数字。

1 <= 数组长度 <= 10000

- **注意: 原题描述变量使用混乱, 不好理解, 这里我们稍作替换, 将题目中的 n-1 替换为 n, 自然 n 就替换为了 n+1, 替换后的描述如下, 之后的分析都基于下面的描述**
- 一个长度为 n 的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围 0 ～ n 之内。在范围 0 ～ n 内的 n+1 个数字中有且只有一个数字不在该数组中，请找出这个数字。

## 题目样例

### 示例

- 输入: [0,1,3]
- 输出: 2

- 输入: [0,1,2,3,4,5,6,7,9]
- 输出: 8

## 题目思考

1. 可以用小于 O(N)的时间复杂度得出结果吗?
2. 可以做到 O(1) 空间复杂度吗?

## 解决方案

### 方案 1

#### 思路

- 根据题目描述, 在范围 0 ～ n 内的 n + 1 个数字中有且只有一个数字不在该数组中
- 所以一个比较容易想到的思路是求 1~n 的和, 然后减去数组本身所有数字的和, 这样得到的数字就是缺失的
- 不过这种方案的时间复杂度达到了 O(N), 没有用到排序这一条件, 并不是最优解
- 但如果去掉排序这一前提, 这种方案就没毛病了, 所以还是值得写下来供大家参考的

#### 复杂度

- 时间复杂度 O(N): 求和的时间消耗
- 空间复杂度 O(1): 只定义了几个变量

#### 代码

```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        # 方法1: 利用前n项和求和公式
        n = len(nums)
        return n * (n + 1) // 2 - sum(nums)
```

### 方案 2

#### 思路

- 那如何利用排序这一条件呢? 首先想到的还是二分查找, 但这里需要进行一些改变
- 仍然分析三种情况:
  - `nums[m] < m`?: **nums[m]不可能小于 m**, 否则的话前面部分必然会有重复的元素, 这样才能到下标 m 时对应的值小于 m, 排除这种情况
  - `nums[m] == m`: 此时显然缺失数字在右侧, 左侧都是下标和值一一对应的; 而且缺失数字绝不可能是 m, 所以找右半部分, 且要排除 m, 即 `s = m + 1`
  - `nums[m] > m`: 此时缺失数字必然在左侧, 因为只有一个缺失数字, 且当前值已经大于下标了, 所以一定是左侧有个值没有, 才导致下标小于值; 而且缺失数字有可能就是 m, 所以找左半部分, 且不能排除 m, 即 `e = m`
- 与上面分析配套的循环条件是 `s < e`, 这样就保证了 **m 必不等于 e** (因为 e 至少为 s+1, 此时 m 是 s, 不等于 e, 更不用说更大的 e 了), 所以`e = m`不会陷入死循环, 最终一定收敛到 `s==e` 的情况, 且根据上述分析, 该值一定即为缺失的数字 (因为一直是**排除不可能的情况向中间靠拢**)
- 另外注意, 有可能缺失值就是长度 n 本身, 所以初始化的右边界应该为 n, 而不是通常二分查找的 n-1. 但这样也不会导致数组越界, 因为中位数 m 永远不可能等于 n (根据循环条件是`s < e`的分析, m 必不等于 e)

#### 复杂度

- 时间复杂度 O(logN): 二分查找
- 空间复杂度 O(1): 只定义了几个变量

#### 代码

```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        # 方法2: 二分查找变种
        # 注意初始化的终点需要为数字长度n, 因为最终缺失数字可能是n, 不能排除这一情况
        s, e = 0, len(nums)
        while s < e:
            m = (s + e) >> 1
            # 注意nums[m]不可能小于m, 否则的话前面部分必然会有重复的元素
            if nums[m] == m:
                # 缺失数字在右侧, 且绝不可能是m, 所以找右半部分, 且要排除m
                s = m + 1
            elif nums[m] > m:
                # 缺失数字在左侧, 且有可能是m, 所以找左半部分, 但要保留m
                e = m
        # 最终收敛到的数字即为缺失的
        return s
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
