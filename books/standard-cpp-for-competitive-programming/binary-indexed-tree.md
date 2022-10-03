---
title: "Binary Indexed Tree (BIT)"
free: true
---

C++ 標準ライブラリを用いた Binary Indexed Tree (BIT, 別名 Fenwick Tree) の実装です。


# 1. Binary Indexed Tree のテンプレート

| 機能 | 1.1 | 1.2 |
|----|:----:|:----:|
| 数列の区間和を求める | ✅ | ✅ |
| 数列に対して一点加算をする | ✅ | ✅ |
| 数列に対して区間加算をする |   |✅ |


## 1.1 基本実装
:::details コード
```cpp
# include <iostream>
# include <vector>

// Binary Indexed Tree (1.1 基本実装)
// 1-based indexing
class BIT
{
public:

	BIT() = default;

	// 長さ size の数列で初期化
	explicit BIT(size_t size)
		: m_bit(size + 1) {}

	// 数列で初期化
	explicit BIT(const std::vector<long long>& v)
		: BIT(v.size())
	{
		for (int i = 0; i < v.size(); ++i)
		{
			add((i + 1), v[i]);
		}
	}

	// 閉区間 [1, r] の合計を返す (1-based indexing)
	long long sum(int r) const
	{
		long long ret = 0;

		for (; 0 < r; r -= (r & -r))
		{
			ret += m_bit[r];
		}

		return ret;
	}

	// 閉区間 [l, r] の合計を返す (1-based indexing)
	long long sum(int l, int r) const
	{
		return (sum(r) - sum(l - 1));
	}

	// 数列の i 番目の要素を加算 (1-based indexing)
	void add(int i, long long value)
	{
		for (; i < m_bit.size(); i += (i & -i))
		{
			m_bit[i] += value;
		}
	}

private:

	std::vector<long long> m_bit;
};
```

- [Library Checker - Static Range Sum](https://judge.yosupo.jp/submission/106827) / [Library Checker - Point Add Range Sum](https://judge.yosupo.jp/submission/106828)
:::

## 1.2 区間加算対応
:::details コード
```cpp
# include <iostream>
# include <vector>

class BIT
{
public:

	BIT() = default;

	// 長さ size の数列で初期化
	explicit BIT(size_t size)
		: m_bit(size + 1) {}

	// 数列で初期化
	explicit BIT(const std::vector<long long>& v)
		: BIT(v.size())
	{
		for (int i = 0; i < v.size(); ++i)
		{
			add((i + 1), v[i]);
		}
	}

	// 閉区間 [1, r] の合計を返す (1-based indexing)
	long long sum(int r) const
	{
		long long ret = 0;

		for (; 0 < r; r -= (r & -r))
		{
			ret += m_bit[r];
		}

		return ret;
	}

	// 閉区間 [l, r] の合計を返す (1-based indexing)
	long long sum(int l, int r) const
	{
		return (sum(r) - sum(l - 1));
	}

	// 数列の i 番目の要素を加算 (1-based indexing)
	void add(int i, long long value)
	{
		for (; i < m_bit.size(); i += (i & -i))
		{
			m_bit[i] += value;
		}
	}

private:

	std::vector<long long> m_bit;
};

// Binary Indexed Tree (1.2 区間加算対応)
// 1-based indexing
class BIT_RAQ
{
public:

	BIT_RAQ() = default;

	explicit BIT_RAQ(size_t size)
		: m_bit0(size)
		, m_bit1(size) {}

	explicit BIT_RAQ(const std::vector<long long>& v)
		: m_bit0(v)
		, m_bit1(v.size()) {}

	// 閉区間 [1, r] の合計を返す (1-based indexing)
	long long sum(int r) const
	{
		return (m_bit0.sum(r) + m_bit1.sum(r) * r);
	}

	// 閉区間 [l, r] の合計を返す (1-based indexing)
	long long sum(int l, int r) const
	{
		return (sum(r) - sum(l - 1));
	}

	// 数列の i 番目の要素を加算 (1-based indexing)
	void add(int i, long long value)
	{
		m_bit0.add(i, value);
	}

	// 閉区間 [l, r] の要素を加算 (1-based indexing)
	void add(int l, int r, long long value)
	{
		m_bit0.add(l, (-value * (l - 1)));
		m_bit0.add((r + 1), (value * r));
		m_bit1.add(l, value);
		m_bit1.add((r + 1), -value);
	}

private:

	BIT m_bit0;

	BIT m_bit1;
};
```

- [Library Checker - Static Range Sum](https://judge.yosupo.jp/submission/106937) / [Library Checker - Point Add Range Sum](https://judge.yosupo.jp/submission/106938) / [AOJ DSL_2_G - RSQ and RAQ](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=7008192#2)
:::


# 2. Binary Indexed Tree の例題

### [AOJ DSL_2_B - Range Sum Query (RSQ)](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=DSL_2_B)
:::details コード
```cpp
# include <iostream>
# include <vector>

// Binary Indexed Tree (1.1 基本実装)
// 1-based indexing
class BIT
{
public:

	BIT() = default;

	// 長さ size の数列で初期化
	explicit BIT(size_t size)
		: m_bit(size + 1) {}

	// 数列で初期化
	explicit BIT(const std::vector<long long>& v)
		: BIT(v.size())
	{
		for (int i = 0; i < v.size(); ++i)
		{
			add((i + 1), v[i]);
		}
	}

	// 閉区間 [1, r] の合計を返す (1-based indexing)
	long long sum(int r) const
	{
		long long ret = 0;

		for (; 0 < r; r -= (r & -r))
		{
			ret += m_bit[r];
		}

		return ret;
	}

	// 閉区間 [l, r] の合計を返す (1-based indexing)
	long long sum(int l, int r) const
	{
		return (sum(r) - sum(l - 1));
	}

	// 数列の i 番目の要素を加算 (1-based indexing)
	void add(int i, long long value)
	{
		for (; i < m_bit.size(); i += (i & -i))
		{
			m_bit[i] += value;
		}
	}

private:

	std::vector<long long> m_bit;
};

int main()
{
	int n, q;
	std::cin >> n >> q;

	BIT bit(n);

	while (q--)
	{
		int com, x, y;
		std::cin >> com >> x >> y;

		if (com == 0)
		{
			bit.add(x, y);
		}
		else
		{
			std::cout << bit.sum(x, y) << '\n';
		}
	}
}
```
:::


### [Chokudai SpeedRun 001 J - 転倒数](https://atcoder.jp/contests/chokudai_S001/tasks/chokudai_S001_j)
:::details コード
```cpp
# include <iostream>
# include <vector>

// Binary Indexed Tree (1.1 基本実装)
// 1-based indexing
class BIT
{
public:

	BIT() = default;

	// 長さ size の数列で初期化
	explicit BIT(size_t size)
		: m_bit(size + 1) {}

	// 数列で初期化
	explicit BIT(const std::vector<long long>& v)
		: BIT(v.size())
	{
		for (int i = 0; i < v.size(); ++i)
		{
			add((i + 1), v[i]);
		}
	}

	// 閉区間 [1, r] の合計を返す (1-based indexing)
	long long sum(int r) const
	{
		long long ret = 0;

		for (; 0 < r; r -= (r & -r))
		{
			ret += m_bit[r];
		}

		return ret;
	}

	// 閉区間 [l, r] の合計を返す (1-based indexing)
	long long sum(int l, int r) const
	{
		return (sum(r) - sum(l - 1));
	}

	// 数列の i 番目の要素を加算 (1-based indexing)
	void add(int i, long long value)
	{
		for (; i < m_bit.size(); i += (i & -i))
		{
			m_bit[i] += value;
		}
	}

private:

	std::vector<long long> m_bit;
};

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	BIT bit(N);

	long long answer = 0;

	for (int i = 0; i < N; ++i)
	{
		answer += bit.sum(A[i], N);
		bit.add(A[i], 1);
	}

	std::cout << answer << '\n';
}
```
:::


### [ABC 190 F - Shift and Inversions](https://atcoder.jp/contests/abc190/tasks/abc190_f)
:::details コード
```cpp
# include <iostream>
# include <vector>

// Binary Indexed Tree (1.1 基本実装)
// 1-based indexing
class BIT
{
public:

	BIT() = default;

	// 長さ size の数列で初期化
	explicit BIT(size_t size)
		: m_bit(size + 1) {}

	// 数列で初期化
	explicit BIT(const std::vector<long long>& v)
		: BIT(v.size())
	{
		for (int i = 0; i < v.size(); ++i)
		{
			add((i + 1), v[i]);
		}
	}

	// 閉区間 [1, r] の合計を返す (1-based indexing)
	long long sum(int r) const
	{
		long long ret = 0;

		for (; 0 < r; r -= (r & -r))
		{
			ret += m_bit[r];
		}

		return ret;
	}

	// 閉区間 [l, r] の合計を返す (1-based indexing)
	long long sum(int l, int r) const
	{
		return (sum(r) - sum(l - 1));
	}

	// 数列の i 番目の要素を加算 (1-based indexing)
	void add(int i, long long value)
	{
		for (; i < m_bit.size(); i += (i & -i))
		{
			m_bit[i] += value;
		}
	}

private:

	std::vector<long long> m_bit;
};

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	BIT bit(N);

	// 転倒数の記録
	long long answer = 0;

	for (const auto& a : A)
	{
		answer += bit.sum((a + 1), N);

		bit.add((a + 1), 1);
	}

	for (const auto& a : A)
	{
		std::cout << answer << '\n';

		// 先頭を取り除くと a より小さい数だけ転倒数が減る
		answer -= a;

		// 末尾に a を追加すると a より大きい数だけ転倒数が増える
		answer += (N - 1 - a);
	}
}
```
:::


### [AOJ DSL_2_G - RSQ and RAQ](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=DSL_2_G)
:::details コード
```cpp
# include <iostream>
# include <vector>

class BIT
{
public:

	BIT() = default;

	// 長さ size の数列で初期化
	explicit BIT(size_t size)
		: m_bit(size + 1) {}

	// 数列で初期化
	explicit BIT(const std::vector<long long>& v)
		: BIT(v.size())
	{
		for (int i = 0; i < v.size(); ++i)
		{
			add((i + 1), v[i]);
		}
	}

	// 閉区間 [1, r] の合計を返す (1-based indexing)
	long long sum(int r) const
	{
		long long ret = 0;

		for (; 0 < r; r -= (r & -r))
		{
			ret += m_bit[r];
		}

		return ret;
	}

	// 閉区間 [l, r] の合計を返す (1-based indexing)
	long long sum(int l, int r) const
	{
		return (sum(r) - sum(l - 1));
	}

	// 数列の i 番目の要素を加算 (1-based indexing)
	void add(int i, long long value)
	{
		for (; i < m_bit.size(); i += (i & -i))
		{
			m_bit[i] += value;
		}
	}

private:

	std::vector<long long> m_bit;
};

// Binary Indexed Tree (1.2 区間加算対応)
// 1-based indexing
class BIT_RAQ
{
public:

	BIT_RAQ() = default;

	explicit BIT_RAQ(size_t size)
		: m_bit0(size)
		, m_bit1(size) {}

	explicit BIT_RAQ(const std::vector<long long>& v)
		: m_bit0(v)
		, m_bit1(v.size()) {}

	// 閉区間 [1, r] の合計を返す (1-based indexing)
	long long sum(int r) const
	{
		return (m_bit0.sum(r) + m_bit1.sum(r) * r);
	}

	// 閉区間 [l, r] の合計を返す (1-based indexing)
	long long sum(int l, int r) const
	{
		return (sum(r) - sum(l - 1));
	}

	// 数列の i 番目の要素を加算 (1-based indexing)
	void add(int i, long long value)
	{
		m_bit0.add(i, value);
	}

	// 閉区間 [l, r] の要素を加算 (1-based indexing)
	void add(int l, int r, long long value)
	{
		m_bit0.add(l, (-value * (l - 1)));
		m_bit0.add((r + 1), (value * r));
		m_bit1.add(l, value);
		m_bit1.add((r + 1), -value);
	}

private:

	BIT m_bit0;

	BIT m_bit1;
};

int main()
{
	int n, q;
	std::cin >> n >> q;

	BIT_RAQ bit(n);

	while (q--)
	{
		int query;
		std::cin >> query;

		if (query == 0)
		{
			int s, t, x;
			std::cin >> s >> t >> x;
			bit.add(s, t, x);
		}
		else
		{
			int s, t;
			std::cin >> s >> t;
			std::cout << bit.sum(s, t) << '\n';
		}
	}
}
```
:::