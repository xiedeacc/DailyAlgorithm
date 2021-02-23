> [竞赛链接](https://leetcode-cn.com/contest/biweekly-contest-26/)

> 新鲜出炉昨天的双周赛, 这场发挥超常, 最终排名 18 (可能是晚上吃了个蛋糕补充了太多能量?😂)

> 总的来说这场双周赛题目相对比较简单, 特别是前三题, 感觉难度都可以算是 easy 吧, 第四题也是经典题目的变种

# [1446] 连续字符

> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/consecutive-characters/)

## 题目描述

给你一个字符串 s ，字符串的「能量」定义为：只包含一种字符的最长非空子字符串的长度。

请你返回字符串的能量。

- 1 <= s.length <= 500
- s 只包含小写英文字母。

## 题目样例

### 示例 1

#### 输入

s = "leetcode"

#### 输出

2

#### 解释

子字符串 "ee" 长度为 2 ，只包含字符 'e' 。

### 示例 2

#### 输入

s = "abbcccddddeeeeedcba"

#### 输出

5

#### 解释

子字符串 "eeeee" 长度为 5 ，只包含字符 'e' 。

### 示例 3

#### 输入

s = "triplepillooooow"

#### 输出

5

## 题目思考

1. 需要定义哪些变量?
2. 可以一次遍历搞定吗?

## 解决方案

### 思路

- 存当前连续字符的数目 cnt, 初始化为 1, 然后从下标 1 开始遍历
- 比较当前字符和前一个字符的关系, 相等则 cnt 增加, 并更新 res, 否则重置 cnt

### 复杂度

- 时间复杂度 O(N): 需要遍历每个元素一次
- 空间复杂度 O(1): 只使用了几个变量

### 代码

#### Python 3

```python
class Solution:
    def maxPower(self, s: str) -> int:
        res = 1
        cnt = 1
        for i in range(1, len(s)):
            if s[i] == s[i - 1]:
                # 连续, 更新cnt
                cnt += 1
                res = max(res, cnt)
            else:
                # 重置cnt
                cnt = 1
        return res
```

#### C++

```cpp
class Solution {
public:
    int maxPower(string s) {
        int res = 1;
        int cnt = 1;

        for (int i = 1; i < s.size(); ++i) {
            if (s[i] == s[i-1]) {
                res = max(res, ++cnt);
            } else {
                cnt = 1;
            }
        }

        return res;
    }
};
```

---

# [1447] 最简分数

> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/simplified-fractions/)

## 题目描述

给你一个整数 n ，请你返回所有 0 到 1 之间（不包括 0 和 1）满足分母小于等于 n 的 最简 分数 。分数可以以 任意 顺序返回。

- 1 <= n <= 100

## 题目样例

### 示例 1

#### 输入

n = 2

#### 输出

["1/2"]

#### 解释

"1/2" 是唯一一个分母小于等于 2 的最简分数。

### 示例 2

#### 输入

n = 4

#### 输出

["1/2","1/3","1/4","2/3","3/4"]

#### 解释

"2/4" 不是最简分数，因为它可以化简为 "1/2" 。

### 示例 3

#### 输入

n = 1

#### 输出

[]

## 题目思考

1. 题目很直白, 数据规模又小, 直接遍历每个分母和分子可以吗?
2. 有没有什么优化手段?

## 解决方案

### 方案 1

#### 思路

- 分母为 1 是不可能的, 因为题目要求分数不能为 1
- 所以分母从 2 开始遍历到 n, 然后分子从 1 开始遍历到当前分母-1(注意分数也不能为 0)
- 根据分子和分母的 gcd 是否为 1 即可判断是否为最简分数

#### 复杂度

- 时间复杂度 O(N^2logN): 需要两重循环, 然后循环内部求 gcd 需要 log(N)
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

##### Python 3

```python
import math

class Solution:
    def simplifiedFractions(self, n: int) -> List[str]:
        res = []
        for fenmu in range(2, n + 1):
            for fenzi in range(1, fenmu):
                if math.gcd(fenzi, fenmu) == 1:
                    res.append(str(fenzi) + '/' + str(fenmu))
        return res
```

##### C++

```cpp
class Solution {
public:
    vector<string> simplifiedFractions(int n) {
        vector<string> res;

        for (int b = 2; b <= n; ++b) {
            for (int a = 1; a < b; ++a) {
                if (gcd(a, b) == 1) {
                    res.push_back(to_string(a) + '/' + to_string(b));
                }
            }
        }

        return res;
    }
};
```

### 方案 2

#### 思路

- 如果按照分母和分子都从小到大遍历的话, 每次最先遇到的都是最简形式, 因为假如不是的话一定有一个更小的分母分子组合满足条件, 已经在之前被遍历到了
- 所以可以将每个分数值存入集合中, 然后从小到大遍历并判断该分数是否已经存在即可, 没存在的话加入最终结果中, 否则不加
- 这种方案省去了求 gcd 的过程, 相对方案 1 更快一些, 缺点是需要额外的空间复杂度

#### 复杂度

- 时间复杂度 O(N^2): 需要两重循环, 遍历分母和分子
- 空间复杂度 O(N^2): 需要存 N^2 数量级的分数

#### 代码

##### Python 3

```python
class Solution:
    def simplifiedFractions(self, n: int) -> List[str]:
        res = []
        v = set()
        for fenmu in range(2, n + 1):
            for fenzi in range(1, fenmu):
                value = fenzi / fenmu
                if value not in v:
                    v.add(value)
                    res.append(str(fenzi) + '/' + str(fenmu))
        return res
```

##### C++

```cpp
class Solution
{
public:
    vector<string> simplifiedFractions(int n)
    {
        auto res = vector<string>();
        auto v = unordered_set<double>();
        for (int fenmu = 2; fenmu <= n; ++fenmu)
        {
            for (int fenzi = 1; fenzi < fenmu; ++fenzi)
            {
                double value = (double)fenzi / fenmu;
                if (!v.count(value))
                {
                    v.insert(value);
                    res.push_back(to_string(fenzi) + "/" + to_string(fenmu));
                }
            }
        }

        return res;
    }
};
```

---

# [1448] 统计二叉树中好节点的数目

> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/count-good-nodes-in-binary-tree/)

## 题目描述

给你一棵根为 root 的二叉树，请你返回二叉树中好节点的数目。

「好节点」X 定义为：从根到该节点 X 所经过的节点中，没有任何节点的值大于 X 的值。

- 二叉树中节点数目范围是 [1, 10^5] 。
- 每个节点权值的范围是 [-10^4, 10^4] 。

## 题目样例

### 示例 1

#### 输入

root = [3,1,4,3,null,1,5]

![示例图](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/05/16/test_sample_1.png)

#### 输出

4

#### 解释

图中蓝色节点为好节点。

- 根节点 (3) 永远是个好节点。
- 节点 4 -> (3,4) 是路径中的最大值。
- 节点 5 -> (3,4,5) 是路径中的最大值。
- 节点 3 -> (3,1,3) 是路径中的最大值。

### 示例 2

#### 输入

root = [3,3,null,4,2]

![示例图](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/05/16/test_sample_2.png)

#### 输出

3

#### 解释

节点 2 -> (3, 3, 2) 不是好节点，因为 "3" 比它大。

### 示例 3

#### 输入

root = [1]

#### 输出

1

## 题目思考

1. 是否可以在遍历时维护当前的最大值?

## 解决方案

### 思路

- DFS
- 每次遍历到一个节点时维护当前的最大值
  - 如果当前节点大于等于该最大值, 则是好节点
  - 否则不是
- 然后更新新的最大值, 并遍历左右子树

### 复杂度

- 时间复杂度 O(N): 只需要遍历每个节点一次
- 空间复杂度 O(N): 递归的栈的消耗

### 代码

#### Python 3

```python
class Solution:
    def goodNodes(self, root: TreeNode) -> int:
        self.res = 0

        def dfs(node, curmx):
            if not node:
                return
            if node.val >= curmx:
                self.res += 1
            curmx = max(curmx, node.val)
            dfs(node.left, curmx)
            dfs(node.right, curmx)

        dfs(root, -float('inf'))
        return self.res
```

#### C++

```cpp
class Solution {
public:
    int goodNodes(TreeNode* root) {
        int res = 0;

        function<void(TreeNode*, int)> dfs = [&](TreeNode* node, int curmx) {
            if (!node) {
                return;
            }

            if (node->val >= curmx) {
                curmx = node->val;
                ++res;
            }

            dfs(node->left, curmx);
            dfs(node->right, curmx);
        };

        // 这里是根据数据范围定的一个最小值
        dfs(root, -1e6);

        return res;
    }
};
```

---

# [1449] 数位成本和为目标值的最大数字

> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/form-largest-integer-with-digits-that-add-up-to-target/)

## 题目描述

给你一个整数数组  cost  和一个整数  target 。请你返回满足如下规则可以得到的   最大   整数：

给当前结果添加一个数位（i + 1）的成本为  cost[i] （cost  数组下标从 0 开始）。
总成本必须恰好等于  target 。
添加的数位中没有数字 0 。
由于答案可能会很大，请你以字符串形式返回。

如果按照上述要求无法得到任何整数，请你返回 "0" 。

- cost.length == 9
- 1 <= cost[i] <= 5000
- 1 <= target <= 5000

## 题目样例

### 示例 1

#### 输入

cost = [4,3,2,5,6,7,2,5,5], target = 9

#### 输出

"7772"

#### 解释

添加数位 '7' 的成本为 2 ，添加数位 '2' 的成本为 3 。所以 "7772" 的代价为 2*3+ 3*1 = 9 。 "997" 也是满足要求的数字，但 "7772" 是较大的数字。
|数字| 成本|
|---|---|
|1 | 4|
|2 | 3|
|3 | 2|
|4 | 5|
|5 | 6|
|6 | 7|
|7 | 2|
|8 | 5|
|9 | 5|

### 示例 2

#### 输入

cost = [7,6,5,5,5,6,8,7,8], target = 12

#### 输出

"85"

#### 解释

添加数位 '8' 的成本是 7 ，添加数位 '5' 的成本是 5 。"85" 的成本为 7 + 5 = 12 。

### 示例 3

#### 输入

cost = [2,4,6,2,4,6,4,4,4], target = 5

#### 输出

0

## 题目思考

1. 这道题每个数字都有 cost, 然后总 cost 要是固定值, 且要求最终结果尽可能大, 是否想到了什么类似问题?

## 解决方案

### 思路

- 动态规划, 典型的完全背包问题, 具体步骤如下
  - 设 dp[c]是总 cost 是 c 时候的最大整数字符串
  - 初始化只有 dp[0]是'', 其他 dp 值都是 None, 代表不可能存在的情况(因为最终结果是要求总花费**恰好**等于 target)
  - 因为每个数位可以无限次使用, 那么对于每个数位 i 而言, 遍历所有可能的花费 c(即从 cost[i-1]一直到 target)
  - 如果遍历到的花费减去 cost[i-1]是一个有效值的话(即不是 None), 新的字符串就是 str(i)+dp[c-cost[i-1]], 如果新的字符串比当前的 dp[c]的值更大(dp[c]也可能是 None, 需要注意这种情况)时, 就更新它
  - 最终求 dp[target]即可, 如果它是 None 就表明没有组合能恰好凑得总花费为 target, 就返回'0'
- 最终 python 3 只需要 8 行代码即可搞定~

### 复杂度

- 时间复杂度 O(N): 对于每个数位, 需要遍历 N 个值, 由于数位的数目是常数 9, 所以复杂度为 O(9N) = O(N)
- 空间复杂度 O(N): 需要使用长度为 N 的 dp 数组

### 代码

#### Python 3

```python
class Solution:
    def largestNumber(self, cost: List[int], target: int) -> str:
        dp = [''] + [None] * target
        for i in range(1, len(cost) + 1):
            for v in range(cost[i - 1], target + 1):
                if dp[v - cost[i - 1]] is not None:
                    newval = str(i) + dp[v - cost[i - 1]]
                    if dp[v] is None or int(newval) > int(dp[v]):
                        dp[v] = newval
        return '0' if dp[target] is None else dp[target]
```

#### C++

```cpp
class Solution {
public:
    string largestNumber(vector<int>& cost, int target) {
        vector<string> dp(target + 1, "NAN");
        dp[0] = "";

        // 将字符串转成整数会溢出，因此特别写需要一个比较大小的函数
        auto largeThan = [](string& s1, string& s2) {
            if (s1.size() > s2.size()) {
                return true;
            }

            if (s1.size() < s2.size()) {
                return false;
            }

            return s1 > s2;
        };

        for (int i = 1; i <= cost.size(); ++i) {
            for (int v = cost[i - 1]; v <= target; ++v) {
                if (dp[v - cost[i - 1]] != "NAN") {
                    string newVal = to_string(i) + dp[v - cost[i - 1]];
                    if (dp[v] == "NAN" || largeThan(newVal, dp[v])) {
                        dp[v] = newVal;
                    }
                }
            }
        }

        return dp[target] == "NAN" ? string("0") : dp[target];
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
