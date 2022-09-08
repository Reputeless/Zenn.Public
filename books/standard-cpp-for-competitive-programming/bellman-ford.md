---
title: "ベルマンフォード法"
free: true
---

C++ 標準ライブラリを用いたベルマンフォード法 (Bellman–Ford algorithm) の実装です。


# 1. ベルマンフォード法のテンプレート

| 機能 | 1.1 | 1.2 | 1.3 | 1.4 | 1.5 |
|----|:----:|:----:|:----:|:----:|:----:|
| 単一始点の最短経路問題を解く | ✅ | ✅ | ✅ | ✅ | ✅ |
| 負閉路を検出する | ✅ | ✅ | ✅ | ✅ | ✅ |
| 負閉路の影響を受ける頂点を調べる |   | ✅ | ✅ |   |   |
| 最短経路を再構築する |   |   | ✅ |   | ✅ |
| SPFA (Shortest Path Faster Algorithm) による定数倍高速化 |   |   |   | ✅ | ✅ |


## 1.1 基本実装
:::details コード
```cpp
#include <iostream>
#include <vector>

constexpr long long INF = (1LL << 60);

// 辺
struct Edge
{
	int from;
	int to;
	int cost;
};

// ベルマンフォード法 (1.1 基本実装)
// 負閉路が存在する場合 true を返す
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
bool BellmanFord(const std::vector<Edge>& edges, std::vector<long long>& distances, int startIndex)
{
	distances[startIndex] = 0;

	for (size_t i = 0; i < distances.size(); ++i)
	{
		bool changed = false;

		// 各辺について
		for (const auto& edge : edges)
		{
			// (INF + cost) は INF なので処理しない
			if (distances[edge.from] == INF)
			{
				continue;
			}

			// to までの新しい距離
			const long long d = (distances[edge.from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				distances[edge.to] = d;

				changed = true;
			}
		}

		// どの頂点も更新されなかったら終了
		if (!changed)
		{
			return false;
		}
	}

	// 頂点回数分だけループしても更新が続くのは, 負閉路が存在するから
	return true;
}
```

- [AOJ GRL_1_B](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=6949805#2)
:::


## 1.2 負閉路の影響を受ける頂点を調べる
:::details コード
```cpp
#include <iostream>
#include <vector>

constexpr long long INF = (1LL << 60);

// 辺
struct Edge
{
	int from;
	int to;
	int cost;
};

// ベルマンフォード法 (1.2 負閉路の影響を受ける頂点を調べる)
// 負の閉路が存在する場合 true を返し, 負閉路の影響を受ける頂点は -INF にセットされる
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
bool BellmanFord(const std::vector<Edge>& edges, std::vector<long long>& distances, int startIndex)
{
	distances[startIndex] = 0;

	for (size_t i = 0; i < distances.size(); ++i)
	{
		bool changed = false;

		// 各辺について
		for (const auto& edge : edges)
		{
			// (INF + cost) は INF なので処理しない
			if (distances[edge.from] == INF)
			{
				continue;
			}

			// to までの新しい距離
			const long long d = (distances[edge.from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				distances[edge.to] = d;

				changed = true;
			}
		}

		// どの頂点も更新されなかったら終了
		if (!changed)
		{
			return false;
		}
	}

	// 頂点数分だけさらに繰り返し, 負閉路の影響を受ける頂点に -INF を伝播
	for (size_t i = 0; i < distances.size(); ++i)
	{
		for (const auto& edge : edges)
		{
			if (distances[edge.from] == INF)
			{
				continue;
			}

			const long long d = (distances[edge.from] + edge.cost);

			if (d < distances[edge.to])
			{
				// 負閉路の影響を受ける頂点を -INF に
				distances[edge.to] = -INF;
			}
		}
	}

	return true;
}
```

- [AOJ GRL_1_B](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=6949815#2) / [ABC 137 E](https://atcoder.jp/contests/abc137/submissions/34687694)
:::


## 1.3 最短経路を再構築する
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

constexpr long long INF = (1LL << 60);

// 辺
struct Edge
{
	int from;
	int to;
	int cost;
};

// ベルマンフォード法 (1.3 最短経路を再構築する)
// 負の閉路が存在する場合 true を返し, 負閉路の影響を受ける頂点は -INF にセットされる
// 頂点 targetIndex が負閉路の影響を受けない場合, 頂点 startIndex からの最短経路を path に格納する
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
bool BellmanFord(const std::vector<Edge>& edges, std::vector<long long>& distances, int startIndex, int targetIndex, std::vector<int>& path)
{
	distances[startIndex] = 0;

	// 直前の頂点を記録する配列
	std::vector<int> p(distances.size(), -1);

	bool changed = false;

	for (size_t i = 0; i < distances.size(); ++i)
	{
		changed = false;

		// 各辺について
		for (const auto& edge : edges)
		{
			// (INF + cost) は INF なので処理しない
			if (distances[edge.from] == INF)
			{
				continue;
			}

			// to までの新しい距離
			const long long d = (distances[edge.from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				distances[edge.to] = d;

				// 直前の頂点を記録
				p[edge.to] = edge.from;

				changed = true;
			}
		}

		// どの頂点も更新されなかったら終了
		if (!changed)
		{
			break;
		}
	}

	if (changed) // 負閉路あり
	{
		// 頂点数分だけさらに繰り返し, 負閉路の影響を受ける頂点に -INF を伝播
		for (size_t i = 0; i < distances.size(); ++i)
		{
			for (const auto& edge : edges)
			{
				if (distances[edge.from] == INF)
				{
					continue;
				}

				const long long d = (distances[edge.from] + edge.cost);

				if (d < distances[edge.to])
				{
					// 負閉路の影響を受ける頂点を -INF に
					distances[edge.to] = -INF;
				}
			}
		}
	}

	if ((distances[targetIndex] != INF)
		&& (distances[targetIndex] != -INF)) // 頂点 targetIndex に到達不可または負閉路が影響する場合は最短経路無し
	{
		// 経路を再構築
		for (int i = targetIndex; i != -1; i = p[i])
		{
			path.push_back(i);
		}

		std::reverse(path.begin(), path.end());
	}

	return changed;
}
```

- [AOJ GRL_1_B](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=6949819#2) / [Library Checker](https://judge.yosupo.jp/submission/103533) (ダイクストラ法より計算量が大きいため、一部ケースは TLE) / [ABC 137 E](https://atcoder.jp/contests/abc137/submissions/34687122)
:::


## 1.4 SPFA 基本実装
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <queue>

constexpr long long INF = (1LL << 60);

// 辺
struct Edge
{
	int to;
	int cost;
};

using Graph = std::vector<std::vector<Edge>>;

// ベルマンフォード法 (1.4 SPFA 基本実装)
// 負閉路が存在する場合 true を返す
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
bool SPFA(const Graph& grpah, std::vector<long long>& distances, int startIndex)
{
	const size_t N = distances.size();
	std::vector<int> counts(N, 0);
	std::vector<bool> inqueue(N);
	std::queue<int> q;

	distances[startIndex] = 0;
	q.push(startIndex);
	inqueue[startIndex] = true;

	while (!q.empty())
	{
		const int from = q.front(); q.pop();
		inqueue[from] = false;

		for (const auto& edge : grpah[from])
		{
			// to までの新しい距離
			const long long d = (distances[from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				distances[edge.to] = d;
			
				if (!inqueue[edge.to])
				{
					q.push(edge.to);
					inqueue[edge.to] = true;
					++counts[edge.to];
					
					if (N < counts[edge.to])
					{
						return true; // 負閉路あり
					}
				}
			}
		}
	}

	return false;
}
```

- [AOJ GRL_1_B](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=6949980#2)
:::


## 1.5 SPFA + 最短経路の再構築
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <algorithm>

constexpr long long INF = (1LL << 60);

// 辺
struct Edge
{
	int to;
	int cost;
};

using Graph = std::vector<std::vector<Edge>>;

// ベルマンフォード法 (1.5 SPFA + 最短経路の再構築)
// 負閉路が存在する場合 true を返す
// 負閉路が存在しない場合, 頂点 startIndex から頂点 targetIndex の最短経路を path に格納する
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
bool SPFA(const Graph& grpah, std::vector<long long>& distances, int startIndex, int targetIndex, std::vector<int>& path)
{
	const size_t N = distances.size();
	std::vector<int> counts(N, 0);
	std::vector<bool> inqueue(N);
	std::queue<int> q;

	// 直前の頂点を記録する配列
	std::vector<int> p(N, -1);

	distances[startIndex] = 0;
	q.push(startIndex);
	inqueue[startIndex] = true;

	while (!q.empty())
	{
		const int from = q.front(); q.pop();
		inqueue[from] = false;

		for (const auto& edge : grpah[from])
		{
			// to までの新しい距離
			const long long d = (distances[from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				distances[edge.to] = d;
				p[edge.to] = from;
			
				if (!inqueue[edge.to])
				{
					q.push(edge.to);
					inqueue[edge.to] = true;
					++counts[edge.to];
					
					if (N < counts[edge.to])
					{
						return true; // 負閉路あり
					}
				}
			}
		}
	}

	if (distances[targetIndex] != INF) // 頂点 targetIndex に到達不可の場合は最短経路無し
	{
		// 経路を再構築
		for (int i = targetIndex; i != -1; i = p[i])
		{
			path.push_back(i);
		}

		std::reverse(path.begin(), path.end());
	}

	return false;
}
```

- [AOJ GRL_1_B](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=6949981#2) / [Library Checker](https://judge.yosupo.jp/submission/103545) (1.3 より実行時間改善)
:::


# 2. ベルマンフォード法の例題

### [AOJ GRL_1_B - Single Source Shortest Path (Negative Edges)](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=GRL_1_B&lang=ja)
:::details コード
```cpp
#include <iostream>
#include <vector>

constexpr long long INF = (1LL << 60);

// 辺
struct Edge
{
	int from;
	int to;
	int cost;
};

// ベルマンフォード法 (1.1 基本実装)
// 負の閉路が存在する場合 true を返す
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
bool BellmanFord(const std::vector<Edge>& edges, std::vector<long long>& distances, int startIndex)
{
	distances[startIndex] = 0;

	for (size_t i = 0; i < distances.size(); ++i)
	{
		bool changed = false;

		// 各辺について
		for (const auto& edge : edges)
		{
			// (INF + cost) は INF なので処理しない
			if (distances[edge.from] == INF)
			{
				continue;
			}

			// to までの新しい距離
			const long long d = (distances[edge.from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				distances[edge.to] = d;

				changed = true;
			}
		}

		// どの頂点も更新されなかったら終了
		if (!changed)
		{
			return false;
		}
	}

	// 頂点回数分だけループしても更新が続くのは, 負閉路が存在するから
	return true;
}

int main()
{
	int V, E, r;
	std::cin >> V >> E >> r;

	std::vector<Edge> edges(E);
	for (auto& edge : edges)
	{
		std::cin >> edge.from >> edge.to >> edge.cost;
	}

	// 始点から各頂点への距離を格納する配列
	std::vector<long long> distances(V, INF);

	if (BellmanFord(edges, distances, r)) // 負閉路が存在
	{
		std::cout << "NEGATIVE CYCLE\n";
	}
	else
	{
		for (const auto& distance : distances)
		{
			if (distance == INF)
			{
				std::cout << "INF\n";
			}
			else
			{
				std::cout << distance << '\n';
			}
		}
	}
}
```
:::


### [ABC 061 D - Score Attack](https://atcoder.jp/contests/abc061/tasks/abc061_d)
:::details コード
```cpp
#include <iostream>
#include <vector>

constexpr long long INF = (1LL << 60);

// 辺
struct Edge
{
	int from;
	int to;
	int cost;
};

// ベルマンフォード法 (1.2 負閉路の影響を受ける頂点を調べる)
// 負の閉路が存在する場合 true を返し, 負閉路の影響を受ける頂点は -INF にセットされる
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
bool BellmanFord(const std::vector<Edge>& edges, std::vector<long long>& distances, int startIndex)
{
	distances[startIndex] = 0;

	for (size_t i = 0; i < distances.size(); ++i)
	{
		bool changed = false;

		// 各辺について
		for (const auto& edge : edges)
		{
			// (INF + cost) は INF なので処理しない
			if (distances[edge.from] == INF)
			{
				continue;
			}

			// to までの新しい距離
			const long long d = (distances[edge.from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				distances[edge.to] = d;

				changed = true;
			}
		}

		// どの頂点も更新されなかったら終了
		if (!changed)
		{
			return false;
		}
	}

	// 頂点数分だけさらに繰り返し, 負閉路の影響を受ける頂点に -INF を伝播
	for (size_t i = 0; i < distances.size(); ++i)
	{
		for (const auto& edge : edges)
		{
			if (distances[edge.from] == INF)
			{
				continue;
			}

			const long long d = (distances[edge.from] + edge.cost);

			if (d < distances[edge.to])
			{
				// 負閉路の影響を受ける頂点を -INF に
				distances[edge.to] = -INF;
			}
		}
	}

	return true;
}

int main()
{
	int N, M;
	std::cin >> N >> M;

	std::vector<Edge> edges(M);
	for (auto& edge : edges)
	{
		std::cin >> edge.from >> edge.to >> edge.cost;
		--edge.from; --edge.to;
		edge.cost *= -1;
	}

	std::vector<long long> distances(N, INF);

	BellmanFord(edges, distances, 0);

	if (distances[N - 1] == -INF) // 頂点 N へと至る負閉路が存在する場合
	{
		std::cout << "inf\n";
	}
	else
	{
		std::cout << -distances[N - 1] << '\n';
	}
}
```
:::


### [ABC 137 E - Coins Respawn](https://atcoder.jp/contests/abc137/tasks/abc137_e)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

constexpr long long INF = (1LL << 60);

// 辺
struct Edge
{
	int from;
	int to;
	int cost;
};

// ベルマンフォード法 (1.2 負閉路の影響を受ける頂点を調べる)
// 負の閉路が存在する場合 true を返し, 負閉路の影響を受ける頂点は -INF にセットされる
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
bool BellmanFord(const std::vector<Edge>& edges, std::vector<long long>& distances, int startIndex)
{
	distances[startIndex] = 0;

	for (size_t i = 0; i < distances.size(); ++i)
	{
		bool changed = false;

		// 各辺について
		for (const auto& edge : edges)
		{
			// (INF + cost) は INF なので処理しない
			if (distances[edge.from] == INF)
			{
				continue;
			}

			// to までの新しい距離
			const long long d = (distances[edge.from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				distances[edge.to] = d;

				changed = true;
			}
		}

		// どの頂点も更新されなかったら終了
		if (!changed)
		{
			return false;
		}
	}

	// 頂点数分だけさらに繰り返し, 負閉路の影響を受ける頂点に -INF を伝播
	for (size_t i = 0; i < distances.size(); ++i)
	{
		for (const auto& edge : edges)
		{
			if (distances[edge.from] == INF)
			{
				continue;
			}

			const long long d = (distances[edge.from] + edge.cost);

			if (d < distances[edge.to])
			{
				// 負閉路の影響を受ける頂点を -INF に
				distances[edge.to] = -INF;
			}
		}
	}

	return true;
}

int main()
{
	// N 頂点, M 辺, 1 分あたりのペナルティ P
	int N, M, P;
	std::cin >> N >> M >> P;

	std::vector<Edge> edges(M);
	for (auto& edge : edges)
	{
		std::cin >> edge.from >> edge.to >> edge.cost;
		--edge.from;
		--edge.to;
		edge.cost = (-edge.cost + P);
	}

	std::vector<long long> distances(N, INF);

	BellmanFord(edges, distances, 0);

	if (distances[N - 1] == -INF) // 頂点 N へと至る負閉路が存在する場合
	{
		std::cout << "-1\n";
	}
	else
	{
		std::cout << std::max(-distances[N - 1], 0LL) << '\n';
	}
}
```
:::
