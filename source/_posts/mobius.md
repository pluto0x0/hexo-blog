---
title: 莫比乌斯反演例题
date: 2021-07-30
---

关于莫比乌斯反演，[这篇](https://oi-wiki.org/math/mobius/)说得非常清楚了。

记录以下做过的莫比乌斯反演例题。

## [P2257 YY的GCD](https://www.luogu.com.cn/problem/P2257)

$$\begin{aligned}
&\sum_{i=1}^N \sum_{j=1}^M \sum_{p \in P ,\, p|i ,\, p|j}[\gcd\left(\frac{i}{p},\frac{j}{p}\right)=1] \\

=&\sum_{p \in P} \sum _{i=1}^{ \lfloor \frac{N}{p} \rfloor } \sum _{j=1}^{ \lfloor \frac{M}{p} \rfloor } \varepsilon(\gcd(i,j)) \\

=&\sum_{p \in P} \sum _{i=1}^{ \lfloor \frac{N}{p} \rfloor } \sum _{j=1}^{ \lfloor \frac{M}{p} \rfloor } \sum_{d|\gcd(i,j)}\mu(d) \\

=& \sum_{p\in P}\sum_{d=1}\sum _{i=1}^{ \lfloor \frac{N}{pd} \rfloor } \sum _{j=1}^{ \lfloor \frac{M}{pd} \rfloor} \mu(d)
\end{aligned}$$

令 $t=pd$ 

$$
\sum_t\lfloor \frac{N}{t}\rfloor \lfloor \frac{M}{t}\rfloor \sum_{p|t,\, p\in P}\mu\left(\frac{t}{p}\right)
$$

只需要预处理

$ $f(t)=\sum_{p|t,\, p\in P}\mu\left(\frac{t}{p}\right)$ $

一般的做法是直接枚举 $p$ ，然后暴力更新。例如：
```cpp
for (int i = 1; i <= cnt; i++)
    for (int j = 1; prime[i] * j <= 10000000; j++)
        f[j * prime[i]] += mu[j];
```

然而是有 $O(n)$ 预处理方法的。

$ $f(t) \neq 0$ 当且仅当 $t$ 有不超过一个平方因子。当 $t$ 有平方因子 $p_0^2$ $时，
只有上式中的 $p$ 取 $p_0$ 
时有贡献，否则任意 $p$ 贡献相同，都为 $-\mu(t)$ 。

预处理时分类即可，注意每个合数指挥被其最小质因子筛出。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 10000010;
typedef long long ll;

int T, N, M;
int p[MAXN / 10], tot = 0;
int mu[MAXN], f[MAXN];
char no[MAXN];

void getp(int n) {
    memset(no, 0, sizeof no);
    no[1] = 1, mu[1] = 1;
    for (int i = 2; i <= n; i++) {
        if (!no[i]) p[++tot] = i, mu[i] = -1, f[i] = 1;
        for (int j = 1, k; j <= tot && (k = p[j] * i) <= n; j++) {
            no[k] = 1;
            if (i % p[j] == 0) {
                mu[k] = 0;
                if (mu[i] != 0)
                    f[k] = mu[i];
                else
                    f[k] = 0;
                break;
            }
            mu[k] = -mu[i];
            if (mu[i] == 0 && f[i] != 0)
                f[k] = -f[i];
            else
                f[k] = (abs(f[i]) + 1) * mu[i];
        }
    }
    f[0] = f[1] = 0;
    for (int i = 2; i <= n; i++) f[i] += f[i - 1];
}

ll solve() {
    ll res = 0;
    for (int l = 1, r; l <= N; l = r + 1) {
        r = min(N / (N / l), M / (M / l));
        res += (ll)(N / l) * (ll)(M / l) * (ll)(f[r] - f[l - 1]);
    }
    return res;
}

int main() {
    cin.tie(0);
    cin.sync_with_stdio(false);
    getp(10000000);

    cin >> T;
    while (T--) {
        cin >> N >> M;
        if (N > M) swap(N, M);
        cout << solve() << endl;
    }
    return 0;
}
```
