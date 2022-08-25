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

int main()
{
	int N, Q;
	std::cin >> N >> Q;

	UnionFind uf(N);

	while (Q--)
	{
		int t, u, v;
		std::cin >> t >> u >> v;

		if (t == 0)
		{
			uf.merge(u, v);
		}
		else // t == 1
		{
			std::cout << (uf.connected(u, v) ? "Yes\n" : "No\n");
		}
	}
}
```
:::


### [ABC 075 C - Bridge](https://atcoder.jp/contests/abc075/tasks/abc075_c)
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

int main()
{
	// N 頂点 M 辺
	int N, M;
	std::cin >> N >> M;

	std::vector<int> A(M), B(M);

	for (int i = 0; i < M; ++i)
	{
		int a, b;
		std::cin >> a >> b;
		A[i] = --a;
		B[i] = --b;
	}

	int ans = 0;

	// 各辺について
	for (int i = 0; i < M; ++i)
	{
		UnionFind uf(N);

		// 辺 i を取り除いた Union-Find 木を作る
		for (int k = 0; k < M; ++k)
		{
			if (i != k)
			{
				uf.merge(A[k], B[k]);
			}
		}

		// root がいくつあるか
		int count = 0;

		// 各頂点について
		for (int k = 0; k < N; ++k)
		{
			// 自身が root なら count を増やす
			if (k == uf.find(k))
			{
				++count;
			}
		}

		// 最終的にグラフが非連結になっていたら
		if (1 < count)
		{
			// 削除した辺は橋であった
			++ans;
		}
	}

	std::cout << ans << '\n';
}
```


### [ABC 177 D - Friends](https://atcoder.jp/contests/abc177/tasks/abc177_d)
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

int main()
{
	int N, M;
	std::cin >> N >> M;

	UnionFind uf(N);

	while (M--)
	{
		int A, B;
		std::cin >> A >> B;
		--A; --B;
		uf.merge(A, B);
	}

	int answer = 0;

	for (int i = 0; i < N; ++i)
	{
		answer = std::max(answer, uf.size(i));
	}

	std::cout << answer << '\n';
}
```
:::


### [ABC 049 D - 連結](https://atcoder.jp/contests/abc049/tasks/arc065_b)



### [AOJ GRL_2_A - Minimum Spanning Tree](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=GRL_2_A&lang=ja)
- クラスカル法

:::details コード
```cpp
#include <iostream>
#include <vector>
#include <numeric> // std::iota()
#include <algorithm> // std::sort()

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

struct Edge
{
	int from;
	int to;
	int cost;

	// コストに基づく大小定義
	bool operator <(const Edge& other) const
	{
		return (cost < other.cost);
	}
};

int main()
{
	int V, E;
	std::cin >> V >> E;

	std::vector<Edge> edges(E);
	for (auto& edge : edges)
	{
		std::cin >> edge.from >> edge.to >> edge.cost;
	}

	std::sort(edges.begin(), edges.end());

	UnionFind uf(V);
	long long sum = 0;

	for (const auto& edge : edges)
	{
		if (!uf.connected(edge.from, edge.to))
		{
			uf.merge(edge.from, edge.to);
			sum += edge.cost;
		}
	}

	std::cout << sum << '\n';
}
```
:::


### [ARC 032 B - 道路工事](https://atcoder.jp/contests/arc032/tasks/arc032_2)



### [ABC 264 E - Blackout 2](https://atcoder.jp/contests/abc264/tasks/abc264_e)
:::details コード 1
```cpp
#include <iostream>
#include <vector>
#include <numeric> // std::iota()
#include <utility> // std::swap()
#include <algorithm> // std::reverse()

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

struct Edge
{
	int from;
	int to;
};

int main()
{
	int N, M, E;
	std::cin >> N >> M >> E;

	std::vector<Edge> edges(E);
	for (auto& edge : edges)
	{
		std::cin >> edge.from >> edge.to;
		--edge.from; --edge.to;
	}

	int Q;
	std::cin >> Q;

	// ある送電線が最終時点で残っているかを記録する配列
	std::vector<bool> finallyConnected(E, true);

	std::vector<int> X(Q);
	for (auto& x : X)
	{
		std::cin >> x;
		--x;
		finallyConnected[x] = false;
	}

	// 各地点が配電されているか (root 用)
	std::vector<bool> electrified_root(N + M);
	for (int i = N; i < (N + M); ++i)
	{
		electrified_root[i] = true;
	}

	UnionFind uf(N + M);

	// 配電されている都市の数
	int sum = 0;

	// 最終時点での接続情報を構築する
	for (int i = 0; i < E; ++i)
	{
		if (!finallyConnected[i])
		{
			continue;
		}

		// 接続構築
		{
			const Edge edge = edges[i];

			if (uf.connected(edge.from, edge.to))
			{
				continue;
			}

			const bool eFrom = electrified_root[uf.find(edge.from)];
			const bool eTo = electrified_root[uf.find(edge.to)];

			if ((eFrom == false) && (eTo == true)) // eFrom 側が新たに電化する
			{
				sum += uf.size(edge.from);
			}
			else if ((eFrom == true) && (eTo == false)) // eTo 側が新たに電化する
			{
				sum += uf.size(edge.to);
			}

			uf.merge(edge.from, edge.to);
			electrified_root[uf.find(edge.from)] = (eFrom || eTo);
		}
	}

	std::vector<int> results;

	// 接続イベントを逆からたどる
	std::reverse(X.begin(), X.end());

	for (const auto& x : X)
	{
		// 現時点での配電都市数を記録
		results.push_back(sum);

		// 接続構築
		{
			const Edge edge = edges[x];

			if (uf.connected(edge.from, edge.to))
			{
				continue;
			}

			const bool eFrom = electrified_root[uf.find(edge.from)];
			const bool eTo = electrified_root[uf.find(edge.to)];

			if ((eFrom == false) && (eTo == true)) // eFrom 側が新たに電化する
			{
				sum += uf.size(edge.from);
			}
			else if ((eFrom == true) && (eTo == false)) // eTo 側が新たに電化する
			{
				sum += uf.size(edge.to);
			}

			uf.merge(edge.from, edge.to);
			electrified_root[uf.find(edge.from)] = (eFrom || eTo);
		}
	}

	std::reverse(results.begin(), results.end());

	for (const auto& result : results)
	{
		std::cout << result << '\n';
	}
}
```
:::


:::details コード 2
```cpp
#include <iostream>
#include <vector>
#include <numeric> // std::iota()
#include <utility> // std::swap()
#include <algorithm> // std::reverse()

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

struct Edge
{
	int from;
	int to;
};

int main()
{
	int N, M, E;
	std::cin >> N >> M >> E;

	std::vector<Edge> edges(E);
	for (auto& edge : edges)
	{
		std::cin >> edge.from >> edge.to;
		edge.from = std::min((edge.from - 1), N);
		edge.to = std::min((edge.to - 1), N);
	}

	int Q;
	std::cin >> Q;

	// ある送電線が最終時点で残っているかを記録する配列
	std::vector<bool> finallyConnected(E, true);

	std::vector<int> X(Q);
	for (auto& x : X)
	{
		std::cin >> x;
		--x;
		finallyConnected[x] = false;
	}

	UnionFind uf(N + 1);

	for (int i = 0; i < E; ++i)
	{
		if (!finallyConnected[i])
		{
			continue;
		}

		const Edge edge = edges[i];

		uf.merge(edge.from, edge.to);
	}

	std::vector<int> results;

	// 接続イベントを逆からたどる
	std::reverse(X.begin(), X.end());

	for (const auto& x : X)
	{
		// 現時点での配電都市数を記録
		results.push_back(uf.size(N) - 1);

		const Edge edge = edges[x];

		uf.merge(edge.from, edge.to);
	}

	std::reverse(results.begin(), results.end());

	for (const auto& result : results)
	{
		std::cout << result << '\n';
	}
}
```
:::


### [ABC 183 F - Confluence](https://atcoder.jp/contests/abc183/tasks/abc183_f)

