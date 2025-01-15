---
title: "トポロジカルソート（Kahn のアルゴリズム）"
free: true
---

## 1. 概要
- **トポロジカルソート**は、有向非巡回グラフ（DAG）の頂点を一列に並べたとき、すべての辺が左から右に向かうような順序（トポロジカル順序）を求めるアルゴリズム
	- 依存関係のあるタスクの実行順序を決める際などに用いられる
- **Kahn のアルゴリズム**は、トポロジカルソートを実現する代表的なアルゴリズムの 1 つ

:::details 有向非巡回グラフ（DAG）
- Directed Acyclic Graph
- グラフの辺が有向であり、閉路が存在しない（非巡回）
- DAG では、少なくとも 1 つのトポロジカル順序が必ず存在する
:::

### 1.1 データ構造
```cpp
std::vector<std::vector<int>> graph(V);
std::vector<int> indegrees(V);
std::queue<int> q;
std::vector<int> result;
```
- 隣接リスト `graph` によりグラフを表現
- `indegrees` 配列に、各頂点の入次数（いりじすう）（その頂点に入ってくる辺の本数）を記録
- キュー `q` は、入次数が 0 の頂点を管理
- `result` に、アルゴリズムで求めたトポロジカル順序を格納


### 1.2 アルゴリズム

#### 手順 1. 入次数（いりじすう）に着目する
- ある頂点に入ってくる辺の本数を入次数（in-degree）と呼ぶ
- 入次数が 0 の頂点は、それより前に来なければならない頂点が存在しないため、トポロジカル順序の先頭に置くことができる
- まず、入次数が 0 の頂点をすべてキューに追加する

#### 手順 2. 入次数が 0 の頂点から探索する
- キューから頂点を 1 つ取り出して、トポロジカル順序（`result`）に追加する
- その頂点から出ている辺をすべて「削除」する
	- 実際には辺を物理的に削除するのではなく、辺の先の頂点の入次数を 1 減らすことで表現する
- これによって新しく入次数が 0 になった頂点があれば、キューに追加する

#### 手順 3. キューが空になるまで繰り返す
- キューが空になるまで、手順 2 を繰り返す
- 結果として、すべての頂点が `result` に追加される

### 1.3 計算量
- 計算量は $O(V + E)$（V: 頂点数、E: 辺数）
- どの頂点と辺も 1 回ずつしか処理しないため

### 1.4 トポロジカル順序の一意性
- トポロジカル順序は一般に一意ではなく、複数の有効な順序が存在する
- アルゴリズムの実行中に、キューのサイズが途中で 2 以上になった場合、その時点で「次に処理できる頂点」が複数存在することを意味し、順序に自由度が生じる
- 逆に、**キューのサイズが常に 1 以下であれば、トポロジカル順序は一意**に定まる

### 1.5 閉路を検出する
- Kahn のアルゴリズムは、グラフに閉路があっても途中までは動作するが、閉路が存在する場合、すべての頂点をトポロジカル順序に追加する前にキューが枯渇する
- したがって、**`result` のサイズがグラフの頂点数 `V` よりも少ない**場合、閉路があったと判断できる

## 2. 実装

### 2.1 Kahn のアルゴリズム

https://www.youtube.com/watch?v=C6v9E1-Ey3o

```cpp
#include <vector>
#include <queue>

/// @brief Kahn のアルゴリズムでトポロジカルソートを行い、トポロジカル順序を返します。
/// @param graph 隣接リスト表現のグラフ
/// @return トポロジカル順序。閉路がある場合は空の配列
/// @note 出典:『競プロのための標準 C++』
std::vector<int> TopologicalSort(const std::vector<std::vector<int>>& graph)
{
	// 各頂点の入次数を管理する配列
	std::vector<int> indegrees(graph.size());

	// 入次数を計算する
	for (const auto& v : graph)
	{
		for (const auto& to : v)
		{
			++indegrees[to];
		}
	}

	// 入次数が 0 の頂点を管理するキュー
	std::queue<int> q;

	// 現時点で入次数が 0 の頂点をすべてキューに追加する
	for (int i = 0; i < static_cast<int>(graph.size()); ++i)
	{
		if (indegrees[i] == 0)
		{
			q.push(i);
		}
	}

	// 結果（トポロジカル順序）を格納する配列
	std::vector<int> result;

	while (!q.empty())
	{
		// 入次数が 0 の頂点を 1 つ取り出す
		const int from = q.front(); q.pop();

		// トポロジカル順序に追加する
		result.push_back(from);

		// その頂点から出る各辺について
		for (const auto& to : graph[from])
		{
			// その先の頂点の入次数を減らし、新たに 0 になったらキューに追加する
			if (--indegrees[to] == 0)
			{
				q.push(to);
			}
		}
	}

	// 閉路が存在する（頂点をすべて処理できていない）場合
	if (result.size() < graph.size())
	{
		// 空の配列を返す
		return{};
	}

	return result;
}
```

### 2.2 一意なトポロジカル順序であるかの判定
- キューのサイズが 2 以上になった瞬間があれば、次に処理可能な頂点が複数存在したことを意味し、結果として複数のトポロジカル順序が成立する
- 次のようにキューのサイズをチェックすることで判定する

```cpp
while (!q.empty())
{
	// キューに 2 つ以上要素がある場合、順序が一意にはならない
	if (1 < q.size())
	{
		// トポロジカル順序は一意ではない
	}

	// 入次数が 0 の頂点を取り出す
	const int from = q.front(); q.pop();

	// ...
}
```

### 2.3 辞書順最小のトポロジカル順序
- キューとして `std::priority_queue<int, std::vector<int>, std::greater<>>` を使用すると、頂点番号が小さいものから優先的に取り出すことができ、**辞書順最小**のトポロジカル順序を求めることができる

```cpp
#include <vector>
#include <queue>
#include <functional>

/// @brief Kahn のアルゴリズムでトポロジカルソートを行い、辞書順最小のトポロジカル順序を返します。
/// @param graph 隣接リスト表現のグラフ
/// @return 辞書順最小のトポロジカル順序。閉路がある場合は空の配列
/// @note 出典:『競プロのための標準 C++』
std::vector<int> TopologicalSort(const std::vector<std::vector<int>>& graph)
{
	// 各頂点の入次数を管理する配列
	std::vector<int> indegrees(graph.size());

	// 入次数を計算する
	for (const auto& v : graph)
	{
		for (const auto& to : v)
		{
			++indegrees[to];
		}
	}

	// 入次数が 0 の頂点を管理するキュー（辞書順最小の結果を得るため、優先度付きキューを使用する）
	std::priority_queue<int, std::vector<int>, std::greater<>> q;

	// 現時点で入次数が 0 の頂点をすべてキューに追加する
	for (int i = 0; i < static_cast<int>(graph.size()); ++i)
	{
		if (indegrees[i] == 0)
		{
			q.push(i);
		}
	}

	// 結果（トポロジカル順序）を格納する配列
	std::vector<int> result;

	while (!q.empty())
	{
		// 入次数が 0 の頂点のうち、頂点番号が小さいものから取り出す
		const int from = q.top(); q.pop();

		// トポロジカル順序に追加する
		result.push_back(from);

		// その頂点から出る各辺について
		for (const auto& to : graph[from])
		{
			// その先の頂点の入次数を減らし、新たに 0 になったらキューに追加する
			if (--indegrees[to] == 0)
			{
				q.push(to);
			}
		}
	}

	// 閉路が存在する（頂点をすべて処理できていない）場合
	if (result.size() < graph.size())
	{
		// 空の配列を返す
		return{};
	}

	return result;
}
```


## 3. 練習問題

### [✅ AOJ GRL_4_B - Topological Sort](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=GRL_4_B&lang=ja)

:::details コード
```cpp
#include <iostream>
#include <vector>
#include <queue>

/// @brief Kahn のアルゴリズムでトポロジカルソートを行い、トポロジカル順序を返します。
/// @param graph 隣接リスト表現のグラフ
/// @return トポロジカル順序。閉路がある場合は空の配列
/// @note 出典:『競プロのための標準 C++』
std::vector<int> TopologicalSort(const std::vector<std::vector<int>>& graph)
{
	// 各頂点の入次数を管理する配列
	std::vector<int> indegrees(graph.size());

	// 入次数を計算する
	for (const auto& v : graph)
	{
		for (const auto& to : v)
		{
			++indegrees[to];
		}
	}

	// 入次数が 0 の頂点を管理するキュー
	std::queue<int> q;

	// 現時点で入次数が 0 の頂点をすべてキューに追加する
	for (int i = 0; i < static_cast<int>(graph.size()); ++i)
	{
		if (indegrees[i] == 0)
		{
			q.push(i);
		}
	}

	// 結果（トポロジカル順序）を格納する配列
	std::vector<int> result;

	while (!q.empty())
	{
		// 入次数が 0 の頂点を 1 つ取り出す
		const int from = q.front(); q.pop();

		// トポロジカル順序に追加する
		result.push_back(from);

		// その頂点から出る各辺について
		for (const auto& to : graph[from])
		{
			// その先の頂点の入次数を減らし、新たに 0 になったらキューに追加する
			if (--indegrees[to] == 0)
			{
				q.push(to);
			}
		}
	}

	// 閉路が存在する（頂点をすべて処理できていない）場合
	if (result.size() < graph.size())
	{
		// 空の配列を返す
		return{};
	}

	return result;
}

int main()
{
	// 頂点数 V, 辺数 E
	int V, E;
	std::cin >> V >> E;

	// 隣接リスト表現のグラフを構築する
	std::vector<std::vector<int>> graph(V);
	for (int i = 0; i < E; ++i)
	{
		int s, t;
		std::cin >> s >> t;
		graph[s].push_back(t);
	}

	// トポロジカル順序
	const std::vector<int> result = TopologicalSort(graph);

	for (const auto& v : result)
	{
		std::cout << v << '\n';
	}
}
```
:::


### [✅ JOI 本選 2007 D - 最悪の記者](https://atcoder.jp/contests/joi2007ho/tasks/joi2007ho_d)
- 一意なトポロジカル順序であるかの判定を行う問題

:::details コード
```cpp
#include <iostream>
#include <vector>
#include <queue>

std::vector<int> TopologicalSort(const std::vector<std::vector<int>>& graph, bool& isUnique)
{
	// 各頂点の入次数を管理する配列
	std::vector<int> indegrees(graph.size());

	// 入次数を計算する
	for (const auto& v : graph)
	{
		for (const auto& to : v)
		{
			++indegrees[to];
		}
	}

	// 入次数が 0 の頂点を管理するキュー
	std::queue<int> q;

	// 現時点で入次数が 0 の頂点をすべてキューに追加する
	for (int i = 0; i < static_cast<int>(graph.size()); ++i)
	{
		if (indegrees[i] == 0)
		{
			q.push(i);
		}
	}

	// 結果（トポロジカル順序）を格納する配列
	std::vector<int> result;

	isUnique = true;

	while (!q.empty())
	{
		// キューに 2 つ以上要素がある場合、順序が一意にはならない
		if (1 < q.size())
		{
			isUnique = false;
		}

		// 入次数が 0 の頂点を 1 つ取り出す
		const int from = q.front(); q.pop();

		// トポロジカル順序に追加する
		result.push_back(from);

		// その頂点から出る各辺について
		for (const auto& to : graph[from])
		{
			// その先の頂点の入次数を減らし、新たに 0 になったらキューに追加する
			if (--indegrees[to] == 0)
			{
				q.push(to);
			}
		}
	}

	// 閉路が存在する（頂点をすべて処理できていない）場合
	if (result.size() < graph.size())
	{
		// 空の配列を返す
		return{};
	}

	return result;
}

int main()
{
	// N 個のチーム, M 個の対戦結果
	int N, M;
	std::cin >> N >> M;

	// 隣接リスト表現のグラフを構築する
	// s が t に勝ったなら、s から t に辺を張る
	std::vector<std::vector<int>> graph(N);
	for (int i = 0; i < M; ++i)
	{
		int s, t;
		std::cin >> s >> t;
		--s; --t;
		graph[s].push_back(t);
	}

	// トポロジカル順序が一意かどうか
	bool isUnique = true;

	// トポロジカル順序
	const std::vector<int> result = TopologicalSort(graph, isUnique);

	for (const auto& v : result)
	{
		std::cout << (v + 1) << '\n';
	}

	// トポロジカル順序が一意かどうかを出力する
	if (isUnique)
	{
		std::cout << "0\n";
	}
	else
	{
		std::cout << "1\n";
	}
}
```
:::


### [✅ ABC223D - Restricted Permutation]()
- 辞書順最小のトポロジカル順序を求める問題

:::details コード
```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <functional>

/// @brief Kahn のアルゴリズムでトポロジカルソートを行い、辞書順最小のトポロジカル順序を返します。
/// @param graph 隣接リスト表現のグラフ
/// @return 辞書順最小のトポロジカル順序。閉路がある場合は空の配列
/// @note 出典:『競プロのための標準 C++』
std::vector<int> TopologicalSort(const std::vector<std::vector<int>>& graph)
{
	// 各頂点の入次数を管理する配列
	std::vector<int> indegrees(graph.size());

	// 入次数を計算する
	for (const auto& v : graph)
	{
		for (const auto& to : v)
		{
			++indegrees[to];
		}
	}

	// 入次数が 0 の頂点を管理するキュー（辞書順最小の結果を得るため、優先度付きキューを使用する）
	std::priority_queue<int, std::vector<int>, std::greater<>> q;

	// 現時点で入次数が 0 の頂点をすべてキューに追加する
	for (int i = 0; i < static_cast<int>(graph.size()); ++i)
	{
		if (indegrees[i] == 0)
		{
			q.push(i);
		}
	}

	// 結果（トポロジカル順序）を格納する配列
	std::vector<int> result;

	while (!q.empty())
	{
		// 入次数が 0 の頂点のうち、頂点番号が小さいものから取り出す
		const int from = q.top(); q.pop();

		// トポロジカル順序に追加する
		result.push_back(from);

		// その頂点から出る各辺について
		for (const auto& to : graph[from])
		{
			// その先の頂点の入次数を減らし、新たに 0 になったらキューに追加する
			if (--indegrees[to] == 0)
			{
				q.push(to);
			}
		}
	}

	// 閉路が存在する（頂点をすべて処理できていない）場合
	if (result.size() < graph.size())
	{
		// 空の配列を返す
		return{};
	}

	return result;
}

int main()
{
	int N, M;
	std::cin >> N >> M;

	// 隣接リスト表現のグラフを構築する
	std::vector<std::vector<int>> graph(N);
	for (int i = 0; i < M; ++i)
	{
		int A, B;
		std::cin >> A >> B;
		--A; --B;
		graph[A].push_back(B);
	}

	// 辞書順最小のトポロジカル順序
	const std::vector<int> result = TopologicalSort(graph);

	// 閉路が存在する場合
	if (result.empty())
	{
		std::cout << "-1\n";
		return 0;
	}

	for (const auto& v : result)
	{
		std::cout << (v + 1) << ' ';
	}

	std::cout << '\n';
}
```
:::

