---
title: "ABC B 問題 C++ 解法"
free: true
---

- AtCoder Beginner Contest（ABC）B 問題の模範解答
- C++ 標準ライブラリの機能を効果的に活用した、クリーンな C++ コード
- 🟢 マーク → C++20 の機能を使用 / 🟣 マーク → C++23 の機能を使用

## ABC460～

:::details ABC461 B - The Honest Woodcutters
### [ABC461 B - The Honest Woodcutters](https://atcoder.jp/contests/abc461/tasks/abc461_b)
```cpp
#include <iostream>
#include <vector>

int main()
{
	// N 人の木こり
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
		--a; // 0-based index に変換
	}

	std::vector<int> B(N);
	for (auto& b : B)
	{
		std::cin >> b;
		--b; // 0-based index に変換
	}

	for (int i = 0; i < N; ++i)
	{
		// 木こり i が主張する斧: A[i]
		// 斧 A[i] の本当の持ち主: B[A[i]]
		// 木こり i の主張が正しいかどうかを確認し、矛盾があれば "No" を出力して終了
		if (B[A[i]] != i)
		{
			std::cout << "No\n";
			return 0;
		}
	}

	std::cout << "Yes\n";
}
```
:::

:::details ABC460 B - Two Rings
### [ABC460 B - Two Rings](https://atcoder.jp/contests/abc460/tasks/abc460_b)
```cpp
#include <iostream>

// x の二乗を計算する関数
long long Square(long long x)
{
	return (x * x);
}

int main()
{
	// テストケース T 個
	int T;
	std::cin >> T;

	for (int i = 0; i < T; ++i)
	{
		// double 型を使用して距離を計算すると誤差が生じるため, long long 型で計算する
		long long X1, Y1, R1, X2, Y2, R2;
		std::cin >> X1 >> Y1 >> R1 >> X2 >> Y2 >> R2;

		// 円の中心間の距離の二乗を計算
		const long long distanceSq = (Square(X1 - X2) + Square(Y1 - Y2));

		// 円周が共有点を持つ条件は, 円の中心間の距離が | R1 - R2 | 以上 (R1 + R2) 以下であること
		if ((Square(R1 - R2) <= distanceSq) && (distanceSq <= Square(R1 + R2)))
		{
			std::cout << "Yes\n";
		}
		else
		{
			std::cout << "No\n";
		}
	}
}
```
:::


## ABC450～ABC459

:::details ABC459 B - 459
### [ABC459 B - 459](https://atcoder.jp/contests/abc459/tasks/abc459_b)
```cpp
#include <iostream>
#include <string>

// 文字に対応する値を返す関数
int ToValue(char c)
{
	if (c <= 'c')
	{
		return 2;
	}
	else if (c <= 'f')
	{
		return 3;
	}
	else if (c <= 'i')
	{
		return 4;
	}
	else if (c <= 'l')
	{
		return 5;
	}
	else if (c <= 'o')
	{
		return 6;
	}
	else if (c <= 's')
	{
		return 7;
	}
	else if (c <= 'v')
	{
		return 8;
	}
	else
	{
		return 9;
	}
}

int main()
{
	// N 個の文字列
	int N;
	std::cin >> N;

	for (int i = 0; i < N; ++i)
	{
		std::string S;
		std::cin >> S;
		std::cout << ToValue(S[0]);
	}

	std::cout << '\n';
}
```
:::

:::details ABC458 B - Count Adjacent Cells
### [ABC458 B - Count Adjacent Cells](https://atcoder.jp/contests/abc458/tasks/abc458_b)
```cpp
#include <iostream>

int main()
{
	// H 行, W 列
	int H, W;
	std::cin >> H >> W;

	for (int y = 0; y < H; ++y)
	{
		for (int x = 0; x < W; ++x)
		{
			int count = 0;
			count += (0 <= (y - 1)); // 上に隣接するマスが存在するか
			count += (0 <= (x - 1)); // 左に隣接するマスが存在するか
			count += ((y + 1) < H); // 下に隣接するマスが存在するか
			count += ((x + 1) < W); // 右に隣接するマスが存在するか
			std::cout << count << ' ';
		}

		std::cout << '\n';
	}
}
```
:::

:::details ABC457 B - Arrays
### [ABC457 B - Arrays](https://atcoder.jp/contests/abc457/tasks/abc457_b)
```cpp
#include <iostream>
#include <vector>

int main()
{
	// N 個の数列
	int N;
	std::cin >> N;

	std::vector<std::vector<int>> A(N);

	for (int i = 0; i < N; ++i)
	{
		// 数列の長さ L
		int L;
		std::cin >> L;

		A[i].resize(L);
		
		for (auto& a : A[i])
		{
			std::cin >> a;
		}
	}

	int X, Y;
	std::cin >> X >> Y;
	--X, --Y; // 0-based index に変換

	std::cout << A[X][Y] << '\n';
}
```
:::

:::details ABC456 B - 456
### [ABC456 B - 456](https://atcoder.jp/contests/abc456/tasks/abc456_b)
```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<std::vector<int>> A(3, std::vector<int>(6));

	for (auto& die : A) // 各サイコロについて
	{
		for (auto& a : die)
		{
			std::cin >> a;
		}
	}

	// 条件を満たす結果の個数
	int count = 0;

	for (const auto& a1 : A[0])
	{
		for (const auto& a2 : A[1])
		{
			for (const auto& a3 : A[2])
			{
				// 3 つのサイコロの出た目の積が 120（4 * 5 * 6）であるか
				if ((a1 * a2 * a3) == 120)
				{
					++count;
				}
			}
		}
	}

	// 条件を満たす結果の個数を、全ての結果の個数（6 * 6 * 6）で割る
	const double result = (static_cast<double>(count) / (6 * 6 * 6));
	std::cout << result << '\n';
}
```
:::

:::details ABC455 B - Spiral Galaxy
### [ABC455 B - Spiral Galaxy](https://atcoder.jp/contests/abc455/tasks/abc455_b)
```cpp
#include <iostream>
#include <vector>
#include <string>

int main()
{
	// H 行, W 列のグリッド
	int H, W;
	std::cin >> H >> W;

	std::vector<std::string> S(H);
	for (auto& s : S)
	{
		std::cin >> s;
	}

	int count = 0;

	// 領域の高さ
	for (int h = 1; h <= H; ++h)
	{
		// 領域の幅
		for (int w = 1; w <= W; ++w)
		{
			// 高さ h, 幅 w からなる領域の左上の座標を全探索
			for (int oy = 0; oy <= (H - h); ++oy)
			{
				for (int ox = 0; ox <= (W - w); ++ox)
				{
					// その領域の文字を並べる
					std::string colors;
					for (int y = oy; y < (oy + h); ++y)
					{
						for (int x = ox; x < (ox + w); ++x)
						{
							colors.push_back(S[y][x]);
						}
					}

					// 逆順にした文字列
					const std::string reversed(colors.rbegin(), colors.rend());

					// 逆順にしても同じなら点対称
					count += (colors == reversed);
				}
			}
		}
	}

	std::cout << count << '\n';
}
```
:::

:::details ABC454 B - Mapping
### [ABC454 B - Mapping](https://atcoder.jp/contests/abc454/tasks/abc454_b)
```cpp
#include <iostream>
#include <set>

int main()
{
	// N 人, M 種類の服
	int N, M;
	std::cin >> N >> M;

	// 服の種類を格納する set
	std::set<int> set;
	for (int i = 0; i < N; ++i)
	{
		int F;
		std::cin >> F;
		set.insert(F);
	}

	// set のサイズが N と同じならば、全員が異なる服を着ていることになる
	std::cout << ((set.size() == N) ? "Yes\n" : "No\n");

	// set のサイズが M と同じならば、全ての種類の服が使用されていることになる
	std::cout << ((set.size() == M) ? "Yes\n" : "No\n");
}
```
:::

:::details ABC453 B - Sensor Data Logging
### [ABC453 B - Sensor Data Logging](https://atcoder.jp/contests/abc453/tasks/abc453_b)
```cpp
#include <iostream>
#include <cmath>

int main()
{
	// 時刻 T まで, 差の絶対値が X 以上の時保存
	int T, X;
	std::cin >> T >> X;

	// 最後に保存した値
	int last = 0;

	// 時刻 0, 1, ..., T について
	for (int t = 0; t <= T; ++t)
	{
		int a;
		std::cin >> a;

		// 初回（時刻 0）, または前回の値との差が X 以上のとき保存
		if ((t == 0) || (X <= std::abs(last - a)))
		{
			std::cout << t << ' ' << a << '\n';

			// 最後の記録を更新
			last = a;
		}
	}
}
```
:::

:::details ABC452 B - Draw Frame
### [ABC452 B - Draw Frame](https://atcoder.jp/contests/abc452/tasks/abc452_b)
```cpp
#include <iostream>

int main()
{
	// H 行, W 列のマス目
	int H, W;
	std::cin >> H >> W;

	for (int y = 0; y < H; ++y)
	{
		for (int x = 0; x < W; ++x)
		{
			// 端のマスは '#'、それ以外のマスは '.' を出力
			if ((y == 0) || (y == (H - 1)) || (x == 0) || (x == (W - 1)))
			{
				std::cout << '#';
			}
			else
			{
				std::cout << '.';
			}
		}
		
		std::cout << '\n';
	}
}
```
:::

:::details ABC451 B - Personnel Change
### [ABC451 B - Personnel Change](https://atcoder.jp/contests/abc451/tasks/abc451_b)
```cpp
#include <iostream>
#include <vector>

int main()
{
	// N 人の社員, M 個の部門
	int N, M;
	std::cin >> N >> M;

	// 今期の各部門の人数
	std::vector<int> A(M);

	// 来期の各部門の人数
	std::vector<int> B(M);

	for (int i = 0; i < N; ++i)
	{
		int a, b;
		std::cin >> a >> b;
		--a, --b; // 0-based index に変換

		// 対応する部門の人数を増やす
		++A[a];
		++B[b];
	}

	// 各部門について
	for (int i = 0; i < M; ++i)
	{
		// (来期の人数 - 今期の人数) を出力
		std::cout << (B[i] - A[i]) << '\n';
	}
}
```
:::

:::details ABC450 B - Split Ticketing
### [ABC450 B - Split Ticketing](https://atcoder.jp/contests/abc450/tasks/abc450_b)
```cpp
#include <iostream>
#include <vector>

int main()
{
	// N 個の駅
	int N;
	std::cin >> N;

	// C[from][to] : 駅 from～to のコスト
	// from, to は 1-based index で表す。0, 1, ... N まで収まるよう, N + 1 のサイズで配列を用意
	std::vector<std::vector<int>> C((N + 1), std::vector<int>(N + 1));

	// 駅 from～to のコストを入力
	for (int from = 1; from <= (N - 1); ++from)
	{
		for (int to = (from + 1); to <= N; ++to)
		{
			std::cin >> C[from][to];
		}
	}

	// 駅 from～to のパターンを列挙
	for (int from = 1; from <= (N - 1); ++from)
	{
		for (int to = (from + 1); to <= N; ++to)
		{
			// 中間駅を列挙
			for (int mid = (from + 1); mid <= (to - 1); ++mid)
			{
				// (駅 from～mid のコスト) + (駅 mid～to のコスト)
				const int cost = (C[from][mid] + C[mid][to]);

				// それが (駅 from～to のコスト) より小さければ
				if (cost < C[from][to])
				{
					std::cout << "Yes\n";
					return 0;
				}
			}
		}
	}

	std::cout << "No\n";
}
```
:::


## ABC440～ABC449

:::details ABC449 B - Deconstruct Chocolate
### [ABC449 B - Deconstruct Chocolate](https://atcoder.jp/contests/abc449/tasks/abc449_b)
```cpp

```
:::

:::details ABC448 B - Pepper Addiction
### [ABC448 B - Pepper Addiction](https://atcoder.jp/contests/abc448/tasks/abc448_b)
```cpp

```
:::

:::details ABC447 B - mpp
### [ABC447 B - mpp](https://atcoder.jp/contests/abc447/tasks/abc447_b)
```cpp

```
:::

:::details ABC446 B - Greedy Draft
### [ABC446 B - Greedy Draft](https://atcoder.jp/contests/abc446/tasks/abc446_b)
```cpp

```
:::

:::details ABC445 B - Center Alignment
### [ABC445 B - Center Alignment](https://atcoder.jp/contests/abc445/tasks/abc445_b)
```cpp

```
:::

:::details ABC444 B - Digit Sum
### [ABC444 B - Digit Sum](https://atcoder.jp/contests/abc444/tasks/abc444_b)
```cpp

```
:::

:::details ABC443 B - Setsubun
### [ABC443 B - Setsubun](https://atcoder.jp/contests/abc443/tasks/abc443_b)
```cpp

```
:::

:::details ABC442 B - Music Player
### [ABC442 B - Music Player](https://atcoder.jp/contests/abc442/tasks/abc442_b)
```cpp

```
:::

:::details ABC441 B - Two Languages
### [ABC441 B - Two Languages](https://atcoder.jp/contests/abc441/tasks/abc441_b)
```cpp

```
:::

:::details ABC440 B - Trifecta
### [ABC440 B - Trifecta](https://atcoder.jp/contests/abc440/tasks/abc440_b)
```cpp

```
:::


## ～ABC439

:::details ABC439 B - Happy Number
### [ABC439 B - Happy Number](https://atcoder.jp/contests/abc439/tasks/abc439_b)
```cpp

```
:::


