---
title: "トポロジカルソート"
free: true
---

C++ 標準ライブラリを用いた、**トポロジカルソート** (Topological Sort) の実装です。


# 1. トポロジカルソートのテンプレート

| 機能 | 1.1 | 1.2 | 1.3 |
|----|:----:|:----:|:----:|
| 深さ優先探索を用いたトポロジカルソート | ✅ |  |  |
| 幅優先探索を用いたトポロジカルソート |   | ✅ | ✅ |
| 閉路を検出する |  | ✅ | ✅ |
| 辞書順最小のトポロジカルソート結果を得る |  |   | ✅ |


## 1.1 深さ優先探索を用いたトポロジカルソート
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // std::reverse()

/// @brief 深さ優先探索を行い, 帰りがけ順の頂点列を得る
/// @param graph グラフ
/// @remark グラフは有向非巡回グラフ (DAG) でなければならない
/// @param from 注目している頂点
/// @param visited 訪問済みの頂点を記録する配列
/// @param result 帰りがけ順の頂点列を格納する配列
void DFS(const std::vector<std::vector<int>>& graph, int from, std::vector<bool>& visited, std::vector<int>& result)
{
	visited[from] = true;

	for (const auto& to : graph[from])
	{
		if (!visited[to])
		{
			DFS(graph, to, visited, result);
		}
	}

	// 帰りがけ順
	result.push_back(from);
}

/// @brief トポロジカルソート
/// @param graph グラフ
/// @remark グラフは有向非巡回グラフ (DAG) でなければならない
/// @return トポロジカルソートの結果
/// @note 1.1 深さ優先探索を用いたトポロジカルソート
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/topological-sort
std::vector<int> TopologicalSort(const std::vector<std::vector<int>>& graph)
{
	std::vector<int> result;

	std::vector<bool> visited(graph.size());

	for (int i = 0; i < static_cast<int>(graph.size()); ++i)
	{
		if (!visited[i])
		{
			DFS(graph, i, visited, result);
		}
	}

	std::reverse(result.begin(), result.end());

	return result;
}

int main()
{
	int V, E;
	std::cin >> V >> E;

	std::vector<std::vector<int>> graph(V);

	for (int i = 0; i < E; ++i)
	{
		//int s, t;
		//std::cin >> s >> t;
		//graph[s].push_back(t);
	}

	const std::vector<int> result = TopologicalSort(graph);

	for (const auto& v : result)
	{
		std::cout << v << '\n';
	}
}
```

- [AOJ GRL_4_B - Topological Sort](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=7366550#2)
:::


## 1.2 幅優先探索を用いたトポロジカルソート
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <queue> // std::queue

/// @brief トポロジカルソート
/// @param graph グラフ
/// @return トポロジカルソートの結果。グラフが閉路を含む場合は空の配列
/// @note 1.2 幅優先探索を用いたトポロジカルソート
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/topological-sort
std::vector<int> TopologicalSort(const std::vector<std::vector<int>>& graph)
{
	std::vector<int> indegrees(graph.size());

	for (const auto& v : graph)
	{
		for (const auto& to : v)
		{
			++indegrees[to];
		}
	}

	std::queue<int> q;

	for (int i = 0; i < (int)graph.size(); ++i)
	{
		if (indegrees[i] == 0)
		{
			q.push(i);
		}
	}

	std::vector<int> result;

	while (!q.empty())
	{
		const int from = q.front(); q.pop();

		result.push_back(from);

		for (const auto& to : graph[from])
		{
			if (--indegrees[to] == 0)
			{
				q.push(to);
			}
		}
	}

	if (result.size() != graph.size())
	{
		return{};
	}

	return result;
}

int main()
{
	int V, E;
	std::cin >> V >> E;

	std::vector<std::vector<int>> graph(V);

	for (int i = 0; i < E; ++i)
	{
		//int s, t;
		//std::cin >> s >> t;
		//graph[s].push_back(t);
	}

	const std::vector<int> result = TopologicalSort(graph);

	for (const auto& v : result)
	{
		std::cout << v << '\n';
	}
}
```

- [AOJ GRL_4_B - Topological Sort](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=7366903#2)
:::


## 1.3 トポロジカルソートの結果で辞書順最小のもの
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <queue> // std::priority_queue
#include <functional> // std::greater

/// @brief トポロジカルソート
/// @param graph グラフ
/// @return トポロジカルソートの結果で辞書順最小のもの。グラフが閉路を含む場合は空の配列
/// @note 1.3 トポロジカルソートの結果で辞書順最小のもの
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/topological-sort
std::vector<int> TopologicalSort(const std::vector<std::vector<int>>& graph)
{
	std::vector<int> indegrees(graph.size());

	for (const auto& v : graph)
	{
		for (const auto& to : v)
		{
			++indegrees[to];
		}
	}

	std::priority_queue<int, std::vector<int>, std::greater<int>> pq;

	for (int i = 0; i < (int)graph.size(); ++i)
	{
		if (indegrees[i] == 0)
		{
			pq.push(i);
		}
	}

	std::vector<int> result;

	while (!pq.empty())
	{
		const int from = pq.top(); pq.pop();

		result.push_back(from);

		for (const auto& to : graph[from])
		{
			if (--indegrees[to] == 0)
			{
				pq.push(to);
			}
		}
	}

	if (result.size() != graph.size())
	{
		return{};
	}

	return result;
}

int main()
{
	int V, E;
	std::cin >> V >> E;

	std::vector<std::vector<int>> graph(V);

	for (int i = 0; i < E; ++i)
	{
		//int s, t;
		//std::cin >> s >> t;
		//graph[s].push_back(t);
	}

	const std::vector<int> result = TopologicalSort(graph);

	for (const auto& v : result)
	{
		std::cout << v << '\n';
	}
}
```

- [AOJ GRL_4_B - Topological Sort](https://judge.u-aizu.ac.jp/onlinejudge/review.jsp?rid=7367006#2)
- [ABC 223 D - Restricted Permutation](https://atcoder.jp/contests/abc223/submissions/38229991)
:::



# 2. トポロジカルソートの例題

### [AOJ GRL_4_B - Topological Sort](https://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=GRL_4_B&lang=ja)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <queue> // std::queue

/// @brief トポロジカルソート
/// @param graph グラフ
/// @return トポロジカルソートの結果。閉路を含む場合は空の配列
/// @note 1.2 幅優先探索によるトポロジカルソート
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/topological-sort
std::vector<int> TopologicalSort(const std::vector<std::vector<int>>& graph)
{
	std::vector<int> indegrees(graph.size());

	for (const auto& v : graph)
	{
		for (const auto& to : v)
		{
			++indegrees[to];
		}
	}

	std::queue<int> q;

	for (int i = 0; i < (int)graph.size(); ++i)
	{
		if (indegrees[i] == 0)
		{
			q.push(i);
		}
	}

	std::vector<int> result;

	while (!q.empty())
	{
		const int from = q.front(); q.pop();

		result.push_back(from);

		for (const auto& to : graph[from])
		{
			if (--indegrees[to] == 0)
			{
				q.push(to);
			}
		}
	}

	if (result.size() != graph.size())
	{
		return{};
	}

	return result;
}

int main()
{
	int V, E;
	std::cin >> V >> E;

	std::vector<std::vector<int>> graph(V);

	for (int i = 0; i < E; ++i)
	{
		int s, t;
		std::cin >> s >> t;
		graph[s].push_back(t);
	}

	const std::vector<int> result = TopologicalSort(graph);

	for (const auto& v : result)
	{
		std::cout << v << '\n';
	}
}
```
:::


### [JOI 本選 2007 D - 最悪の記者](https://atcoder.jp/contests/joi2007ho/tasks/joi2007ho_d)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <queue> // std::queue
#include <set> // std::set
#include <utility> // std::pair

/// @brief トポロジカルソート
/// @param graph グラフ
/// @return トポロジカルソートの結果。閉路を含む場合は空の配列
/// @note 1.2 幅優先探索によるトポロジカルソート
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/topological-sort
std::vector<int> TopologicalSort(const std::vector<std::vector<int>>& graph)
{
	std::vector<int> indegrees(graph.size());

	for (const auto& v : graph)
	{
		for (const auto& to : v)
		{
			++indegrees[to];
		}
	}

	std::queue<int> q;

	for (int i = 0; i < (int)graph.size(); ++i)
	{
		if (indegrees[i] == 0)
		{
			q.push(i);
		}
	}

	std::vector<int> result;

	while (!q.empty())
	{
		const int from = q.front(); q.pop();

		result.push_back(from);

		for (const auto& to : graph[from])
		{
			if (--indegrees[to] == 0)
			{
				q.push(to);
			}
		}
	}

	if (result.size() != graph.size())
	{
		return{};
	}

	return result;
}

int main()
{
	int N, M;
	std::cin >> N >> M;

	std::vector<std::vector<int>> graph(N);

	std::set<std::pair<int, int>> set;

	for (int i = 0; i < M; ++i)
	{
		int s, t;
		std::cin >> s >> t;
		--s; --t;
		graph[s].push_back(t);
		set.emplace(s, t);
	}

	const std::vector<int> result = TopologicalSort(graph);

	for (const auto& v : result)
	{
		std::cout << (v + 1) << '\n';
	}

	// すべての連続する要素が辺でつながっているか調べる
	for (size_t i = 0; i < (result.size() - 1); ++i)
	{
		if (set.count({ result[i], result[i + 1] }) == 0)
		{
			std::cout << "1\n";
			return 0;
		}
	}

	std::cout << "0\n";
}
```
:::


### [ABC 223 D - Restricted Permutation](https://atcoder.jp/contests/abc223/tasks/abc223_d)
:::details コード
```cpp
#include <iostream>
#include <vector>
#include <queue> // std::priority_queue
#include <functional> // std::greater

/// @brief トポロジカルソート
/// @param graph グラフ
/// @return トポロジカルソートの結果で辞書順最小のもの。グラフが閉路を含む場合は空の配列
/// @note 1.3 トポロジカルソートの結果で辞書順最小のものを得る
/// @see https://zenn.dev/reputeless/books/standard-cpp-for-competitive-programming/viewer/topological-sort
std::vector<int> TopologicalSort(const std::vector<std::vector<int>>& graph)
{
	std::vector<int> indegrees(graph.size());

	for (const auto& v : graph)
	{
		for (const auto& to : v)
		{
			++indegrees[to];
		}
	}

	std::priority_queue<int, std::vector<int>, std::greater<int>> pq;

	for (int i = 0; i < (int)graph.size(); ++i)
	{
		if (indegrees[i] == 0)
		{
			pq.push(i);
		}
	}

	std::vector<int> result;

	while (!pq.empty())
	{
		const int from = pq.top(); pq.pop();

		result.push_back(from);

		for (const auto& to : graph[from])
		{
			if (--indegrees[to] == 0)
			{
				pq.push(to);
			}
		}
	}

	if (result.size() != graph.size())
	{
		return{};
	}

	return result;
}

int main()
{
	int N, M;
	std::cin >> N >> M;

	std::vector<std::vector<int>> graph(N);

	for (int i = 0; i < M; ++i)
	{
		int A, B;
		std::cin >> A >> B;
		--A; --B;
		graph[A].push_back(B);
	}

	const std::vector<int> result = TopologicalSort(graph);

	if (result.empty())
	{
		std::cout << "-1\n";
		return 0;
	}

	for (const auto& v : result)
	{
		std::cout << (v + 1) << ' ';
	}
}
```
:::
