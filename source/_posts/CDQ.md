---
title: 简述CDQ分治
date: 2021-07-26
---

luogu随机到了一道题，看了题解才知道是CDQ。。

## 三维偏序

求满足

$ $i<j,a_{i}<a_{j},b_{i}<b_{j}$ $

的 $(i,j)$ 对数。

用 $solve(l,r)$ 表示求解区间 $[l,r]$ 。把区间折半，可以递归求解 $solve(l,mid)$ 和 $solve(mid+1,r)$ 。现在关心

$ $l\le i \le mid,\;mid+1\le j\le r$ $

的求解。

将 $[l,mid]$ 和 $[mid+1,r]$ 分别按照 $a$ 排序，利用双指针，对于每一个 $j \le [mid+1, r]$ 将所有 $a_i < a_j$ 的 $i$ 加入树状数组。加入树状数组时，在 $b_i$ 位置 $+1$ （可能要离散化）。对于每个 $j$ ，询问树状数组 $\le b_j$ 位置的和求和即可。

复杂度 $O(n\,log^2n)$ 。

## dp转移

考虑dp转移

$ $dp_{i}=1+ \max_{j=1}^{i-1}dp_{j}[a_{j}<a_{i}][b_{j}<b_{i}]$ $

其实就是二维的最长上升子序列。

用树状数组维护最大值，其他差不多，但是注意 $l\le i \le mid,\;mid+1\le j\le r$ 的求解要在 $solve(l,mid)$ 和 $solve(mid+1,r)$ 之间进行，因为如果先执行 $solve(mid+1,r)$ ，此时 $mid+1$ wei位置还没有通过 $mid$ 之前位置来转移，不是正确的dp值，不能用来更新 $mid+1$ 位置以后的位置，而使用“中序遍历”则可以解决这个问题。

### luogu 4093

将一个位置能达到的最小值（包括原始数 $a$ ）记为 $mn$ ， 最大值（包括原始数 $a$ ）记为 $mx$ ，不难发现转移是 $mx_i \le a_j$ 且 $a_i \le mn_j$ 。

左右两边分别按照 $mx$ 和 $a$ 升序。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 100011;

int n, m, a[N], mn[N], mx[N], maxa = 0;
int f[N], ans = 0;
int tr[100011];

inline void add(int pos, int val) {
    for (; pos <= maxa + 1; pos += pos & (-pos)) tr[pos] = max(tr[pos], val);
}

inline void del(int pos, int val) {
    for (; pos <= maxa + 1; pos += pos & (-pos)) tr[pos] = 0;
}

inline int query(int pos) {
    int res = 0;
    for (; pos; pos -= pos & (-pos)) res = max(res, tr[pos]);
    return res;
}

inline bool cmpMx(int x, int y) { return mx[x] < mx[y]; }
inline bool cmpA(int x, int y) { return a[x] < a[y]; }

int id[N];
void solve(int l, int r) {
    if (l >= r) return;
    int mid = (l + r) / 2;
    solve(l, mid);
    for (int i = l; i <= r; i++) id[i] = i;
    sort(id + l, id + mid + 1, cmpMx);
    sort(id + mid + 1, id + r + 1, cmpA);
    int i = l;
    for (int j = mid + 1; j <= r; j++) {
        while (i <= mid && mx[id[i]] <= a[id[j]]) {
            add(a[id[i]], f[id[i]]);
            i++;
        }
        f[id[j]] = max(f[id[j]], query(mn[id[j]]) + 1);
    }
    for (int j = l; j < i; j++) del(a[id[j]]);
    solve(mid + 1, r);
}

int main() {
    cin.tie(0);
    cin.sync_with_stdio(false);
    memset(tr, 0, sizeof tr);

    cin >> n >> m;
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
        mx[i] = mn[i] = a[i];
        maxa = max(maxa, a[i]);
    }

    for (int i = 1, x, y; i <= m; i++) {
        cin >> x >> y;
        if (y < mn[x]) mn[x] = y;
        if (y > mx[x]) mx[x] = y;
    }
    for (int i = 1; i <= n; i++) f[i] = 1;

    solve(1, n);

    for (int i = 1; i <= n; i++)
        if (f[i] > ans) ans = f[i];
    cout << ans << endl;

    return 0;
}
```

_upd: [原代码](https://paste.ubuntu.com/p/vN7kPG5CsQ/) 存在一些重大bug：每次solve暴力memset树状数组清空，竟然跑出了860ms。。。_

## 时间序列

将修改、询问等一系列操作根据时间序列折半，将在操作离线。
