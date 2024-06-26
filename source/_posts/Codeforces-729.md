---
title: Codeforces 729
date: 2021-07-17
---

[Codeforces Round #729 (Div. 2)](https://codeforces.com/contest/1542)

## A

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
int T, n;
int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        int t, odd = 0, even = 0;
        for (int i = 1; i <= n * 2; i++) {
            scanf("%d", &t);
            if (t % 2 == 1)
                odd++;
            else
                even++;
        }
        printf(odd == n ? "Yes\n" : "No\n");
    }
    return 0;
}
```

## B

问题就是

$ $n = a ^ x + y * b$ $

是否有解。

但一开始还以为是

$ $a^x \equiv n \mod b$ $

是否有解。。结果发现 $n > a ^ x$ 。。。

因此就可以暴力枚举。


```cpp
#include <cstdio>
typedef long long ll;
int T;
ll n, a, b;
int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%lld %lld %lld", &n, &a, &b);
        if (a == 1) {
            printf(n % b == 1 || b == 1 ? "Yes\n" : "No\n");
            continue;
        }
        bool yes = false;
        for (ll i = 1; i <= n; i *= a) {
            if (i % b == n % b) {
                printf("Yes\n");
                yes = true;
                break;
            }
        }
        if (!yes) printf("No\n");
    }
    return 0;
}
```

## C

就是 $1 \cdots n$ 求个 $LCM$ 这样子。

```cpp
#include <cstdio>
#define MOD 1000000007ll
typedef long long ll;
int T;
ll n, f[43];
int gcd(ll x, ll y) {
    if (y == 0) return x;
    return gcd(y, x % y);
}
int main() {
    f[1] = 1;
    for (int i = 2; i <= 42; i++) {
        f[i] = f[i - 1] * i / gcd(f[i - 1], i);
        // printf("%d : %lld\n",i,f[i]);
    }
    scanf("%d", &T);
    while (T--) {
        scanf("%lld", &n);
        ll res = n;
        for (int i = 1; f[i] <= n; i++) 
            res = (res + n / f[i] % MOD) % MOD;
        printf("%lld\n", res);
    }
    return 0;
}
```

## D

dp题。。看了半天题解。。。

就是算每个 $+ n$ 对答案的贡献。

首先定义考虑第 $I$ 个 $+$ 操作，将操作数记为 $X$ .

定义： $f(i,j)$ 表示前 $i$ 个数，操作完有 $j$ 个数小于 $X$ 的方案数。

分几类讨论：

不进行 $i+1$ 操作： $f(i + 1,j) += f(i,j)$ 

第 $i+1$ 个操作为 $+ A$ 

- $A \ge X$ ： $f(i + 1,j) += f(i,j)$ 
- $A < X$ ： $f(i + 1,j + 1) += f(i,j)$ 

第 $i+1$ 个操作为 $-$ 

- $i + 1 < I$ ： $f(i + 1,max(j - 1,0)) += f(i,j)$ 
- $i + 1 > I$ ： $f(i + 1,j - 1) += f(i,j)$ （这里要求 $j > 1$ ，即不能把X去掉）

最后答案

$ $ans += x \times \sum_{j \ge 0}f(n,j)$ $

```cpp
#include <bits/stdc++.h>
using namespace std;
#define N 511
#define MOD 998244353ll
// #define MOD 1000ll
typedef long long ll;
int n;
ll a[N], f[N][N], ans = 0;
char s[11];
bool add[N];
int main() {
    memset(add, 0, sizeof add);
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%s", s);
        if (s[0] == '+') {
            scanf("%lld", &a[i]);
            add[i] = true;
        }
    }
    for (int i = 1; i <= n; i++) {
        if (add[i]) {
            memset(f, 0, sizeof f);
            f[0][0] = 1;
            for (int j = 0; j < n; j++) {
                for (int k = 0; k <= j; k++) {
                    if (j + 1 != i) (f[j + 1][k] += f[j][k]) %= MOD;
                    if (add[j + 1]) {
                        if (a[j + 1] < a[i] || 
                            (a[j + 1] == a[i] && j + 1 < i)) {
                            (f[j + 1][k + 1] += f[j][k]) %= MOD;
                        } else {
                            (f[j + 1][k] += f[j][k]) %= MOD;
                        }
                    } else {
                        if (j + 1 < i) {
                            (f[j + 1][max(0, k - 1)] += f[j][k]) %= MOD;
                        }
                        else if(k > 0){
                            (f[j + 1][k - 1] += f[j][k]) %= MOD;
                        }
                    }
                }
            }
            for (int j = 0; j < n; j++) {
                (ans += (a[i] * f[n][j]) % MOD) %= MOD;
            }
        }
    }
    printf("%lld\n", ans);
    return 0;
}
```

记得要每个地方都要取模。。

## F F2

无

## 总结

D一开始都没想到是dp。最重要的还是状态定义。转移要考虑清楚。
