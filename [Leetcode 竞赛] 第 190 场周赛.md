> [竞赛链接](https://leetcode-cn.com/contest/weekly-contest-190/)

> 新鲜出炉今天的周赛, 这场周赛难度没有很大, 但是当时做的时候第二题都错了两次, 直接影响了后面两题的心态..最后排到 200 多名, 只能拿参与奖了 😥 不过还是要赞下新功能一键填入示例, 终于不需要每次手动把测试用例输入进去了, 懒人福音啊..

# [1455] 检查单词是否为句中其他单词的前缀

> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/contest/weekly-contest-190/problems/check-if-a-word-occurs-as-a-prefix-of-any-word-in-a-sentence/)

## 题目描述

给你一个字符串 sentence 作为句子并指定检索词为 searchWord ，其中句子由若干用 单个空格 分隔的单词组成。

请你检查检索词 searchWord 是否为句子 sentence 中任意单词的前缀。

- 如果 searchWord 是某一个单词的前缀，则返回句子 sentence 中该单词所对应的下标（下标从 1 开始）。
- 如果 searchWord 是多个单词的前缀，则返回匹配的第一个单词的下标（最小下标）。
- 如果 searchWord 不是任何单词的前缀，则返回 -1 。
  字符串 S 的 「前缀」是 S 的任何前导连续子字符串。

- 1 <= sentence.length <= 100
- 1 <= searchWord.length <= 10
- sentence 由小写英文字母和空格组成。
- searchWord 由小写英文字母组成。
- 前缀就是紧密附着于词根的语素，中间不能插入其它成分，并且它的位置是固定的——-位于词根之前。（引用自 前缀\_百度百科 ）

## 题目样例

### 示例 1

#### 输入

sentence = "i love eating burger", searchWord = "burg"

#### 输出

4

#### 解释

"burg" 是 "burger" 的前缀，而 "burger" 是句子中第 4 个单词。

### 示例 2

#### 输入

sentence = "this problem is an easy problem", searchWord = "pro"

#### 输出

2

#### 解释

"pro" 是 "problem" 的前缀，而 "problem" 是句子中第 2 个也是第 6 个单词，但是应该返回最小下标 2 。

### 示例 3

#### 输入

sentence = "i am tired", searchWord = "you"

#### 输出

0

## 题目思考

1. 题目很直白, 直接遍历匹配就行了吧..

## 解决方案

### 思路

- 遍历数组, 检查当前字符串的前缀是目标字符串即可

### 复杂度

- 时间复杂度 O(NM): N 表示单词个数, M 表示搜索字符串长度, 对于每个单词需要匹配一遍搜索字符串, 所以复杂度是 O(NM)
- 空间复杂度 O(1) 或 O(N): 如果利用 split 转成字符串列表就是 O(N), 如果直接对字符串本身进行处理就是 O(1). 下面的代码中用的是 O(N)方法, 更简洁一些, 感兴趣的同学可以自己试试 O(1)空间的做法, 就是碰到空格后重置下标即可

### 代码

#### Python 3

```python
class Solution:
    def isPrefixOfWord(self, sentence: str, searchWord: str) -> int:
        a = sentence.split()
        res = -1
        for j, w in enumerate(a):
            if len(w) < len(searchWord):
                # 长度小于搜索字符串, 直接不满足要求
                continue
            for i in range(len(searchWord)):
                if w[i] != searchWord[i]:
                    # 某一位不匹配了, 直接break
                    break
            else:
                # 都匹配, 就是要求的结果
                # 注意要求的下标是从1开始的
                return j + 1
        return res
```

#### C++

```cpp
class Solution {
public:
    int isPrefixOfWord(string sentence, string searchWord) {
        auto split = [](const string& s, const char delim = ' ') {
            vector<string> res;
            istringstream stream(s);
            string substr;
            while(getline(stream, substr, delim)) {
                res.emplace_back(substr);
            }
            return res;
        };

        auto words = split(sentence);
        auto searchSize = searchWord.size();

        for (int i = 0; i < words.size(); ++i) {
            if (words[i].size() >= searchSize && words[i].compare(0, searchSize, searchWord) == 0) {
                return i + 1;
            }
        }

        return -1;
    }
};
```

---

# [1456] 定长子串中元音的最大数目

> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/contest/weekly-contest-190/problems/maximum-number-of-vowels-in-a-substring-of-given-length/)

## 题目描述

给你字符串 s 和整数 k 。

请返回字符串 s 中长度为 k 的单个子字符串中可能包含的最大元音字母数。

英文中的 元音字母 为（a, e, i, o, u）。

- 1 <= s.length <= 10^5
- s 由小写英文字母组成
- 1 <= k <= s.length

## 题目样例

### 示例 1

#### 输入

s = "abciiidef", k = 3

#### 输出

3

#### 解释

子字符串 "iii" 包含 3 个元音字母。

### 示例 2

#### 输入

s = "aeiou", k = 2

#### 输出

2

#### 解释

任意长度为 2 的子字符串都包含 2 个元音字母。

### 示例 3

#### 输入

s = "leetcode", k = 3

#### 输出

2

## 题目思考

1. 固定长度为 k 的字符串, 你想到了什么方法?

## 解决方案

### 思路

- 滑动窗口
- 固定一个长度为 k 的窗口, 以及窗口内元音字母的数目
- 然后窗口每次向右滑动 1 格, 更新元音字母数目, 以及结果即可
- 特别注意边界条件和初始的滑动过程, 我当时做的时候就是在窗口初始化之后, 错误地让左边界从 1 开始移除, 结果错了两次的..

### 复杂度

- 时间复杂度 O(N): 每个字符只需要遍历两次
- 空间复杂度 O(1): 只使用了几个变量和常数空间的集合

### 代码

#### Python 3

```python
class Solution:
    def maxVowels(self, s: str, k: int) -> int:
        v = {'a', 'e', 'i', 'o', 'u'}
        res = 0
        vcnt = 0
        for i in range(k):
            if s[i] in v:
                # 先初始化计算[0,k]窗口的元音字母个数
                vcnt += 1
        res = vcnt
        for b in range(len(s)):
            e = b + k
            if e >= len(s):
                break
            if s[e] in v:
                # 更新新的右边界的值
                vcnt += 1
            if s[b] in v:
                # 去除旧的左边界的值
                vcnt -= 1
            res = max(res, vcnt)
        return res
```

#### C++

```cpp
class Solution {
public:
    int maxVowels(string s, int k) {
        vector<int> isVowel(s.size(), 0);
        unordered_set<char> d = {'a', 'e', 'i', 'o', 'u'};

        for (int i = 0; i < s.size(); ++i) {
            if (d.find(s[i]) != d.end()) {
                isVowel[i] = 1;
            }
        }

        if (s.size() <= k) {
            return accumulate(isVowel.begin(), isVowel.end(), 0);
        }

        int num = accumulate(isVowel.begin(), isVowel.begin() + k, 0);
        int res = num;

        for (int i = k; i < s.size(); ++i) {
            num += isVowel[i] - isVowel[i - k];
            res = max(res, num);
        }

        return res;
    }
};
```

---

# [1457] 二叉树中的伪回文路径

> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/contest/weekly-contest-190/problems/pseudo-palindromic-paths-in-a-binary-tree/)

## 题目描述

给你一棵二叉树，每个节点的值为 1 到 9 。我们称二叉树中的一条路径是 「伪回文」的，当它满足：路径经过的所有节点值的排列中，存在一个回文序列。

请你返回从根到叶子节点的所有路径中 伪回文 路径的数目。

- 给定二叉树的节点数目在 1 到 10^5 之间。
- 节点值在 1 到 9 之间。

## 题目样例

### 示例 1

#### 输入

root = [2,3,1,3,1,null,1]

![示例图](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/05/23/palindromic_paths_1.png)

#### 输出

2

#### 解释

- 上图为给定的二叉树。总共有 3 条从根到叶子的路径：红色路径 [2,3,3] ，绿色路径 [2,1,1] 和路径 [2,3,1] 。
- 在这些路径中，只有红色和绿色的路径是伪回文路径，因为红色路径 [2,3,3] 存在回文排列 [3,2,3] ，绿色路径 [2,1,1] 存在回文排列 [1,2,1] 。

### 示例 2

#### 输入

root = [2,1,1,1,3,null,null,null,null,null,1]

![示例图](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/05/23/palindromic_paths_2.png)

#### 输出

1

#### 解释

- 上图为给定二叉树。总共有 3 条从根到叶子的路径：绿色路径 [2,1,1] ，路径 [2,1,3,1] 和路径 [2,1] 。
- 这些路径中只有绿色路径是伪回文路径，因为 [2,1,1] 存在回文排列 [1,2,1] 。

### 示例 3

#### 输入

[9]

#### 输出

1

## 题目思考

1. 先看数据规模, 数量是 10^5, O(N^2)算法很可能超时
2. 分析题目, 回文序列有哪些性质, 如何利用这一点?
3. 注意到每个节点的值只有 1~9, 你能想到什么优化思路吗?

## 解决方案

### 方案 1

#### 思路

- 根据回文序列性质, 其中的每个字符的个数是有要求的, 最多只能有 1 个字符是奇数个(放在最中间), 其他字符都必须是偶数个. 当然也可以所有字符都是偶数个, 即对称中心是两字符的中间, 而不是某个字符本身.
- 利用这一点, 我们可以 dfs 遍历, 然后遍历过程中维护一个计数字典, key 是节点值, value 是对应的个数
- 然后遍历到叶子节点时, 统计当前计数字典是否满足回文序列的要求即可
- 可以额外增加一个变量 oddcnt 代表计数字典中数目为奇数的元素个数, 加速运算, 不需要每次在叶子节点处遍历所有计数字典统计奇数数目 key 的个数了
- 进一步优化: 可以将上面的计数字典和 oddcnt 转换成一个集合, 只存数目为奇数的元素即可, 然后每次该元素数目变化时, 就根据其新的个数将其从集合移入或移除

#### 复杂度

- 时间复杂度 O(N): 每个节点只需要遍历一遍
- 空间复杂度 O(N): 递归的栈的消耗, 集合只使用了常数个元素, 因为节点的值是 1~9

#### 代码

##### Python 3

```python
class Solution:
    def pseudoPalindromicPaths(self, root: TreeNode) -> int:
        self.res = 0
        if not root:
            return 0

        # 把集合移入和移出操作提取出来, 简化代码
        def changeset(oddsets, v):
            if v in oddsets:
                oddsets.remove(v)
            else:
                oddsets.add(v)

        def dfs(node, oddsets):
            if not node.left and not node.right:
                # 叶子节点, 且奇数个数的元素数目不大于1就是满足条件的路径
                if len(oddsets) <= 1:
                    self.res += 1
                return
            if node.left:
                # 注意每次改变集合状态后, 在dfs遍历完要恢复成原始状态, 避免对后面的遍历产生影响, 下同
                changeset(oddsets, node.left.val)
                dfs(node.left, oddsets)
                changeset(oddsets, node.left.val)
            if node.right:
                changeset(oddsets, node.right.val)
                dfs(node.right, oddsets)
                changeset(oddsets, node.right.val)

        dfs(root, {root.val})
        return self.res
```

##### C++

```cpp
class Solution {
public:
    int pseudoPalindromicPaths (TreeNode* root) {
        int res = 0;
        unordered_set<int> oddsets;

        auto changeset = [&oddsets](int val) {
            if (oddsets.find(val) == oddsets.end()) {
                oddsets.insert(val);
            } else {
                oddsets.erase(val);
            }
        };

        function<void(TreeNode*)> dfs = [&](TreeNode* root) {
            if (!root) {
                return;
            }

            changeset(root->val);

            if (root->left || root->right) {
                dfs(root->left);
                dfs(root->right);
            } else {
                if (oddsets.size() <= 1) {
                    ++res;
                }
            }

            changeset(root->val);
        };

        dfs(root);

        return res;
    }
};
```

### 方案 2

#### 思路

- 回顾方案 1, 我们使用了一个集合来标记奇数数目元素
- 根据节点取值范围, 以及上面的 changeset 操作, 我们是不是也可以用位运算?
- 我们只需要取一个 10 位的数, 然后 changeset 就对应异或的过程, 这样相比方案 1 代码会简化很多
- 通常来说, 如果状态数不多的时候, 我们总是可以尝试把字典或者集合转成位运算, 这样时间和空间都会节省不少

#### 复杂度

- 时间复杂度 O(N): 每个节点只需要遍历两遍
- 空间复杂度 O(N): 递归的栈的消耗

#### 代码

##### Python 3

```python
class Solution:
    def pseudoPalindromicPaths(self, root: TreeNode) -> int:
        self.res = 0
        if not root:
            return 0

        def get1bit(n):
            # 计算一个数字1的位数
            res = 0
            while n:
                n &= n - 1
                res += 1
            return res

        def dfs(node, mask):
            if not node.left and not node.right:
                # 叶子节点, 且奇数个数的元素数目不大于1就是满足条件的路径
                if get1bit(mask) <= 1:
                    self.res += 1
                return
            if node.left:
                dfs(node.left, mask ^ (1 << node.left.val))
            if node.right:
                dfs(node.right, mask ^ (1 << node.right.val))

        dfs(root, 1 << root.val)
        return self.res
```

##### C++

```cpp
class Solution {
public:
    int pseudoPalindromicPaths (TreeNode* root) {
        int res = 0;

        auto countBits = [](int value) {
            int count = 0;

            while (value) {
                ++count;
                value &= value - 1;
            }

            return count;
        };

        function<void(TreeNode*, int)> countPath = [&](TreeNode* root, int mask) {
            if (!root) {
                return;
            }

            mask ^= 1 << (root->val);

            if (root->left || root->right) {
                countPath(root->left, mask);
                countPath(root->right, mask);
            } else if (countBits(mask) <= 1) {
                ++res;
            }
        };

        countPath(root, 0);

        return res;
    }
};
```

---

# [1458] 两个子序列的最大点积

> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/contest/weekly-contest-190/problems/max-dot-product-of-two-subsequences/)

## 题目描述

给你两个数组 nums1 和 nums2 。

请你返回 nums1 和 nums2 中两个长度相同的 非空 子序列的最大点积。

数组的非空子序列是通过删除原数组中某些元素（可能一个也不删除）后剩余数字组成的序列，但不能改变数字间相对顺序。比方说，[2,3,5] 是 [1,2,3,4,5] 的一个子序列而 [1,5,3] 不是。

- 1 <= nums1.length, nums2.length <= 500
- -1000 <= nums1[i], nums2[i] <= 100

定义 a = [a1, a2,…, an] 和 b = [b1, b2,…, bn] 的点积为：![](https://upload.wikimedia.org/math/c/3/2/c329bf86e747d74f55ed2e17c36fd83f.png)

## 题目样例

### 示例 1

#### 输入

nums1 = [2,1,-2,5], nums2 = [3,0,-6]

#### 输出

18

#### 解释

从 nums1 中得到子序列 [2,-2] ，从 nums2 中得到子序列 [3,-6] 。
它们的点积为 (2\*3 + (-2)\*(-6)) = 18 。

### 示例 2

#### 输入

nums1 = [3,-2], nums2 = [2,-6,7]

#### 输出

21

#### 解释

从 nums1 中得到子序列 [3] ，从 nums2 中得到子序列 [7] 。
它们的点积为 (3\*7) = 21 。

### 示例 3

#### 输入

nums1 = [-1,-1], nums2 = [1,1]

#### 输出

-1

## 题目思考

1. 观察数据规模, O(N^2)复杂度肯定可以, O(N^3)复杂度不太确定
2. 可以尝试动态规划的思路吗?
3. 如何进行预处理?

## 解决方案

### 思路

- 这种求子序列的问题可以先尝试能否用动态规划解决, 此题也是可以的, 不过需要一些优化
- 设 dp[i,j]为使用 nums1 的第 i 个元素和 nums2 的第 j 个元素所能得到的最大序列
- 那么不难看出: `dp[i,j] = max(nums1[i]*nums2[j], nums1[i]*nums2[j]+mx)`, mx 是 `max(dp[ii,jj]) (ii<i 且 jj<j)`, 可能为负数, 这时候就只用 i 和 j 自身的乘积了
- 如果我们不做任何优化的话, 也即每次计算 dp[i,j]的时候都计算一遍 mx, 这个复杂度就达到了 O(NMNM), 显然是不能接受的, 所以我们必须进行预处理
- 注意到 mx 的下标取值范围是小于当前 i 和 j 的所有组合, 所以我们完全可以利用这一点
- 额外引入两个数组 premx 和 curmx, 其长度为 nums2 的长度, premx[j]和 curmx[j]分别表示 i-1,j 及之前的最大 dp 值, 以及当前正在计算的 i,j 及之前的最大 dp 值
- 那么自然上面的 mx 就是 premx[j-1]了
- 而 curmx[j] 则可以根据当前的 dp 值以及上一个 curmx[j-1]进行更新
- 然后每次循环到新的 i 时就将 curmx 的值复制到 premx 即可
- 这样我们就做到了只需要两重循环计算出结果

### 复杂度

- 时间复杂度 O(MN): 只需要二重遍历, M 和 N 分别表示 nums1 和 nums2 的长度
- 空间复杂度 O(MN): 使用了大小为 MN 的 dp 数组

### 代码

#### Python 3

```python
class Solution:
    def maxDotProduct(self, nums1: List[int], nums2: List[int]) -> int:
        dp = collections.defaultdict(int)
        n1, n2 = len(nums1), len(nums2)
        res = -float('inf')
        premx = [-float('inf')] * n2
        curmx = [-float('inf')] * n2
        for i in range(n1):
            # 复制premx的值
            premx = curmx[::]
            for j in range(n2):
                v = nums1[i] * nums2[j]
                # dp转移公式, 注意考虑j-1<0和premx[j - 1] < 0的特殊情况
                dp[i,
                   j] = v if j - 1 < 0 or premx[j - 1] < 0 else v + premx[j -
                                                                          1]
                res = max(res, dp[i, j])
                # 更新curmx
                curmx[j] = max(curmx[j], dp[i, j])
                if j - 1 >= 0:
                    curmx[j] = max(curmx[j], curmx[j - 1])
        return res
```

#### C++

```cpp
class Solution {
public:
    int maxDotProduct(vector<int>& nums1, vector<int>& nums2) {
        int res = INT_MIN;
        int n1 = nums1.size();
        int n2 = nums2.size();
        vector<int> curmx(n2, INT_MIN);

        for (int i = 0; i < n1; ++i) {
            auto premx = curmx;
            for (int j = 0; j < n2; ++j) {
                int value = nums1[i] * nums2[j];
                if (j > 0 && premx[j - 1] > 0) {
                    value += premx[j - 1];
                }
                res = max(res, value);
                curmx[j] = max(curmx[j], value);
                if (j > 0) {
                    curmx[j] = max(curmx[j], curmx[j - 1]);
                }
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
