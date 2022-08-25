---
title: "Union-Find"
free: true
---

C++ 標準ライブラリを用いた Union-Find (別名: Disjoint-set) の実装です。


# 1. Union-Find のテンプレート

| 機能 | 説明 | 1.1 | 1.2 | 1.3 | 1.4 |
|----|----|:----:|:----:|:----:|:----:|
| `.find(i)` | i を含むグループの root (代表) を返す | ✅ | ✅ | ✅ | ✅ |
| `.merge(a, b)` | a を含むグループと b を含むグループを併合する | ✅ | ✅ | ✅ | ✅ |
| `.connected(a, b)` | a と b が同じグループに属すかを返す | ✅ | ✅ | ✅ | ✅ |
| `.size(i)` | i を含むグループの要素数を返す |  | ✅ | ✅ | ✅ |
| 経路圧縮 | 計算量をおよそ $O(log N)$ に減らす | ✅ | ✅ | ✅ | ✅ |
| union by size | 経路圧縮との組み合わせで、計算量を競プロの実用範囲でほぼ $O(1)$ に減らす |  |  | ✅ | ✅ |

## 1.1 シンプルな実装
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <numeric> // std::iota()

// Union-Find 木 (1.1 シンプルな実装)
class UnionFind
{
public:

	UnionFind() = default;

	// n 個の要素
	explicit UnionFind(size_t n)
		: m_parents(n)
	{
		std::iota(m_parents.begin(), m_parents.end(), 0);
	}

	// i の root を返す
	int find(int i)
	{
		if (m_parents[i] == i)
		{
			return i;
		}

		// 経路圧縮
		return (m_parents[i] = find(m_parents[i]));
	}

	// a の木と b の木を統合
	void merge(int a, int b)
	{
		a = find(a);
		b = find(b);

		if (a != b)
		{
			m_parents[b] = a;
		}
	}

	// a と b が同じ木に属すかを返す
	bool connected(int a, int b)
	{
		return (find(a) == find(b));
	}

private:

	// m_parents[i] は i の 親,
	// root の場合は自身が親
	std::vector<int> m_parents;
};
```

- [Library Checker](https://judge.yosupo.jp/submission/101500)
:::


## 1.2 グループの要素数取得対応
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <numeric> // std::iota()

// Union-Find 木 (1.2 グループの要素数取得対応)
class UnionFind
{
public:

	UnionFind() = default;

	// n 個の要素
	explicit UnionFind(size_t n)
		: m_parents(n)
		, m_sizes(n, 1)
	{
		std::iota(m_parents.begin(), m_parents.end(), 0);
	}

	// i の root を返す
	int find(int i)
	{
		if (m_parents[i] == i)
		{
			return i;
		}

		// 経路圧縮
		return (m_parents[i] = find(m_parents[i]));
	}

	// a の木と b の木を統合
	void merge(int a, int b)
	{
		a = find(a);
		b = find(b);

		if (a != b)
		{
			m_sizes[a] += m_sizes[b];
			m_parents[b] = a;
		}
	}

	// a と b が同じ木に属すかを返す
	bool connected(int a, int b)
	{
		return (find(a) == find(b));
	}

	// i が属するグループの要素数を返す
	int size(int i)
	{
		return m_sizes[find(i)];
	}

private:

	// m_parents[i] は i の 親,
	// root の場合は自身が親
	std::vector<int> m_parents;

	// グループの要素数 (root 用)
	// i が root のときのみ, m_sizes[i] はそのグループに属する要素数を表す
	std::vector<int> m_sizes;
};
```

- [Library Checker](https://judge.yosupo.jp/submission/101501) / [ABC 177 D](https://atcoder.jp/contests/abc177/submissions/34319606)
:::


## 1.3 高速化
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <numeric> // std::iota()
#include <utility> // std::swap()

// Union-Find 木 (1.3 高速化)
class UnionFind
{
public:

	UnionFind() = default;

	// n 個の要素
	explicit UnionFind(size_t n)
		: m_parents(n)
		, m_sizes(n, 1)
	{
		std::iota(m_parents.begin(), m_parents.end(), 0);
	}

	// i の root を返す
	int find(int i)
	{
		if (m_parents[i] == i)
		{
			return i;
		}

		// 経路圧縮
		return (m_parents[i] = find(m_parents[i]));
	}

	// a の木と b の木を統合
	void merge(int a, int b)
	{
		a = find(a);
		b = find(b);

		if (a != b)
		{
			// union by size (小さいほうが子になる）
			if (m_sizes[a] < m_sizes[b])
			{
				std::swap(a, b);
			}

			m_sizes[a] += m_sizes[b];
			m_parents[b] = a;
		}
	}

	// a と b が同じ木に属すかを返す
	bool connected(int a, int b)
	{
		return (find(a) == find(b));
	}

	// i が属するグループの要素数を返す
	int size(int i)
	{
		return m_sizes[find(i)];
	}

private:

	// m_parents[i] は i の 親,
	// root の場合は自身が親
	std::vector<int> m_parents;

	// グループの要素数 (root 用)
	// i が root のときのみ, m_sizes[i] はそのグループに属する要素数を表す
	std::vector<int> m_sizes;
};
```

- [Library Checker](https://judge.yosupo.jp/submission/101503) / [ABC 177 D](https://atcoder.jp/contests/abc177/submissions/34319608)
:::


## 1.4 高速化 + 省メモリ化
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::swap()

// Union-Find 木 (1.4 高速化 + 省メモリ化)
class UnionFind
{
public:

	UnionFind() = default;

	// n 個の要素
	explicit UnionFind(size_t n)
		: m_parentsOrSize(n, -1) {}

	// i の root を返す
	int find(int i)
	{
		if (m_parentsOrSize[i] < 0)
		{
			return i;
		}

		// 経路圧縮
		return (m_parentsOrSize[i] = find(m_parentsOrSize[i]));
	}

	// a の木と b の木を統合
	void merge(int a, int b)
	{
		a = find(a);
		b = find(b);

		if (a != b)
		{
			// union by size (小さいほうが子になる）
			if (-m_parentsOrSize[a] < -m_parentsOrSize[b])
			{
				std::swap(a, b);
			}

			m_parentsOrSize[a] += m_parentsOrSize[b];
			m_parentsOrSize[b] = a;
		}
	}

	// a と b が同じ木に属すかを返す
	bool connected(int a, int b)
	{
		return (find(a) == find(b));
	}

	// i が属するグループの要素数を返す
	int size(int i)
	{
		return -m_parentsOrSize[find(i)];
	}

private:

	// m_parentsOrSize[i] は i の 親,
	// ただし root の場合は (-1 * そのグループに属する要素数)
	std::vector<int> m_parentsOrSize;
};
```

- [Library Checker](https://judge.yosupo.jp/submission/101504) / [ABC 177 D](https://atcoder.jp/contests/abc177/submissions/34319612)
:::

# 2. Union-Find の例題

### [ATC 001 B - Union Find](https://atcoder.jp/contests/atc001/tasks/unionfind_a)

### [ABC 075 C - Bridge](https://atcoder.jp/contests/abc075/tasks/abc075_c)

### [ABC 049 D - 連結](https://atcoder.jp/contests/abc049/tasks/arc065_b)

### [ABC 177 D - Friends](https://atcoder.jp/contests/abc177/tasks/abc177_d)

### [ABC 183 F - Confluence](https://atcoder.jp/contests/abc183/tasks/abc183_f)

### [ABC 264 E - Blackout 2](https://atcoder.jp/contests/abc264/tasks/abc264_e)

### [ARC 032 B - 道路工事](https://atcoder.jp/contests/arc032/tasks/arc032_2)

