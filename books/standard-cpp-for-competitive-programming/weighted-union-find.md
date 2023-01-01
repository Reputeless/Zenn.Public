---
title: "重み付き Union-Find"
free: true
---

C++ 標準ライブラリを用いた、**重み付き Union-Find** の実装です。


# 1. 重み付き Union-Find のテンプレート

| 機能 | 説明 | 1.1 | 1.2 |
|----|----|:----:|:----:|
| `.find(i)` | i を含むグループの root (代表) を返す | ✅ | ✅ |
| `.merge(a, b, w)` | a を含むグループと b を含むグループを併合し、b の重みは a と比べて w 大きくなるようにする | ✅ | ✅ |
| `.diff(a, b)` | (b の重み) - (a の重み) を返す。a と b が同じグループに属さない場合の結果は不定 | ✅ | ✅ |
| `.connected(a, b)` | a と b が同じグループに属すかを返す | ✅ | ✅ |
| `.size(i)` | i を含むグループの要素数を返す | ✅ | ✅ |
| 計算量の削減 | 経路圧縮と union by size | ✅ | ✅ |

## 1.1 シンプルな実装
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <numeric> // std::iota()
#include <utility> // std::swap()

/// @brief 重み付き Union-Find 木
/// @tparam Type 重みの表現に使う型
/// @note 1.1 シンプルな実装
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/weighted-union-find
template <class Type>
class WeightedUnionFind
{
public:

	WeightedUnionFind() = default;

	/// @brief 重み付き Union-Find 木を構築します。
	/// @param n 要素数
	explicit WeightedUnionFind(size_t n)
		: m_parents(n)
		, m_sizes(n, 1)
		, m_diffWeights(n)
	{
		std::iota(m_parents.begin(), m_parents.end(), 0);
	}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parents[i] == i)
		{
			return i;
		}

		const int root = find(m_parents[i]);

		m_diffWeights[i] += m_diffWeights[m_parents[i]];

		// 経路圧縮
		return (m_parents[i] = root);
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @param w (b の重み) - (a の重み)
	void merge(int a, int b, Type w)
	{
		w += weight(a);
		w -= weight(b);

		a = find(a);
		b = find(b);

		if (a != b)
		{
			// union by size (小さいほうが子になる）
			if (m_sizes[a] < m_sizes[b])
			{
				std::swap(a, b);
				w = -w;
			}

			m_sizes[a] += m_sizes[b];
			m_parents[b] = a;
			m_diffWeights[b] = w;
		}
	}

	/// @brief (b の重み) - (a の重み) を返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @remark a と b が同じグループに属さない場合の結果は不定です。
	/// @return (b の重み) - (a の重み)
	Type diff(int a, int b)
	{
		return (weight(b) - weight(a));
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
		return m_sizes[find(i)];
	}

private:

	// m_parents[i] は i の 親,
	// root の場合は自身が親
	std::vector<int> m_parents;

	// グループの要素数 (root 用)
	// i が root のときのみ, m_sizes[i] はそのグループに属する要素数を表す
	std::vector<int> m_sizes;

	// 重み
	std::vector<Type> m_diffWeights;

	Type weight(int i)
	{
		find(i); // 経路圧縮
		return m_diffWeights[i];
	}
};
```

- [AOJ DSL_1_B](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=7276899#2)
:::

## 1.2 省メモリ化
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::swap()

/// @brief 重み付き Union-Find 木
/// @tparam Type 重みの表現に使う型
/// @note 1.2 省メモリ化
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/weighted-union-find
template <class Type>
class WeightedUnionFind
{
public:

	WeightedUnionFind() = default;

	/// @brief 重み付き Union-Find 木を構築します。
	/// @param n 要素数
	explicit WeightedUnionFind(size_t n)
		: m_parentsOrSize(n, -1)
		, m_diffWeights(n) {}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parentsOrSize[i] < 0)
		{
			return i;
		}

		const int root = find(m_parentsOrSize[i]);

		m_diffWeights[i] += m_diffWeights[m_parentsOrSize[i]];

		// 経路圧縮
		return (m_parentsOrSize[i] = root);
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @param w (b の重み) - (a の重み)
	void merge(int a, int b, Type w)
	{
		w += weight(a);
		w -= weight(b);

		a = find(a);
		b = find(b);

		if (a != b)
		{
			// union by size (小さいほうが子になる）
			if (-m_parentsOrSize[a] < -m_parentsOrSize[b])
			{
				std::swap(a, b);
				w = -w;
			}

			m_parentsOrSize[a] += m_parentsOrSize[b];
			m_parentsOrSize[b] = a;
			m_diffWeights[b] = w;
		}
	}

	/// @brief (b の重み) - (a の重み) を返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @remark a と b が同じグループに属さない場合の結果は不定です。
	/// @return (b の重み) - (a の重み)
	Type diff(int a, int b)
	{
		return (weight(b) - weight(a));
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

	// 重み
	std::vector<Type> m_diffWeights;

	Type weight(int i)
	{
		find(i); // 経路圧縮
		return m_diffWeights[i];
	}
};
```

- [AOJ DSL_1_B](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=7276925#2)
:::

# 2. 重み付き Union-Find の例題

### [AOJ DSL_1_B - Weighted Union Find Trees](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=DSL_1_B&lang=ja)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <numeric> // std::iota()
#include <utility> // std::swap()

/// @brief 重み付き Union-Find 木
/// @tparam Type 重みの表現に使う型
/// @note 1.1 シンプルな実装
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/weighted-union-find
template <class Type>
class WeightedUnionFind
{
public:

	WeightedUnionFind() = default;

	/// @brief 重み付き Union-Find 木を構築します。
	/// @param n 要素数
	explicit WeightedUnionFind(size_t n)
		: m_parents(n)
		, m_sizes(n, 1)
		, m_diffWeights(n)
	{
		std::iota(m_parents.begin(), m_parents.end(), 0);
	}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parents[i] == i)
		{
			return i;
		}

		const int root = find(m_parents[i]);

		m_diffWeights[i] += m_diffWeights[m_parents[i]];

		// 経路圧縮
		return (m_parents[i] = root);
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @param w (b の重み) - (a の重み)
	void merge(int a, int b, Type w)
	{
		w += weight(a);
		w -= weight(b);

		a = find(a);
		b = find(b);

		if (a != b)
		{
			// union by size (小さいほうが子になる）
			if (m_sizes[a] < m_sizes[b])
			{
				std::swap(a, b);
				w = -w;
			}

			m_sizes[a] += m_sizes[b];
			m_parents[b] = a;
			m_diffWeights[b] = w;
		}
	}

	/// @brief (b の重み) - (a の重み) を返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @remark a と b が同じグループに属さない場合の結果は不定です。
	/// @return (b の重み) - (a の重み)
	Type diff(int a, int b)
	{
		return (weight(b) - weight(a));
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
		return m_sizes[find(i)];
	}

private:

	// m_parents[i] は i の 親,
	// root の場合は自身が親
	std::vector<int> m_parents;

	// グループの要素数 (root 用)
	// i が root のときのみ, m_sizes[i] はそのグループに属する要素数を表す
	std::vector<int> m_sizes;

	// 重み
	std::vector<Type> m_diffWeights;

	Type weight(int i)
	{
		find(i); // 経路圧縮
		return m_diffWeights[i];
	}
};

int main()
{
	int n, q;
	std::cin >> n >> q;

	WeightedUnionFind<int> uf(n);

	while (q--)
	{
		int t;
		std::cin >> t;

		if (t == 0)
		{
			int x, y, z;
			std::cin >> x >> y >> z;
			uf.merge(x, y, z);
		}
		else
		{
			int x, y;
			std::cin >> x >> y;

			if (uf.connected(x, y))
			{
				std::cout << uf.diff(x, y) << '\n';
			}
			else
			{
				std::cout << "?\n";
			}
		}
	}
}
```
:::


### [ABC 087 D - People on a Line](https://atcoder.jp/contests/abc087/tasks/arc090_b)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <numeric> // std::iota()
#include <utility> // std::swap()

/// @brief 重み付き Union-Find 木
/// @tparam Type 重みの表現に使う型
/// @note 1.1 シンプルな実装
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/weighted-union-find
template <class Type>
class WeightedUnionFind
{
public:

	WeightedUnionFind() = default;

	/// @brief 重み付き Union-Find 木を構築します。
	/// @param n 要素数
	explicit WeightedUnionFind(size_t n)
		: m_parents(n)
		, m_sizes(n, 1)
		, m_diffWeights(n)
	{
		std::iota(m_parents.begin(), m_parents.end(), 0);
	}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parents[i] == i)
		{
			return i;
		}

		const int root = find(m_parents[i]);

		m_diffWeights[i] += m_diffWeights[m_parents[i]];

		// 経路圧縮
		return (m_parents[i] = root);
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @param w (b の重み) - (a の重み)
	void merge(int a, int b, Type w)
	{
		w += weight(a);
		w -= weight(b);

		a = find(a);
		b = find(b);

		if (a != b)
		{
			// union by size (小さいほうが子になる）
			if (m_sizes[a] < m_sizes[b])
			{
				std::swap(a, b);
				w = -w;
			}

			m_sizes[a] += m_sizes[b];
			m_parents[b] = a;
			m_diffWeights[b] = w;
		}
	}

	/// @brief (b の重み) - (a の重み) を返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @remark a と b が同じグループに属さない場合の結果は不定です。
	/// @return (b の重み) - (a の重み)
	Type diff(int a, int b)
	{
		return (weight(b) - weight(a));
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
		return m_sizes[find(i)];
	}

private:

	// m_parents[i] は i の 親,
	// root の場合は自身が親
	std::vector<int> m_parents;

	// グループの要素数 (root 用)
	// i が root のときのみ, m_sizes[i] はそのグループに属する要素数を表す
	std::vector<int> m_sizes;

	// 重み
	std::vector<Type> m_diffWeights;

	Type weight(int i)
	{
		find(i); // 経路圧縮
		return m_diffWeights[i];
	}
};

int main()
{
	// N 頂点 M 辺
	int N, M;
	std::cin >> N >> M;

	WeightedUnionFind<int> uf(N);

	for (int i = 0; i < M; ++i)
	{
		int l, r, d;
		std::cin >> l >> r >> d;
		--l, --r;

		if (!uf.connected(l, r))
		{
			uf.merge(l, r, d);
		}
		else if (uf.diff(l, r) != d)
		{
			std::cout << "No\n";
			return 0;
		}
	}

	std::cout << "Yes\n";
}
```
:::


### [ABC 280 F - Pay or Receive](https://atcoder.jp/contests/abc280/tasks/abc280_f)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <numeric> // std::iota()
#include <utility> // std::swap()

/// @brief 重み付き Union-Find 木
/// @tparam Type 重みの表現に使う型
/// @note 1.1 シンプルな実装
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/weighted-union-find
template <class Type>
class WeightedUnionFind
{
public:

	WeightedUnionFind() = default;

	/// @brief 重み付き Union-Find 木を構築します。
	/// @param n 要素数
	explicit WeightedUnionFind(size_t n)
		: m_parents(n)
		, m_sizes(n, 1)
		, m_diffWeights(n)
	{
		std::iota(m_parents.begin(), m_parents.end(), 0);
	}

	/// @brief 頂点 i の root のインデックスを返します。
	/// @param i 調べる頂点のインデックス
	/// @return 頂点 i の root のインデックス
	int find(int i)
	{
		if (m_parents[i] == i)
		{
			return i;
		}

		const int root = find(m_parents[i]);

		m_diffWeights[i] += m_diffWeights[m_parents[i]];

		// 経路圧縮
		return (m_parents[i] = root);
	}

	/// @brief a のグループと b のグループを統合します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @param w (b の重み) - (a の重み)
	void merge(int a, int b, Type w)
	{
		w += weight(a);
		w -= weight(b);

		a = find(a);
		b = find(b);

		if (a != b)
		{
			// union by size (小さいほうが子になる）
			if (m_sizes[a] < m_sizes[b])
			{
				std::swap(a, b);
				w = -w;
			}

			m_sizes[a] += m_sizes[b];
			m_parents[b] = a;
			m_diffWeights[b] = w;
		}
	}

	/// @brief (b の重み) - (a の重み) を返します。
	/// @param a 一方のインデックス
	/// @param b 他方のインデックス
	/// @remark a と b が同じグループに属さない場合の結果は不定です。
	/// @return (b の重み) - (a の重み)
	Type diff(int a, int b)
	{
		return (weight(b) - weight(a));
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
		return m_sizes[find(i)];
	}

private:

	// m_parents[i] は i の 親,
	// root の場合は自身が親
	std::vector<int> m_parents;

	// グループの要素数 (root 用)
	// i が root のときのみ, m_sizes[i] はそのグループに属する要素数を表す
	std::vector<int> m_sizes;

	// 重み
	std::vector<Type> m_diffWeights;

	Type weight(int i)
	{
		find(i); // 経路圧縮
		return m_diffWeights[i];
	}
};

int main()
{
	int N, M, Q;
	std::cin >> N >> M >> Q;

	WeightedUnionFind<long long> uf(N);
	std::vector<bool> ng(N);

	for (int i = 0; i < M; ++i)
	{
		int A, B, C;
		std::cin >> A >> B >> C;
		--A, --B;

		if (!uf.connected(A, B))
		{
			uf.merge(A, B, C);
		}
		else if (uf.diff(A, B) != C)
		{
			// 矛盾が発生する場合、そのグループは負閉路を含む
			ng[uf.find(A)] = true;
		}
	}

	while (Q--)
	{
		int X, Y;
		std::cin >> X >> Y;
		--X, --Y;

		if (!uf.connected(X, Y))
		{
			std::cout << "nan\n";
		}
		else if (ng[uf.find(X)])
		{
			std::cout << "inf\n";
		}
		else
		{
			std::cout << uf.diff(X, Y) << '\n';
		}
	}
}
```
:::

