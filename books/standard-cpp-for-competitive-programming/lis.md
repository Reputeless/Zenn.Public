---
title: "最長増加部分列 (LIS)"
free: true
---

C++ 標準ライブラリを用いた、**最長増加部分列** (LIS: Longest Increasing Subsequence) の実装です。


# 1. 最長増加部分列 (LIS) のテンプレート

| 機能 | 1.1 | 1.2 | 1.3 | 1.4 |
|----|:----:|:----:|:----:|:----:|
| 最長増加部分列の長さを取得する（狭義単調増加） | ✅ | ✅ | ✅ | ✅ |
| 最長増加部分列の長さを取得する（広義単調増加） |   | ✅ | ✅ | ✅ |
| 途中経過も含んだ最長増加部分列の長さを取得する |   |   | ✅ |   |
| 最長増加部分列を復元する |  |   |   | ✅ |


## 1.1 最長増加部分列の長さの取得（狭義単調増加）
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // std::lower_bound()

/// @brief 最長増加部分列（LIS）の長さを返します（狭義単調増加）
/// @tparam Type 数列の要素の型
/// @param v 数列
/// @return 最長増加部分列（LIS）の長さ
/// @note 1.1 最長増加部分列の長さの取得（狭義単調増加）
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/lis
template <class Type>
size_t LIS(const std::vector<Type>& v)
{
	std::vector<Type> dp;

	for (const auto& elem : v)
	{
		auto it = std::lower_bound(dp.begin(), dp.end(), elem);

		if (it == dp.end())
		{
			dp.push_back(elem);
		}
		else
		{
			*it = elem;
		}
	}

	return dp.size();
}

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		//std::cin >> a;
	}

	std::cout << LIS(A) << '\n';
}
```

- [Chokudai SpeedRun 001 H](https://atcoder.jp/contests/chokudai_S001/submissions/38212598)
:::


## 1.2 最長増加部分列の長さの取得
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // std::lower_bound(), std::upper_bound()

/// @brief 最長増加部分列（LIS）の長さを返します
/// @tparam Strict 狭義単調増加の場合 true, 広義単調増加の場合 false
/// @tparam Type 数列の要素の型
/// @param v 数列
/// @return 最長増加部分列（LIS）の長さ
/// @note 1.2 最長増加部分列の長さの取得
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/lis
template <bool Strict, class Type>
size_t LIS(const std::vector<Type>& v)
{
	std::vector<Type> dp;

	auto it = dp.begin();

	for (const auto& elem : v)
	{
		if constexpr (Strict)
		{
			it = std::lower_bound(dp.begin(), dp.end(), elem);
		}
		else
		{
			it = std::upper_bound(dp.begin(), dp.end(), elem);
		}

		if (it == dp.end())
		{
			dp.push_back(elem);
		}
		else
		{
			*it = elem;
		}
	}

	return dp.size();
}

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		//std::cin >> a;
	}

	std::cout << LIS<true>(A) << '\n';
}
```

- [Chokudai SpeedRun 001 H](https://atcoder.jp/contests/chokudai_S001/submissions/38213392)
:::


## 1.3 途中経過も含んだ最長増加部分列の長さの取得
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // std::lower_bound(), std::upper_bound()

/// @brief 途中経過も含む, 最長増加部分列（LIS）の長さを返します
/// @tparam Strict 狭義単調増加の場合 true, 広義単調増加の場合 false
/// @tparam Type 数列の要素の型
/// @param v 数列
/// @return 途中経過も含む, 最長増加部分列（LIS）の長さ
/// @note 1.3 途中経過も含んだ最長増加部分列の長さの取得
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/lis
template <bool Strict, class Type>
std::vector<size_t> LIS(const std::vector<Type>& v)
{
	std::vector<Type> dp;

	auto it = dp.begin();

	// 最長増加部分列の長さの途中経過を記録する配列
	std::vector<size_t> counts;

	for (const auto& elem : v)
	{
		if constexpr (Strict)
		{
			it = std::lower_bound(dp.begin(), dp.end(), elem);
		}
		else
		{
			it = std::upper_bound(dp.begin(), dp.end(), elem);
		}

		if (it == dp.end())
		{
			dp.push_back(elem);
		}
		else
		{
			*it = elem;
		}

		counts.push_back(dp.size());
	}

	return counts;
}

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		//std::cin >> a;
	}

	std::cout << LIS<true>(A).back() << '\n';
}
```

- [Chokudai SpeedRun 001 H](https://atcoder.jp/contests/chokudai_S001/submissions/38215082) / [競プロ典型 90 問 - 060](https://atcoder.jp/contests/typical90/submissions/38215295)
:::


## 1.4 最長増加部分列の復元
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // std::lower_bound(), std::upper_bound()

/// @brief 最長増加部分列（LIS）のインデックスを返します
/// @tparam Strict 狭義単調増加の場合 true, 広義単調増加の場合 false
/// @tparam Type 数列の要素の型
/// @param v 数列
/// @return 最長増加部分列（LIS）のインデックス
/// @note 1.4 最長増加部分列の復元
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/lis
template <bool Strict, class Type>
std::vector<int> LIS(const std::vector<Type>& v)
{
	std::vector<Type> dp;

	auto it = dp.begin();

	std::vector<int> positions;

	for (const auto& elem : v)
	{
		if constexpr (Strict)
		{
			it = std::lower_bound(dp.begin(), dp.end(), elem);
		}
		else
		{
			it = std::upper_bound(dp.begin(), dp.end(), elem);
		}

		positions.push_back(static_cast<int>(it - dp.begin()));

		if (it == dp.end())
		{
			dp.push_back(elem);
		}
		else
		{
			*it = elem;
		}
	}

	std::vector<int> subseq(dp.size());

	int si = static_cast<int>(subseq.size()) - 1;

	int pi = static_cast<int>(positions.size()) - 1;

	while ((0 <= si) && (0 <= pi))
	{
		if (positions[pi] == si)
		{
			subseq[si] = pi;

			--si;
		}

		--pi;
	}

	return subseq;
}

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		//std::cin >> a;
	}

	std::cout << LIS<true>(A).size() << '\n';
}
```

- [Chokudai SpeedRun 001 H](https://atcoder.jp/contests/chokudai_S001/submissions/38216003) / [Library Checker](https://judge.yosupo.jp/submission/122430)
:::


# 2. 最長増加部分列 (LIS) の例題

### [AOJ DPL_1_D - Longest Increasing Subsequence](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=DPL_1_D&lang=jp)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // std::lower_bound()

/// @brief 最長増加部分列（LIS）の長さを返します（狭義単調増加）
/// @tparam Type 数列の要素の型
/// @param v 数列
/// @return 最長増加部分列（LIS）の長さ
/// @note 1.1 最長増加部分列の長さの取得（狭義単調増加）
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/lis
template <class Type>
size_t LIS(const std::vector<Type>& v)
{
	std::vector<Type> dp;

	for (const auto& elem : v)
	{
		auto it = std::lower_bound(dp.begin(), dp.end(), elem);

		if (it == dp.end())
		{
			dp.push_back(elem);
		}
		else
		{
			*it = elem;
		}
	}

	return dp.size();
}

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	std::cout << LIS(A) << '\n';
}
```
:::


### [JOI 春合宿 2007 day2-1 Building](https://atcoder.jp/contests/joisc2007/tasks/joisc2007_buildi)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // std::lower_bound()

/// @brief 最長増加部分列（LIS）の長さを返します（狭義単調増加）
/// @tparam Type 数列の要素の型
/// @param v 数列
/// @return 最長増加部分列（LIS）の長さ
/// @note 1.1 最長増加部分列の長さの取得（狭義単調増加）
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/lis
template <class Type>
size_t LIS(const std::vector<Type>& v)
{
	std::vector<Type> dp;

	for (const auto& elem : v)
	{
		auto it = std::lower_bound(dp.begin(), dp.end(), elem);

		if (it == dp.end())
		{
			dp.push_back(elem);
		}
		else
		{
			*it = elem;
		}
	}

	return dp.size();
}

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	std::cout << LIS(A) << '\n';
}
```


### [ABC 006 D - トランプ挿入ソート](https://atcoder.jp/contests/abc006/tasks/abc006_4)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // std::lower_bound()

/// @brief 最長増加部分列（LIS）の長さを返します（狭義単調増加）
/// @tparam Type 数列の要素の型
/// @param v 数列
/// @return 最長増加部分列（LIS）の長さ
/// @note 1.1 最長増加部分列の長さの取得（狭義単調増加）
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/lis
template <class Type>
size_t LIS(const std::vector<Type>& v)
{
	std::vector<Type> dp;

	for (const auto& elem : v)
	{
		auto it = std::lower_bound(dp.begin(), dp.end(), elem);

		if (it == dp.end())
		{
			dp.push_back(elem);
		}
		else
		{
			*it = elem;
		}
	}

	return dp.size();
}

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	std::cout << (N - LIS(A)) << '\n';
}
```
:::


### [競プロ典型 90 問 060 - Chimera（★5）](https://atcoder.jp/contests/typical90/tasks/typical90_bh)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // std::lower_bound(), std::upper_bound()

/// @brief 途中経過も含む, 最長増加部分列（LIS）の長さを返します
/// @tparam Strict 狭義単調増加の場合 true, 広義単調増加の場合 false
/// @tparam Type 数列の要素の型
/// @param v 数列
/// @return 途中経過も含む, 最長増加部分列（LIS）の長さ
/// @note 1.3 途中経過も含んだ最長増加部分列の長さの取得
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/lis
template <bool Strict, class Type>
std::vector<size_t> LIS(const std::vector<Type>& v)
{
	std::vector<Type> dp;

	auto it = dp.begin();

	// 最長増加部分列の長さの途中経過を記録する配列
	std::vector<size_t> counts;

	for (const auto& elem : v)
	{
		if constexpr (Strict)
		{
			it = std::lower_bound(dp.begin(), dp.end(), elem);
		}
		else
		{
			it = std::upper_bound(dp.begin(), dp.end(), elem);
		}

		if (it == dp.end())
		{
			dp.push_back(elem);
		}
		else
		{
			*it = elem;
		}

		counts.push_back(dp.size());
	}

	return counts;
}

int main()
{
	// 長さ N の数列
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}
	// A の逆順
	std::vector<int> B(A.rbegin(), A.rend());

	// 各要素までの LIS の長さを記録する配列
	const std::vector<size_t> countsA = LIS<true>(A);
	const std::vector<size_t> countsB = LIS<true>(B);

	size_t answer = 0;

	// ((A の LIS の長さ) + (B の LIS の長さ) - 1) で, 最も大きくなる値を探す
	for (int i = 0; i < N; ++i)
	{
		answer = std::max(answer, (countsA[i] + countsB[N - i - 1] - 1));
	}

	std::cout << answer << '\n';
}
```
:::


### [ABC 134 E - Sequence Decomposing](https://atcoder.jp/contests/abc134/tasks/abc134_e)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // std::lower_bound(), std::upper_bound()

/// @brief 最長増加部分列（LIS）の長さを返します
/// @tparam Strict 狭義単調増加の場合 true, 広義単調増加の場合 false
/// @tparam Type 数列の要素の型
/// @param v 数列
/// @return 最長増加部分列（LIS）の長さ
/// @note 1.2 最長増加部分列の長さの取得
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/lis
template <bool Strict, class Type>
size_t LIS(const std::vector<Type>& v)
{
	std::vector<Type> dp;

	auto it = dp.begin();

	for (const auto& elem : v)
	{
		if constexpr (Strict)
		{
			it = std::lower_bound(dp.begin(), dp.end(), elem);
		}
		else
		{
			it = std::upper_bound(dp.begin(), dp.end(), elem);
		}

		if (it == dp.end())
		{
			dp.push_back(elem);
		}
		else
		{
			*it = elem;
		}
	}

	return dp.size();
}

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	std::reverse(A.begin(), A.end());

	std::cout << LIS<false>(A) << '\n';
}
```
:::


### [ABC 038 D - プレゼント](https://atcoder.jp/contests/abc038/tasks/abc038_d)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // std::lower_bound()
#include <utility> // std::pair

size_t LIS(const std::vector<std::pair<int, int>>& v)
{
	std::vector<int> dp;
 
	for (const auto& elem : v)
	{
		auto it = std::lower_bound(dp.begin(), dp.end(), elem.second);
 
		if (it == dp.end())
		{
			dp.push_back(elem.second);
		}
		else
		{
			*it = elem.second;
		}
	}
 
	return dp.size();
}

int main()
{
	int N;
	std::cin >> N;

	std::vector<std::pair<int, int>> A(N);
	for (auto& a : A)
	{
		std::cin >> a.first >> a.second;

		// 幅が小さい順, 同じ幅では高さが大きい順にソートするため, 高さの符号を反転
		a.second *= -1;
	}

	std::sort(A.begin(), A.end());

	// 反転した符号を元に戻す
	for (auto& a : A)
	{
		a.second *= -1;
	}

	std::cout << LIS(A) << '\n';
}
```
:::


### [Chokudai SpeedRun 002 L - 長方形 β](https://atcoder.jp/contests/chokudai_S002/tasks/chokudai_S002_l)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // std::lower_bound()
#include <utility> // std::pair

size_t LIS(const std::vector<std::pair<int, int>>& v)
{
	std::vector<int> dp;

	for (const auto& elem : v)
	{
		auto it = std::lower_bound(dp.begin(), dp.end(), elem.second);

		if (it == dp.end())
		{
			dp.push_back(elem.second);
		}
		else
		{
			*it = elem.second;
		}
	}

	return dp.size();
}

int main()
{
	int N;
	std::cin >> N;

	std::vector<std::pair<int, int>> boxes;

	while (N--)
	{
		int A, B;
		std::cin >> A >> B;
		
		// 幅が小さい順, 同じ幅では高さが大きい順にソートするため, 高さの符号を反転
		boxes.emplace_back(A, -B);

		// 幅と高さの入れ替え（元の長方形とは重ならない）
		boxes.emplace_back(B, -A);
	}

	std::sort(boxes.begin(), boxes.end());

	// 反転した符号を元に戻す
	for (auto& box: boxes)
	{
		box.second *= -1;
	}

	std::cout << LIS(boxes) << '\n';
}
```
:::

