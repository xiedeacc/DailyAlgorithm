> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/check-if-a-string-can-break-another-string/description/)

> 这道题难度适中, 有不止一种思路, 个人感觉质量不错, 特别是对算法优化的过程, 值得一做

## 题目描述

给你两个字符串  s1  和  s2 ，它们长度相等，请你检查是否存在一个  s1  的排列可以打破 s2  的一个排列，或者是否存在一个  s2  的排列可以打破 s1 的一个排列。

字符串  x  可以打破字符串  y （两者长度都为  n ）需满足对于所有  i（在  0  到  n - 1  之间）都有  x[i] >= y[i]（字典序意义下的顺序）。

- s1.length == n
- s2.length == n
- 1 <= n <= 10^5
- 所有字符串都只包含小写英文字母。

## 题目样例

### 示例 1

#### 输入

s1 = "abc", s2 = "xya"

#### 输出

true

#### 解释

"ayx" 是 s2="xya" 的一个排列，"abc" 是字符串 s1="abc" 的一个排列，且 "ayx" 可以打破 "abc" 。

### 示例 2

#### 输入

s1 = "abe", s2 = "acd"

#### 输出

false

#### 解释

- s1="abe" 的所有排列包括："abe"，"aeb"，"bae"，"bea"，"eab" 和 "eba"
- s2="acd" 的所有排列包括："acd"，"adc"，"cad"，"cda"，"dac" 和 "dca"
- 然而没有任何 s1 的排列可以打破 s2 的排列。也没有 s2 的排列能打破 s1 的排列。

## 题目思考

1. 根据数据规模, 这道题显然不能暴力得到其中一个字符串的全排列然后跟另一个字符串比较, 那样的话长度几十的字符串都会很慢了
2. 那可不可以进行一些预处理?
3. 要想 s1 打破 s2, 只要要求 s1 的字符大于等于 s2 的相同位置的字符即可, 有没有什么直觉思考呢?

## 解决方案

### 方案 1

#### 思路

- 直观想法(贪心)
  - 因为题目可以使用任意排列, 所以可以先预处理得到 s1 的字符计数字典
  - 然后对于 s2 的每个位置的字符而言, **优先使用 s1 中存在的大于等于该字符且最接近该字符的字符即可**, 使用之后计数-1 (这种贪心的做法能保证 s2 的每个字符都不能被 s1 的更小字符所打破了, 如果这样都找不到解那么一定不存在解)
  - 直到遍历完成(存在打破的方案), 或者找不到满足条件的 s1 字符(不存在打破方案)
- 然后如果 s1 不能打破 s2 的话, 交换它们看看 s2 能不能打破 s1

#### 复杂度

- 时间复杂度 O(N): 预处理字符计数字典需要 O(N), 遍历的时候需要检查当前字符之后的每个字符, 因为都是小写字符, 所以是 O(26\*N), 一共两次遍历, 所以总时间是 O(2\*(N+26N)) = O(N). 另外 C++的话可以使用 vector 来代替字典, 会有一些运行时间上的效率提升
- 空间复杂度 O(1): 使用了一个计数字典, 但由于字符数目是常数(26), 所以空间是 O(1)

#### 代码

##### Python 3

```python
import collections

class Solution:
    def checkIfCanBreak(self, s1: str, s2: str) -> bool:
        def check(x, y):
            # 固定y, 对x求字符字典, 看是否能打破
            cnt = collections.defaultdict(int)
            for c in x:
                cnt[c] += 1
            for c in y:
                for o in range(ord(c), ord('z') + 1):
                    cc = chr(o)
                    if cnt[cc] > 0:
                        cnt[cc] -= 1
                        break
                else:
                    return False
            return True

        return check(s1, s2) or check(s2, s1)
```

##### C++

```cpp
class Solution {
public:
    bool checkIfCanBreak(string s1, string s2) {
        auto checker = [](const string& s1, const string& s2) {
            vector<int> count(26, 0);
            for (auto c : s1) {
                ++count[c - 'a'];
            }
            for (auto c : s2) {
                bool found = false;
                for (char i = c - 'a'; i < 26; ++i) {
                    if (count[i] > 0) {
                        found = true;
                        --count[i];
                        break;
                    }
                }
                if (!found) {
                    return false;
                }
            }
            return true;
        };

        return checker(s1, s2) || checker(s2, s1);
    }
};
```

### 方案 2

#### 思路

- 回顾方案 1, 我们是固定一个字符串, 然后用另一个字符串来比较的
- 但是题目里说的是全排列, 意味着两个字符串的顺序都是可以任意交换的
- 所以我们是否可以直接预处理两个字符串, 得到两个字符计数字典后再比较呢?
- 根据上述分析, 可得方案如下
  - 维护两个 flag 表示 s1 能否打破 s2 和 s2 能否打破 s1, 以及一个变量表示当前为止 s1 的字符数与 s2 的字符数的差值
  - 得到两个字符字典后, 按字符从小到大遍历
  - 如果差值为正, 则说明此刻 s1 可能打破 s2, s2 不可能打破 s1 了, 把对应的 flag 置为 false
  - 反之如果差值为负, 则把 s1 打破 s2 的 flag 置为 false
  - 如果两个 flag 都是 false, 则说明两个字符串谁都不能打破谁, 直接返回 false 即可
  - 反之如果循环结束了, 说明至少一方可以打破另一方, 返回 true

#### 复杂度

- 时间复杂度 O(N): 预处理字符计数字典需要 O(N), 之后遍历字典只需要 O(26), 所以总时间复杂度是 O(N+26) = O(N), 相比方案 1 降低不少, 主要是方案 1 的常系数比较大.. 另外 C++的话可以使用 vector 来代替字典, 会有一些运行时间上的效率提升
- 空间复杂度 O(1): 只使用了几个变量, 和两个常数空间的字符计数字典

#### 代码

##### Python 3

```python
import collections

class Solution:
    def checkIfCanBreak(self, s1: str, s2: str) -> bool:
        cnts = [collections.defaultdict(int), collections.defaultdict(int)]
        for i, s in ((0, s1), (1, s2)):
            for c in s:
                cnts[i][c] += 1

        diff = 0
        s1BeatS2, s2BeatS1 = True, True
        for o in range(26):
            c = chr(ord('a') + o)
            diff += cnts[0][c] - cnts[1][c]
            if diff > 0:
                s1BeatS2 = False
            elif diff < 0:
                s2BeatS1 = False
            if not s1BeatS2 and not s2BeatS1:
                return False
        return True
```

##### C++

```cpp
class Solution {
public:
    bool checkIfCanBreak(string s1, string s2) {
        auto getCounter = [](const string& s) {
            vector<int> count(26, 0);
            for (auto c : s) {
                ++count[c - 'a'];
            }
            return count;
        };

        auto s1Count = getCounter(s1);
        auto s2Count = getCounter(s2);

        int diff = 0;
        bool s1BeatS2 = true;
        bool s2BeatS1 = true;

        for (int i = 0; i < 26; ++i) {
            diff += s1Count[i] - s2Count[i];
            if (diff > 0) {
                s1BeatS2 = false;
            } else if (diff < 0) {
                s2BeatS1 = false;
            }
            if (!s1BeatS2 && !s2BeatS1) {
                return false;
            }
        }

        return true;
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
