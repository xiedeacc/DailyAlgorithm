> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/valid-palindrome-ii/)

> 今天继续做每日一题, 这道题虽然难度是简单, 但好像通过率不是很高(不到 40%), 值得思考一下. 另外还可以进一步扩展, 比如最多可以删除 k 个字符时判断能否成回文 (方案 3 给出了一些思路, 感兴趣的同学可以自行尝试)

## 题目描述

给定一个非空字符串 s，最多删除一个字符。判断是否能成为回文字符串。

- 字符串只包含从 a-z 的小写字母。字符串的最大长度是 50000。

## 题目样例

### 示例 1

#### 输入

"aba"

#### 输出

True

#### 解释

本身就是回文

### 示例 2

#### 输入

"abca"

#### 输出

True

#### 解释

可以删除 b 或者 c 字符

## 题目思考

1. 注意数据规模很大, 如果直接删除每个字符再验证的话需要 O(N^2)时间, 肯定会超时
2. 怎么利用只删除 1 个字符这个条件?
3. 如果把题目扩展成可以删除 k 个字符呢, 你能想到什么做法吗?

## 解决方案

### 方案 1

#### 思路

- 回顾双指针判断字符串是否回文的过程, 此题加入了可以删除一个字符的可选项
- 所以可以递归处理, 在原来的基础上加入一个 flag, 判断当前是否还能删除
- 如果遇到不相等的字符时, 可以左边指针往右移动一位, 或者右边向左移动一位, 然后把 flag 置为 false 即可, 再遇到不相等的就说明不能变成回文了
- 递归传入左边界和右边界表示双指针, 注意递归出口
- 该方案优点是代码非常简洁, 思路也比较直观, 缺点是需要额外的空间消耗

#### 复杂度

- 时间复杂度 O(N): 遇到不相等的字符时, 需要再判断两次, 所以总时间是 O(2N) = O(N)
- 空间复杂度 O(N): 递归的栈的消耗

#### 代码

##### Python 3

```python
class Solution:
    def validPalindrome(self, s: str) -> bool:
        def check(b, e, canDelete):
            if b >= e:
                return True
            if s[b] == s[e]:
                return check(b + 1, e - 1, canDelete)
            elif canDelete:
                # 还有一次删除机会, 左指针右移或者右指针左移, 同时更新flag保证之后的判断不能再删除字符了
                return check(b + 1, e, False) or check(b, e - 1, False)
            else:
                # 遇到不相等的字符且不能再删除了, 直接返回false
                return False

        return check(0, len(s) - 1, True)
```

##### C++

```c++
class Solution {
public:
    bool validPalindrome(string s) {
        function<bool(int, int, bool)> check = [&](int b, int e, bool canDelete) {
            while (b < e && s[b] == s[e]) {
                ++b;
                --e;
            }

            if (b >= e) {
                return true;
            }

            if (!canDelete) {
                return false;
            }

            return check(b + 1, e, false) || check(b, e - 1, false);
        };

        return check(0, s.size() - 1, true);
    }
};
```

### 方案 2

#### 思路

- 回顾方案 1, 我们是把迭代做法的双指针改成了递归版本, 那可不可以继续迭代搞定呢? 这样就不需要递归的栈的空间消耗了.
- 答案也是肯定的, 我们可以把在不能删除字符前提下的判断回文的双指针算法给提取出来
- 然后从两边开始往中间遍历, 遇到不相等的字符时就先后将两边的指针各往中间移动一位, 对剩下的两个子字符串调用那个算法, 如果有一边是回文, 则说明符合要求
- 该方案优点是空间复杂度更小, 但缺点是代码没有方案 1 简洁

#### 复杂度

- 时间复杂度 O(N): 遇到不相等的字符时, 需要再判断两次, 所以总时间是 O(2N) = O(N)
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

##### Python 3

```python
class Solution:
    def validPalindrome(self, s: str) -> bool:
        b = 0
        e = len(s) - 1

        def isPalindrome(b, e):
            while b < e:
                if s[b] == s[e]:
                    b += 1
                    e -= 1
                else:
                    return False
            return True

        while b < e:
            if s[b] == s[e]:
                b += 1
                e -= 1
            else:
                return isPalindrome(b + 1, e) or isPalindrome(b, e - 1)
        return True
```

##### C++

```c++
class Solution {
public:
    bool validPalindrome(string s) {
        auto isPalindrome = [&](int b, int e) {
            for(; b < e; ++b, --e) {
                if (s[b] != s[e]) {
                    return false;
                }
            }
            return true;
        };

        int b = 0;
        int e = s.size() - 1;

        while (b < e && s[b] == s[e]) {
            ++b;
            --e;
        }

        if (b >= e) {
            return true;
        }

        return isPalindrome(b + 1, e) || isPalindrome(b, e - 1);
    }
};
```

### 方案 3

#### 思路

- 现在思考扩展问题: `最多可以删除 k 个字符时判断能否成回文`
- 当 k 比较小的时候(<10)
  - 此时我们还可以继续利用方案 1, 可以把 flag 改成一个计数器, 初始化为 k, 每次删除一个字符后计数器减 1, 直到计数器为 0 就不能删除了.
  - 但是如果 k 比较大的时候这种方案就不可行了, 因为每次计数-1 时都要考虑左或者右指针移动的情况, 即有 2 种选择, 总共会有 2^k 个选择
- 当 k 比较大的时候
  - 这时候的瓶颈在 k 而不在 n, 那我们完全可以换一种思路.
  - 从原字符串删除某些字符, 其实等价于找原字符串的子序列
  - 那么如果我们能**找出原始字符串的最长回文子序列**, 然后比较原始长度 n 和它的长度之差是否小于等于 k, 是的话则说明可以删除两者长度差个字符使得生成的字符串子序列回文
  - 找最长回文子序列可以参考[516. 最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/), 属于经典的二维动态规划问题, 大致思路就是 dp[b,e]表示[b,e]区间内的最长回文子序列, 转移方程根据 s[b]和 s[e]是否相等分两种情况, 此处就不赘述了, 有兴趣的同学可以自己尝试写下代码~

#### 复杂度

- 时间复杂度
  - 如果继续利用方案 1 的思路, 每次对计数器 -1 时都要进行 2 次选择, 总共就是 2^k 次选择, 每次判断回文要 O(N), 所以总的时间复杂度是 O(N\*2^k)
  - 如果利用最长回文子序列的思路, 则二维动态规划需要两重循环, 总的时间复杂度是 O(N^2)
- 空间复杂度
  - 方案 1 思路仍然为 O(N), 递归的栈的消耗
  - 最长回文子序列的思路是 O(N^2), dp 数组的消耗

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的简书](https://www.jianshu.com/u/3a17f1fdfd67)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
