---
title: 结论题：Codeforces 726
category: solution
date: 2021-07-12
---
[Codeforces Round #726 (Div. 2)](https://codeforces.com/contest/1537)

## A
没什么好说的
```cpp
#include <cstdio> 
int T,n;
int main(){
    scanf("%d",&T);
    while(T--){
	    scanf("%d",&n);
	    int tmp, sum = 0;
	    for(int i = 1;i <= n;i++){
		    scanf("%d",&tmp);
		    sum += tmp;
		}
	    if(sum == n)
		    printf("0\n");
	    else if(sum <= n + 1)
		    printf("1\n");
	    else printf("%d\n",sum - n);
	}
    return 0;
}
```
## B
找结论
```cpp
#include <cstdio>
int T,n,m,x,y;
int main()
{
    scanf("%d",&T);
    while(T--){
	    scanf("%d %d %d %d",&n,&m,&x,&y);
	    printf("1 1 %d %d\n",n,m);
	}
    return 0;
}
```
## C
不难发现，最优的方案：中->高,低->中
```cpp
#include <cstdio>
#include <algorithm>
#define N 200001
int T,n; 
int a[N];
int main()
{
    scanf("%d",&T);
    while(T--){
	    scanf("%d",&n);
	    for(int i = 1;i <= n;i++)
		    scanf("%d",&a[i]);
	    std::sort(a + 1,a + n + 1);
	    int ans = 1;
	    for(int i = 2;i < n;i++)
		    if(a[i + 1] - a[i] < a[ans + 1] - a[ans])
			    ans = i;
	    if(n == 2)
		    printf("%d %d",a[1],a[2]);
	    else{
		    for(int i = ans + 1;i <= n;i++)
			    printf("%d ",a[i]);
		    for(int i = 1;i <= ans;i++)
			    printf("%d ",a[i]);
		}
	    printf("\n");
	}
    return 0;
}
```
## D
数学题，挺难想到的
n有三种情况
1. 奇数
2. 偶数（非2的次幂）
3. 2的次幂 
### 奇数
此时n的因子一定全为奇数。所以一次操作后n一定变为偶数（且一定含有奇数因子），所以是情况2。
### 偶数（非2的次幂）
这意味着n有存在一个奇数因子。因此减去这个因子，一定能得到一个奇数（情况1），那么再经过对方的一次操作，一定能回到这种情况（或直接失败）。因此，这种情况是先手必胜。
### 2的次幂
如果将n减半，那么显然还是情况三。否则将变为情况二，这样对手就处于必胜，这是必败策略。因此，只能每次将n减半，最终取得1的一方失败。

```cpp
#include "bits/stdc++.h"

using namespace std;
int main()
{
    int t;
    cin >> t;
    
    while(t--){
        
        int n;
        cin >> n;
        if(n % 2 == 1){
            cout << "Bob\n";
            continue;
        }
        int cnt = 0;
        while(n % 2 == 0){
            cnt++;
            n /= 2;
        }
 
        if(n > 1){
            cout << "Alice\n";
        }else if(cnt % 2 == 0){
            cout << "Alice\n";
        }else cout << "Bob\n";
    }
}
```

## E2
不难发现，所求字符串的本质就是原串的若干前缀相加。进一步可以发现，字典序最小时，一定是一个固定前缀的重复。需要找到这个前缀
### 情况一
例子：
![image.png](https://i.loli.net/2021/07/11/6nBXoRY8vG27bgI.png)
原串：DCAFB
目标字符串为：DCADCADCA...
结论：找到第一个大于原串开头（'D'）的位置（'F'）即可。
### 情况二
就是先遇到了与开头相同的情况。
例子：
讨论第一个不同的位置（红色）：
**原串大:**
![image.png](https://i.loli.net/2021/07/11/WqJawVzhmulEUeO.png)
则选择原串DCBA部分为前缀组成目标字符串。
**原串小:**
![ad](https://i.loli.net/2021/07/11/OoaZ34weMF1ImUb.png)
继续向后移动（绿色）

### 复杂度
原串每个位置只会被比较一次，因此复杂度为O(N)

题解好像是用[Z函数](https://oi-wiki.org/string/z-func/)。。学习一下。
## F
首先\\\(\sum{target_i-v_i}\\\)一定是偶数，否则为NO，因为每条边操作的该变量是偶数。
然后考虑二分图。
### 二分图
二分图左右点集各自的必须相等，因为所有边都在两个点集之间。
符合，就是YES，否则NO
### 非二分图
意味着存在连接一个点集内部的边，通过这类边的变化，一定可以满足二分图情况的条件。
```cpp
#include <cstdio>
#include <vector>
#include <queue>
#include <cstdlib>
#include <cstring>
#define N 200001
using namespace std;
vector<int> e[N];
typedef vector<int>::iterator it;
typedef long long ll;
int T;
int n,m;
ll a[N];
char state[N];
queue<pair<int,char> > q;
bool bfs(int node){
    queue<pair<int,char> > Empty;
    q.swap(Empty);
    q.push(make_pair(node,(char)1));
    state[node] = 1;
    ll sum[3] = {0ll, 0ll, 0ll};
    while(!q.empty()){
        pair<int,char> u = q.front();
        q.pop();
        sum[u.second] += a[u.first];
        char cur = (char)(3 - u.second);
        for(it i = e[u.first].begin();i != e[u.first].end();i++){
            if(state[*i] == 0){
                state[*i] = cur;
                q.push(make_pair(*i,cur));
            }
            else if(state[*i] != cur)
                return true;
        }
    }
    return sum[1] == sum[2];
}
int main()
{
    scanf("%d",&T);
    while(T--){
        scanf("%d %d",&n,&m);
        for(int i = 1;i <= n;i++)
            e[i].clear();
        for(int i = 1;i <= n;i++)
            scanf("%lld",&a[i]);
        ll t0;
        for(int i = 1;i <= n;i++)
            scanf("%lld",&t0), a[i] = t0 - a[i];
        int tmp,tmp2;
        for(int i = 1;i <= m;i++){
            scanf("%d %d",&tmp,&tmp2);
            e[tmp].push_back(tmp2);
            e[tmp2].push_back(tmp);
        }
        ll sum = 0;
        for(int i = 1;i <= n;i++){
            sum = sum + a[i];
            sum = (sum % 2ll + 2ll) % 2ll;
        }
        if(sum == 1ll){
            printf("NO\n");
            continue;
        }
        memset(state,0,sizeof state);
        bool no = false;
        for(int i = 1;i <= n;i++){
            if(state[i] == 0){
                if(i > 1){
                    printf("!!!!!!!\n");
                }
                if(!bfs(i)){
                    printf("NO\n");
                    no = true;
                    break;
                }
            }
        }
        if(!no)
            printf("YES\n");
    }
    return 0;
}
```
二分图确实挺难想到的。
## 总结
感觉都是结论题D、F题的结论都很巧妙，自己没有想到。
