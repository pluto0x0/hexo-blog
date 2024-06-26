---
title: Codeforces 730
date: 2021-07-16
---

[Codeforces Round #730 (Div. 2)](https://codeforces.com/contest/1543/)

## A

```cpp
#include <cstdio>
#include <algorithm>
typedef long long ll;
ll T,n,m;
int main()
{
    scanf("%lld",&T);
    while (T--) {
        scanf("%lld %lld",&n,&m);
        if(n == m){
            printf("0 0\n");
            continue;
        }
        if(n > m)
            std::swap(n,m);
        ll d = m - n;
        printf("%lld %lld\n",d,std::min(n % d,d - n % d));
    }
    return 0;
}
```

## B

```cpp
#include <cstdio>
typedef long long ll;
int T,n;
ll sum;
int main()
{
    scanf("%d",&T);
    while (T--){
        scanf("%d",&n);
        ll tmp;
        sum = 0;
        for(int i = 1;i <= n;i++){
            scanf("%lld",&tmp);
            sum += tmp;
        }
        sum %= n;
        printf("%lld\n",sum * ((ll)n - sum));
    }
    return 0;
}
```

## C

根据题意暴力dfs即可。

```cpp
#include <algorithm>
#include <cstdio>
int T;
double c, m, p, v;
double dfs(double x, double y) {
    if (x <= 1e-8 && y <= 1e-8) return 1;
    if (y <= 1e-8) std::swap(x, y);
    double dy = std::min(y, v);
    if (x <= 1e-8) return (1 - y) + y * (1 + dfs(0, y - dy));
    double dx = std::min(x, v);
    return (1 - x - y) + x * (dfs(x - dx, y + dx / 2) + 1)+
           y * (dfs(x + dy / 2, y - dy) + 1);
}
int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%lf %lf %lf %lf", &c, &m, &p, &v);
        printf("%.12lf\n", dfs(c, m));
    }
    return 0;
}
```

## D1

从0到n-1依次猜，每次将猜的数依次xor前面所有的猜过的。

```cpp
#include <cstdio>
#define N 200011
int T;
int n, k;
int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%d %d", &n, &k);
        int res;
        for (int i = 0;i < n;i++) {
            printf("%d\n",i == 0 ? 0 : i ^ (i - 1));
            fflush(stdout);
            scanf("%d",&res);
            if(res) break;
        }
    }
 
    return 0;
}
```

## D2

思想同D1，其中xor用k进制下的操作代替（不进位的加法），同时用类似方法（不进位减法）实现逆操作。

```cpp
#include <cstdio>
#define N 200011
int T;
int n, k;
int sub(int x, int y) {
    int kx, ky, d = 1, res = 0;
    while (x || y) {
        kx = x % k, ky = y % k;
        x /= k, y /= k;
        res += (kx + k - ky) % k * d;
        d *= k;
    }
    return res;
}
int add(int x, int y) {
    int kx, ky, d = 1, res = 0;
    while (x || y) {
        kx = x % k, ky = y % k;
        x /= k, y /= k;
        res += (kx + ky) % k * d;
        d *= k;
    }
    return res;
}
int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%d %d", &n, &k);
        int res, f = 0, out = 0;
        for (int i = 0; i < n; i++) {
            if(i > 0){
                out = add(f, i);
                if(i % 2 == 1) out = sub(0, out);
            }
            printf("%d\n",out);
            fflush(stdout);
            scanf("%d", &res);
            if (res) break;
            if(i % 2 == 0) f = sub(f,out);
            else f = add(f,out);
        }
    }
 
    return 0;
}
```

## F

主要思路来自[这篇](https://www.luogu.com.cn/blog/111055/solution-cf1543e)

以下做一些解释。

### 1

为什么是**上一层**的**或**和？

在超立方体上，改变一个二进制位相当于走过对应维度的一条边。记起点为0，那么任意节点 $x$ 到起点的最短路径长就是 $x$ 中二进制1的个数。记当前节点为 $u$ ，与之相连的上一层节点是 $v_1,v_2,\cdots$ 
那么从 $v$ 到 $u$ 的过程，一定就是 $v$ 中某一个二进制0变为1的过程，而对于其它的 $v$ ，这一位上一定是1。因此一旦存在某一个 $v$ 在某一位上是1，那么 $u$ 的这一位一定是1。

### 2

首先，对于任意一种颜色的节点，所有 $2^n$ 个节点都有一条与之相连的边，而一个该颜色的节点都被算了 $n$ 次，因此每种颜色的节点数都为 $\frac{2^n}{n}$ 。因此 $n$ 必然为2的幂。

又已知与节点 $x$ 相连的点就是将 $x$ 的 $n$ 个位一次取反的结果。因此，对与一个节点 $x$ ，第 $i$ 位是1，就异或 $i$ ，最终结果就是 $x$ 的颜色。又因为 $n-1$ 为连续几个二进制1，所有异或的结果不会超出 $n-1$ 的范围。

代码略。

p.s. 3B1B有类似超立方体染色问题的视频，可以看一看。

<div style="position: relative; padding: 30% 45%;">
            <iframe style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;" src="https://player.bilibili.com/player.html?aid=711666649&bvid=BV1UD4y1U7or&cid=221130336&page=1" frameborder="no" scrolling="no"></iframe>
        </div>

## 总结

F题难度较大，此类问题应该往二进制上靠，多打暴力观察一下结论。
