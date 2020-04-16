---

title : 算法
toc : true 
categories: 算法学习
date: 2019-10-25 20:10:33
tags: [Java,算法]
---

&#160; &#160; &#160; &#160;有关算法的学习，起步较晚。大二下的寒假才开始，这次计划系统性，认真的学习。首先是为了蓝桥杯取得好的名次为保研加分，其次则是面试的算法题。😁

&#160; &#160; &#160; &#160;关于这部分的内容，学习自AcWing，以及牛客的左程云等多位前辈。

&#160; &#160; &#160; &#160;如有帮助，不胜荣幸。如有错误，欢迎指正！

<!-- more -->

# 时间复杂度

&#160; &#160; &#160; &#160;虽然代码千差万别，但是常见的复杂度量级并不多。我稍微总结了一下，这些复杂度量级几乎涵盖了你今后可以接触的所有代码的复杂度量级。


![复杂度级.png](https://i.loli.net/2019/10/06/sbL7QDgRpy68Xhi.png)


&#160; &#160; &#160; &#160;对于刚罗列的复杂度量级，我们可以粗略地分为两类，多项式量级和非多项式量级。其中，非多项式量级只有两个：O(2n) 和 O(n!)。

&#160; &#160; &#160; &#160;当数据规模 n 越来越大时，非多项式量级算法的执行时间会急剧增加，求解问题的执行时间会无限增长。所以，非多项式时间复杂度的算法其实是非常低效的算法。因此，关于 NP 时间复杂度我就不展开讲了。我们主要来看几种常见的多项式时间复杂度。

## O(1)

&#160; &#160; &#160; &#160;首先必须明确一个概念，O(1) 只是常量级时间复杂度的一种表示方法，并不是指只执行了一行代码。比如这段代码，即便有 3 行，它的时间复杂度也是 O(1），而不是 O(3)。
```java
 int i = 8;
 int j = 6;
 int sum = i + j;
```
&#160; &#160; &#160; &#160;只要代码的执行时间不随 n 的增大而增长，这样代码的时间复杂度我们都记作 O(1)。或者说，一般情况下，只要算法中不存在循环语句、递归语句，即使有成千上万行的代码，其时间复杂度也是Ο(1)。

## O(logn)、O(nlogn)

&#160; &#160; &#160; &#160;对数阶时间复杂度非常常见，同时也是最难分析的一种时间复杂度。我通过一个例子来说明一下。
```java
 i=1;
 while (i <= n)  {
   i = i * 2;
 }
```
&#160; &#160; &#160; &#160;根据我们前面讲的复杂度分析方法，第三行代码是循环执行次数最多的。所以，我们只要能计算出这行代码被执行了多少次，就能知道整段代码的时间复杂度。

&#160; &#160; &#160; &#160;从代码中可以看出，变量 i 的值从 1 开始取，每循环一次就乘以 2。当大于 n 时，循环结束。还记得我们高中学过的等比数列吗？实际上，变量 i 的取值就是一个等比数列。如果我把它一个一个列出来，就应该是这个样子的：

&#160; &#160; &#160; &#160;所以，我们只要知道 x 值是多少，就知道这行代码执行的次数了。通过 2x=n 求解 x 这个问题我们想高中应该就学过了，我就不多说了。x=log2n，所以，这段代码的时间复杂度就是 O(log2n)。

现在，我把代码稍微改下，你再看看，这段代码的时间复杂度是多少？
```java
 i=1;
 while (i <= n)  {
   i = i * 3;
 }
```
&#160; &#160; &#160; &#160;根据我刚刚讲的思路，很简单就能看出来，这段代码的时间复杂度为 O(log3n)。

实际上，不管是以 2 为底、以 3 为底，还是以 10 为底，我们可以把所有对数阶的时间复杂度都记为 O(logn)。为什么呢？

&#160; &#160; &#160; &#160;我们知道，对数之间是可以互相转换的，log3n 就等于 log32 * log2n，所以 O(log3n) = O(C * log2n)，其中 C=log32 是一个常量。基于我们前面的一个理论：在采用大 O 标记复杂度的时候，可以忽略系数，即 O(Cf(n)) = O(f(n))。所以，O(log2n) 就等于 O(log3n)。因此，在对数阶时间复杂度的表示方法里，我们忽略对数的“底”，统一表示为 O(logn)。

&#160; &#160; &#160; &#160;如果你理解了我前面讲的 O(logn)，那 O(nlogn) 就很容易理解了。还记得我们刚讲的乘法法则吗？如果一段代码的时间复杂度是 O(logn)，我们循环执行 n 遍，时间复杂度就是 O(nlogn) 了。而且，O(nlogn) 也是一种非常常见的算法时间复杂度。比如，归并排序、快速排序的时间复杂度都是 O(nlogn)。

## O(m+n)、O(m*n)

&#160; &#160; &#160; &#160;我们再来讲一种跟前面都不一样的时间复杂度，代码的复杂度由两个数据的规模来决定。老规矩，先看代码！
```
int cal(int m, int n) {
  int sum_1 = 0;
  int i = 1;
  for (; i < m; ++i) {
    sum_1 = sum_1 + i;
  }

  int sum_2 = 0;
  int j = 1;
  for (; j < n; ++j) {
    sum_2 = sum_2 + j;
  }

  return sum_1 + sum_2;
}
```
&#160; &#160; &#160; &#160;从代码中可以看出，m 和 n 是表示两个数据规模。我们无法事先评估 m 和 n 谁的量级大，所以我们在表示复杂度的时候，就不能简单地利用加法法则，省略掉其中一个。所以，上面代码的时间复杂度就是 O(m+n)。

&#160; &#160; &#160; &#160;针对这种情况，原来的加法法则就不正确了，我们需要将加法规则改为：T1(m) + T2(n) = O(f(m) + g(n))。但是乘法法则继续有效：T1(m)*T2(n) = O(f(m) * f(n))。

# 技巧

## 使用BufferedReader读取数据

&#160; &#160; &#160; &#160;如一个简单的排序程序，采用Scanner：

```Java
import java.util.Arrays;
import java.util.Scanner;

public class Main {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int length = scanner.nextInt();
        scanner.nextLine();
        String[] temp = scanner.nextLine().split(" ");
        int nums[] = new int[length];
        int i = 0 ;
        for (String s : temp){
            nums[i++] = Integer.parseInt(s);
        }
        Arrays.sort(nums);
        for (int num : nums){
            System.out.print(num +" ");
        }
    }
}

```
&#160; &#160; &#160; &#160;如一个简单的排序程序，采用BufferedReader：

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.Scanner;

public class Main {

    public static void main(String[] args) throws IOException {
        BufferedReader buf=null;
        buf=new BufferedReader(new InputStreamReader(System.in));
        int length = Integer.parseInt(buf.readLine());
        String[] temp = buf.readLine().split(" ");
        int nums[] = new int[length];
        int i = 0 ;
        for (String s : temp){
            nums[i++] = Integer.parseInt(s);
        }
        Arrays.sort(nums);
        for (int num : nums){
            System.out.print(num +" ");
        }
    }
}
```
## 实现一个Pair 

&#160; &#160; &#160; &#160;在`Java`的基础库中，没有实现的`Pair`，但是这个类的设计是十分简单，且十分方便。在`javafx.uitl.`中有以下实现，我们可以学习参考：

```java
public class Pair<K,V> implements Serializable{

    private K key;

    public K getKey() { return key; }

    private V value;

    public V getValue() { return value; }

    public Pair(@NamedArg("key") K key, @NamedArg("value") V value) {
        this.key = key;
        this.value = value;
    }

    @Override
    public String toString() {
        return key + "=" + value;
    }

    @Override
    public int hashCode() {
        return key.hashCode() * 13 + (value == null ? 0 : value.hashCode());
    }

     @Override
     public boolean equals(Object o) {
         if (this == o) return true;
         if (o instanceof Pair) {
             Pair pair = (Pair) o;
             if (key != null ? !key.equals(pair.key) : pair.key != null) return false;
             if (value != null ? !value.equals(pair.value) : pair.value != null) return false;
             return true;
         }
         return false;
     }
 }

```


# 基础算法

## 快速排序

step1: 初始化` i = l - 1, j = r + 1 `, 参照值` x = a[(l+r)>>1]`
step2: 双指针从两侧找，` i `指针找第一个不满足 `a[i] < x `的位置， `j `指针找第一个不满足 `a[j] > x `的位置，此时若满足` i < j `,则交换 `a[i] 和 a[j]`
step3: 递归调用前后两部分，记得下标：当参照值` x` 可以取到 `l` 的时候，要以 `j` 为临界点调用;当 `x` 可以取到 `r` 的时候，要以 i 作为临界点区分调用

```java
void  quick_sort(int a[], int l, int r){
    if(l >= r) {
        return ;
    }
    int i = l - 1, j = r + 1, x = a[(l + r) >> 1];
    while(i < j){
        do {
            i++;
        } while(a[i] < x);
        do {
            j--;
        } while(a[j] > x);
        if(i < j){
            int tmp = a[i];
            a[i] = a[j];
            a[j] = tmp;
        }
    }


    quick_sort(a, l, j);
    quick_sort(a, j + 1, r);
}
```

## 归并排序

Step1: 找分界点`mid`
Step2: 递归左边`left`， 递归右边`right`
Step3: 合并左右`left／right`两个有序数组，借助于`tmp`数组

```java
void mergeSort(int[] nums, int l, int r) {
    if (l == r) {
        return;
    }
    int mid = l + r >> 1);
    mergeSort(nums, l, mid);
    mergeSort(nums, mid + 1, r);

    int p1 = l, p2 = mid + 1, index = 0;
    int[] help = new int[r - l + 1];
    while (p1 <= mid && p2 <= r) {
        help[index++] = nums[p1] <= nums[p2] ? nums[p1++] : nums[p2++];
    }
    while (p1 <= mid) {
        help[index++] = nums[p1++];
    }
    while (p2 <= r) {
        help[index++] = nums[p2++];
    }
    for (int i = l, j = 0; j < help.length; i++, j++) {
        nums[i] = help[j];
    }
}
```

## 二分查找

&#160; &#160; &#160; &#160;二分模板一共有两个，分别适用于不同情况。
&#160; &#160; &#160; &#160;算法思路：假设目标值在闭区间[l, r]中， 每次将区间长度缩小一半，当l = r时，我们就找到了目标值。

### 版本1
&#160; &#160; &#160; &#160;当区间[l, r]的更新操作是r = mid; l = mid + 1;时，计算mid时不需要加1。

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

&#160; &#160; &#160; &#160;以上二分模板用来解，**最大值最小问题**。

### 版本2
&#160; &#160; &#160; &#160;当区间[l, r]的更新操作是r = mid - 1; l = mid;时，计算mid时需要加1。

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
&#160; &#160; &#160; &#160;以上二分模板用来解，**最小值最大问题**。

## 前缀与差分

### 一维前缀和

```java
S[i] = a[1] + a[2] + ... a[i]
a[l] + ... + a[r] = S[r] - S[l - 1]
```

### 二维前缀和

```java
S[i, j] = 第i行j列格子左上部分所有元素的和
以(x1, y1)为左上角，(x2, y2)为右下角的子矩阵的和为：
S[x2, y2] - S[x1 - 1, y2] - S[x2, y1 - 1] + S[x1 - 1, y1 - 1]
```

### 一维差分

```java
给区间[l, r]中的每个数加上c：B[l] += c, B[r + 1] -= c
```

### 二维差分

```java
给以(x1, y1)为左上角，(x2, y2)为右下角的子矩阵中的所有元素加上c：
S[x1, y1] += c, S[x2 + 1, y1] -= c, S[x1, y2 + 1] -= c, S[x2 + 1, y2 + 1] += c
```

## 双指针算法

待更新

## 位运算

```java
求n的第k位数字: n >> k & 1
返回n的最后一位1：lowbit(n) = n & -n
```

## 离散化

待更新

## 区间合并

```java
private static ArrayList<Pair> merge(ArrayList<Pair> list) {
    ArrayList<Pair> res = new ArrayList();
    list.sort((x, y) -> {
        return x.getKey() - y.getKey();
    });
    Pair now = list.get(0);
    Iterator<Pair> iterator = list.iterator();
    Pair temp = null;
    while (iterator.hasNext()) {
        temp = iterator.next();
        int  y1 = now.getValue(), x2 = temp.getKey(), y2 = temp.getValue();
        if (x2 > y1) {
            res.add(now);
            now = temp;
        } else if (y2 > y1) {
                now = new Pair(now.getKey(),y2);
        }
    }
    res.add(now);
    return res;
}
```

# 数据结构

## KMP

## Trie树

```java
// 0号点既是根节点，又是空节点
// son[][]存储树中每个节点的子节点
// cnt[]存储以每个节点结尾的单词数量
static int[][] son = new int[100010][26];
static int idx = 0;
static int[] cnt = new int[100010];

// 插入一个字符串
static void insert(char[] str) {
    int p = 0;
    for (int i = 0; i < str.length; i++) {
        int u = str[i] - 'a';
        if (son[p][u] == 0) {
            son[p][u] = ++idx;
        }
        p = son[p][u];
    }
    cnt[p]++;
}

// 查询字符串出现的次数
static int query(char[] str)
{
    int p = 0;
    for (int i = 0; i < str.length; i ++ )
    {
        int u = str[i] - 'a';
        if (son[p][u] == 0) {
            return 0;
        }
        p = son[p][u];
    }
    return cnt[p];
}
```

