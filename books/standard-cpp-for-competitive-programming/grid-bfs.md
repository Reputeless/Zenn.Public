---
title: "グリッド上の幅優先探索"
free: true
---

## 1. 概要
- グリッド（二次元の格子状の地図）上での最短経路問題は、幅優先探索（BFS）を使用することで効率的に解くことができる
- 幅優先探索は、始点から各マスまでの最短距離を段階的に計算していく探索アルゴリズム

### 1.1 データ構造
```cpp
struct Point { int x, y; };
std::vector<std::vector<int>> distances(H, std::vector<int>(W, -1));
std::queue<Point> q;
```
- `Point` クラスで二次元座標を表現
- `distances` 配列で各マスまでの距離を管理（-1は未訪問）
- `queue` で幅優先探索の処理順序を制御

### 1.2 移動方向の定義
```cpp
constexpr Point Offsets[] = { { 0, -1 }, { 0, 1 }, { -1, 0 }, { 1, 0 } };
```
- 上下左右の4方向への移動を配列で定義
- 各要素は座標のオフセット値を表す

### 1.3 幅優先探索のコアとなるロジック
- スタート地点を距離 0 で初期化し、キューに追加する
- キューが空になるまで以下を繰り返す：
	1. キューから座標を取り出す
	2. その座標から移動可能な隣接マスを全て調べる
	3. 未訪問の有効なマスについて、距離を記録してキューに追加する

### 1.4 探索の制約条件
```cpp
if ((nx < 0) || (W <= nx) || (ny < 0) || (H <= ny)) continue; // 範囲チェック
if (grid[ny][nx] == '#') continue; // 壁チェック
if (distances[ny][nx] != -1) continue; // 訪問済みチェック
```
- グリッドの範囲外へのアクセスを防ぐ
- 壁のマスへの移動を禁止
- 既に訪問したマスの再訪問を防ぐ

### 1.5 実装の特徴
- メモリ効率が良い（訪問済みの管理と距離の記録を一つの配列で行う）
- 幅優先探索の性質として、必ず最短距離を求められる
- 探索の計算量は $O(H×W)$, これは各マスを最大 1 回しか訪問しないため

## 2. 実装

### 2.1 スタート地点から各マスまでの最短距離を求める幅優先探索

```cpp
#include <vector>
#include <string>
#include <queue>

/// @brief 二次元座標
struct Point
{
	int x, y;
};

/// @brief グリッド上の幅優先探索を行い、スタート地点から各マスまでの最短距離を求めます。
/// @param grid グリッド（'#' は壁、それ以外は通路）
/// @param start スタート地点の座標
/// @return スタート地点から各マスまでの最短距離を記録した二次元配列（-1 は未訪問）
/// @note 出典:『競プロのための標準 C++』
[[nodiscard]]
std::vector<std::vector<int>> GridBFS(const std::vector<std::string>& grid, const Point& start)
{
	// グリッドの行数 (高さ)
	const int H = static_cast<int>(grid.size());

	// グリッドの列数 (幅)
	const int W = static_cast<int>(grid[0].size());

	// 各マスまでの最短距離（-1 は未訪問）
	std::vector<std::vector<int>> distances(H, std::vector<int>(W, -1));
	
	// スタート地点の距離は 0 とする
	distances[start.y][start.x] = 0;

	// 幅優先探索のキュー
	std::queue<Point> q;

	// スタート地点をキューに追加する
	q.push(start);

	// 上下左右のマスへのオフセット
	constexpr Point Offsets[] = { { 0, -1 }, { 0, 1 }, { -1, 0 }, { 1, 0 } };

	while (!q.empty())
	{
		// キューの先頭のマスの座標（現在地）
		const Point current = q.front(); q.pop();
		
		// 上下左右の各マスを調べる
		for (const auto& offset : Offsets)
		{
			// 新たなマスの座標
			const int nx = (current.x + offset.x);
			const int ny = (current.y + offset.y);

			// 範囲外の場合はスキップする
			if ((nx < 0) || (W <= nx) || (ny < 0) || (H <= ny))
			{
				continue;
			}

			// 壁の場合はスキップする
			if (grid[ny][nx] == '#')
			{
				continue;
			}

			// すでに訪れている場合はスキップする
			if (distances[ny][nx] != -1)
			{
				continue;
			}

			// 新たなマスまでの距離を記録する
			distances[ny][nx] = (distances[current.y][current.x] + 1);

			// 新たなマスをキューに追加する
			q.emplace(nx, ny);
		}
	}

	return distances;
}
```

### 2.2 複数のスタート地点からの幅優先探索
- 複数のスタート地点から同時に探索を開始する幅優先探索も可能
- 以下のような状況で特に有用:
	- 任意のスタート地点から最も近い目的地を見つける
	- 複数の障害物からの最短距離を求める
	- 複数の供給源からの最短経路を計算する

#### 実装
```cpp
// 複数のスタート地点をキューに追加
for (const auto& start : startPoints)
{
    distances[start.y][start.x] = 0;
    q.push(start);
}
```
- 初期化時に全てのスタート地点を距離 0 でキューに追加する
- 通常の BFS と同じロジックで探索を進める
- 各マスの距離は、いずれかのスタート地点からの最短距離を表す
- 計算量は通常の BFS と同じく $O(H×W)$

## 3. 練習問題

### [✅ ABC007C - 幅優先探索](https://atcoder.jp/contests/abc007/tasks/abc007_3)

:::details コード
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <queue>

/// @brief 二次元座標
struct Point
{
	int x, y;
};

/// @brief グリッド上の幅優先探索を行い、スタート地点から各マスまでの最短距離を求めます。
/// @param grid グリッド（'#' は壁、それ以外は通路）
/// @param start スタート地点の座標
/// @return スタート地点から各マスまでの最短距離を記録した二次元配列（-1 は未訪問）
/// @note 出典:『競プロのための標準 C++』
[[nodiscard]]
std::vector<std::vector<int>> GridBFS(const std::vector<std::string>& grid, const Point& start)
{
	// グリッドの行数 (高さ)
	const int H = static_cast<int>(grid.size());

	// グリッドの列数 (幅)
	const int W = static_cast<int>(grid[0].size());

	// 各マスまでの最短距離（-1 は未訪問）
	std::vector<std::vector<int>> distances(H, std::vector<int>(W, -1));

	// スタート地点の距離は 0 とする
	distances[start.y][start.x] = 0;

	// 幅優先探索のキュー
	std::queue<Point> q;

	// スタート地点をキューに追加する
	q.push(start);

	// 上下左右のマスへのオフセット
	constexpr Point Offsets[] = { { 0, -1 }, { 0, 1 }, { -1, 0 }, { 1, 0 } };

	while (!q.empty())
	{
		// キューの先頭のマスの座標（現在地）
		const Point current = q.front(); q.pop();

		// 上下左右の各マスを調べる
		for (const auto& offset : Offsets)
		{
			// 新たなマスの座標
			const int nx = (current.x + offset.x);
			const int ny = (current.y + offset.y);

			// 範囲外の場合はスキップする
			if ((nx < 0) || (W <= nx) || (ny < 0) || (H <= ny))
			{
				continue;
			}

			// 壁の場合はスキップする
			if (grid[ny][nx] == '#')
			{
				continue;
			}

			// すでに訪れている場合はスキップする
			if (distances[ny][nx] != -1)
			{
				continue;
			}

			// 新たなマスまでの距離を記録する
			distances[ny][nx] = (distances[current.y][current.x] + 1);

			// 新たなマスをキューに追加する
			q.emplace(nx, ny);
		}
	}

	return distances;
}

int main()
{
	// 行数（高さ）と列数（幅）
	int R, C;
	std::cin >> R >> C;

	// スタート地点とゴールの座標
	int sy, sx, gy, gx;
	std::cin >> sy >> sx >> gy >> gx;
	--sy, --sx, --gy, --gx;

	// 迷路の情報を読み込む（'#' は壁, '.' は通路）
	std::vector<std::string> grid(R);
	for (auto& line : grid)
	{
		std::cin >> line;
	}

	// スタート地点の座標
	const Point start{ sx, sy };

	// スタート地点から各マスまでの最短距離を求める
	const auto distances = GridBFS(grid, start);

	// ゴールまでの最短距離を出力する
	std::cout << distances[gy][gx] << '\n';
}
```
:::


### [✅ ABC088D - Grid Repainting](https://atcoder.jp/contests/abc088/tasks/abc088_d)

:::details コード
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <queue>
#include <algorithm>

/// @brief 二次元座標
struct Point
{
	int x, y;
};

/// @brief グリッド上の幅優先探索を行い、スタート地点から各マスまでの最短距離を求めます。
/// @param grid グリッド（'#' は壁、それ以外は通路）
/// @param start スタート地点の座標
/// @return スタート地点から各マスまでの最短距離を記録した二次元配列（-1 は未訪問）
/// @note 出典:『競プロのための標準 C++』
[[nodiscard]]
std::vector<std::vector<int>> GridBFS(const std::vector<std::string>& grid, const Point& start)
{
	// グリッドの行数 (高さ)
	const int H = static_cast<int>(grid.size());

	// グリッドの列数 (幅)
	const int W = static_cast<int>(grid[0].size());

	// 各マスまでの最短距離（-1 は未訪問）
	std::vector<std::vector<int>> distances(H, std::vector<int>(W, -1));

	// スタート地点の距離は 0 とする
	distances[start.y][start.x] = 0;

	// 幅優先探索のキュー
	std::queue<Point> q;

	// スタート地点をキューに追加する
	q.push(start);

	// 上下左右のマスへのオフセット
	constexpr Point Offsets[] = { { 0, -1 }, { 0, 1 }, { -1, 0 }, { 1, 0 } };

	while (!q.empty())
	{
		// キューの先頭のマスの座標（現在地）
		const Point current = q.front(); q.pop();

		// 上下左右の各マスを調べる
		for (const auto& offset : Offsets)
		{
			// 新たなマスの座標
			const int nx = (current.x + offset.x);
			const int ny = (current.y + offset.y);

			// 範囲外の場合はスキップする
			if ((nx < 0) || (W <= nx) || (ny < 0) || (H <= ny))
			{
				continue;
			}

			// 壁の場合はスキップする
			if (grid[ny][nx] == '#')
			{
				continue;
			}

			// すでに訪れている場合はスキップする
			if (distances[ny][nx] != -1)
			{
				continue;
			}

			// 新たなマスまでの距離を記録する
			distances[ny][nx] = (distances[current.y][current.x] + 1);

			// 新たなマスをキューに追加する
			q.emplace(nx, ny);
		}
	}

	return distances;
}

int main()
{
	// 行数（高さ）と列数（幅）
	int H, W;
	std::cin >> H >> W;

	// 迷路の情報を読み込む（'#' は壁, '.' は通路）
	std::vector<std::string> grid(H);
	for (auto& line : grid)
	{
		std::cin >> line;
	}

	// スタート地点の座標
	const Point start{ 0, 0 };

	// スタート地点から各マスまでの最短距離を求める
	const auto distances = GridBFS(grid, start);

	// ゴールまでの最短距離
	const int distanceToGoal = distances[H - 1][W - 1];

	// ゴールまで到達できない場合は -1 を出力する
	if (distanceToGoal == -1)
	{
		std::cout << "-1\n";
		return 0;
	}

	// 白マスの個数
	long long whiteCount = 0;

	// 各行について
	for (const auto& line : grid)
	{
		// '.' の個数を数える
		whiteCount += std::ranges::count(line, '.');
	}

	// 移動経路を除いた白マスの個数を出力する
	std::cout << (whiteCount - (distanceToGoal + 1)) << '\n';
}
```
:::


### [✅ AGC033A - Darker and Darker](https://atcoder.jp/contests/agc033/tasks/agc033_a)
- 複数のスタート地点からの幅優先探索を使用する問題

:::details コード
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <queue>
#include <algorithm>

/// @brief 二次元座標
struct Point
{
	int x, y;
};

// 複数のスタート地点からの最短距離を幅優先探索で求める
[[nodiscard]]
std::vector<std::vector<int>> GridBFS(int H, int W, const std::vector<Point>& startPoints)
{
	// 各マスまでの最短距離（-1 は未訪問）
	std::vector<std::vector<int>> distances(H, std::vector<int>(W, -1));

	// 幅優先探索のキュー
	std::queue<Point> q;

	// すべてのスタート地点をキューに追加する
	for (const auto& start : startPoints)
	{
		distances[start.y][start.x] = 0;
		q.push(start);
	}

	// 上下左右のマスへのオフセット
	constexpr Point Offsets[] = { { 0, -1 }, { 0, 1 }, { -1, 0 }, { 1, 0 } };

	while (!q.empty())
	{
		// キューの先頭のマスの座標（現在地）
		const Point current = q.front(); q.pop();

		// 上下左右の各マスを調べる
		for (const auto& offset : Offsets)
		{
			// 新たなマスの座標
			const int nx = (current.x + offset.x);
			const int ny = (current.y + offset.y);

			// 範囲外の場合はスキップする
			if ((nx < 0) || (W <= nx) || (ny < 0) || (H <= ny))
			{
				continue;
			}

			// すでに訪れている場合はスキップする
			if (distances[ny][nx] != -1)
			{
				continue;
			}

			// 新たなマスまでの距離を記録する
			distances[ny][nx] = (distances[current.y][current.x] + 1);

			// 新たなマスをキューに追加する
			q.emplace(nx, ny);
		}
	}

	return distances;
}

int main()
{
	// 行数（高さ）と列数（幅）
	int H, W;
	std::cin >> H >> W;

	// グリッドの情報を読み込む（'#' は黒, '.' は白）
	std::vector<std::string> grid(H);
	for (auto& line : grid)
	{
		std::cin >> line;
	}

	// スタート地点（黒マス）の座標を取得する
	std::vector<Point> startPoints;
	for (int y = 0; y < H; ++y)
	{
		for (int x = 0; x < W; ++x)
		{
			if (grid[y][x] == '#')
			{
				startPoints.push_back({ x, y });
			}
		}
	}

	// 各マスまでの最短距離を求める
	const auto distances = GridBFS(H, W, startPoints);

	// 最短距離の最大値を求める
	int maxDistance = 0;

	for (const auto& row : distances)
	{
		maxDistance = std::max(maxDistance, std::ranges::max(row));
	}

	std::cout << maxDistance << '\n';
}
```
:::


### [✅ 九州大学プログラミングコンテスト 2018 C - Ito Campus](https://atcoder.jp/contests/qupc2018/tasks/qupc2018_c)
- 複数のスタート地点からの幅優先探索を使用する問題

:::details コード
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <queue>
#include <algorithm>

/// @brief 二次元座標
struct Point
{
	int x, y;
};

/// @brief グリッド上の幅優先探索を行い、スタート地点から各マスまでの最短距離を求めます。
/// @param grid グリッド（'#' は壁、それ以外は通路）
/// @param start スタート地点の座標
/// @return スタート地点から各マスまでの最短距離を記録した二次元配列（-1 は未訪問）
/// @note 出典:『競プロのための標準 C++』
[[nodiscard]]
std::vector<std::vector<int>> GridBFS(const std::vector<std::string>& grid, const Point& start)
{
	// グリッドの行数 (高さ)
	const int H = static_cast<int>(grid.size());

	// グリッドの列数 (幅)
	const int W = static_cast<int>(grid[0].size());

	// 各マスまでの最短距離（-1 は未訪問）
	std::vector<std::vector<int>> distances(H, std::vector<int>(W, -1));

	// スタート地点の距離は 0 とする
	distances[start.y][start.x] = 0;

	// 幅優先探索のキュー
	std::queue<Point> q;

	// スタート地点をキューに追加する
	q.push(start);

	// 上下左右のマスへのオフセット
	constexpr Point Offsets[] = { { 0, -1 }, { 0, 1 }, { -1, 0 }, { 1, 0 } };

	while (!q.empty())
	{
		// キューの先頭のマスの座標（現在地）
		const Point current = q.front(); q.pop();

		// 上下左右の各マスを調べる
		for (const auto& offset : Offsets)
		{
			// 新たなマスの座標
			const int nx = (current.x + offset.x);
			const int ny = (current.y + offset.y);

			// 範囲外の場合はスキップする
			if ((nx < 0) || (W <= nx) || (ny < 0) || (H <= ny))
			{
				continue;
			}

			// 壁の場合はスキップする
			if (grid[ny][nx] == '#')
			{
				continue;
			}

			// すでに訪れている場合はスキップする
			if (distances[ny][nx] != -1)
			{
				continue;
			}

			// 新たなマスまでの距離を記録する
			distances[ny][nx] = (distances[current.y][current.x] + 1);

			// 新たなマスをキューに追加する
			q.emplace(nx, ny);
		}
	}

	return distances;
}

// マップの情報
struct MapInfo
{
	// スタート地点の座標
	Point start;

	// ゴールの座標
	Point goal;

	// イノシシの座標
	std::vector<Point> boars;
};

// マップの情報を取得する関数
MapInfo GetMapInfo(const std::vector<std::string>& grid)
{
	MapInfo info;

	// グリッドの行数 (高さ)
	const int H = static_cast<int>(grid.size());

	// グリッドの列数 (幅)
	const int W = static_cast<int>(grid[0].size());
	
	for (int y = 0; y < H; ++y)
	{
		for (int x = 0; x < W; ++x)
		{
			// (x, y) のマスの文字
			const char c = grid[y][x];
			
			if (c == 'S') // スタート地点
			{
				info.start = { x, y };
			}
			else if (c == 'G') // ゴール
			{
				info.goal = { x, y };
			}
			else if (c == '@') // イノシシ
			{
				info.boars.emplace_back(x, y);
			}
		}
	}

	return info;
}

// イノシシが影響するマスを幅優先探索で調べる関数
// X はイノシシが影響する距離
[[nodiscard]]
std::vector<std::vector<int>> BoarGridBFS(const std::vector<std::string>& grid, const MapInfo& info, int X)
{
	// グリッドの行数 (高さ)
	const int H = static_cast<int>(grid.size());
	
	// グリッドの列数 (幅)
	const int W = static_cast<int>(grid[0].size());
	
	// 任意のイノシシから各マスまでの最短距離（-1 は未訪問）
	std::vector<std::vector<int>> boarDistances(H, std::vector<int>(W, -1));

	// 幅優先探索のキュー
	std::queue<Point> q;

	// すべてのイノシシをキューに追加する
	for (const auto& boar : info.boars)
	{
		boarDistances[boar.y][boar.x] = 0;
		q.push(boar);
	}

	// 上下左右のマスへのオフセット
	constexpr Point Offsets[] = { { 0, -1 }, { 0, 1 }, { -1, 0 }, { 1, 0 } };

	while (!q.empty())
	{
		// キューの先頭のマスの座標（現在地）
		const Point current = q.front(); q.pop();

		// これ以上探索するとイノシシの影響範囲から外れる場合はスキップする
		if (X <= boarDistances[current.y][current.x])
		{
			continue;
		}

		// 上下左右の各マスを調べる
		for (const auto& offset : Offsets)
		{
			// 新たなマスの座標
			const int nx = (current.x + offset.x);
			const int ny = (current.y + offset.y);

			// 範囲外の場合はスキップする
			if ((nx < 0) || (W <= nx) || (ny < 0) || (H <= ny))
			{
				continue;
			}

			// 壁の場合はスキップする
			if (grid[ny][nx] == '#')
			{
				continue;
			}

			// すでに訪れている場合はスキップする
			// （訪問済みのマス距離は, 現在の距離以下なのでスキップして問題ない）
			if (boarDistances[ny][nx] != -1)
			{
				continue;
			}

			// 新たなマスまでの距離を記録する
			boarDistances[ny][nx] = (boarDistances[current.y][current.x] + 1);
			
			// 新たなマスをキューに追加する
			q.emplace(nx, ny);
		}
	}

	return boarDistances;
}

int main()
{
	// 行数（高さ）, 列数（幅）, イノシシが影響する距離
	int H, W, X;
	std::cin >> H >> W >> X;

	// 迷路の情報を読み込む（'#' は壁, '.' は通路）
	std::vector<std::string> grid(H);
	for (auto& line : grid)
	{
		std::cin >> line;
	}

	// マップの情報を取得する
	const MapInfo info = GetMapInfo(grid);

	// イノシシが影響するマスを幅優先探索で調べる
	const auto boarDistances = BoarGridBFS(grid, info, X);

	// イノシシが影響するマスを壁に変更して, 通行不可にする
	for (int y = 0; y < H; ++y)
	{
		for (int x = 0; x < W; ++x)
		{
			// イノシシが影響するマスの場合
			if (boarDistances[y][x] != -1)
			{
				// 壁に変更する
				grid[y][x] = '#';
			}
		}
	}

	// スタート地点から各マスまでの最短距離を求める
	const auto distances = GridBFS(grid, info.start);

	// ゴール地点までの最短距離を出力する（到達不可能な場合は -1）
	std::cout << distances[info.goal.y][info.goal.x] << '\n';
}
```
:::
