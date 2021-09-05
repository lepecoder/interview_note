# 动态规划

> 找到状态和选择 -> 明确 dp 数组/函数的定义 -> 寻找状态之间的关系。
>

## 简单动态规划

### [509.斐波那契数（简单）](https://leetcode-cn.com/problems/fibonacci-number)

状态转移方程

$$
f(n)=\left\{\begin{array}{l}
1, n=1,2 \\
f(n-1)+f(n-2), n>2
\end{array}\right.
$$

带备忘录的递归算法

```python
class Solution:
    def __init__(self) -> None:
        self.memo = [-1] * 31

    def fib(self, N: int) -> int:
        if N < 2:
            return N
        if self.memo[N] == -1:
            self.memo[N] = self.fib(N-1)+self.fib(N-2)
        return self.memo[N]
```

自底向上的方法

```python
class Solution:
    """
    记忆字典仍然是自顶向下的计算，而动态规划一般使用自底向上的计算，
    以迭代代替递归。
    """

    def fib(self, N: int) -> int:
        if N < 2:
            return N
        a, b = 0, 1
        while N:
            a, b = b, a+b
            N -= 1
        return a
```

## 背包问题

> 参考崔添翼的[《背包九讲》](https://github.com/tianyicui/pack)
>

### 01背包

> 有N件物品和一个容量为 $V $ 的背包，放入第 $ i $ 件物品需要占用背包空间 $ C_i$ ，得到的物品价值是 $W_i$ 。求解将哪些物品放入背包可使价值总和最大。
>

**思路：**

设想状态转移方程，我们要解决第 $i$ 件物品放或不放的问题，假设我们已经有了之前的最优解，利用 $F[i,v]$ 表示将前 $i$ 件物品放入容量为 $v$ 的背包可以获得的最大价值，此时我们可以推导出*状态转移方程*。

$$
F[i+1,v]=\max\left\{F[i,v],F[i,v-C_i]+W_i\right\}
$$

此公式可以成立的*关键*在于放入第 $i$ 件物品时为什么可以由 $F[i,v-C_i]$ 获得最优的装入价值。这是因为在每次装入第 $i$ 件物品时都是重新将背包的容量从小到大计算，所以在扩张到容量为 $v-C_i$ 时可以得到最大的价值。

**自底向上的方法：**

> 时间复杂度和空间复杂度都是O(VN)
>

```python
F[0,0...V] = 0
for i in range(N):
    for v in range(C_i,V):
        F[i,v] = max(F[i-1,v].F[i-1,v-C_i]+W_i)
```

**优化空间复杂度：**

> 根据递推公式，第i+1个物品的结果是由第i个物品推导出来的，如果我们能在计算第i+1个物品时保证*第i个物品已经得到最好的结果*，那么我们就只需要一维数组来存储F值。
>
> 为了保证在计算 $F[i+1,v]$ 时 $F[i,v-C_i]$ 仍然是上一次的结果，我们需要从大到小遍历空间v，不然在计算后面时之前的 $F[i,v-C_i]$ 已经被更新为第 $i+1$ 次的结果。
>

```
python
F[0...V] = 0
for i in range(N):
    for v in range(V,C_i,-1):
        F[v] = max(F[v], F[v-C_i]+W_i)
```

**注意：**

如果题目要求必须装满背包，那么在初始化时除了F[0]=1，其它状态都应该设为 $-\infty$ ，因为此时只有0是可行解。

### 完全背包

> 和01背包的区别在于完全背包中每种物品都有无限件
>

**思路：**

直接的思路，在考虑第 $i$ 件物品时不光考虑放不放，还需要确定放几件，那么我们可以得到类似下面的状态转移方程

$$
F[i, v]=\max \left\{F\left[i-1, v-k C_{i}\right]+k W_{i} \mid 0 \leq k C_{i} \leq v\right\}
$$

> 两个简单的优化：
>
> 1. 如果两件物品满足 $C_i<=C_j$ 而且 $W_i>=W_j$ 那么我们可以直接将物品 $j$ 去掉。
> 2. 将费用C大于背包容积v的物品去掉。
>

**转化为01背包问题：**

简单的转化可以直接将一个完全背包物品转化为 $\left\lfloor V / C_{i}\right\rfloor$ 件01背包物品，但这完全没有改进时间复杂度；
一种更有效的转化是利用二进制的思想，将第 $i$ 件完全背包转化为费用为 $C_i2^k$ 、价值为 $W_i2^k$ 的若干件物品，这里的 $k$ 满足 $C_i2^k<=V$ ，将01的组合写成二进制数可以大大减少分解成01物品的数量。

**更巧妙的方法：**

在处理01背包问题我们在计算第 $i$ 个物品时要求之前的状态是前 $i-1$ 个物品，这和完全背包刚好相反，完全背包可以重复放入第 $i$ 件物品，所以我们的状态转移方程可以是：

$$
F[i, v]=\max \left(F[i-1, v], F\left[i, v-C_{i}\right]+W_{i}\right)
$$

```python
python
F[0...V] = 0
for i in range(N):
    for v in range(C_i,V):
        F[v] = max(F[v], F[v-C_i]+W_i)
```

可以看到和之前的01背包相比，只有第二重循环的顺序不同。

### 多重背包

## 子序列问题

### [最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence)

> 一个序列的子序列定义为*在不改变顺序的前提下删除部分字符*构成的序列。比如'ace'是'abcde'的子序列。先在要求计算两个字符串的最长公共子序列。
>

这是一个二维动态规划，如果想不出来会有很大的难度，但如果有思路了，那么无论是代码还是证明都很简单。定义`dp[i,j]`是s1的前i个字符和s2的前j个字符的最长公共子序列长度，那么当我们确定i和j后，只需要考虑i和j指定的字符是否属于公共子序列就可以，由此我们可以得到状态转移方程。

$$
dp(i,j) = \left\{\begin{array}{l}
\begin{aligned}
&dp(i-1,j-1)+1, &s1(i)=s2(j) \\
&\max\{dp(i-1,j),dp(i,j-1)\}, &s1(i)\neq s2(j)
\end{aligned}
\end{array}\right.
$$

由此我们可以得到一个递归的解法

```python
# 递归
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        def dp(i, j):
            if i == -1 or j == -1:
                return 0
            if text1[i] == text2[j]:
                return dp(i - 1, j - 1) + 1
            else:
                return max(dp(i - 1, j), dp(i, j - 1))
        return dp(len(text1) - 1, len(text2) - 1)
```

这种解法包含大量重复计算，必会超时，但是我们可以简单的通过打表来避免重复计算。

```python
# 二维打表
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        m, n = len(text1), len(text2)
        dp_table = [[0] * (n + 1) for _ in range(m + 1)]
        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if text1[i - 1] == text2[j - 1]:
                    dp_table[i][j] = 1 + dp_table[i - 1][j - 1]
                else:
                    dp_table[i][j] = max(dp_table[i][j - 1], dp_table[i - 1][j])

        return dp_table[-1][-1]
```

考虑01背包中对空间复杂度的优化，由于状态转移方程中在计算`i`时只和`i`以及`i-1`相关，所以我们可以只用一个一维列表来存储之前的状态，*注意*，和之前的01背包不同，在状态转移方程里需要同时用到`(i-1,j-1)`和`(i,j-1)`，所以不能简单的通过更改循环次序减少空间占用，而是要用一个额外的临时变量存储。

### [300.最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

> 给定一个整数数组，找出其中最长的严格递增子序列的长度。
>

对于一个数组中的数字，如果它在子序列里，记录它之前的子序列的长度，只要计算之前的数字中比它小的最大的子序列长度+1就可以。

```plaintext
[10,9,2,5,3,7,101,18]
[0, 0,0,1,1,2,3  ,3 ]
# 比如18之前比18小的有2,5,3,7，其中子序列长最大的是7，对应长度2，所以18如果在子序列里，对应长度为3，加上自身，长度为4
```

```
# 定义pre_[i]为前i个元素，必须选择i时最长子序列的长度
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        n = len(nums)
        pre_ = [1] * n
        for i in range(1, n):
            for j in range(i):
                if nums[i] > nums[j]:
                    pre_[i] = max(pre_[i], pre_[j] + 1)
        print(pre_)
        return max(pre_)
```

### [最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

> 给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 1000。
>

```plaintext
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

#### 方法一：动态规划

由于要求出最长的回文子串，但我们很难直接存储字串，可以令`dp[i][j]`表示从`i`到`j`的字串是否是回文串，那么我们只需要找到最长的`j-i+1`就可以，这样一来问题就变成如何判断`dp[i][j]`是否是回文串。

判断一个字串是否是回文串是很简单的

$$
P(i, j)=P(i+1, j-1) \wedge\left(S_{i}==S_{j}\right)
$$

对于边界而言，如果一个串长度为1，那么必定是回文串；如果一个串长度是2，当他们两个字母相同时，是回文串。依据这个思路，我们可以找到所有的回文子串，进而找到最长的回文子串。

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        dp = [[False] * n for _ in range(n)]
        ans = ""
        for l in range(n):  # 字串的长度
            for i in range(n):  # 子串的起始位置
                j = i + l
                if j >= len(s):
                    break
                if l == 0:
                    dp[i][j] = True
                elif l == 1:
                    dp[i][j] = (s[i] == s[j])
                else:
                    dp[i][j] = (dp[i + 1][j - 1] and s[i] == s[j])
                if dp[i][j] and l + 1 > len(ans):
                    ans = s[i:j + 1]
        return ans
```

时间复杂度是 $O(n^2)$ ，而且有很多重复计算，比如当我们知道`dp[i][j]`不是回文串，那么所有`dp[i-k][j+k]`都不是回文串。

#### 方法二：中心扩展

观察状态转移方程：

$$
\left\{\begin{array}{lll}
P(i, i) & =\text { true } \\
P(i, i+1) & =\left(S_{i}==S_{i+1}\right) \\
P(i, j) & =P(i+1, j-1) \wedge\left(S_{i}==S_{j}\right)
\end{array}\right.
$$

和状态转移链：

$$
P(i, j) \leftarrow P(i+1, j-1) \leftarrow P(i+2, j-2) \leftarrow \cdots \leftarrow \text { 某一边界情况 }
$$

也就是说*所有的状态在转移的时候的可能性都是唯一的*，我们可以从中心开始向外扩展，如果有一处为False，那么之后一定不是回文串，也就没有必要再扩展。

```python
class Solution:
    def expand(self, l, r):
        while l >= 0 and r < self.n and self.s[l] == self.s[r]:
            l -= 1
            r += 1
        return l + 1, r - 1

    def longestPalindrome(self, s: str) -> str:
        self.s = s
        self.n = len(s)
        start, end = 0, 0
        for i in range(self.n):
            l1, r1 = self.expand(i, i)  # 奇数扩展
            l2, r2 = self.expand(i, i + 1)  # 偶数扩展
            if r1 - l1 > end - start:
                start, end = l1, r1
            if r2 - l2 > end - start:
                start, end = l2, r2
        return s[start:end + 1]
```

```

```

## 其他问题

### [343.整数拆分](https://leetcode-cn.com/problems/integer-break/)

> 给定一个正整数 *n*，将其拆分为**至少**两个正整数的和，并使这些整数的乘积最大化。 返回你可以获得的最大乘积。
>

#### 方法一：动态规划

一般来讲状态就是我们要求解的问题，比如设置`dp[i]`为将`i`拆分后的能够得到的最大乘积，在求解`dp[i]`时之前的状态都已得到，此时有两种方案

- 将 $i$ 拆分成 $j$ 和 $i-j$ ，且 $i-j$ *不再拆分*，此时的乘积是 $j \times (i-j)$
- 将 $i$ 拆分成 $j$ 和 $i-j$ ，且 $i-j$ *继续拆分*，此时的乘积是 $j \times dp[i-j]$

`j`的取值范围可以是`1`到`i-1`

```python
class Solution: 
def integerBreak(self, n: int) -> int:
dp = [0] * (n + 1)
for i in range(2, n + 1):
for j in range(i):
dp[i] = max(dp[i], j * (i - j), j * dp[i - j])
return dp[n]
```

#### 方法二：优化的动态规划

[https://leetcode-cn.com/problems/integer-break/solution/zheng-shu-chai-fen-by-leetcode-solution/](https://leetcode-cn.com/problems/integer-break/solution/zheng-shu-chai-fen-by-leetcode-solution/)

#### 方法三：数学方法

### [面试题 17.16. 按摩师](https://leetcode-cn.com/problems/the-masseuse-lcci/)

> 一个有名的按摩师会收到源源不断的预约请求，每个预约都可以选择接或不接。在每次预约服务之间要有休息时间，因此她不能接受相邻的预约。给定一个预约请求序列，替按摩师找到最优的预约集合（总预约时间最长），返回总的分钟数。
>

我们可以假设`dp[i]`是前`i`个字串可以得到的最大预约总时间，如果

- 第`i`个不接，那么`dp[i]=dp[i-1]`
- 第`i`个接受，那么`dp[i]=nums[i]+dp[i-1]`

```python
class Solution:
    def massage(self, nums: List[int]) -> int:
        n = len(nums)
        dp = [0]*(n+1)
        for i in range(1,n+1):
            a = dp[i-1]  #不接受第i个
            if i >=2:
                b = nums[i-1]+dp[i-2]
            else:
                b = nums[i-1]
            dp[i] = max(a,b)
        return(dp[-1])
```

### [64.最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)

> 有一个 $m\times n$ 的网格，网络里的数字表示路径长度，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。
>

![](assets/20201220235619-xk6bdvx.jfif)

```plaintext
输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。
```

我们设`dp[i][j]`为从左上角到`i,j`的最短路径长度，由于只能向下或向右走，那么

$$
dp(i,j)=\min \Big(dp(i-1,j),dp(i,j-1)\Big)+grid(i,j)
$$