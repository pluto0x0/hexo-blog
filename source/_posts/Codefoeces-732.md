---
title: Codeforces 732
date: 2021-07-13
---

[Codeforces Round #732 (Div. 2)](https://codeforces.com/contest/1546)

## A

处理出哪些位置是加，那些位置是减。

```cpp
#include <cstdio>
#define N 101
int T;
int n,a[N],b[N];
int opa[11111], opb[11111];
int main()
{
    scanf("%d",&T);
    while (T--){
        scanf("%d",&n);
        for(int i = 1;i <= n;i++)
            scanf("%d",&a[i]);
        for(int i = 1;i <= n;i++)
            scanf("%d",&b[i]);
        int pa = 0, pb = 0;
        for(int i = 1;i <= n;i++){
            if(a[i] > b[i])
                for(int j = 1;j <= a[i] - b[i];j++)
                    opa[++pa] = i;
            else
                for(int j = 1;j <= b[i] - a[i];j++)
                    opb[++pb] = i;
        }
        if(pa != pb)
            printf("-1\n");
        else{
            printf("%d\n",pa);
            for(int i = 1;i <= pa;i++)
                printf("%d %d\n",opa[i],opb[i]);
        }
    }   
    
    return 0;
}
```

## B

各个位置的字符集合是不变的，将各个位置原来的集合减去后来的集合就行了。

```cpp
#include <cstdio>
#include <cstring>
#define M 100001
int T;
int n, m;
char a[M];
int cnt[M][27];
int main()
{
    scanf("%d",&T);
    while (T--) {
        scanf("%d %d",&n,&m);
        memset(cnt,0,sizeof cnt);
        for (int i = 1; i <= n; i++){
            scanf("%s",a + 1);
            for (int j = 1;j <= m;j++)
                cnt[j][a[j] - 'a']++;
        }
        for (int i = 1; i <= n - 1; i++){
            scanf("%s",a + 1);
            for (int j = 1;j <= m;j++)
                cnt[j][a[j] - 'a']--;
        }
        for (int i = 1;i <= m;i++)
            for(int j = 0;j <= 25;j++) 
                if(cnt[i][j] == 1)
                    putchar('a' + j);
        printf("\n");
        fflush(stdout);
    }
    return 0;
}
```

## C

pos[i]记录：原序列中数字i的不同位置中**有几个位置是偶数下标**。

再计算排序后数字i的连续位置中**有几个位置是偶数下标**，即为n0。若成立，有n0=pos[i]

因为再移动前后两位置差为偶数，操作数也一定是偶数。反之亦然。

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#define MAXA 100011
#define N 100011
int T,n,a[N];
int pos[MAXA];
int main()
{
    scanf("%d",&T);
    while (T--) {
        memset(pos,0,sizeof pos);
        scanf("%d",&n);
        for(int i = 1;i <= n;i++){
            scanf("%d",&a[i]);
            if(i % 2 == 0)
                pos[a[i]]++;
        }
        std::sort(a + 1,a + 1 + n);
        int j = 1;
        bool no = false;
        while(j <= n){
            int i = j;
            while(j <= n && a[j] == a[i])
                j++;
            int n0 = (j - i + 1 - i % 2) / 2;
            if(n0 != pos[a[i]]){
                printf("NO\n");
                no = true;
                break;
            }
        }
        if(!no)
            printf("YES\n");
    }
    return 0;
}
```

## D

题意即在这两种情况互相转化：

`011`<->`110`。

那么将相邻两个1几位一组（各个组之间不重叠），那么每次操作后组数不变。

并且所有组数相同的状态都能达到。

因此，记0的个数为m，组数为n，结果就是

$ $\binom{n+m}{m}$ $

至于落单的1，当成对的1排列好后，单个的1位置是固定的，不需要考虑。

```cpp
#include <bits/stdc++.h>
using namespace std;


const int MAXN = 100010;
const int MOD = 998244353;
char str[MAXN];
long long F[MAXN], rF[MAXN];
long long inv(long long a, long long m) {
    if (a == 1) return 1;
    return inv(m%a, m) * (m &mdash; m/a) % m;
}
int main() {
    int T;
    int n;
    F[0] = rF[0] = 1;
    for (int i = 1; i < MAXN; i++) {
        F[i] = F[i-1] * i % MOD;
        rF[i] = rF[i-1] * inv(i, MOD) % MOD;
    }
    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        scanf("%s", str);
        int cg = 0;
        int c0 = 0;
        int c1 = 0;
        for (int i = 0; i < n; i++) {
            if (str[i] == '0') {
                c0++;
            } else if (i+1 < n && str[i+1] == '1') {
                cg++;
                i++;
            } else {
                c1++;
            }
        }
        long long ans = F[cg + c0] * rF[c0] % MOD * rF[cg] % MOD;
        printf("%d\n", (int) ans);
    }
    return 0;
}
```

## 总结
D题这种应当发现其不变量，再用数学解决。

后面还有几题，下次再说。。
