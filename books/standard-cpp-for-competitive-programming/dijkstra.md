---
title: "ダイクストラ法"
free: true
---

C++ 標準ライブラリを用いた、**ダイクストラ法** (Dijkstra algorithm) の実装です。


# 1. ダイクストラ法のテンプレート

| 機能 | 1.1 | 1.2 |
|----|:----:|:----:|
| 単一始点の最短経路問題を解く | ✅ | ✅ |
| 最短経路を再構築する |   | ✅ |


## 1.1 基本実装
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::pair
#include <queue> // std::priority_queue
#include <functional> // std::greater

constexpr long long INF = (1LL << 60);

// 辺の情報
struct Edge
{
	// 行先
	int to;

	// コスト
	int cost;
};

using Graph = std::vector<std::vector<Edge>>;

// { distance, from }
using Pair = std::pair<long long, int>;

// ダイクストラ法 (1.1 基本実装)
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
void Dijkstra(const Graph& graph, std::vector<long long>& distances, int startIndex)
{
	// 「現時点での最短距離, 頂点」の順に取り出す priority_queue
	// デフォルトの priority_queue は降順に取り出すため std::greater を使う
	std::priority_queue<Pair, std::vector<Pair>, std::greater<Pair>> q;
	q.emplace((distances[startIndex] = 0), startIndex);

	while (!q.empty())
	{
		const long long distance = q.top().first;
		const int from = q.top().second;
		q.pop();

		// 最短距離でなければ処理しない
		if (distances[from] < distance)
		{
			continue;
		}

		// 現在の頂点からの各辺について
		for (const auto& edge : graph[from])
		{
			// to までの新しい距離
			const long long d = (distances[from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				q.emplace((distances[edge.to] = d), edge.to);
			}
		}
	}
}
```

- [AOJ GRL_1_A](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=6990266#2)
:::


## 1.2 最短経路を再構築する
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::pair
#include <queue> // std::priority_queue
#include <functional> // std::greater
#include <algorithm> // std::reverse()

constexpr long long INF = (1LL << 60);

// 辺の情報
struct Edge
{
	// 行先
	int to;

	// コスト
	int cost;
};

using Graph = std::vector<std::vector<Edge>>;

// { distance, from }
using Pair = std::pair<long long, int>;

// ダイクストラ法 (1.2 最短経路を再構築する)
// 頂点 startIndex から頂点 targetIndex までの最短経路を path に格納する
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
void Dijkstra(const Graph& graph, std::vector<long long>& distances, int startIndex, int targetIndex, std::vector<int>& path)
{
	// 直前の頂点を記録する配列
	std::vector<int> p(distances.size(), -1);

	// 「現時点での最短距離, 頂点」の順に取り出す priority_queue
	// デフォルトの priority_queue は降順に取り出すため std::greater を使う
	std::priority_queue<Pair, std::vector<Pair>, std::greater<Pair>> q;
	q.emplace((distances[startIndex] = 0), startIndex);

	while (!q.empty())
	{
		const long long distance = q.top().first;
		const int from = q.top().second;
		q.pop();

		// 最短距離でなければ処理しない
		if (distances[from] < distance)
		{
			continue;
		}

		// 現在の頂点からの各辺について
		for (const auto& edge : graph[from])
		{
			// to までの新しい距離
			const long long d = (distances[from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				// 直前の頂点を記録
				p[edge.to] = from;

				q.emplace((distances[edge.to] = d), edge.to);
			}
		}
	}

	// targetIndex に到達できれば最短経路を再構築する
	if (distances[targetIndex] != INF)
	{
		for (int i = targetIndex; i != -1; i = p[i])
		{
			path.push_back(i);
		}

		std::reverse(path.begin(), path.end());
	}
}
```

- [AOJ GRL_1_A](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=6990272#2) / [Library Checker - Shortest Path](https://judge.yosupo.jp/submission/105782)
:::


# 2. ダイクストラ法の例題

### [AOJ GRL_1_A - Single Source Shortest Path](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=GRL_1_A&lang=ja)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::pair
#include <queue> // std::priority_queue
#include <functional> // std::greater

constexpr long long INF = (1LL << 60);

// 辺の情報
struct Edge
{
	// 行先
	int to;

	// コスト
	int cost;
};

using Graph = std::vector<std::vector<Edge>>;

// { distance, from }
using Pair = std::pair<long long, int>;

// ダイクストラ法 (1.1 基本実装)
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
void Dijkstra(const Graph& graph, std::vector<long long>& distances, int startIndex)
{
	// 「現時点での最短距離, 頂点」の順に取り出す priority_queue
	// デフォルトの priority_queue は降順に取り出すため std::greater を使う
	std::priority_queue<Pair, std::vector<Pair>, std::greater<Pair>> q;
	q.emplace((distances[startIndex] = 0), startIndex);

	while (!q.empty())
	{
		const long long distance = q.top().first;
		const int from = q.top().second;
		q.pop();

		// 最短距離でなければ処理しない
		if (distances[from] < distance)
		{
			continue;
		}

		// 現在の頂点からの各辺について
		for (const auto& edge : graph[from])
		{
			// to までの新しい距離
			const long long d = (distances[from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				q.emplace((distances[edge.to] = d), edge.to);
			}
		}
	}
}

int main()
{
	int V, E, r;
	std::cin >> V >> E >> r;

	Graph graph(V);
	while (E--)
	{
		int s, t, d;
		std::cin >> s >> t >> d;
		graph[s].push_back({ t, d });
	}

	std::vector<long long> distances(V, INF);
	Dijkstra(graph, distances, r);

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
```
:::


### [典型 013 - Passing (★5)](https://atcoder.jp/contests/typical90/tasks/typical90_m)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::pair
#include <queue> // std::priority_queue
#include <functional> // std::greater

constexpr long long INF = (1LL << 60);

// 辺の情報
struct Edge
{
	// 行先
	int to;

	// コスト
	int cost;
};

using Graph = std::vector<std::vector<Edge>>;

// { distance, from }
using Pair = std::pair<long long, int>;

// ダイクストラ法 (1.1 基本実装)
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
void Dijkstra(const Graph& graph, std::vector<long long>& distances, int startIndex)
{
	// 「現時点での最短距離, 頂点」の順に取り出す priority_queue
	// デフォルトの priority_queue は降順に取り出すため std::greater を使う
	std::priority_queue<Pair, std::vector<Pair>, std::greater<Pair>> q;
	q.emplace((distances[startIndex] = 0), startIndex);

	while (!q.empty())
	{
		const long long distance = q.top().first;
		const int from = q.top().second;
		q.pop();

		// 最短距離でなければ処理しない
		if (distances[from] < distance)
		{
			continue;
		}

		// 現在の頂点からの各辺について
		for (const auto& edge : graph[from])
		{
			// to までの新しい距離
			const long long d = (distances[from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				q.emplace((distances[edge.to] = d), edge.to);
			}
		}
	}
}

int main()
{
	// N 個の街, M 本の道路
	int N, M;
	std::cin >> N >> M;

	// N 頂点のグラフ
	Graph graph(N);
	for (int i = 0; i < M; ++i)
	{
		int A, B, C;
		std::cin >> A >> B >> C;
		--A; --B;
		graph[A].push_back({ B, C });
		graph[B].push_back({ A, C });
	}

	// 街[0] から各街への最短距離
	std::vector<long long> distancesFrom1(N, INF);
	Dijkstra(graph, distancesFrom1, 0);

	// 街[N-1] から各街への最短距離
	std::vector<long long> distancesFromN(N, INF);
	Dijkstra(graph, distancesFromN, (N - 1));

	// 街[i] を経由して街[N-1] まで移動するときにかかる時間の最小値
	for (int i = 0; i < N; ++i)
	{
		std::cout << (distancesFrom1[i] + distancesFromN[i]) << '\n';
	}
}
```
:::


### [ABC 035 D – トレジャーハント](https://atcoder.jp/contests/abc035/tasks/abc035_d)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <utility> // std::pair
#include <queue> // std::priority_queue
#include <functional> // std::greater
#include <algorithm> // std::max()

constexpr long long INF = (1LL << 60);

// 辺の情報
struct Edge
{
	// 行先
	int to;

	// コスト
	int cost;
};

using Graph = std::vector<std::vector<Edge>>;

// { distance, from }
using Pair = std::pair<long long, int>;

// ダイクストラ法 (1.1 基本実装)
// distances は頂点数と同じサイズ, 全要素 INF で初期化しておく
void Dijkstra(const Graph& graph, std::vector<long long>& distances, int startIndex)
{
	// 「現時点での最短距離, 頂点」の順に取り出す priority_queue
	// デフォルトの priority_queue は降順に取り出すため std::greater を使う
	std::priority_queue<Pair, std::vector<Pair>, std::greater<Pair>> q;
	q.emplace((distances[startIndex] = 0), startIndex);

	while (!q.empty())
	{
		const long long distance = q.top().first;
		const int from = q.top().second;
		q.pop();

		// 最短距離でなければ処理しない
		if (distances[from] < distance)
		{
			continue;
		}

		// 現在の頂点からの各辺について
		for (const auto& edge : graph[from])
		{
			// to までの新しい距離
			const long long d = (distances[from] + edge.cost);

			// d が現在の記録より小さければ更新
			if (d < distances[edge.to])
			{
				q.emplace((distances[edge.to] = d), edge.to);
			}
		}
	}
}

int main()
{
	int N, M, T;
	std::cin >> N >> M >> T;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	std::vector<std::vector<Edge>> graph1(N), graph2(N);
	for (int i = 0; i < M; ++i)
	{
		int a, b, c;
		std::cin >> a >> b >> c;
		--a; --b;
		graph1[a].push_back({ b, c });
		graph2[b].push_back({ a, c });
	}

	std::vector<long long> distances1(N, INF);
	Dijkstra(graph1, distances1, 0);

	std::vector<long long> distances2(N, INF);
	Dijkstra(graph2, distances2, 0);

	long long answer = 0;

	for (int i = 0; i < N; ++i)
	{
		const long long t1 = distances1[i];
		const long long t2 = distances2[i];

		if ((t1 == INF) || (t2 == INF))
		{
			continue;
		}

		const long long huntTime = (T - (t1 + t2));

		answer = std::max(answer, huntTime * A[i]);
	}

	std::cout << answer << '\n';
}
```
:::
