> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/number-of-submatrices-that-sum-to-target/)

> 今天我们来挑战一下, 做道困难题吧~ 我觉得大家如果把这道题彻底弄懂的话, 大部分跟前缀和相关的问题就都能迎刃而解了

## 题目描述

给出矩阵  matrix  和目标值  target，返回元素总和等于目标值的非空子矩阵的数量。

子矩阵  x1, y1, x2, y2  是满足 x1 <= x <= x2  且  y1 <= y <= y2  的所有单元  matrix[x][y]  的集合。

如果  (x1, y1, x2, y2) 和  (x1', y1', x2', y2')  两个子矩阵中部分坐标不同（如：x1 != x1'），那么这两个子矩阵也不同。

- 1 <= matrix.length <= 300
- 1 <= matrix[0].length <= 300
- -1000 <= matrix[i] <= 1000
- -10^8 <= target <= 10^8

## 题目样例

### 示例 1

#### 输入

matrix = [[0,1,0],[1,1,1],[0,1,0]], target = 0

#### 输出

4

#### 解释

四个只含 0 的 1x1 子矩阵。

### 示例 2

#### 输入

matrix = [[1,-1],[-1,1]], target = 0

#### 输出

5

#### 解释

两个 1x2 子矩阵，加上两个 2x1 子矩阵，再加上一个 2x2 子矩阵。

## 题目思考

1. 按惯例先看数据规模, 从规模上看 O(R^2\*C^2)的算法大概率会超时, 因为最大数据量已经是 9W\*9W 了
2. 可以进行哪些预处理?

## 解决方案

### 方案 1

#### 思路

- 最 naive 的方法, 根据本题描述, 很容易联想到数组的前缀和, 只是把一维数组换成了二维矩阵而已
- 如果我们使用一个前缀和字典 d, d[r,c]存左上角到(r,c)的矩阵和, 那么任意一个子矩阵[(sr,sc),(er,ec)]的和都可以求出了
- 大家可以画个图来理解, 子矩阵[(sr,sc),(er,ec)]是子矩阵[(0,0),(er,ec)]的右下角矩形, 想要求出它, 我们只需要根据[(0,0),(er,ec)]的和, 减去上半部分的矩形, 再减去左半部分的矩形, 最后加上左上角被重复减了两次的矩形即可
- 具体就是`sum((sr,sc),(er,ec)) = d[er,ec]-d[sr,ec]-d[er,sc]+d[sr,sc]`
- 至于如何求前缀和字典 d 本身, 也可以利用同样的思路: `d[r,c] = matrix[r][c]+d[r-1,c]+d[r,c-1]-d[r-1,c-1]`
- **注意: 因为复杂度过高, 会 TLE, 所以这个方案只是作为推导下面更优方案的基础. 代码也就不贴了, 相信大家都能写出来~**

#### 复杂度

- 时间复杂度 O(R\*C\*R\*C): 预处理需要 O(R\*C), 计算结果时需要四重循环, 固定起点 rc 以及终点 rc, 所以总时间复杂度就是 O(R\*C\*R\*C)
- 空间复杂度 O(R\*C): 需要 1 个大小为 R\*C 的字典, 用于存放前缀和

### 方案 2

#### 思路

- 回顾方案 1, 我们是统计了所有坐标的前缀和, 虽然预处理的时间复杂度还可以, 但是计算结果时只能暴力 4 重循环实在太慢了
- 如何进行优化? 是否可以适用于一维数组的一些方法?
- 根据上面思考, 我们可以考虑把原来存在前缀和字典的东西给变换一下, 以行或者列为单位, 只存该行或者列从开头到当前元素的前缀和, 相当于把二维矩阵压缩成一维的数组
- 这样计算的时候就可以利用子数组和的求法, 二重循环外再接一个 O(N)的循环即可
- 举个例子, 假设我们以列为单位求前缀和, `colsm[r,c]`表示第 r 行中到第 c 个元素的和, 那么 `colsm[r,x] = matrix[r][0]+matrix[r][1]+...+matrix[r][x]`
- 然后我们固定行的起点 sr 和终点 er, 然后从 0 开始遍历列, 计算 `matrix[er][c]-matrix[sr-1][c]`的前缀和, 相当于把一列列拼成一个子矩阵, 二维变一维数组, 这个过程是不是就很熟悉了?
- 对的! 这个就是经典子数组和问题了: [560. 和为 K 的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/), 这个问题我之前也写过详细解析, 感兴趣的同学关注我的公众号:**每日精选算法题**, 然后回复*子数组和*就能看到啦~~
- 此时我们只需要一个计数字典, 以当前前缀和作为 key, value 是当前前缀和的个数, 如果当前前缀和-target 在字典中, 就在结果中加上对应的计数值, 因为对应的每个中间矩形的和都是 target, 同时不要忘了当前前缀和本身就是 target 的情况, 这时候直接结果+1 即可

#### 复杂度

- 时间复杂度 O(R\*C\*min(R,C)): 预处理需要 O(R\*C), 统计前缀和的时候可以从左向右统计(使用起点和终点 R, 两个 R 循环), 也可以从上往下统计(使用起点和终点 C, 两个 C 循环), 所以优化算法是 O(R\*C\*min(R,C)), 此处代码没有使用此优化, 只演示了从左向右统计, 所以代码复杂度是 O(R\*R\*C)
- 空间复杂度 O(R\*C): 需要一个大小为 R\*C 的字典, 用于存放前缀和

#### 代码

##### Python 3

```python
import collections


class Solution:
    def numSubmatrixSumTarget(self, matrix: List[List[int]],
                              target: int) -> int:
        rows, cols = len(matrix), len(matrix[0])
        colsm = [[0] * cols for r in range(rows)]
        for c in range(cols):
            cur = 0
            for r in range(rows):
                cur += matrix[r][c]
                colsm[r][c] = cur
        res = 0
        for sr in range(rows):
            for er in range(sr, rows):
                d = collections.defaultdict(int)
                cur = 0
                for c in range(cols):
                    # 注意此处减去的是colsm[sr - 1][c], 因为要把起点行sr统计在内
                    pre = 0 if sr - 1 < 0 else colsm[sr - 1][c]
                    cur += colsm[er][c] - pre
                    if cur == target:
                        # cur本身就是target的特殊情况
                        res += 1
                    res += d[cur - target]
                    d[cur] += 1
        return res
```

##### C++

```cpp
class Solution {
public:
    int numSubmatrixSumTarget(vector<vector<int>> &matrix, int target) {
        int rows = matrix.size();
        int cols = matrix[0].size();

        vector<vector<int>> colsm(rows, vector<int>(cols, 0));

        for (int c = 0; c < cols; ++c) {
            int total = 0;
            for (int r = 0; r < rows; ++r) {
                total += matrix[r][c];
                colsm[r][c] = total;
            }
        }

        int res = 0;

        for (int sr = 0; sr < rows; ++sr) {
            for (int er = sr; er < rows; ++er) {
                int total = 0;
                unordered_map<int, int> d;
                for (int c = 0; c < cols; ++c) {
                    int pre = sr < 1 ? 0 : colsm[sr - 1][c];
                    total += colsm[er][c] - pre;
                    if (total == target) {
                        ++res;
                    }
                    if(d.count(total - target)) {
                        res += d[total - target];
                    }
                    d[total] += 1;
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
