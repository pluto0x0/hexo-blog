---
title: 树链剖分
category: 图论
tag:
- 树链剖分
- 模板
date: 2021-09-29
---

[#10138. 「一本通 4.5 例 1」树的统计](https://loj.ac/p/10138)

```cpp
#include <bits/stdc++.h>
#define rep(i, a, b) for (int i = (a); i <= (b); i++)
#define per(i, a, b) for (int i = (a); i >= (b); i--)
#define D(x) cerr<< #x << " : " << x << endl
using namespace std;
const int inf = 0x3fffffff;
const int N = 100011;

vector<int> edge[N];
int n;
int dep[N], fa[N], siz[N], hson[N], top[N], dfn[N], rnk[N], clk = 0;

int w[N];

void dfs1(int u) {
    siz[u] = 1, hson[u] = -1;
    for (int v : edge[u]) {
        if(v == fa[u]) continue;
        dep[v] = dep[u] + 1, fa[v] = u;
        dfs1(v);
        siz[u] += siz[v];
        if (hson[u] == -1 || siz[v] > siz[hson[u]]) hson[u] = v;
    }
}

void dfs2(int u, int tp) {
    dfn[u] = ++clk, rnk[clk] = u;
    top[u] = tp;
    if (hson[u] == -1) return;
    dfs2(hson[u], tp);
    for (int v : edge[u])
        if (v != fa[u] && v != hson[u]) dfs2(v, v);
}

int mx[N], sum[N];

inline void maintain(int u) {
    mx[u] = max(mx[u * 2], mx[u * 2 + 1]);
    sum[u] = sum[u * 2] + sum[u * 2 + 1];
}

void build(int u, int l, int r) {
    if (l == r) {
        mx[u] = sum[u] = w[rnk[l]];
        return;
    }
    int mid = (l + r) / 2;
    build(u * 2, l, mid), build(u * 2 + 1, mid + 1, r);
    maintain (u);
}

void update(int u, int pos, int val, int l, int r) {
    if (l == r) {
        mx[u] = sum[u] = val;
        return;
    }
    int mid = (l + r) / 2;
    if (pos <= mid)
        update(u * 2, pos, val, l, mid);
    else
        update(u * 2 + 1, pos, val, mid + 1, r);
    maintain(u);
}

int queryMx (int u, int l, int r, int L, int R) {
    if (l == L && r == R) return mx[u];
    int mid = (L + R) / 2;
    if (r <= mid) return queryMx(u * 2, l, r, L, mid);
    if (l > mid) return queryMx(u * 2 + 1, l, r, mid + 1, R);
    return max(queryMx(u * 2, l, mid, L, mid),
               queryMx(u * 2 + 1, mid + 1, r, mid + 1, R));
}

int querySum(int u, int l, int r, int L, int R) {
    if (l == L && r == R) return sum[u];
    int mid = (L + R) / 2;
    if (r <= mid) return querySum(u * 2, l, r, L, mid);
    if (l > mid) return querySum(u * 2 + 1, l, r, mid + 1, R);
    return querySum(u * 2, l, mid, L, mid) +
           querySum(u * 2 + 1, mid + 1, r, mid + 1, R);
}

int queryMx(int l,int r) {
    if(l > r) swap(l, r);
    return queryMx(1,l,r,1,n);
}

int querySum(int l,int r) {
    if(l > r) swap(l, r);
    return querySum(1,l,r,1,n);
}

int Sum(int u, int v) {
    int tot = 0;
    while (top[u] != top[v]) {
        if (dep[top[u]] < dep[top[v]]) swap(u, v);
        tot += querySum(dfn[top[u]], dfn[u]);
        u = fa[top[u]];
    }
    return tot + querySum(dfn[u], dfn[v]);
}

int Max(int u, int v) {
    int tot = -inf;
    while (top[u] != top[v]) {
        if (dep[top[u]] < dep[top[v]]) swap(u, v);
        tot = max(tot, queryMx(dfn[top[u]], dfn[u]));
                  u = fa[top[u]];
    }
          return max(tot, queryMx(dfn[u], dfn[v]));
}

int main() {
    cin >> n;
    rep(i, 1, n - 1) {
        int x, y;
        scanf("%d %d", &x, &y);
        edge[x].push_back(y), edge[y].push_back(x);
    }
    rep(i, 1, n) scanf("%d", &w[i]);

    fa[1] = 1, dep[1] = 1, dfs1(1);
    dfs2(1, 1);
    build(1, 1, n);

    char opt[20];
    int m, x, y;
    cin >> m;
    rep(i, 1, m) {
        scanf("%s %d %d", opt, &x, &y);
        if (opt[0] == 'C')
            update(1, dfn[x], y, 1, n);
        else {
            int ans;
            if (opt[1] == 'M')
                ans = Max(x, y);
            else
                ans = Sum(x, y);
            printf("%d\n", ans);
        }
    }
    return 0;
}
```

[#139. 树链剖分](https://loj.ac/p/139)

换根操作：

因为树上路径是唯一的，因此换根不受影响。

子树：查询节点是u，根是v，显然只有在u子树内时对答案才有影响。答案是：所有节点的和-子树t的和，t是u--v路径上距离u最近的点。

```cpp
#include <bits/stdc++.h>
#define rep(i, a, b) for (int i = (a); i <= (b); i++)
#define per(i, a, b) for (int i = (a); i >= (b); i--)
#define D(x) cerr<< #x << " : " << x << endl
using namespace std;
typedef long long ll;
const int inf = 0x3fffffff;
const int N = 100011;

vector<int> edge[N];
int n;
int dep[N], fa[N], siz[N], hson[N], top[N], dfn[N], rnk[N], bottom[N], w[N], clk = 0;

int root = 1;

void dfs1(int u) {
    siz[u] = 1, hson[u] = -1;
    for (int v : edge[u]) {
        if(v == fa[u]) continue;
        dep[v] = dep[u] + 1, fa[v] = u;
        dfs1(v);
        siz[u] += siz[v];
        if (hson[u] == -1 || siz[v] > siz[hson[u]]) hson[u] = v;
    }
}

void dfs2(int u, int tp) {
    bottom[u] = dfn[u] = ++clk, rnk[clk] = u;
    top[u] = tp;
    if (hson[u] == -1) return;
    dfs2(hson[u], tp), bottom[u] = bottom[hson[u]];
    for (int v : edge[u])
        if (v != fa[u])
            if(v != hson[u]) dfs2(v, v), bottom[u] = bottom[v];
}

int L[N * 4], R[N * 4];
ll sum[N * 4], tag[N * 4];

inline void maintain(int u) {
    sum[u] = sum[u * 2] + sum[u * 2 + 1];
}

inline void spread(int u) {
    if(!tag[u]) return;
    sum[u * 2] += (R[u * 2] - L[u * 2] + 1) * tag[u];
    sum[u * 2 + 1] += (R[u * 2 + 1] - L[u * 2 + 1] + 1) * tag[u];
    tag[u * 2] += tag[u], tag[u * 2 + 1] += tag[u];
    tag[u] = 0;
}

void build(int u, int l, int r) {
    L[u] = l, R[u] = r, tag[u] = 0;
    if (l == r) {
        sum[u] = w[rnk[l]];
        return;
    }
    int mid = (l + r) / 2;
    build(u * 2, l, mid), build(u * 2 + 1, mid + 1, r);
    maintain(u);
}


void updateSum(int u, int l, int r, int val) {
    if (l == L[u] && r == R[u]) {
        sum[u] += (ll)(R[u] - L[u] + 1) * (ll)val;
        tag[u] += (ll)val;
        return;
    }
    spread(u);
    int mid = (L[u] + R[u]) / 2;
    if (r <= mid) updateSum(u * 2, l, r, val);
    else if(l > mid) updateSum(u * 2 + 1, l, r, val);
    else updateSum(u * 2, l, mid, val), updateSum(u * 2 + 1, mid + 1, r, val);
    maintain(u);
}

void updateSum(int l,int r,int val) {
    if(l > r) swap(l, r);
    updateSum(1, l, r, val);
}

ll querySum(int u, int l, int r) {
    if (l == L[u] && r == R[u]) return sum[u];
    spread(u);
    int mid = (L[u] + R[u]) / 2;
    if (r <= mid) return querySum(u * 2, l, r);
    if (l > mid) return querySum(u * 2 + 1, l, r);
    return querySum(u * 2, l, mid) +
           querySum(u * 2 + 1, mid + 1, r);
}

ll querySum(int l, int r) {
    if(l > r) swap(l, r);
    return querySum(1, l, r);
}

ll query(int u, int v) {
    ll tot = 0;
    while (top[u] != top[v]) {
        if (dep[top[u]] < dep[top[v]]) swap(u, v);
        tot += querySum(dfn[top[u]], dfn[u]);
        u = fa[top[u]];
    }
    return tot + querySum(dfn[u], dfn[v]);
}

int findSon(int u,int v) {
    int vv;
    while(top[v] != top[u])
        vv = top[v], v = fa[top[v]];
    if(v != u) return hson[u];
    return vv;
}

ll query(int u) {
    if(u == root) return querySum(1, n);
    if(dfn[root] >= dfn[u] && dfn[root] <= bottom[u]) {
        int t = findSon(u, root);
        return querySum(1, n) - querySum(dfn[t], bottom[t]);
    }
    return querySum(dfn[u], bottom[u]);
}

void update(int u, int v, int val) {
    while (top[u] != top[v]) {
        if (dep[top[u]] < dep[top[v]]) swap(u, v);
        updateSum(dfn[top[u]], dfn[u], val);
        u = fa[top[u]];
    }
    updateSum(dfn[u], dfn[v], val);
}

void update(int u, int val) {
    if(u == root) return updateSum(1, n, val);
    if(dfn[root] >= dfn[u] && dfn[root] <= bottom[u]) {
        updateSum(1, n ,val);
        int t = findSon(u, root);
        updateSum(dfn[t], bottom[t], -val);
    }
    else updateSum(dfn[u], bottom[u], val);
}

int main() {
    cin >> n;
    rep(i, 1, n) scanf("%d", &w[i]);
    rep(i, 2, n) {
        int ff;
        scanf("%d", &ff);
        edge[ff].push_back(i), edge[i].push_back(ff);
    }

    fa[1] = 1, dep[1] = 1, dfs1(1);
    dfs2(1, 1);
    build(1, 1, n);

    int m, opt, u, v, k;
    cin>>m;
    rep(i, 1, m) {
        scanf("%d %d", &opt, &u);
        switch(opt) {
        case 1:
            root = u;
            break;
        case 2:
            scanf("%d %d", &v, &k);
            update(u,v,k);
            break;
        case 3:
            scanf("%d",&k);
            update(u,k);
            break;
        case 4:
            scanf("%d",&v);
            printf("%lld\n", query(u,v));
            break;
        case 5:
            printf("%lld\n", query(u));
            break;
        }
    }
    return 0;
}
```
