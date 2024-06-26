---
title: 自适应辛普森法
tags: 自适应辛普森法
categories: 计算几何
toc: false
date: 2021-08-16
---

一般求解定积分，采用自适应辛普森法。

二次函数积分公式（辛普森公式）[^simpson]

$ $\int_l^r f(x) {\mathrm d}x = \frac{(r-l)(f(l)+f(r)+4 f(\frac{l+r}{2}))}{6}$ $

## 普通辛普森法

根据辛普森公式，将一个区间分为连续的若干段，其中连续的两段作为一个周期，分别对应公式里的 $[l,\frac{l+r}{2}]$ 和 $[l,r]$ 。

```cpp
const int N = 1000 * 1000;
double simpson_integration(double a, double b) {
    double h = (b - a) / N;
    double s = f(a) + f(b);
    for (int i = 1; i <= N - 1; ++i) {
        double x = a + h * i;
        s += f(x) * ((i & 1) ? 4 : 2);
    }
    s *= h / 3;
    return s;
}
```

## 自适应辛普森

能够自动满足精度要求的辛普森法。

思想：

一段区间 $[l,r]$ 的积分值记为 $ans$ ，将其分为 $[l,\frac{l+r}{2}]$ ， $[\frac{l+r}{2}, r]$ 两段分别积分的值为 $ans_l$ ， $ans_r$ , 若 $\lvert ans - \left(ans_l+ans_r\right)\rvert \le eps$ ，就不再继续下去（但是一般要确定一个最少执行步数）。

代码[^code]：

```cpp
double simpson(double l, double r) {
    double mid = (l + r) / 2;
    return (r - l) * (f(l) + 4 * f(mid) + f(r)) / 6;  // 辛普森公式
}
double asr(double l, double r, double eqs, double ans, int step) {
    double mid = (l + r) / 2;
    double fl = simpson(l, mid), fr = simpson(mid, r);
    if (abs(fl + fr - ans) <= 15 * eqs && step < 0)
        return fl + fr + (fl + fr - ans) / 15;  // 足够相似的话就直接返回
    return asr(l, mid, eqs / 2, fl, step - 1) +
           asr(mid, r, eqs / 2, fr, step - 1);  // 否则分割成两段递归求解
}
double calc(double l, double r, double eps) {
    return asr(l, r, eps, simpson(l, r), 12);
}
```

[^simpson]: <https://oi-wiki.org/math/integral/#_2>
[^code]: <https://oi-wiki.org/math/integral/#_7>
