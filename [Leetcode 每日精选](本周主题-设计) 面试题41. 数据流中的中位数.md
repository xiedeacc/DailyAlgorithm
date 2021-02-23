> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)

> 今天我们来做一道经典设计题, 掌握了这道题的思路后可以解决很多类似的中位数问题了, 比如 [4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)
> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。

例如，

- [2,3,4]  的中位数是 3

- [2,3] 的中位数是 (2 + 3) / 2 = 2.5

设计一个支持以下两种操作的数据结构：

- void addNum(int num) - 从数据流中添加一个整数到数据结构中。
- double findMedian() - 返回目前所有元素的中位数。

- 最多会对 addNum、findMedia 进行 50000 次调用。

## 题目样例

### 示例 1

#### 输入

s = "abaccdeff"

#### 输出

"b"

### 示例 2

#### 输入

`["MedianFinder","addNum","addNum","findMedian","addNum","findMedian"]`
`[[],[1],[2],[],[3],[]]`

#### 输出

`[null,null,2.00000,null,2.50000]`

## 题目思考

1. 先观察数据规模, 会有 5W 次调用, 那么意味着每次调用的平均时间不能超过 O(N), 不然就需要 5W\*5W 的数量级
2. 有什么数据结构可以做到每次调用的时间复杂度小于 O(N)呢

## 解决方案

### 思路

- 根据中位数性质, 它要么是偶数长度数组的两个中间值的平均数, 要么是奇数长度数组的最中间的值
- 考虑到每次调用复杂度要小于 O(N), 显然 O(1)是不可能的, 我们没办法只根据当前插入的值直接判断更新后的中位值是什么, 必须通过一些扫描和判断
- 那么我们尝试 O(logN)复杂度的数据结构, 注意到中间值左边的部分一定是小于中间值的, 而右边的部分一定是大于中间值的, 是有一定的有序性的
- 我们可以利用这一点, 构造两个堆
  - 左边是一个大顶堆, 存放所有小于等于中间值的数(奇数长度的话, 堆顶就是中间值)
  - 右边是一个小顶堆, 存放所有大于等于中间值的数(因为可能有很多重复元素)
- 查询中位数
  - 当前元素个数为奇数时, 直接返回左堆顶
  - 当前元素个数为偶数时, 返回左堆顶和右堆顶的平均值
- 插入新元素
  - 如果当前左堆和右堆个数相同, 那么左堆需要增加一个元素
    - 具体是增加新元素还是右堆顶, 则要看当前 num 和右堆顶的大小
    - 当前 num 更小的话直接插入左边即可
    - 否则就要先把右堆顶先挪到左堆, 然后右堆再插入新元素
  - 如果当前左堆比右堆多 1, 那么右堆需要增加一个元素
    - 具体是增加新元素还是左堆顶, 则要看当前 num 和左堆顶的大小
    - 当前 num 更大的话直接插入右边即可
    - 否则就要先把左堆顶先挪到右堆, 然后左堆再插入新元素
- 注意代码中实现了两个版本, 都有详细的注释. 一个是系统内置堆/优先队列, 更加简洁, 一个是自定义堆, 方便大家复习堆的构造~

### 复杂度

- 时间复杂度 O(logN): 每次调用只需要常数次堆操作, 复杂度为 O(logN)
- 空间复杂度 O(N): 需要存 N 个元素

### 代码

#### 方法 1 - 使用内置优先队列/堆

##### Python 3

```python
import heapq

class MedianFinder:
    def __init__(self):
        """
        initialize your data structure here.
        """
        # 使用两个堆, 左边大顶堆, 右边小顶堆
        # 注意python内置的heapq是小顶堆
        # 所以大顶堆的话需要将其值取相反数再插入, 也不要忘了pop的时候也要再取反回来
        self.left = []
        self.right = []

    def addNum(self, num: int) -> None:
        if len(self.left) == len(self.right):
            # 左堆需要增加一个元素
            if not self.left or num <= self.right[0]:
                # 左堆不存在, 或者新元素不大于右堆顶, 插到左边大顶堆
                heapq.heappush(self.left, -num)
            else:
                # 否则先把右堆顶插到左边, 然后右堆再插入新元素
                heapq.heappush(self.left, -heapq.heappop(self.right))
                heapq.heappush(self.right, num)
        else:
            # 右堆需要增加一个元素
            # 根据插入逻辑, 此时左堆至少有一个元素, 可以直接拿到左堆顶
            if num >= -self.left[0]:
                # 新元素不小于左堆顶, 直接插到右边小顶堆即可
                heapq.heappush(self.right, num)
            else:
                # 否则先把左堆顶插到右边, 然后左堆再插入新元素
                heapq.heappush(self.right, -heapq.heappop(self.left))
                heapq.heappush(self.left, -num)

    def findMedian(self) -> float:
        if not self.left:
            return 0
        if len(self.left) == len(self.right):
            # 偶数个元素, 取两个堆顶的平均值
            res = (-self.left[0] + self.right[0]) / 2
        else:
            # 奇数个元素, 左堆个数至少为1, 取左堆顶
            res = -self.left[0]
        return res
```

##### C++

```cpp
class MedianFinder {
public:
    /** initialize your data structure here. */
    MedianFinder() {
    }

    void addNum(int num) {
        if (left.size() == right.size()) {
            if (left.empty() || num <= -right.top()) {
                left.push(num);
            } else {
                left.push(-right.top());
                right.pop();
                right.push(-num);
            }
        } else {
            if (num >= left.top()) {
                right.push(-num);
            } else {
                right.push(-left.top());
                left.pop();
                left.push(num);
            }
        }
    }

    double findMedian() {
        if (left.empty()) {
            return 0;
        }
        if (left.size() == right.size()) {
            return 1.0 * (left.top() - right.top()) / 2;
        }

        return left.top();
    }

private:
    // 使用两个堆, 左边大顶堆, 右边小顶堆
    // priority_queue默认是大顶堆
    // 所以小顶堆的话需要将其值取相反数再插入, 也不要忘了top的时候也要再取反回来
    priority_queue<int> left;
    priority_queue<int> right;
};
```

#### 方法 2 - 自定义最大最小堆

##### Python 3

```python
class Heap:
    def __init__(self, heapType):
        # smallHeap用于标记这是一个小顶堆还是大顶堆
        self.smallHeap = True if heapType == 'SmallHeap' else False
        # 注意下标为0的元素不会被用, 此处作为占位符
        self.heap = [0]

    def __len__(self):
        return len(self.heap)

    def empty(self):
        return len(self.heap) == 1

    def push(self, v):
        # 先在堆末尾加入新元素
        self.heap.append(v)
        # 然后进行上浮操作
        cur = len(self.heap) - 1
        parent = cur >> 1
        while parent >= 1:
            if self.smallHeap and self.heap[parent] > self.heap[
                    cur] or not self.smallHeap and self.heap[
                        parent] < self.heap[cur]:
                self.heap[cur], self.heap[parent] = self.heap[
                    parent], self.heap[cur]
                cur, parent = parent, parent >> 1
            else:
                break

    def pop(self):
        res = self.heap[1]
        tail = self.heap.pop()
        if self.empty():
            return res
        # 将堆末尾元素弹出并放到堆顶
        self.heap[1] = tail
        # 然后进行下沉操作
        cur = 1
        child = 2
        while child < len(self.heap):
            if child + 1 < len(self.heap):
                if self.smallHeap and self.heap[child + 1] < self.heap[
                        child] or not self.smallHeap and self.heap[
                            child + 1] > self.heap[child]:
                    child += 1
            if self.smallHeap and self.heap[child] < self.heap[
                    cur] or not self.smallHeap and self.heap[
                        child] > self.heap[cur]:
                self.heap[cur], self.heap[child] = self.heap[child], self.heap[
                    cur]
                cur, child = child, child << 1
            else:
                break
        return res


class MedianFinder:
    def __init__(self):
        """
        initialize your data structure here.
        """
        # 使用两个堆, 左边大顶堆, 右边小顶堆
        self.left = Heap('BigHeap')
        self.right = Heap('SmallHeap')

    def addNum(self, num: int) -> None:
        if len(self.left) == len(self.right):
            # 左堆需要增加一个元素
            if self.left.empty() or num <= self.right.heap[1]:
                # 左堆不存在, 或者新元素不大于右堆顶, 插到左边大顶堆
                self.left.push(num)
            else:
                # 否则先把右堆顶插到左边, 然后右堆再插入新元素
                self.left.push(self.right.pop())
                self.right.push(num)
        else:
            # 右堆需要增加一个元素
            # 根据插入逻辑, 此时左堆至少有一个元素, 可以直接拿到左堆顶
            if num >= self.left.heap[1]:
                # 新元素不小于左堆顶, 直接插到右边小顶堆即可
                self.right.push(num)
            else:
                # 否则先把左堆顶插到右边, 然后左堆再插入新元素
                self.right.push(self.left.pop())
                self.left.push(num)

    def findMedian(self) -> float:
        if self.left.empty():
            return 0
        if len(self.left) == len(self.right):
            # 偶数个元素, 取两个堆顶的平均值
            res = (self.left.heap[1] + self.right.heap[1]) / 2
        else:
            # 奇数个元素, 左堆个数至少为1, 取左堆顶
            res = self.left.heap[1]
        return res
```

##### C++

```cpp
class Heap {
public:
    Heap(string type) : smallHeap(type == "SmallHeap"), heap(1, 0) {}

    size_t size() const {
        return heap.size();
    }

    bool empty() const {
        return size() == 1;
    }

    int top() const {
        return empty() ? -1 : heap[1];
    }

    void push(int v) {
        heap.push_back(v);
        int cur = size() - 1;
        int parent = cur >> 1;
        while (parent >= 1) {
            if ((smallHeap && heap[parent] > heap[cur]) ||
                (!smallHeap && heap[parent] < heap[cur])) {
                swap(heap[cur], heap[parent]);
                cur = parent;
                parent >>= 1;
            } else {
                break;
            }
        }
    }

    int pop() {
        int res = heap[1];
        int tail = heap.back();
        heap.pop_back();
        if (empty()) {
            return res;
        }
        heap[1] = tail;
        int cur = 1;
        int child = 2;
        while (child < size()) {
            if (child + 1 < size()) {
                if ((smallHeap && heap[child + 1] < heap[child]) ||
                (!smallHeap && heap[child + 1] > heap[child])) {
                    ++child;
                }
            }
            if ((smallHeap && heap[child] < heap[cur]) ||
                (!smallHeap && heap[child] > heap[cur])) {
                swap(heap[cur], heap[child]);
                cur = child;
                child <<= 1;
            } else {
                break;
            }
        }
        return res;
    }

private:
    bool smallHeap;
    vector<int> heap;
};

class MedianFinder {
public:
    /** initialize your data structure here. */
    MedianFinder() : left("BigHeap"), right("SmallHeap") {
    }

    void addNum(int num) {
        if (left.size() == right.size()) {
            if (left.empty() || num <= right.top()) {
                left.push(num);
            } else {
                left.push(right.pop());
                right.push(num);
            }
        } else {
            if (num >= left.top()) {
                right.push(num);
            } else {
                right.push(left.pop());
                left.push(num);
            }
        }
    }

    double findMedian() {
        if (left.empty()) {
            return 0;
        }
        if (left.size() == right.size()) {
            return 1.0 * (left.top() + right.top()) / 2;
        }

        return left.top();
    }

private:
    Heap left;
    Heap right;
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
