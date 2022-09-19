---
title: "ワーシャルフロイド法"
free: true
---

C++ 標準ライブラリを用いたワーシャルフロイド法 (Floyd–Warshall Algorithm) の実装です。


# 1. ワーシャルフロイド法のテンプレート

| 機能 | 1.1 |
|----|:----:|
| 全点対最短路問題を解く | ✅ |
| 負閉路を検出する |✅ |


## 1.1 基本実装
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

constexpr long long INF = (1LL << 60);

// ワーシャルフロイド法 (1.1 基本実装)
// 負閉路が存在する場合 true を返す
bool FloydWarshall(std::vector<std::vector<long long>>& distances)
{
	const size_t v = distances.size();

	for (size_t i = 0; i < v; ++i)
	{
		for (size_t from = 0; from < v; ++from)
		{
			for (size_t to = 0; to < v; ++to)
			{
				if ((distances[from][i] < INF) && (distances[i][to] < INF))
				{
					distances[from][to] = std::min(distances[from][to], (distances[from][i] + distances[i][to]));
				}
			}
		}
	}

	for (size_t i = 0; i < v; ++i)
	{
		// 負閉路が含まれている場合, distances[i][i] が負になるような i が存在する
		if (distances[i][i] < 0)
		{
			return true;
		}
	}

	return false;
}
```

- [AOJ GRL_1_C](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=6974063#1)
:::


# 2. ワーシャルフロイド法の例題

### [AOJ GRL_1_C - All Pairs Shortest Path](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=GRL_1_C&lang=ja)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

constexpr long long INF = (1LL << 60);

// ワーシャルフロイド法 (1.1 基本実装)
// 負閉路が存在する場合 true を返す
bool FloydWarshall(std::vector<std::vector<long long>>& distances)
{
	const size_t v = distances.size();

	for (size_t i = 0; i < v; ++i)
	{
		for (size_t from = 0; from < v; ++from)
		{
			for (size_t to = 0; to < v; ++to)
			{
				if ((distances[from][i] < INF) && (distances[i][to] < INF))
				{
					distances[from][to] = std::min(distances[from][to], (distances[from][i] + distances[i][to]));
				}
			}
		}
	}

	for (size_t i = 0; i < v; ++i)
	{
		// 負閉路が含まれている場合, distances[i][i] が負になるような i が存在する
		if (distances[i][i] < 0)
		{
			return true;
		}
	}

	return false;
}

int main()
{
	int V, E;
	std::cin >> V >> E;

	std::vector<std::vector<long long>> distances(V, std::vector<long long>(V, INF));

	for (int i = 0; i < E; ++i)
	{
		int s, t, d;
		std::cin >> s >> t >> d;
		distances[s][t] = d;
	}

	for (int i = 0; i < V; ++i)
	{
		distances[i][i] = 0;
	}

	if (FloydWarshall(distances))
	{
		std::cout << "NEGATIVE CYCLE\n";
		return 0;
	}

	for (int from = 0; from < V; ++from)
	{
		for (int to = 0; to < V; ++to)
		{
			if (INF == distances[from][to])
			{
				std::cout << "INF";
			}
			else
			{
				std::cout << distances[from][to];
			}

			if (to != (V - 1))
			{
				std::cout << ' ';
			}
		}

		std::cout << '\n';
	}
}
```
:::


### [ABC 012 D - バスと避けられない運命](https://atcoder.jp/contests/abc012/tasks/abc012_4)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

constexpr long long INF = (1LL << 60);

// ワーシャルフロイド法 (1.1 基本実装)
// 負閉路が存在する場合 true を返す
bool FloydWarshall(std::vector<std::vector<long long>>& distances)
{
	const size_t v = distances.size();

	for (size_t i = 0; i < v; ++i)
	{
		for (size_t from = 0; from < v; ++from)
		{
			for (size_t to = 0; to < v; ++to)
			{
				if ((distances[from][i] < INF) && (distances[i][to] < INF))
				{
					distances[from][to] = std::min(distances[from][to], (distances[from][i] + distances[i][to]));
				}
			}
		}
	}

	for (size_t i = 0; i < v; ++i)
	{
		// 負閉路が含まれている場合, distances[i][i] が負になるような i が存在する
		if (distances[i][i] < 0)
		{
			return true;
		}
	}

	return false;
}

int main()
{
	int N, M;
	std::cin >> N >> M;

	std::vector<std::vector<long long>> distances(N, std::vector<long long>(N, INF));

	for (int i = 0; i < M; ++i)
	{
		int a, b, c;
		std::cin >> a >> b >> c;
		--a; --b;
		distances[a][b] = distances[b][a] = c;
	}

	for (int i = 0; i < N; ++i)
	{
		distances[i][i] = 0;
	}

	FloydWarshall(distances);

	long long answer = INF;

	for (int from = 0; from < N; ++from)
	{
		answer = std::min(answer, *std::max_element(distances[from].begin(), distances[from].end()));
	}

	std::cout << answer << '\n';
}
```
:::


### [ABC 037 D - joisino's travel](https://gist.github.com/Reputeless/ec08155c22445b5de271b783b19866ab)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

constexpr long long INF = (1LL << 60);

// ワーシャルフロイド法 (1.1 基本実装)
// 負閉路が存在する場合 true を返す
bool FloydWarshall(std::vector<std::vector<long long>>& distances)
{
	const size_t v = distances.size();

	for (size_t i = 0; i < v; ++i)
	{
		for (size_t from = 0; from < v; ++from)
		{
			for (size_t to = 0; to < v; ++to)
			{
				if ((distances[from][i] < INF) && (distances[i][to] < INF))
				{
					distances[from][to] = std::min(distances[from][to], (distances[from][i] + distances[i][to]));
				}
			}
		}
	}

	for (size_t i = 0; i < v; ++i)
	{
		// 負閉路が含まれている場合, distances[i][i] が負になるような i が存在する
		if (distances[i][i] < 0)
		{
			return true;
		}
	}

	return false;
}

int main()
{
	int N, M, R;
	std::cin >> N >> M >> R;

	std::vector<int> vR(R);
	for (auto& r : vR)
	{
		std::cin >> r;
		--r;
	}

	std::vector<std::vector<long long>> distances(N, std::vector<long long>(N, INF));

	for (int i = 0; i < M; ++i)
	{
		int a, b, c;
		std::cin >> a >> b >> c;
		--a; --b;
		distances[a][b] = c;
		distances[b][a] = c;
	}

	for (int i = 0; i < N; ++i)
	{
		distances[i][i] = 0;
	}

	FloydWarshall(distances);

	std::sort(vR.begin(), vR.end());

	long long answer = INF;

	do
	{
		long long sum = 0;

		for (size_t i = 1; i < vR.size(); ++i)
		{
			const int from = vR[i - 1];
			const int to = vR[i];
			sum += distances[from][to];
		}

		answer = std::min(answer, sum);

	} while (std::next_permutation(vR.begin(), vR.end()));

	std::cout << answer << '\n';
}
```
:::
