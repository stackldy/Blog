# ACwing 蓝桥辅导课

## 整数二分

所谓的整数二分，就是以下两类问题：

- **最大值最小问题**
    ![最大值最小问题.png](https://i.loli.net/2020/02/11/3r7XMwAgmo6JnEQ.png)
- **最小值最大问题**
    ![最小值最大问题.png](https://i.loli.net/2020/02/11/L8Op5MfuDxARh7s.png)

二分模板一共有两个，分别适用于不同情况。
算法思路：假设目标值在闭区间[l, r]中， 每次将区间长度缩小一半，当l = r时，我们就找到了目标值。

### 最大值最小问题

 ![最大值最小问题.png](https://i.loli.net/2020/02/11/3r7XMwAgmo6JnEQ.png)

当区间[l, r]的更新操作是r = mid; l = mid + 1;时，计算mid时不需要加1。

```java
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;
        else l = mid + 1;
    }
    return l;
}
```

以上二分模板用来解**最大值最小问题**。

### 最小值最大问题

![最小值最大问题.png](https://i.loli.net/2020/02/11/L8Op5MfuDxARh7s.png)

当区间[l, r]的更新操作是r = mid - 1; l = mid;时，计算mid时需要加1。

```java
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}
```

以上二分模板用来解**最小值最大问题**。

### 例题 - 数的范围

【例题】：[Acwing789 数的范围](https://www.acwing.com/problem/content/791/)

#### 思路

![数的范围.png](https://i.loli.net/2020/02/11/QhqO2J8CBEySPW5.png)

有了上图的思路，在把check函数完成，是一道**模板题**。

#### 类似题

【例题】：[Acwing790 三次方根](https://www.acwing.com/problem/content/792/)

该题非整数二分，仅仅是普通二分查找，故更新条件为`r=mid`和`l=mid`。

【例题】：[Acwing730 机器人跳跃问题](https://www.acwing.com/problem/content/732/)

该题就是最大值最小问题，借助模板即可。😊

【例题】：[Acwing1221 四平方和](https://www.acwing.com/problem/content/1223/)

这道题与leetcode上面一道题目类似:[四数相加2](https://leetcode-cn.com/problems/4sum-ii/)

【例题】：[Acwing1227 分巧克力](https://www.acwing.com/problem/content/1229/)

这是一道可以用最小值最大的二分求解。

## 递归与递推

&#160;&#160;&#160;&#160;该类题目主要有三类：
- 递归实现指数型枚举
- 递归实现排列型枚举
- 递归实现组合型枚举
  ![递归与递推.png](https://i.loli.net/2020/02/10/fXFYtpATq1cRhbZ.png)

### 递归实现指数型枚举

【例题】：[Acwing92 递归实现指数型枚举](https://www.acwing.com/problem/content/94/)

```java
/*
 * 用二进制状态压缩。也就是用二进制上的位来记录数有没有被用过。
 */
// m是当前枚举到的数 k是二进制数记录哪些数被选
public static void dfs(int m, int k) {
    if (m == n) {
        for (int i = 0; i < n; i++) {
             //判断第i位是不是1，即有没有被选，如果被选就输出
            if (((k >> i) & 1) == 1) {
                 //因为题目是从1开始的，所以要加1
                System.out.print((i + 1) + " ");
            }
        }
        System.out.println();
        return;
    }
    //不用这个数，不进行操作
    dfs(m + 1, k);
    //用这个数，把第u位变成1
    dfs(m + 1, k | (1 << m));
}
```

### 递归实现排列型枚举

这正是蓝桥杯常考的全排列。即 `n!`个可能性，也就是
```math
 A ^{n-1}_{n}
```

【例题】：[Acwing94 递归实现排列型枚举](https://www.acwing.com/problem/content/96/)

```java
public static void dfs(int i) throws IOException {
    if (i == n) {
        for (int j = 0; j < n; j++) {
            bufferedWriter.write(map[j] + " ");
        }
        bufferedWriter.write("\n");
        return;
    }
    for (int j = 0; j < n; j++) {
        if (!st[j]) {
            st[j] = true;
            map[i] = j + 1;
            dfs(i + 1);
            st[j] = false;
        }
    }
}
```

### 递归实现组合型枚举

即 `n!/(n-m)!`，也就是：

```math
 C ^{m}_{n}
```

【例题】：[Acwing93 递归实现组合型枚举](https://www.acwing.com/problem/content/95/)

```java
private static void dfs(int x, int start) throws IOException {
    if (x == m) {
        for (int i = 0; i < m; i++) {
            bufferedWriter.write(map[i] + " ");
        }
        bufferedWriter.write("\n");
        return;
    }
    for (int i = start; i < n; i++) {
        map[x] = i + 1;
        dfs(x + 1, i + 1);
    }
}
```

### 全排列 - 例题

【例题】 [Acwing1209 带分数](https://www.acwing.com/problem/content/1211/)

![代码.png](https://i.loli.net/2020/02/10/O6E7VDFXJM3hwzq.png)

### 费解的开关 - 例题 

【例题】 [Acwing95 费解的开关](https://www.acwing.com/problem/content/97/)

![思路.png](https://i.loli.net/2020/02/10/6vhlzuwjYcaJEUy.png)

#### 开关灯的技巧

```java
static int[] x = {-1, 0, 1, 0, 0};
static int[] y = {0, 0, 0, -1, 1};
private static void trun(int i, int j) {
    for (int k = 0; k < 5; k++) {
        int a = i + x[k];
        int b = j + y[k];
        if (a < 0 || a == 5 || b < 0 || b == 5) {
            continue;
        }
        map[a][b] ^= 1;
    }
}
```
【类似】 [Acwing1208 翻硬币](https://www.acwing.com/problem/content/1210/)

【类似】 [Acwing116 飞行员兄弟](https://www.acwing.com/problem/content/118/)

---
## 前缀和

### 一维前缀和

对应数组`a[n]`,他的前缀和用`b[n]`表式的话有：

```java
b[i] = a[i] + a[i-1]...a[2] + a[1]
b[l,r] = b[r] - b[l-1]
```

### 二维前缀和

S[i, j] = 第i行j列格子左上部分所有元素的和以(x1, y1)为左上角，(x2,y2)为右下角的子矩阵的和为 `S[x2, y2] - S[x1 - 1, y2] - S[x2, y1 - 1] + S[x1- 1, y1 - 1]`


### 一维前缀和 - 例题

【例题】: [acwing 795 前缀和](https://www.acwing.com/problem/content/797/)

#### 类似题

【例题】 ： [acwing 1230 K倍区间](https://www.acwing.com/problem/content/1232/)

#### O(n^2)解法

```java
for (int i = 1; i <= n; i++) {
    for (int j = (i - 1); j >= 0 ; j--) {
        if ((map[i]- map[j]) % k == 0 ){
            res++;
        }
    }
}
```

#### 优化

发现`(map[i]- map[j]) % k == 0`就等于`map[i] % k - map[j] % k = 0`。即我们可以统计该数的`余数`在之前出现的次数，以实现O(n)时间复杂度的优化。

```java
 for (int i = 1; i <= N; i++) {
    String[] s = in.readLine().split(" ");
    sum[i] = (sum[i-1] + Integer.parseInt(s[0]))%K;
    //count 作为统计当前数出现的个数
    //如果当前数在前面已经出现过k次，那么现在有一个同样的数，答案的个数就会新增k+1个数
    ans += count[sum[i]];
    //当前数出现的个数+1
    count[sum[i]]++;
}
//如果一个区间的和为k的倍数，那么(sum[k] - sum[j])%k==0
//可以推出来 sum[k] % k = sum[j] % k
System.out.println(ans);
```

### 二维前缀和 - 例题

【例题】: [acwing 796 子矩阵的和](https://www.acwing.com/problem/content/798/)

#### 类似题

【例题】 ： [acwing 99 激光炸弹](https://www.acwing.com/problem/content/101/)

![二分和前缀和.png](https://i.loli.net/2020/02/12/cQSoIHv1sEqaOgl.png)

如图所示，其实就是求[x-1,y-1]到[x+1,y+1]的前缀和。

## 数学

这一部分需要不断的积累，多做题才行。且没有方法可言！

### 买不到的数目

【例题】 [acwing1205 买不到的数目](https://www.acwing.com/problem/content/1207/)

#### 结论
**对于n和m两个互质的数**

满足最大不能组合数为 **n\*m-n-m**；

所有不能表示的数的个数为 **（n-1）\*（m-1）/2**；

### 饮料换购

【例题】 [acwing1216 饮料换购](https://www.acwing.com/problem/content/1218/)

这道题似乎只是一道`while()`循环结构的题目

```java
package acwing;

import java.util.Scanner;

public class Main1216 {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();
        int res = n;
        while (n > 2) {
            res += n / 3;
            n = n / 3 + n % 3;
        }
        System.out.println(res);
    }

}
```

但是y总上课提到A/B**上取整**的公式如下：

```math
\frac{A}{B} = \frac{A+B-1}{B} 
```

### 蚂蚁感冒

【例题】 [acwing1211 蚂蚁感冒](https://www.acwing.com/problem/content/1213/)

![感冒的蚂蚁.png](https://i.loli.net/2020/02/14/eDmaMuVdqSwfczr.png)

[参考题解](https://www.acwing.com/solution/AcWing/content/7077/)

首先我们必须要明白两只蚂蚁相撞掉头可以看作时一只蚂蚁穿过了另一只蚂蚁，因为相撞之后两只蚂蚁都感冒了，掉不掉头其实无所谓，毕竟都感冒了，这样的话这题就简单多了。我们先不考虑特殊情况，先来看看一般情况：

第一只蚂蚁不管方向朝哪里，只要它右边的蚂蚁向左走就可能碰撞感染，同样，第一只蚂蚁左边的蚂蚁只要朝右边走也可能被感染，这样就很容易得到ans=right+left+1。这里left表示左边蚂蚁向右走的数量，right表示右边蚂蚁向左走的数量，11是指第一只蚂蚁本身。

还有一种特殊情况，就是当第一只蚂蚁向左走的时候，如果第一只蚂蚁左边没有向右爬行的蚂蚁，由于爬行速度相同，所以不管第一只蚂蚁右边有多少向左爬行的，其右边的蚂蚁永远不可能被感染。同理，当第一只蚂蚁向右走的时候，如果第一只蚂蚁右边没有向左爬行的蚂蚁，其左边也永远不可能感染。