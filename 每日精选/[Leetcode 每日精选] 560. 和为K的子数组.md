> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

> 感觉今天官方的每日一题不错, 题目没有像前两天的基础层序遍历/只出现一次的数字那样烂大街, 题目描述也很简明易懂, 所以今天也来做这道吧 😆

## 题目描述

给定一个整数数组和一个整数 k，你需要找到该数组中和为 k 的连续的子数组的个数。

- 数组的长度为 [1, 20,000]。
- 数组中元素的范围是 [-1000, 1000] ，且整数 k 的范围是 [-1e7, 1e7]。

## 题目样例

### 示例 1

#### 输入

nums = [1,1,1], k = 2

#### 输出

2

#### 解释

[1,1] 与 [1,1] 为两种不同的情况。

## 题目思考

1. 根据数据规模, 如果算法是 O(N^3)复杂度肯定超时, O(N^2)的也够呛, 有没有更优的做法呢?
2. 根据**连续子数组之和**, 你有没有联想到什么预处理手段?

## 解决方案

### 方案 1

#### 思路

- 很容易想到可以固定连续子数组的起点, 然后遍历之后的元素, 使用一个变量 sum 表示从起点到当前下标的和, 如果和为 k 的话就把结果加 1
- **注意: 因为这个方法复杂度还是有点高, 导致有些语言会 TLE, 所以只把它作为推导下面更优方案的基础...**

#### 复杂度

- 时间复杂度 O(N^2): 总共 N 个起点, 然后每个起点开始要遍历 N 个元素, 所以是 O(N^2)
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

##### Python 3

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        res = 0
        for start in range(len(nums)):
            cursum = 0
            for end in range(start, len(nums)):
                cursum += nums[end]
                if cursum == k:
                    res += 1
        return res
```

##### C++

```cpp
class Solution
{
public:
    int subarraySum(vector<int> &nums, int k)
    {
        int res = 0;
        for (int start = 0; start < nums.size(); ++start)
        {
            int cursum = 0;
            for (int end = start; end < nums.size(); ++end)
            {
                cursum += nums[end];
                if (cursum == k)
                {
                    res += 1;
                }
            }
        }
        return res;
    }
};
```

### 方案 2

#### 思路

- 回顾方案 1, 我们对于每个起点都遍历了一遍从它开始的前缀和, 但是这些前缀和完全可以通过从 0 开始的两个前缀和相减所得: 例如要求[2,3]的和, 可以用[0,3]的和减去[0,1]的和即可得到
- 这样只需要遍历一遍数组, 存下所有的前缀和, 就能得到任意连续区间的和了
- 所以具体思路如下
  1. 使用一个变量记录当前前缀和
  2. 使用一个计数字典统计当前前缀和的出现次数
  3. 然后遍历到每个元素时, 看看当前前缀和 cursum 减去 k 是否在字典中, 在的话最终结果就加上出现的次数 cnt 即可. 举个例子, 假设[0,1]的和是 cursum-k, [0,3]的和是 cursum, 那么[2,3]的和就是 k, 是一个满足要求的连续子数组
  4. 最后不要忘了加上本身前缀和就是 k 的区间数目
- 还有要注意一点是不能先更新了计数字典, 再累加结果, 因为 k 有可能是 0, 这时如果先更新的话就会错误地把每个前缀和的次数加到最终结果里, 显然结果就不对了..

#### 复杂度

- 时间复杂度 O(N): 只需要遍历一遍
- 空间复杂度 O(N): 使用了一个前缀和计数字典

#### 代码

##### Python 3

```python
import collections

class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        sumdicts = collections.defaultdict(int)
        cursum = 0
        res = 0
        for n in nums:
            cursum += n
            if cursum - k in sumdicts:
                res += sumdicts[cursum - k]
            sumdicts[cursum] += 1
        res += sumdicts[k]
        return res
```

##### C++

```cpp
class Solution
{
public:
    int subarraySum(vector<int> &nums, int k)
    {
        unordered_map<int, int> sumdicts;
        int cursum = 0;
        int res = 0;
        for (int n : nums)
        {
            cursum += n;
            if (sumdicts.count(cursum - k))
            {
                res += sumdicts[cursum - k];
            }
            ++sumdicts[cursum];
        }
        res += sumdicts[k];
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
