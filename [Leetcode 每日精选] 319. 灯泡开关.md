> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/bulb-switcher/)

## 题目描述

初始时有 n 个灯泡关闭。

- 第 1 轮，你打开所有的灯泡。
- 第 2 轮，每两个灯泡你关闭一次。
- 第 3 轮，每三个灯泡切换一次开关（如果关闭则开启，如果开启则关闭）。
- 第 i 轮，每 i 个灯泡切换一次开关。
- 第 n 轮，你只切换最后一个灯泡的开关。

找出 n 轮后有多少个亮着的灯泡。

## 题目样例

### 示例 1

#### 输入

3

#### 输出

1

#### 解释

初始时, 灯泡状态 [关闭, 关闭, 关闭].

- 第一轮后, 灯泡状态 [开启, 开启, 开启].
- 第二轮后, 灯泡状态 [开启, 关闭, 开启].
- 第三轮后, 灯泡状态 [开启, 关闭, 关闭].

你应该返回 1，因为只有一个灯泡还亮着。

## 题目思考

1. 对于轮次 i 而言, 哪些灯泡状态改变取决于其下标是否为 i 的倍数
2. 由此可得, 最终开着的灯泡应该有奇数个因子, 这样才能保证最终状态是开着的. 因为如果有偶数个因子的话, 每次开和关都和原状态一致, 而最初又是关着的, 那么最终也会是关着的.
3. 进一步思考: 什么数的因子个数为奇数?

## 解决方案

### 方案 1

#### 思路

- 最 naive 的方法, 根据上面思考, 求每个下标的因子个数, 统计有奇数因子的下标即可
- **注意: 因为复杂度过高, 会 TLE, 所以这个方案只是作为推导下面更优方案的基础...**

#### 复杂度

- 时间复杂度 O(N\*sqrt(N)): 需要遍历每个下标, 而每个下标求因子数需要遍历到它的平方根
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

##### Python 3

```python
class Solution:
    def bulbSwitch(self, n: int) -> int:
        def getFactorCount(num):
            res = 0
            for i in range(1, int(math.sqrt(num)) + 1):
                if num % i == 0:
                    res += 1 if i == num // i else 2
            return res

        res = 0
        for num in range(1, n + 1):
            if getFactorCount(num) & 1:
                res += 1
        return res
```

##### C++

```cpp
class Solution
{
private:
    int getFactorCount(int num)
    {
        int res = 0;
        for (int i = 1; i <= int(sqrt(num)); ++i)
        {
            if (num % i == 0)
            {
                res += i == num / i ? 1 : 2;
            }
        }
        return res;
    }

public:
    int bulbSwitch(int n)
    {
        int res = 0;
        for (int num = 1; num <= n; ++num)
        {
            if (getFactorCount(num) & 1)
            {
                ++res;
            };
        }
        return res;
    }
};
```

### 方案 2

#### 思路

- 回顾因子是怎么计算的: i\*(n/i), 通常因子都是成对的, 只有存在 i==n/i 时才会是单独一个因子, 即==>平方数
- 如何快速求平方数? 只需要将平方根 i 从 1 开始循环, 当其平方大于 n 时退出即可, 此时的 i-1 即为所求

#### 复杂度

- 时间复杂度 O(sqrt(N)): 只需要遍历 N 的平方根个数
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

##### Python 3

```python
class Solution:
    def bulbSwitch(self, n: int) -> int:
        i = 1
        while i * i <= n:
            i += 1
        return i - 1
```

##### C++

```cpp
class Solution
{
public:
    int bulbSwitch(int n)
    {
        int i = 1;
        while (i * i <= n)
        {
            ++i;
        }
        return i - 1;
    }
};
```

### 方案 3

#### 思路

- 进一步思考: 观察方案 2 的代码, 是不是很熟悉?
- 没错! 最终结果其实就是 int(sqrt(n))..
- 所以一行代码搞定

#### 复杂度

- 时间复杂度 O(M(n)): 只需要调用 sqrt 即可, sqrt 根据语言/平台/硬件的不同, 其复杂度也不一样, 简单来说可认为和一次除法的复杂度差不多..感兴趣的话可参考[Computational complexity of mathematical operations](https://en.wikipedia.org/wiki/Computational_complexity_of_mathematical_operations)
  - 这里 M 是指乘法算法的复杂度, M 根据实现的不同, 其复杂度介于 nlogn 到 n^2 之间, n 是数字的位数不是数字本身..
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

##### Python 3

```python
class Solution:
    def bulbSwitch(self, n: int) -> int:
        return int(math.sqrt(n))
```

##### C++

```cpp
class Solution
{
public:
    int bulbSwitch(int n)
    {
        return int(sqrt(n));
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
