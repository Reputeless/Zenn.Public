---
title: "最小全域木 / 最大全域木"
free: true
---

C++ 標準ライブラリを用いた、**最小全域木** (Minimum Spanning Tree) / **最大全域木** (Maximum Spanning Tree) の実装です。


# 1. 最小全域木 / 最大全域木のテンプレート

| 機能 | 1.1 | 1.2 |
|----|:----:|:----:|
| クラスカル法で最小全域木の辺の重みの総和を求める | ✅ |  |
| クラスカル法で最大全域木の辺の重みの総和を求める |   | ✅ |


## 1.1 クラスカル法で最小全域木の辺の重みの総和を求める
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::swap()
#include <algorithm> // std::sort()

/// @brief Union-Find 木
/// @note 1.4 高速化 + 省メモリ化
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/union-find
class UnionFind
{
public:

	UnionFind() = default;

	/// @brief Union-Find 木を構築します。
	/// @param n 要素数
	explicit UnionFind(size_t n)
		: m_parentsOrSize(n, -1) {}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parentsOrSize[i] < 0)
		{
			return i;
		}

		// 経路圧縮
		return (m_parentsOrSize[i] = find(m_parentsOrSize[i]));
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
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

	/// @brief a と b が同じグループに属すかを返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @return a と b が同じグループに属す場合 true, それ以外の場合は false
	bool connected(int a, int b)
	{
		return (find(a) == find(b));
	}

	/// @brief i が属するグループの要素数を返します。
	/// @param i インデックス
	/// @return i が属するグループの要素数
	int size(int i)
	{
		return -m_parentsOrSize[find(i)];
	}

private:

	// m_parentsOrSize[i] は i の 親,
	// ただし root の場合は (-1 * そのグループに属する要素数)
	std::vector<int> m_parentsOrSize;
};

/// @brief 辺
struct Edge
{
	/// @brief 辺の一方の頂点
	int u;

	/// @brief 辺のもう一方の頂点
	int v;

	/// @brief 辺の重み
	long long cost;

	bool operator <(const Edge& other) const
	{
		return (cost < other.cost);
	}
};

/// @brief 最小全域木
/// @note 1.1 クラスカル法で最小全域木の辺の重みの総和を求める
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/minimum-spanning-tree
int main()
{
	// 頂点数 V, 辺数 E
	int V, E;
	std::cin >> V >> E;

	// 辺
	std::vector<Edge> edges(E);

	for (auto& edge : edges)
	{
		//std::cin >> edge.u >> edge.v >> edge.cost;
		//--edge.u; --edge.v;
	}

	// 辺をコストの小さい順にソート
	std::sort(edges.begin(), edges.end());

	// Union-Find 木
	UnionFind uf(V);

	// 最小全域木の辺の重みの総和
	long long sum = 0;

	// コストが小さい順に並んだ各辺について
	for (const auto& edge : edges)
	{
		// この辺を加えても閉路にならない場合
		if (!uf.connected(edge.u, edge.v))
		{
			// グループに加える
			uf.merge(edge.u, edge.v);

			// 辺のコストを加算
			sum += edge.cost;
		}
	}

	std::cout << sum << '\n';
}
```

- [AtCoder GRL_2_A - Minimum Spanning Tree](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=7315241#2)
:::


## 1.2 クラスカル法で最大全域木の辺の重みの総和を求める
- 辺をコストの大きい順にソートしたクラスカル法は最大全域木

:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::swap()
#include <algorithm> // std::sort()

/// @brief Union-Find 木
/// @note 1.4 高速化 + 省メモリ化
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/union-find
class UnionFind
{
public:

	UnionFind() = default;

	/// @brief Union-Find 木を構築します。
	/// @param n 要素数
	explicit UnionFind(size_t n)
		: m_parentsOrSize(n, -1) {}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parentsOrSize[i] < 0)
		{
			return i;
		}

		// 経路圧縮
		return (m_parentsOrSize[i] = find(m_parentsOrSize[i]));
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
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

	/// @brief a と b が同じグループに属すかを返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @return a と b が同じグループに属す場合 true, それ以外の場合は false
	bool connected(int a, int b)
	{
		return (find(a) == find(b));
	}

	/// @brief i が属するグループの要素数を返します。
	/// @param i インデックス
	/// @return i が属するグループの要素数
	int size(int i)
	{
		return -m_parentsOrSize[find(i)];
	}

private:

	// m_parentsOrSize[i] は i の 親,
	// ただし root の場合は (-1 * そのグループに属する要素数)
	std::vector<int> m_parentsOrSize;
};

/// @brief 辺
struct Edge
{
	/// @brief 辺の一方の頂点
	int u;

	/// @brief 辺のもう一方の頂点
	int v;

	/// @brief 辺の重み
	long long cost;

	bool operator <(const Edge& other) const
	{
		return (cost < other.cost);
	}
};

/// @brief 最大全域木
/// @note 1.2 クラスカル法で最大全域木の辺の重みの総和を求める
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/minimum-spanning-tree
int main()
{
	// 頂点数 V, 辺数 E
	int V, E;
	std::cin >> V >> E;

	// 辺
	std::vector<Edge> edges(E);

	for (auto& edge : edges)
	{
		//std::cin >> edge.u >> edge.v >> edge.cost;
		//--edge.u; --edge.v;
	}

	// 辺をコストの大きい順にソート
	std::sort(edges.rbegin(), edges.rend());

	// Union-Find 木
	UnionFind uf(V);

	// 最大全域木の辺の重みの総和
	long long sum = 0;

	// コストが大きい順に並んだ各辺について
	for (const auto& edge : edges)
	{
		// この辺を加えても閉路にならない場合
		if (!uf.connected(edge.u, edge.v))
		{
			// グループに加える
			uf.merge(edge.u, edge.v);

			// 辺のコストを加算
			sum += edge.cost;
		}
	}

	std::cout << sum << '\n';
}
```

- [鉄則 B67 - Max MST](https://atcoder.jp/contests/tessoku-book/submissions/37907492)
:::


# 2. 最小全域木 / 最大全域木の例題

### [AtCoder GRL_2_A - Minimum Spanning Tree](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=GRL_2_A&lang=ja)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::swap()
#include <algorithm> // std::sort()

/// @brief Union-Find 木
/// @note 1.4 高速化 + 省メモリ化
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/union-find
class UnionFind
{
public:

	UnionFind() = default;

	/// @brief Union-Find 木を構築します。
	/// @param n 要素数
	explicit UnionFind(size_t n)
		: m_parentsOrSize(n, -1) {}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parentsOrSize[i] < 0)
		{
			return i;
		}

		// 経路圧縮
		return (m_parentsOrSize[i] = find(m_parentsOrSize[i]));
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
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

	/// @brief a と b が同じグループに属すかを返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @return a と b が同じグループに属す場合 true, それ以外の場合は false
	bool connected(int a, int b)
	{
		return (find(a) == find(b));
	}

	/// @brief i が属するグループの要素数を返します。
	/// @param i インデックス
	/// @return i が属するグループの要素数
	int size(int i)
	{
		return -m_parentsOrSize[find(i)];
	}

private:

	// m_parentsOrSize[i] は i の 親,
	// ただし root の場合は (-1 * そのグループに属する要素数)
	std::vector<int> m_parentsOrSize;
};

/// @brief 辺
struct Edge
{
	/// @brief 辺の一方の頂点
	int u;

	/// @brief 辺のもう一方の頂点
	int v;

	/// @brief 辺の重み
	long long cost;

	bool operator <(const Edge& other) const
	{
		return (cost < other.cost);
	}
};

/// @brief 最小全域木
/// @note 1.1 クラスカル法で最小全域木の辺の重みの総和を求める
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/minimum-spanning-tree
int main()
{
	// 頂点数 V, 辺数 E
	int V, E;
	std::cin >> V >> E;

	// 辺
	std::vector<Edge> edges(E);

	for (auto& edge : edges)
	{
		std::cin >> edge.u >> edge.v >> edge.cost;
	}

	// 辺をコストの小さい順にソート
	std::sort(edges.begin(), edges.end());

	// Union-Find 木
	UnionFind uf(V);

	// 最小全域木の辺の重みの総和
	long long sum = 0;

	// コストが小さい順に並んだ各辺について
	for (const auto& edge : edges)
	{
		// この辺を加えても閉路にならない場合
		if (!uf.connected(edge.u, edge.v))
		{
			// グループに加える
			uf.merge(edge.u, edge.v);

			// 辺のコストを加算
			sum += edge.cost;
		}
	}

	std::cout << sum << '\n';
}
```
:::


### [鉄則 A67 - MST (Minimum Spanning Tree)](https://atcoder.jp/contests/tessoku-book/tasks/tessoku_book_bo)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::swap()
#include <algorithm> // std::sort()

/// @brief Union-Find 木
/// @note 1.4 高速化 + 省メモリ化
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/union-find
class UnionFind
{
public:

	UnionFind() = default;

	/// @brief Union-Find 木を構築します。
	/// @param n 要素数
	explicit UnionFind(size_t n)
		: m_parentsOrSize(n, -1) {}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parentsOrSize[i] < 0)
		{
			return i;
		}

		// 経路圧縮
		return (m_parentsOrSize[i] = find(m_parentsOrSize[i]));
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
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

	/// @brief a と b が同じグループに属すかを返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @return a と b が同じグループに属す場合 true, それ以外の場合は false
	bool connected(int a, int b)
	{
		return (find(a) == find(b));
	}

	/// @brief i が属するグループの要素数を返します。
	/// @param i インデックス
	/// @return i が属するグループの要素数
	int size(int i)
	{
		return -m_parentsOrSize[find(i)];
	}

private:

	// m_parentsOrSize[i] は i の 親,
	// ただし root の場合は (-1 * そのグループに属する要素数)
	std::vector<int> m_parentsOrSize;
};

/// @brief 辺
struct Edge
{
	/// @brief 辺の一方の頂点
	int u;

	/// @brief 辺のもう一方の頂点
	int v;

	/// @brief 辺の重み
	long long cost;

	bool operator <(const Edge& other) const
	{
		return (cost < other.cost);
	}
};

/// @brief 最小全域木
/// @note 1.1 クラスカル法で最小全域木の辺の重みの総和を求める
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/minimum-spanning-tree
int main()
{
	// 頂点数 V, 辺数 E
	int V, E;
	std::cin >> V >> E;

	// 辺
	std::vector<Edge> edges(E);

	for (auto& edge : edges)
	{
		std::cin >> edge.u >> edge.v >> edge.cost;
		--edge.u; --edge.v;
	}

	// 辺をコストの小さい順にソート
	std::sort(edges.begin(), edges.end());

	// Union-Find 木
	UnionFind uf(V);

	// 最小全域木の辺の重みの総和
	long long sum = 0;

	// コストが小さい順に並んだ各辺について
	for (const auto& edge : edges)
	{
		// この辺を加えても閉路にならない場合
		if (!uf.connected(edge.u, edge.v))
		{
			// グループに加える
			uf.merge(edge.u, edge.v);

			// 辺のコストを加算
			sum += edge.cost;
		}
	}

	std::cout << sum << '\n';
}
```
:::


### [鉄則 B67 - Max MST](https://atcoder.jp/contests/tessoku-book/tasks/tessoku_book_en)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::swap()
#include <algorithm> // std::sort()

/// @brief Union-Find 木
/// @note 1.4 高速化 + 省メモリ化
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/union-find
class UnionFind
{
public:

	UnionFind() = default;

	/// @brief Union-Find 木を構築します。
	/// @param n 要素数
	explicit UnionFind(size_t n)
		: m_parentsOrSize(n, -1) {}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parentsOrSize[i] < 0)
		{
			return i;
		}

		// 経路圧縮
		return (m_parentsOrSize[i] = find(m_parentsOrSize[i]));
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
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

	/// @brief a と b が同じグループに属すかを返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @return a と b が同じグループに属す場合 true, それ以外の場合は false
	bool connected(int a, int b)
	{
		return (find(a) == find(b));
	}

	/// @brief i が属するグループの要素数を返します。
	/// @param i インデックス
	/// @return i が属するグループの要素数
	int size(int i)
	{
		return -m_parentsOrSize[find(i)];
	}

private:

	// m_parentsOrSize[i] は i の 親,
	// ただし root の場合は (-1 * そのグループに属する要素数)
	std::vector<int> m_parentsOrSize;
};

/// @brief 辺
struct Edge
{
	/// @brief 辺の一方の頂点
	int u;

	/// @brief 辺のもう一方の頂点
	int v;

	/// @brief 辺の重み
	long long cost;

	bool operator <(const Edge& other) const
	{
		return (cost < other.cost);
	}
};

/// @brief 最大全域木
/// @note 1.2 クラスカル法で最大全域木の辺の重みの総和を求める
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/minimum-spanning-tree
int main()
{
	// 頂点数 V, 辺数 E
	int V, E;
	std::cin >> V >> E;

	// 辺
	std::vector<Edge> edges(E);

	for (auto& edge : edges)
	{
		std::cin >> edge.u >> edge.v >> edge.cost;
		--edge.u; --edge.v;
	}

	// 辺をコストの大きい順にソート
	std::sort(edges.rbegin(), edges.rend());

	// Union-Find 木
	UnionFind uf(V);

	// 最大全域木の辺の重みの総和
	long long sum = 0;

	// コストが大きい順に並んだ各辺について
	for (const auto& edge : edges)
	{
		// この辺を加えても閉路にならない場合
		if (!uf.connected(edge.u, edge.v))
		{
			// グループに加える
			uf.merge(edge.u, edge.v);

			// 辺のコストを加算
			sum += edge.cost;
		}
	}

	std::cout << sum << '\n';
}
```
:::


### [iroha Day2 D - 楽しすぎる家庭菜園](https://atcoder.jp/contests/iroha2019-day2/tasks/iroha2019_day2_d)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::swap()
#include <algorithm> // std::sort()

/// @brief Union-Find 木
/// @note 1.4 高速化 + 省メモリ化
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/union-find
class UnionFind
{
public:

	UnionFind() = default;

	/// @brief Union-Find 木を構築します。
	/// @param n 要素数
	explicit UnionFind(size_t n)
		: m_parentsOrSize(n, -1) {}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parentsOrSize[i] < 0)
		{
			return i;
		}

		// 経路圧縮
		return (m_parentsOrSize[i] = find(m_parentsOrSize[i]));
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
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

	/// @brief a と b が同じグループに属すかを返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @return a と b が同じグループに属す場合 true, それ以外の場合は false
	bool connected(int a, int b)
	{
		return (find(a) == find(b));
	}

	/// @brief i が属するグループの要素数を返します。
	/// @param i インデックス
	/// @return i が属するグループの要素数
	int size(int i)
	{
		return -m_parentsOrSize[find(i)];
	}

private:

	// m_parentsOrSize[i] は i の 親,
	// ただし root の場合は (-1 * そのグループに属する要素数)
	std::vector<int> m_parentsOrSize;
};

/// @brief 辺
struct Edge
{
	/// @brief 辺の一方の頂点
	int u;

	/// @brief 辺のもう一方の頂点
	int v;

	/// @brief 辺の重み
	long long cost;

	// 水路の番号
	int index;

	bool operator <(const Edge& other) const
	{
		return (cost < other.cost);
	}
};

/// @brief 最大全域木
/// @note 1.2 クラスカル法で最大全域木の辺の重みの総和を求める
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/minimum-spanning-tree
int main()
{
	// 頂点数 V, 辺数 E
	int V, E;
	std::cin >> V >> E;

	// 辺
	std::vector<Edge> edges(E);
	{
		int index = 0;

		for (auto& edge : edges)
		{
			std::cin >> edge.u >> edge.v >> edge.cost;
			--edge.u; --edge.v;
			edge.index = ++index;
		}
	}

	// 辺をコストの大きい順にソート
	std::sort(edges.rbegin(), edges.rend());

	// Union-Find 木
	UnionFind uf(V);

	// 使用する水路の番号を格納する配列
	std::vector<int> indices;

	// コストが大きい順に並んだ各辺について
	for (const auto& edge : edges)
	{
		// この辺を加えても閉路にならない場合
		if (!uf.connected(edge.u, edge.v))
		{
			// グループに加える
			uf.merge(edge.u, edge.v);

			// 水路の番号を追加
			indices.push_back(edge.index);
		}
	}

	// 水路の番号を昇順にする
	std::sort(indices.begin(), indices.end());

	for (const auto& index : indices)
	{
		std::cout << index << '\n';
	}
}
```
:::


### [AOJ 2224 - Save your cats](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2224&lang=ja)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::swap()
#include <algorithm> // std::sort()
#include <cmath> // std::hypot()

/// @brief Union-Find 木
/// @note 1.4 高速化 + 省メモリ化
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/union-find
class UnionFind
{
public:

	UnionFind() = default;

	/// @brief Union-Find 木を構築します。
	/// @param n 要素数
	explicit UnionFind(size_t n)
		: m_parentsOrSize(n, -1) {}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parentsOrSize[i] < 0)
		{
			return i;
		}

		// 経路圧縮
		return (m_parentsOrSize[i] = find(m_parentsOrSize[i]));
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
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

	/// @brief a と b が同じグループに属すかを返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @return a と b が同じグループに属す場合 true, それ以外の場合は false
	bool connected(int a, int b)
	{
		return (find(a) == find(b));
	}

	/// @brief i が属するグループの要素数を返します。
	/// @param i インデックス
	/// @return i が属するグループの要素数
	int size(int i)
	{
		return -m_parentsOrSize[find(i)];
	}

private:

	// m_parentsOrSize[i] は i の 親,
	// ただし root の場合は (-1 * そのグループに属する要素数)
	std::vector<int> m_parentsOrSize;
};

/// @brief 辺
struct Edge
{
	/// @brief 辺の一方の頂点
	int u;

	/// @brief 辺のもう一方の頂点
	int v;

	/// @brief 辺の重み
	double cost;

	bool operator <(const Edge& other) const
	{
		return (cost < other.cost);
	}
};

struct Point
{
	int x, y;
};

double Distance(const Point& a, const Point& b)
{
	return std::hypot(a.x - b.x, a.y - b.y);
}

/// @brief 最大全域木
/// @note 1.2 クラスカル法で最大全域木の辺の重みの総和を求める
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/minimum-spanning-tree
int main()
{
	// 頂点数 N, 辺数 M
	int N, M;
	std::cin >> N >> M;

	std::vector<Point> points(N);
	for (auto& point : points)
	{
		std::cin >> point.x >> point.y;
	}

	double allCost = 0.0;

	// 辺
	std::vector<Edge> edges(M);

	for (auto& edge : edges)
	{
		std::cin >> edge.u >> edge.v;
		--edge.u; --edge.v;
		edge.cost = Distance(points[edge.u], points[edge.v]);
		allCost += edge.cost;
	}

	// 辺をコストの大きい順にソート
	std::sort(edges.rbegin(), edges.rend());

	// Union-Find 木
	UnionFind uf(N);

	// 最大全域木の辺の重みの総和
	double sum = 0;

	// コストが大きい順に並んだ各辺について
	for (const auto& edge : edges)
	{
		// この辺を加えても閉路にならない場合
		if (!uf.connected(edge.u, edge.v))
		{
			// グループに加える
			uf.merge(edge.u, edge.v);

			// 辺のコストを加算
			sum += edge.cost;
		}
	}

	// 小数点以下 6 桁表示
	std::cout << std::fixed << (allCost - sum) << '\n';
}
```
:::


### [ABC 218 E - Destruction](https://atcoder.jp/contests/abc218/tasks/abc218_e)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::swap()
#include <algorithm> // std::sort()

/// @brief Union-Find 木
/// @note 1.4 高速化 + 省メモリ化
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/union-find
class UnionFind
{
public:

	UnionFind() = default;

	/// @brief Union-Find 木を構築します。
	/// @param n 要素数
	explicit UnionFind(size_t n)
		: m_parentsOrSize(n, -1) {}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parentsOrSize[i] < 0)
		{
			return i;
		}

		// 経路圧縮
		return (m_parentsOrSize[i] = find(m_parentsOrSize[i]));
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
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

	/// @brief a と b が同じグループに属すかを返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @return a と b が同じグループに属す場合 true, それ以外の場合は false
	bool connected(int a, int b)
	{
		return (find(a) == find(b));
	}

	/// @brief i が属するグループの要素数を返します。
	/// @param i インデックス
	/// @return i が属するグループの要素数
	int size(int i)
	{
		return -m_parentsOrSize[find(i)];
	}

private:

	// m_parentsOrSize[i] は i の 親,
	// ただし root の場合は (-1 * そのグループに属する要素数)
	std::vector<int> m_parentsOrSize;
};

/// @brief 辺
struct Edge
{
	/// @brief 辺の一方の頂点
	int u;

	/// @brief 辺のもう一方の頂点
	int v;

	/// @brief 辺の重み
	long long cost;

	bool operator <(const Edge& other) const
	{
		return (cost < other.cost);
	}
};

/// @brief 最小全域木
/// @note 1.1 クラスカル法で最小全域木の辺の重みの総和を求める
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/minimum-spanning-tree
int main()
{
	// 頂点数 N, 辺数 M
	int N, M;
	std::cin >> N >> M;

	// 辺
	std::vector<Edge> edges(M);

	// 報酬
	long long answer = 0;

	for (auto& edge : edges)
	{
		std::cin >> edge.u >> edge.v >> edge.cost;
		--edge.u; --edge.v;

		// 最初からすべて除去したと考える
		answer += edge.cost;
	}

	// 辺をコストの小さい順（報酬の小さい順）にソート
	std::sort(edges.begin(), edges.end());

	// Union-Find 木
	UnionFind uf(N);

	// コストが小さい順に並んだ各辺について
	for (const auto& edge : edges)
	{
		// 負の報酬あるいはこの辺を加えても閉路にならない場合
		if ((edge.cost < 0) || !uf.connected(edge.u, edge.v))
		{
			// グループに加える
			uf.merge(edge.u, edge.v);

			// 辺を戻したのでコストをキャンセル
			answer -= edge.cost;
		}
	}

	std::cout << answer << '\n';
}
```
:::
