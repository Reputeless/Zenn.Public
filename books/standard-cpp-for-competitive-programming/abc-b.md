---
title: "ABC B 問題 C++ 解法 | ABC400～499"
free: true
---

- **AtCoder Beginner Contest（ABC）B 問題**
- C++ 標準ライブラリの機能を効果的に活用した、クリーンな C++ コードによる模範解答集
- 🟢 → C++20 の機能を使用 / 🟣 → C++23 の機能を使用

## ABC460～ABC469

:::details ABC469 B - Isolated Seats
### [ABC469 B - Isolated Seats](https://atcoder.jp/contests/abc469/tasks/abc469_b)
```cpp
```
:::

:::details ABC468 B - Corridor Watch
### [ABC468 B - Corridor Watch](https://atcoder.jp/contests/abc468/tasks/abc468_b)
```cpp
```
:::

:::details ABC467 B - Keep the Change
### [ABC467 B - Keep the Change](https://atcoder.jp/contests/abc467/tasks/abc467_b)
```cpp
```
:::

:::details ABC466 B - Representative Balls
### [ABC466 B - Representative Balls](https://atcoder.jp/contests/abc466/tasks/abc466_b)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// N 個のボール, M 種類の色
	int N, M;
	std::cin >> N >> M;

	// maxBalls[i] = 色 i のボールの大きさの最大値, -1 はボールが存在しないことを表す 
	std::vector<int> maxBalls(M, -1);

	while (N--)
	{
		// 色 C, 大きさ S
		int C, S;
		std::cin >> C >> S;
		--C; // 0-based index に変換

		// maxBalls[C] の記録を更新する
		maxBalls[C] = std::max(maxBalls[C], S);
	}

	// 各色について
	for (const auto& maxBall : maxBalls)
	{
		// 最大の大きさを出力する（ボールが存在しない場合は -1 が出力される）
		std::cout << maxBall << ' ';
	}

	std::cout << '\n';
}
```
:::

:::details ABC465 B - Parking 2
### [ABC465 B - Parking 2](https://atcoder.jp/contests/abc465/tasks/abc465_b)
```cpp
#include <iostream>

int main()
{
	// L 時～ R 時は X 円/時間、それ以外は Y 円/時間
	// A 時から B 時までの料金を求める
	int X, Y, L, R, A, B;
	std::cin >> X >> Y >> L >> R >> A >> B;

	// 料金の合計
	int total = 0;

	// 各時について
	for (int i = A; i < B; ++i)
	{
		if ((L <= i) && (i < R))
		{
			total += X;
		}
		else
		{
			total += Y;
		}
	}

	std::cout << total << '\n';
}
```
:::

:::details ABC464 B - Crop
### [ABC464 B - Crop](https://atcoder.jp/contests/abc464/tasks/abc464_b)
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	// 高さ H ピクセル, 幅 W ピクセル
	int H, W;
	std::cin >> H >> W;

	int top = H; // 黒がある最初の行
	int bottom = -1; // 黒がある最後の行
	int left = W; // 黒がある最初の列
	int right = -1; // 黒がある最後の列

	std::vector<std::string> image(H, std::string(W, ' '));
	for (int y = 0; y < H; ++y)
	{
		for (int x = 0; x < W; ++x)
		{
			std::cin >> image[y][x];

			if (image[y][x] == '#') // もし黒なら
			{
				// それぞれの記録を更新する
				top = std::min(top, y);
				bottom = std::max(bottom, y);
				left = std::min(left, x);
				right = std::max(right, x);
			}
		}
	}

	// 対象範囲のみを出力する
	for (int y = top; y <= bottom; ++y)
	{
		for (int x = left; x <= right; ++x)
		{
			std::cout << image[y][x];
		}

		std::cout << '\n';
	}
}
```
:::

:::details ABC463 B - Train Reservation
### [ABC463 B - Train Reservation](https://atcoder.jp/contests/abc463/tasks/abc463_b)
```cpp
#include <iostream>
#include <vector>
#include <string>

int main()
{
	// N 行の座席
	int N;
	std::cin >> N;

	// 調べる列の文字
	char X;
	std::cin >> X;
	const int xIndex = (X - 'A'); // 文字を 0 から始まるインデックスに変換

	std::vector<std::string> S(N);
	for (auto& s : S)
	{
		std::cin >> s;
	}

	// 空席があるかどうか
	bool found = false;

	// 各行について
	for (const auto& s : S)
	{
		// 指定された列の座席が空席かどうかを確認する
		if (s[xIndex] == 'o')
		{
			found = true;
			break;
		}
	}

	std::cout << (found ? "Yes\n" : "No\n");
}
```
:::

:::details ABC462 B - Gift
### [ABC462 B - Gift](https://atcoder.jp/contests/abc462/tasks/abc462_b)
```cpp
#include <iostream>
#include <vector>

int main()
{
	// N 人
	int N;
	std::cin >> N;

	// A[i] は i 番目の人がもらったギフトの送り主一覧
	std::vector<std::vector<int>> A(N);

	// 各人について
	for (int i = 0; i < N; ++i)
	{
		// K 人に送る
		int K;
		std::cin >> K;

		while (K--)
		{
			// 送り先の番号 a
			int a;
			std::cin >> a;
			--a; // 0-based index に変換
			A[a].push_back(i); // a 番目の人が i 番目の人からギフトをもらったことを記録する
		}
	}

	// 各人について
	for (const auto& a : A)
	{
		// 送られたギフトの数を出力する
		std::cout << a.size() << ' ';

		// 送られたギフトの送り主の番号を出力する
		for (const auto& sender : a)
		{
			std::cout << (sender + 1) << ' '; // 1-based index に変換して出力
		}

		std::cout << '\n';
	}
}
```
:::

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
#include <iostream>

int main()
{
	// H 行, W 列のブロック, Q 個のクエリ
	int H, W, Q;
	std::cin >> H >> W >> Q;

	while (Q--)
	{
		int i, n;
		std::cin >> i >> n;

		if (i == 1)
		{
			// n 行減らす
			std::cout << (n * W) << '\n';
			H -= n;
		}
		else
		{
			// n 列減らす
			std::cout << (n * H) << '\n';
			W -= n;
		}
	}
}
```
:::

:::details ABC448 B - Pepper Addiction
### [ABC448 B - Pepper Addiction](https://atcoder.jp/contests/abc448/tasks/abc448_b)
```cpp
#include <iostream>
#include <vector>

int main()
{
	// N 個の料理, M 種類のコショウ
	int N, M;
	std::cin >> N >> M;

	// 各コショウの重量
	std::vector<int> C(M);
	for (auto& c : C)
	{
		std::cin >> c;
	}

	// 使用したコショウの総重量
	int sum = 0;

	// 各料理について
	for (int i = 0; i < N; ++i)
	{
		// コショウ a を最大 b かけられる
		int a, b;
		std::cin >> a >> b;
		--a; // 0-based index に変換

		// この料理に使用できる最大のコショウの重量
		const int p = std::min(C[a], b);

		// コショウの残量を減らす
		C[a] -= p;

		// 使用したコショウの重量を加算する
		sum += p;
	}

	std::cout << sum << '\n';
}
```
:::

:::details ABC447 B - mpp 🟢
### [ABC447 B - mpp](https://atcoder.jp/contests/abc447/tasks/abc447_b)
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::string S;
	std::cin >> S;

	// 各文字の出現回数
	std::vector<int> counts(26);
	for (const auto& ch : S)
	{
		++counts[ch - 'a'];
	}

	// 最も多く出現する文字の出現回数
	const int maxCount = std::ranges::max(counts);

	// 出現回数が maxCount である文字を S から削除する
	std::erase_if(S, [&counts, maxCount](char ch) { return (counts[ch - 'a'] == maxCount); });

	std::cout << S << '\n';
}
```
:::

:::details ABC446 B - Greedy Draft
### [ABC446 B - Greedy Draft](https://atcoder.jp/contests/abc446/tasks/abc446_b)
```cpp
#include <iostream>
#include <vector>

int main()
{
	// N 人の客, M 本の缶ジュース
	int N, M;
	std::cin >> N >> M;

	// 各缶ジュースが残っているか（true: 残っている, false: 売り切れ）
	std::vector<bool> available(M, true);

	// 各客について
	for (int i = 0; i < N; ++i)
	{
		// 長さ L の希望リスト
		int L;
		std::cin >> L;

		// 希望リスト
		std::vector<int> X(L);
		for (auto& x : X)
		{
			std::cin >> x;
			--x; // 0-based index に変換
		}

		// 缶ジュースが決まったか
		bool found = false;

		// 希望する各缶ジュースについて
		for (const auto& x : X)
		{
			// もしその缶ジュースが残っていれば
			if (available[x])
			{
				// その缶ジュースを出力する
				std::cout << (x + 1) << '\n';

				// その缶ジュースを売り切れにする
				available[x] = false;
				
				// 缶ジュースが決まった
				found = true;

				break;
			}
		}

		// もし缶ジュースが決まらなかったら, 0 を出力する
		if (!found)
		{
			std::cout << "0\n";
		}
	}
}
```
:::

:::details ABC445 B - Center Alignment
### [ABC445 B - Center Alignment](https://atcoder.jp/contests/abc445/tasks/abc445_b)
```cpp
#include <iostream>
#include <vector>
#include <string>

int main()
{
	// N 個の文字列
	int N;
	std::cin >> N;

	std::vector<std::string> S(N);

	// 最も長い文字列の長さ
	size_t maxLength = 0;
	
	for (auto& s : S)
	{
		std::cin >> s;
		maxLength = std::max(maxLength, s.size());
	}

	// 各文字列について
	for (const auto& s : S)
	{
		// 左右にそれぞれ追加する「.」の数
		const size_t padding = ((maxLength - s.size()) / 2);
		
		// 文字列の両端に追加する「.」列
		const std::string dots(padding, '.');

		std::cout << dots << s << dots << '\n';
	}
}
```
:::

:::details ABC444 B - Digit Sum
### [ABC444 B - Digit Sum](https://atcoder.jp/contests/abc444/tasks/abc444_b)
```cpp
#include <iostream>
#include <string>

int main()
{
	// N 以下の正整数で, 桁和が K であるものを数える
	int N, K;
	std::cin >> N >> K;

	// 結果の個数
	int count = 0;

	// 1 から N までの各整数について
	for (int i = 1; i <= N; ++i)
	{
		// 桁和
		int sum = 0;

		// i を文字列に変換して各桁を処理
		for (const auto& ch : std::to_string(i))
		{
			sum += (ch - '0');
		}

		// 桁和が K と等しい場合, count を増やす
		count += (sum == K);
	}

	std::cout << count << '\n';
}
```
:::

:::details ABC443 B - Setsubun
### [ABC443 B - Setsubun](https://atcoder.jp/contests/abc443/tasks/abc443_b)
```cpp
#include <iostream>

int main()
{
	// 現在 N 歳, 合計 K 個以上の豆を食べる
	int N, K;
	std::cin >> N >> K;

	// 食べた豆の合計
	int sum = 0;

	// i 年後に食べる豆の数は N + i 個
	for (int i = 0;; ++i)
	{
		sum += (N + i);

		// 合計が K 個以上になったら終了
		if (K <= sum)
		{
			std::cout << i << '\n';
			break;
		}
	}
}
```
:::

:::details ABC442 B - Music Player
### [ABC442 B - Music Player](https://atcoder.jp/contests/abc442/tasks/abc442_b)
```cpp
#include <iostream>

int main()
{
	// Q 回の操作
	int Q;
	std::cin >> Q;

	// 音量
	int volume = 0;

	// 再生しているか
	bool isPlaying = false;

	while (Q--)
	{
		int A;
		std::cin >> A;

		if (A == 1)
		{
			// 音量を 1 上げる
			++volume;
		}
		else if (A == 2)
		{
			// 音量が 1 以上なら, 音量を 1 下げる
			if (1 <= volume)
			{
				--volume;
			}
		}
		else
		{
			// 再生状態を反転させる
			isPlaying = !isPlaying;
		}

		// 音量が 3 以上で再生しているか
		std::cout << (((3 <= volume) && isPlaying) ? "Yes\n" : "No\n");
	}
}
```
:::

:::details ABC441 B - Two Languages
### [ABC441 B - Two Languages](https://atcoder.jp/contests/abc441/tasks/abc441_b)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 高橋語では長さ N の文字列 S, 青木語では長さ M の文字列 T に含まれる文字を使う
	int N, M;
	std::cin >> N >> M;
	std::string S, T;
	std::cin >> S >> T;

	// Q 個の単語
	int Q;
	std::cin >> Q;

	while (Q--)
	{
		std::string w;
		std::cin >> w;

		// w が S に含まれる文字だけで構成されているか（高橋語として成立するか）
		// w.find_first_not_of(S) は、w の中から S に含まれない最初の文字の位置を返す
		const bool validTakahashi = (w.find_first_not_of(S) == std::string::npos);

		// w が T に含まれる文字だけで構成されているか（青木語として成立するか）
		const bool validAoki = (w.find_first_not_of(T) == std::string::npos);

		if (!validTakahashi) // 高橋語でないなら必ず青木語
		{
			std::cout << "Aoki\n";
		}
		else if (!validAoki) // 青木語でないなら必ず高橋語
		{
			std::cout << "Takahashi\n";
		}
		else
		{
			std::cout << "Unknown\n";
		}
	}
}
```
:::

:::details ABC440 B - Trifecta 🟢
### [ABC440 B - Trifecta](https://atcoder.jp/contests/abc440/tasks/abc440_b)
```cpp
#include <iostream>
#include <vector>
#include <utility>
#include <algorithm>

int main()
{
	// N 頭の馬
	int N;
	std::cin >> N;

	// { 番号, 時間 } の配列
	std::vector<std::pair<int, int>> T(N);
	for (int i = 0; i < N; ++i)
	{
		int t;
		std::cin >> t;
		T[i] = { (i + 1), t };
	}

	// 時間（pair の second）の昇順でソート
	std::ranges::sort(T, {}, [](const auto& p) { return p.second; });

	// 上位 3 頭の番号を出力
	for (int i = 0; i < 3; ++i)
	{
		std::cout << T[i].first << ' ';
	}
}
```
:::


## ～ABC439

:::details ABC439 B - Happy Number
### [ABC439 B - Happy Number](https://atcoder.jp/contests/abc439/tasks/abc439_b)
```cpp
#include <iostream>
#include <string>
#include <set>

// 十進法表記の各桁の数字の二乗和を取った整数を返す関数
int Convert(int n)
{
	int sum = 0;

	for (const auto& ch : std::to_string(n))
	{
		const int x = (ch - '0');
		sum += (x * x);
	}

	return sum;
}

int main()
{
	int N;
	std::cin >> N;

	// すでに出現した数を記録する set
	std::set<int> used;

	for (;;)
	{
		// 操作を行う
		N = Convert(N);

		// 1 になったらハッピー数
		if (N == 1)
		{
			std::cout << "Yes\n";
			break;
		}

		// すでに出現した数が出てきた場合, ループしているためハッピー数ではない
		if (used.contains(N))
		{
			std::cout << "No\n";
			break;
		}

		// 出現した数を記録する
		used.insert(N);
	}
}
```
:::

