---
title: 非旋转式Treap
tags:
- treap
- 数据结构
- 平衡树
categories: 平衡树
date: 2021-08-14
---

Treap分为旋转式和非旋转式，编写复杂度都不高。非旋转式的基本操作是分裂、合并，因此支持可持久化。

这里采用了记录权值出现次数的操作，由 $cnt$ 这一字段记录，树的大小由 $siz$ 记录。

以下是基本操作：

## 分裂 split

把树分别分裂成 $\le val$ 和 $> val$ 的两部分。

递归分裂左或右子树。

## 合并 merge

合并两棵树，其中第一棵树中所有值小于第二棵树的。

比较 $pri$ 字段值的大小，维护大根堆性质。

## 查找 find

查找值 $val$ 。

## 前缀 pre

查找前缀。

若 $val < \text{当前值}$ ，向左子树递归，否则向右。

## 后缀 sub

查找后缀。

若 $val > \text{当前值}$ ，向右子树递归，否则向左。

## 排名 rnk

查询值 $val$ 的排名。

## 第k大 num

查找第 $k$ 大的数。

## 删除 val

删除一个权值 $val$ 。

若 $cnt > 1$ ，则 $cnt--$

## 其他私有方法

### maintain

向上更新。

### upd

更新根节点到 $val$ 路径上节点的 $siz$ 信息。

模板：

<https://www.luogu.com.cn/problem/P3369>

<https://www.luogu.com.cn/problem/P6136>

```cpp
template <typename T>
class Treap {
   private:
    struct node {
        T val;
        int pri, siz, cnt;
        node *l, *r;
        node() {
            l = r = nullptr;
            siz = cnt = 0;
        }
        node(T v) {
            l = r = nullptr;
            val = v, pri = rand();
            siz = cnt = 1;
        }
    };
    node *root;

   private:
    inline void maintain(node *u) {
        u->siz = u->cnt;
        if (u->l != nullptr) u->siz += u->l->siz;
        if (u->r != nullptr) u->siz += u->r->siz;
    }

   private:
    pair<node *, node *> split(node *u, T val) {
        if (u == nullptr) return make_pair(nullptr, nullptr);
        if (val < u->val) {
            auto v = split(u->l, val);
            u->l = v.second;
            maintain(u);
            return make_pair(v.first, u);
        } else {
            auto v = split(u->r, val);
            u->r = v.first;
            maintain(u);
            return make_pair(u, v.second);
        }
    }

   private:
    node *merge(node *u, node *v) {
        if (u == nullptr) return v;
        if (v == nullptr) return u;
        if (u->pri < v->pri) {
            v->l = merge(u, v->l);
            maintain(v);
            return v;
        } else {
            u->r = merge(u->r, v);
            maintain(u);
            return u;
        }
    }

   private:
    node *find(node *u, T val) {
        if (u == nullptr) return nullptr;
        if (u->val == val) return u;
        if (val < u->val) return find(u->l, val);
        return find(u->r, val);
    }

   private:
    node *pre(node *u, T val) {
        if (u == nullptr) return nullptr;
        if (val > u->val) {
            auto ret = pre(u->r, val);
            if (ret == nullptr) ret = u;
            return ret;
        }
        return pre(u->l, val);
    }

   private:
    node *sub(node *u, T val) {
        if (u == nullptr) return nullptr;
        if (val < u->val) {
            auto ret = sub(u->l, val);
            if (ret == nullptr) ret = u;
            return ret;
        }
        return sub(u->r, val);
    }

   private:
    node *num(node *u, int rnk) {
        int sz = 0;
        if (u->l != nullptr) sz = u->l->siz;
        if (rnk >= sz + 1 && rnk <= sz + u->cnt) return u;
        if (rnk <= sz) return num(u->l, rnk);
        return num(u->r, rnk - sz - u->cnt);
    }

   private:
    int rnk(node *u, T val) {
        // count from 1
        if (u == nullptr) return 1;
        int sz = 0;
        if (u->l != nullptr) sz = u->l->siz;
        if (val == u->val) return sz + 1;
        if (val < u->val) return rnk(u->l, val);
        return sz + u->cnt + rnk(u->r, val);
    }

   private:
    void _upd(node *u, T val) {
        if (u == nullptr || u->val == val) return;
        if (val < u->val)
            _upd(u->l, val);
        else
            _upd(u->r, val);
        maintain(u);
    }

   public:
    node *insert(T val) {
        auto ret = find(root, val);
        if (ret != nullptr) {
            ret->cnt++, ret->siz++;
            _upd(root, val);
            return ret;
        }
        ret = new node(val);
        auto v = split(root, val);
        root = merge(merge(v.first, ret), v.second);
        return ret;
    }

   public:
    bool del(T val) {
        auto v = find(root, val);
        if (v == nullptr) return false;
        if (v->cnt > 1) {
            v->cnt--, v->siz--;
            _upd(root, val);
        } else {
            auto o = split(root, val - 1);
            auto p = split(o.second, val);
            delete p.first;
            root = merge(o.first, p.second);
        }
        return true;
    }

   public:
    int rnk(T val) { return rnk(root, val); }

   public:
    T num(int rnk) { return num(root, rnk)->val; }

   public:
    T pre(T val) { return pre(root, val)->val; }

   public:
    T sub(T val) { return sub(root, val)->val; }
};
```
