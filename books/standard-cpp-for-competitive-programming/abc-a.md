---
title: "ABC A 問題 C++ 解法 ABC400～ABC499"
free: true
---

- **AtCoder Beginner Contest（ABC）A 問題**
- C++ 標準ライブラリの機能を効果的に活用した、クリーンな C++ コードによる模範解答集
- **ABC300～ABC399** は次ページ
- 🟢 → C++20 の機能を使用 / 🟣 → C++23 の機能を使用

## ABC460～ABC469

:::details ABC469 A - Train Car
### [ABC469 A - Train Car](https://atcoder.jp/contests/abc469/tasks/abc469_a)
```cpp
#include <iostream>

int main()
{
	// N 両編成, 前から K 両目
	int N, K;
	std::cin >> N >> K;
	
	std::cout << (N - K + 1) << '\n';
}
```
:::

:::details ABC468 A - Maximal Value
### [ABC468 A - Maximal Value](https://atcoder.jp/contests/abc468/tasks/abc468_a)
```cpp
#include <iostream>
#include <vector>

int main()
{
	// 長さ N の整数列
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	int count = 0;

	for (int i = 0; i < (N - 2); ++i)
	{
		count += ((A[i] < A[i + 1]) && (A[i + 1] > A[i + 2]));
	}

	std::cout << count << '\n';
}
```
:::

:::details ABC467 A - Obesity
### [ABC467 A - Obesity](https://atcoder.jp/contests/abc467/tasks/abc467_a)
```cpp
#include <iostream>

int main()
{
	// 身長 H cm, 体重 W kg
	int H, W;
	std::cin >> H >> W;

	// BMI（浮動小数点数誤差を避けるために整数で計算）
	const int bmi = (W * 10000 / (H * H));

	if (25 <= bmi)
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC466 A - Compromise
### [ABC466 A - Compromise](https://atcoder.jp/contests/abc466/tasks/abc466_a)
```cpp
#include <iostream>

int main()
{
	int N;
	std::cin >> N;

	// どの選択肢を選んでも嬉しさが負になるか
	bool allNegative = true;

	while (N--)
	{
		int X;
		std::cin >> X;

		if (0 <= X) // 嬉しさが 0 以上なら
		{
			allNegative = false;
			break;
		}
	}

	std::cout << (allNegative ? "Yes\n" : "No\n");
}
```
:::

:::details ABC465 A - Supermajority
### [ABC465 A - Supermajority](https://atcoder.jp/contests/abc465/tasks/abc465_a)
```cpp
#include <iostream>

int main()
{
	int A, B;
	std::cin >> A >> B;

	if ((B * 2) < (A * 3))
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC464 A - Decisive Battle 🟢
### [ABC464 A - Decisive Battle](https://atcoder.jp/contests/abc464/tasks/abc464_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	std::string S;
	std::cin >> S;

	// E が過半数かを判定する
	if ((S.size() / 2) < std::ranges::count(S, 'E'))
	{
		std::cout << "East\n";
	}
	else
	{
		std::cout << "West\n";
	}
}
```
:::

:::details ABC463 A - 16:9
### [ABC463 A - 16:9](https://atcoder.jp/contests/abc463/tasks/abc463_a)
```cpp
#include <iostream>

int main()
{
	// 横 X ピクセル, 縦 Y ピクセル 
	int X, Y;
	std::cin >> X >> Y;

	// X:Y == 16:9 なら, X*9 == Y*16
	if (X * 9 == Y * 16)
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC462 A - Secret Numbers
### [ABC462 A - Secret Numbers](https://atcoder.jp/contests/abc462/tasks/abc462_a)
```cpp
#include <iostream>
#include <string>
#include <cctype>

int main()
{
	std::string S;
	std::cin >> S;

	// 各文字について
	for (const auto& ch : S)
	{
		// 数字であれば出力
		if (std::isdigit(ch))
		{
			std::cout << ch;
		}
	}

	std::cout << '\n';
}
```
:::

:::details ABC461 A - Armor
### [ABC461 A - Armor](https://atcoder.jp/contests/abc461/tasks/abc461_a)
```cpp
#include <iostream>

int main()
{
	// 威力 A の攻撃, D まで防げる鎧
	int A, D;
	std::cin >> A >> D;
	std::cout << ((A <= D) ? "Yes\n" : "No\n");
}
```
:::

:::details ABC460 A - Mod While Positive
### [ABC460 A - Mod While Positive](https://atcoder.jp/contests/abc460/tasks/abc460_a)
```cpp
#include <iostream>

int main()
{
	int N, M;
	std::cin >> N >> M;

	// 操作の回数
	int count = 0;

	while (M != 0)
	{
		const int x = (N % M);
		M = x;
		++count;
	}

	std::cout << count << '\n';
}
```
:::


## ABC450～ABC459

:::details ABC459 A - Hell, World!
### [ABC459 A - Hell, World!](https://atcoder.jp/contests/abc459/tasks/abc459_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	int X;
	std::cin >> X;

	// "HelloWorld" の X 番目の文字を削除する
	std::string s = "HelloWorld";
	s.erase(s.begin() + (X - 1));

	std::cout << s << '\n';
}
```
:::

:::details ABC458 A - Chompers
### [ABC458 A - Chompers](https://atcoder.jp/contests/abc458/tasks/abc458_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string S;
	std::cin >> S;

	int N;
	std::cin >> N;

	// S の先頭 N 文字と末尾 N 文字を除いた文字列を出力する
	std::cout << S.substr(N, (S.size() - 2 * N)) << '\n';
}
```
:::

:::details ABC457 A - Array
### [ABC457 A - Array](https://atcoder.jp/contests/abc457/tasks/abc457_a)
```cpp
#include <iostream>
#include <vector>

int main()
{
	// 数列の長さ N
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	// X 番目の要素を出力
	int X;
	std::cin >> X;
	std::cout << A[X - 1] << '\n';
}
```
:::

:::details ABC456 A - Dice
### [ABC456 A - Dice](https://atcoder.jp/contests/abc456/tasks/abc456_a)
```cpp
#include <iostream>

int main()
{
	int X;
	std::cin >> X;
	std::cout << (((3 <= X) && (X <= 18)) ? "Yes\n" : "No\n");
}
```
:::

:::details ABC455 A - 455
### [ABC455 A - 455](https://atcoder.jp/contests/abc455/tasks/abc455_a)
```cpp
#include <iostream>

int main()
{
	int A, B, C;
	std::cin >> A >> B >> C;
	std::cout << (((A != B) && (B == C)) ? "Yes\n" : "No\n");
}
```
:::

:::details ABC454 A - Closed interval
### [ABC454 A - Closed interval](https://atcoder.jp/contests/abc454/tasks/abc454_a)
```cpp
#include <iostream>

int main()
{
	// L 以上 R 以下の整数の個数を求める
	int L, R;
	std::cin >> L >> R;
	std::cout << (R - L + 1) << '\n';
}
```
:::

:::details ABC453 A - Trimo 🟢
### [ABC453 A - Trimo](https://atcoder.jp/contests/abc453/tasks/abc453_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	int N;
	std::cin >> N;

	std::string S;
	std::cin >> S;

	// o から始まるなら、先頭の文字を削除することを繰り返す
	while (S.starts_with('o'))
	{
		S.erase(S.begin());
	}

	std::cout << S << '\n';
}
```
:::

:::details ABC452 A - Gothec
### [ABC452 A - Gothec](https://atcoder.jp/contests/abc452/tasks/abc452_a)
```cpp
#include <iostream>

int main()
{
	// M 月 D 日
	int M, D;
	std::cin >> M >> D;

	// 日付を 1 つの整数に変換する
	int x = (M * 100 + D);

	if ((x == 107) || (x == 303) || (x == 505) || (x == 707) || (x == 909))
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC451 A - illegal
### [ABC451 A - illegal](https://atcoder.jp/contests/abc451/tasks/abc451_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string S;
	std::cin >> S;

	// S の文字数が 5 の倍数であれば "Yes"、そうでなければ "No" を出力する
	std::cout << (((S.size() % 5) == 0) ? "Yes\n" : "No\n");
}
```
:::

:::details ABC450 A - 3,2,1,GO
### [ABC450 A - 3,2,1,GO](https://atcoder.jp/contests/abc450/tasks/abc450_a)
```cpp
#include <iostream>

int main()
{
	int N;
	std::cin >> N;

	for (int i = N; 0 < i; --i)
	{
		// 最初の数字でない場合は、前にカンマを出力する
		if (i != N)
		{
			std::cout << ',';
		}

		std::cout << i;
	}

	std::cout << '\n';
}
```
:::


## ABC440～ABC449

:::details ABC449 A - π
### [ABC449 A - π](https://atcoder.jp/contests/abc449/tasks/abc449_a)
```cpp
#include <iostream>
#include <numbers>

int main()
{
	// 直径 D
	int D;
	std::cin >> D;

	// 半径 r
	const double r = (D / 2.0);

	// 面積 = πr^2
	const double area = (std::numbers::pi * r * r);

	// 小数第 6 位まで出力
	std::cout << std::fixed << area << '\n';
}
```
:::

:::details ABC448 A - chmin
### [ABC448 A - chmin](https://atcoder.jp/contests/abc448/tasks/abc448_a)
```cpp
#include <iostream>

int main()
{
	// N 個の整数, 初期最小値 X
	int N, X;
	std::cin >> N >> X;

	for (int i = 0; i < N; ++i)
	{
		int a;
		std::cin >> a;

		if (a < X)
		{
			std::cout << "1\n";
			X = a;
		}
		else
		{
			std::cout << "0\n";
		}
	}
}
```
:::

:::details ABC447 A - Seats 2
### [ABC447 A - Seats 2](https://atcoder.jp/contests/abc447/tasks/abc447_a)
```cpp
#include <iostream>

int main()
{
	// N 個の座席、隣り合わないよう M 人座らせる
	int N, M;
	std::cin >> N >> M;
	std::cout << (((M * 2 - 1) <= N) ? "Yes\n" : "No\n");
}
```
:::

:::details ABC446 A - Handmaid
### [ABC446 A - Handmaid](https://atcoder.jp/contests/abc446/tasks/abc446_a)
```cpp
#include <iostream>
#include <string>
#include <cctype>

int main()
{
	std::string S;
	std::cin >> S;

	// 先頭の文字を小文字に変換する
	S[0] = std::tolower(S[0]);

	std::cout << "Of" << S << '\n';
}
```
:::

:::details ABC445 A - Strong Word
### [ABC445 A - Strong Word](https://atcoder.jp/contests/abc445/tasks/abc445_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string S;
	std::cin >> S;

	// 文字列 S の先頭と末尾の文字が同じかを判定する
	std::cout << ((S.front() == S.back()) ? "Yes\n" : "No\n");
}
```
:::

:::details ABC444 A - Repdigit
### [ABC444 A - Repdigit](https://atcoder.jp/contests/abc444/tasks/abc444_a)
```cpp
#include <iostream>

int main()
{
	int N;
	std::cin >> N;

	// 111 の倍数であれば、すべての桁が同じ数字
	std::cout << ((N % 111 == 0) ? "Yes\n" : "No\n");
}
```
:::

:::details ABC443 A - Append s
### [ABC443 A - Append s](https://atcoder.jp/contests/abc443/tasks/abc443_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string S;
	std::cin >> S;
	std::cout << S << "s\n";
}
```
:::

:::details ABC442 A - Count . 🟢
### [ABC442 A - Count .](https://atcoder.jp/contests/abc442/tasks/abc442_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	std::string S;
	std::cin >> S;

	// S に含まれる 'i' と 'j' の個数の合計を出力
	std::cout << (std::ranges::count(S, 'i') + std::ranges::count(S, 'j')) << '\n';
}
```
:::

:::details ABC441 A - Black Square
### [ABC441 A - Black Square](https://atcoder.jp/contests/abc441/tasks/abc441_a)
```cpp
#include <iostream>

int main()
{
	// マス (P, Q) を一番左上のマスとする 100x100 の領域が黒
	int P, Q;
	std::cin >> P >> Q;

	// マス (X, Y) が黒か
	int X, Y;
	std::cin >> X >> Y;

	if (((P <= X) && (X < (P + 100))) && ((Q <= Y) && (Y < (Q + 100))))
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::


## ABC430～ABC439

:::details ABC440 A - Octave
### [ABC440 A - Octave](https://atcoder.jp/contests/abc440/tasks/abc440_a)
```cpp
#include <iostream>

int main()
{
	// 周波数 X ヘルツの音の高さを Y オクターブ上げる
	int X, Y;
	std::cin >> X >> Y;

	// X * 2^Y
	std::cout << (X * (1 << Y)) << '\n';
}
```
:::

:::details ABC439 A - 2^n
### [ABC439 A - 2^n](https://atcoder.jp/contests/abc439/tasks/abc439_a)
```cpp
#include <iostream>

int main()
{
	int N;
	std::cin >> N;

	// 2^N - 2N
	std::cout << ((1 << N) - 2 * N) << '\n';
}
```
:::

:::details ABC438 A - First Contest of the Year
### [ABC438 A - First Contest of the Year](https://atcoder.jp/contests/abc438/tasks/abc438_a)
```cpp
#include <iostream>

int main()
{
	// 1 年の長さが D 日, 7 日ごとにコンテスト開催,
	// ある年の初回コンテストが F 日目の場合, 次の年の初回コンテストは何日目か
	int D, F;
	std::cin >> D >> F;

	// ある年の初回以降の残り日数
	const int remainingDays = (D - F);

	// ある年の最後のコンテスト以降の残り日数
	const int daysAfterLastContest = (remainingDays % 7);

	std::cout << (7 - daysAfterLastContest) << '\n';
}
```
:::

:::details ABC437 A - Feet
### [ABC437 A - Feet](https://atcoder.jp/contests/abc437/tasks/abc437_a)
```cpp
#include <iostream>

int main()
{
	// A フィート B インチは何インチか
	int A, B;
	std::cin >> A >> B;

	std::cout << (A * 12 + B) << '\n';
}
```
:::

:::details ABC436 A - o-padding
### [ABC436 A - o-padding](https://atcoder.jp/contests/abc436/tasks/abc436_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 長さが N になるまで S の前に 'o' を追加した文字列を出力する
	int N;
	std::string S;
	std::cin >> N >> S;

	std::cout << std::string((N - S.size()), 'o') << S << '\n';
}
```
:::

:::details ABC435 A - Triangular Number
### [ABC435 A - Triangular Number](https://atcoder.jp/contests/abc435/tasks/abc435_a)
```cpp
#include <iostream>

int main()
{
	// 1 以上 N 以下の整数の和を求める
	int N;
	std::cin >> N;

	// 等差数列の和の公式を使う
	std::cout << (N * (N + 1)) / 2 << '\n';
}
```
:::

:::details ABC434 A - Balloon Trip
### [ABC434 A - Balloon Trip](https://atcoder.jp/contests/abc434/tasks/abc434_a)
```cpp
#include <iostream>

int main()
{
	// 体重 W kg, 風船 1 個あたり B g の浮力
	int W, B;
	std::cin >> W >> B;

	std::cout << ((W * 1000 / B) + 1) << '\n';
}
```
:::

:::details ABC433 A - Happy Birthday! 4
### [ABC433 A - Happy Birthday! 4](https://atcoder.jp/contests/abc433/tasks/abc433_a)
```cpp
#include <iostream>

int main()
{
	// 高橋君 X 歳, 青木君 Y 歳
	// 現在以降, 高橋君が青木君のちょうど Z 倍になることがあるか
	int X, Y, Z;
	std::cin >> X >> Y >> Z;

	for (;;)
	{
		if ((Y * Z) == X) // ちょうど Z 倍
		{
			std::cout << "Yes\n";
			break;
		}
		else if (X < (Y * Z)) // これ以降 Z 倍になることはない
		{
			std::cout << "No\n";
			break;
		}

		++X;
		++Y;
	}
}
```
:::

:::details ABC432 A - Permute to Maximize 🟢
### [ABC432 A - Permute to Maximize](https://atcoder.jp/contests/abc432/tasks/abc432_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	// 1 以上 9 以下の数字 A, B, C
	char A, B, C;
	std::cin >> A >> B >> C;

	std::string result = { A, B, C };

	// 降順にソートする
	std::ranges::sort(result, std::greater{});

	std::cout << result << '\n';
}
```
:::

:::details ABC431 A - Robot Balance
### [ABC431 A - Robot Balance](https://atcoder.jp/contests/abc431/tasks/abc431_a)
```cpp
#include <iostream>
#include <algorithm>

int main()
{
	// 頭パーツの重さ H, 体パーツの重さ B
	int H, B;
	std::cin >> H >> B;

	std::cout << std::max((H - B), 0) << '\n';
}
```
:::

:::details ABC430 A - Candy Cookie Law
### [ABC430 A - Candy Cookie Law](https://atcoder.jp/contests/abc430/tasks/abc430_a)
```cpp
#include <iostream>

int main()
{
	// 飴 A 個以上所持ならクッキー B 個以上所持していないと違反
	// 飴 C 個、クッキー D 個所持は違反か
	int A, B, C, D;
	std::cin >> A >> B >> C >> D;

	if ((A <= C) && (D < B)) // 違反
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::


## ABC420～ABC429

:::details ABC429 A - Too Many Requests
### [ABC429 A - Too Many Requests](https://atcoder.jp/contests/abc429/tasks/abc429_a)
```cpp
#include <iostream>

int main()
{
	// N 行出力、目標値 M
	int N, M;
	std::cin >> N >> M;

	for (int i = 0; i < N; ++i)
	{
		if ((i + 1) <= M)
		{
			std::cout << "OK\n";
		}
		else
		{
			std::cout << "Too Many Requests\n";
		}
	}
}
```
:::

:::details ABC428 A - Grandma's Footsteps
### [ABC428 A - Grandma's Footsteps](https://atcoder.jp/contests/abc428/tasks/abc428_a)
```cpp
#include <iostream>

int main()
{
	// 毎秒 S メートルの速さで A 秒間進み、その後 B 秒間休む
	// X 秒間で何メートル進むか
	int S, A, B, X;
	std::cin >> S >> A >> B >> X;

	// 現在のセッションで走る残り時間
	int runCount = A;

	// 現在のセッションで休む残り時間
	int restCount = B;

	// 進んだ距離
	int distance = 0;

	// X 秒の各ステップについて
	for (int i = 0; i < X; ++i)
	{
		if (runCount) // 走る時間が残っていれば
		{
			distance += S;
			--runCount;
		}
		else if (restCount) // 休む時間が残っていれば
		{
			--restCount;

			// 休む時間が終わったら次から新規セッション開始
			if (restCount == 0)
			{
				runCount = A;
				restCount = B;
			}
		}
	}

	std::cout << distance << '\n';
}
```
:::

:::details ABC427 A - ABC -> AC
### [ABC427 A - ABC -> AC](https://atcoder.jp/contests/abc427/tasks/abc427_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string S;
	std::cin >> S;

	// 中央の文字を削除する
	S.erase(S.begin() + (S.size() / 2));

	std::cout << S << '\n';
}
```
:::


:::details ABC426 A - OS Versions
### [ABC426 A - OS Versions](https://atcoder.jp/contests/abc426/tasks/abc426_a)
```cpp
#include <iostream>
#include <map>
#include <string>

int main()
{
	// バージョン名と、バージョン番号の対応表
	std::map<std::string, int> versions =
	{
		{ "Ocelot", 1 },
		{ "Serval", 2 },
		{ "Lynx", 3 }
	};

	// バージョン名 X, Y
	std::string X, Y;
	std::cin >> X >> Y;

	// X が Y 以降のバージョンかどうかを判定する
	if (versions[Y] <= versions[X])
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC425 A - Sigma Cubes
### [ABC425 A - Sigma Cubes](https://atcoder.jp/contests/abc425/tasks/abc425_a)
```cpp
#include <iostream>

// (-1)^i * i^3 を返す関数
int F(int i)
{
	if (i % 2 == 0)
	{
		return i * i * i;
	}
	else
	{
		return -(i * i * i);
	}
}

int main()
{
	int N;
	std::cin >> N;

	int sum = 0;

	for (int i = 1; i <= N; ++i)
	{
		sum += F(i);
	}

	std::cout << sum << '\n';
}
```
:::

:::details ABC424 A - Isosceles
### [ABC424 A - Isosceles](https://atcoder.jp/contests/abc424/tasks/abc424_a)
```cpp
#include <iostream>

int main()
{
	// 三角形の各辺の長さ a, b, c
	int a, b, c;
	std::cin >> a >> b >> c;

	// いずれかの 2 辺の長さが等しいか判定する
	if ((a == b) || (b == c) || (c == a))
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC423 A - Scary Fee
### [ABC423 A - Scary Fee](https://atcoder.jp/contests/abc423/tasks/abc423_a)
```cpp
#include <iostream>

int main()
{
	// 残高 X 円、1000 円あたりの引き出し手数料 C 円
	int X, C;
	std::cin >> X >> C;

	// 引き出し 1 単位あたりの手数料を含む金額
	const int unit = (1000 + C);

	// 引き出せる単位数
	const int count = (X / unit);

	// 引き出せる金額（単位数 × 1000 円）
	std::cout << (count * 1000) << '\n';
}
```
:::

:::details ABC422 A - Stage Clear
### [ABC422 A - Stage Clear](https://atcoder.jp/contests/abc422/tasks/abc422_a)
```cpp
#include <iostream>

int main()
{
	// ワールド番号, ステージ番号を読み込む
	int world, stage;
	// 区切り文字を読み飛ばす用の変数
	char separator;
	std::cin >> world >> separator >> stage;

	if (stage == 8)
	{
		// ステージ番号が 8 の場合、次のワールドのステージ 1
		std::cout << (world + 1) << "-1\n";
	}
	else
	{
		// それ以外の場合、同じワールドの次のステージ
		std::cout << world << "-" << (stage + 1) << "\n";
	}
}
```
:::

:::details ABC421 A - Misdelivery
### [ABC421 A - Misdelivery?](https://atcoder.jp/contests/abc421/tasks/abc421_a)
```cpp
#include <iostream>
#include <vector>
#include <string>

int main()
{
	// N 号室までの N 個の部屋
	int N;
	std::cin >> N;

	// 1 号室から N 号室までの住人の名前
	std::vector<std::string> S(N);
	for (auto& s : S)
	{
		std::cin >> s;
	}

	// 宛先が X 号室の Y さん
	int X;
	std::string Y;
	std::cin >> X >> Y;

	// 宛先が正しいか判定する
	if (S[X - 1] == Y)
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC420 A - What month is it?
### [ABC420 A - What month is it?](https://atcoder.jp/contests/abc420/tasks/abc420_a)
```cpp
#include <iostream>

int main()
{
	// X 月の Y か月後を求める
	int X, Y;
	std::cin >> X >> Y;

	std::cout << ((X + Y - 1) % 12 + 1) << '\n';
}
```
:::

## ABC410～ABC419

:::details ABC419 A - AtCoder Language
### [ABC419 A - AtCoder Language](https://atcoder.jp/contests/abc419/tasks/abc419_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string S;
	std::cin >> S;

	if (S == "red")
	{
		std::cout << "SSS\n";
	}
	else if (S == "blue")
	{
		std::cout << "FFF\n";
	}
	else if (S == "green")
	{
		std::cout << "MMM\n";
	}
	else
	{
		std::cout << "Unknown\n";
	}
}
```
:::

:::details ABC418 A - I'm a teapot 🟢
### [ABC418 A - I'm a teapot](https://atcoder.jp/contests/abc418/tasks/abc418_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 長さ N の文字列
	int N;
	std::cin >> N;

	std::string S;
	std::cin >> S;

	// 文字列 S が "tea" で終わるかを判定する
	if (S.ends_with("tea"))
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC417 A - A Substring
### [ABC417 A - A Substring](https://atcoder.jp/contests/abc417/tasks/abc417_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 長さ N の文字列
	int N;
	std::cin >> N;

	// 先頭から A 文字, 末尾から B 文字取り除く
	int A, B;
	std::cin >> A >> B;

	std::string S;
	std::cin >> S;

	// A 文字目から (N - A - B) 文字分を出力する
	std::cout << S.substr(A, (N - A - B)) << '\n';
}
```
:::

:::details ABC416 A - Vacation Validation 🟢
### [ABC416 A - Vacation Validation](https://atcoder.jp/contests/abc416/tasks/abc416_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	// 長さ N の文字列, L 文字目から R 文字目
	int N, L, R;
	std::cin >> N >> L >> R;

	std::string S;
	std::cin >> S;

	// L 文字目から R 文字目までがすべて 'o' であるか
	if (std::ranges::all_of((S.begin() + L - 1), (S.begin() + R),
		[](char ch) { return (ch == 'o'); }))
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC415 A - Unsupported Type
### [ABC415 A - Unsupported Type](https://atcoder.jp/contests/abc415/tasks/abc415_a)
```cpp
#include <iostream>
#include <set>

int main()
{
	// 長さ N の整数列
	int N;
	std::cin >> N;

	// 整数列に含まれる整数を格納するセット
	std::set<int> set;
	for (int i = 0; i < N; ++i)
	{
		int a;
		std::cin >> a;
		set.insert(a);
	}

	// 含まれているか判定したい整数
	int X;
	std::cin >> X;

	// セットが X を含んでいるか判定
	if (set.contains(X))
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC414 A - Streamer Takahashi
### [ABC414 A - Streamer Takahashi](https://atcoder.jp/contests/abc414/tasks/abc414_a)
```cpp
#include <iostream>

int main()
{
	// N 人のリスナー, 配信は L 時から R 時
	int N, L, R;
	std::cin >> N >> L >> R;

	int count = 0;

	for (int i = 0; i < N; ++i)
	{
		// リスナーは X 時から Y 時まで配信を見ることができる
		int X, Y;
		std::cin >> X >> Y;

		// リスナーが配信を見ることができる場合
		if ((X <= L) && (R <= Y))
		{
			++count;
		}
	}

	std::cout << count << '\n';
}
```
:::

:::details ABC413 A - Content Too Large
### [ABC413 A - Content Too Large](https://atcoder.jp/contests/abc413/tasks/abc413_a)
```cpp
#include <iostream>

int main()
{
	// N 個の品物, カバンの大きさ M
	int N, M;
	std::cin >> N >> M;

	// 品物の大きさの合計
	int sum = 0;

	for (int i = 0; i < N; ++i)
	{
		int A;
		std::cin >> A;

		sum += A;
	}

	std::cout << ((sum <= M) ? "Yes\n" : "No\n");
}
```
:::

:::details ABC412 A - Task Failed Successfully
### [ABC412 A - Task Failed Successfully](https://atcoder.jp/contests/abc412/tasks/abc412_a)
```cpp
#include <iostream>

int main()
{
	// N 日間のタスク目標
	int N;
	std::cin >> N;

	// 目標より多くのタスクを完了した日数
	int count = 0;

	for (int i = 0; i < N; ++i)
	{
		// 目標タスク A 個, 実際に完了したタスク B 個
		int A, B;
		std::cin >> A >> B;

		if (A < B)
		{
			++count;
		}
	}

	std::cout << count << '\n';
}
```
:::


:::details ABC411 A - Required Length
### [ABC411 A - Required Length](https://atcoder.jp/contests/abc411/tasks/abc411_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// パスワード文字列
	std::string P;
	std::cin >> P;

	// 長さ L 以上でなければならない
	int L;
	std::cin >> L;

	// パスワードの長さが L 以上の場合
	if (L <= P.size())
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::


:::details ABC410 A - G1 🟢
### [ABC410 A - G1](https://atcoder.jp/contests/abc410/tasks/abc410_a)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// N 個のレース
	int N;
	std::cin >> N;

	// 各レースで A[i] 歳以下が出場可能
	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	// K 歳の馬
	int K;
	std::cin >> K;

	// K 歳の馬が出場可能なレースの数を出力する
	std::cout << std::ranges::count_if(A, [K](int a) { return (K <= a); }) << '\n';
}
```
:::


## ABC400～ABC409

:::details ABC409 A - Conflict
### [ABC409 A - Conflict](https://atcoder.jp/contests/abc409/tasks/abc409_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// N 個の商品
	int N;
	std::cin >> N;

	// 高橋君が欲しがっている商品の配列（o: 欲しい, x: 欲しくない）
	std::string T;
	std::cin >> T;

	// 青木君が欲しがっている商品の配列（o: 欲しい, x: 欲しくない）
	std::string A;
	std::cin >> A;

	for (int i = 0; i < N; ++i)
	{
		// 2 人とも欲しがっている商品があれば
		if ((T[i] == 'o') && (A[i] == 'o'))
		{
			std::cout << "Yes\n";
			return 0;
		}
	}

	std::cout << "No\n";
}
```
:::


:::details ABC408 A - Timeout
### [ABC408 A - Timeout](https://atcoder.jp/contests/abc408/tasks/abc408_a)
```cpp
#include <iostream>

int main()
{
	// N 回叩く。(S + 0.5) 秒間が空くと寝てしまう
	int N, S;
	std::cin >> N >> S;

	// 最後に叩いた時刻
	int lastTouch = 0;

	for (int i = 0; i < N; ++i)
	{
		// 時刻 T に叩く
		int T;
		std::cin >> T;
	
		// 前回叩いた時刻からの経過時間が S より大きい場合
		if (S < (T - lastTouch))
		{
			std::cout << "No\n";
			return 0;
		}

		// 最後に叩いた時刻を更新する
		lastTouch = T;
	}

	std::cout << "Yes\n";
}
```
:::


:::details ABC407 A - Approximation
### [ABC407 A - Approximation](https://atcoder.jp/contests/abc407/tasks/abc407_a)
```cpp
#include <iostream>
#include <cmath>

int main()
{
	// 正整数 A と正の奇数 B
	int A, B;
	std::cin >> A >> B;

	// A / B を四捨五入して出力する
	std::cout << std::round(static_cast<double>(A) / B) << '\n';
}
```
:::


:::details ABC406 A - Not Acceptable
### [ABC406 A - Not Acceptable](https://atcoder.jp/contests/abc406/tasks/abc406_a)
```cpp
#include <iostream>

int main()
{
	// 締切 A 時 B 分 
	int A, B;
	std::cin >> A >> B;

	// 提出 C 時 D 分
	int C, D;
	std::cin >> C >> D;

	// 時と分をまとめて 1 つの整数に変換して, 比較しやすくする
	// 例: 12 時 34 分 → (12 * 100 + 34) = 1234
	if ((C * 100 + D) <= (A * 100 + B))
	{
		// 締切前に提出
		std::cout << "Yes\n";
	}
	else
	{
		// 締切後に提出
		std::cout << "No\n";
	}
}
```
:::


:::details ABC405 A - Is it rated?
### [ABC405 A - Is it rated?](https://atcoder.jp/contests/abc405/tasks/abc405_a)
```cpp
#include <iostream>

int main()
{
	// レーティング R
	int R;
	std::cin >> R;

	// Div.X
	int X;
	std::cin >> X;

	// Rated 対象であるか
	bool rated = false;

	if (X == 1) // Div.1
	{
		// 1600～2999 が Rated 対象
		rated = ((1600 <= R) && (R <= 2999));
	}
	else // Div.2
	{
		// 1200～2399 が Rated 対象
		rated = ((1200 <= R) && (R <= 2399));
	}

	std::cout << (rated ? "Yes\n" : "No\n");
}
```
:::


:::details ABC404 A - Not Found 🟣
### [ABC404 A - Not Found](https://atcoder.jp/contests/abc404/tasks/abc404_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 文字列
	std::string S;
	std::cin >> S;

	// 'a' から 'z' までの各文字に対して
	for (char c = 'a'; c <= 'z'; ++c)
	{
		// S が文字 c を含まない場合
		if (!S.contains(c))
		{
			// その文字を出力する
			std::cout << c << '\n';
			return 0;
		}
	}
}
```
:::


:::details ABC403 A - Odd Position Sum
### [ABC403 A - Odd Position Sum](https://atcoder.jp/contests/abc403/tasks/abc403_a)
```cpp
#include <iostream>

int main()
{
	// 長さ N の正整数列
	int N;
	std::cin >> N;

	// 合計を記録する変数
	int sum = 0;

	// 1 から N
	for (int i = 1; i <= N; ++i)
	{
		int a;
		std::cin >> a;

		// i が奇数のとき, a を合計に加える
		if ((i % 2) == 1)
		{
			sum += a;
		}
	}

	std::cout << sum << '\n';
}
```
:::



:::details ABC402 A - CBC
### [ABC402 A - CBC](https://atcoder.jp/contests/abc402/tasks/abc402_a)
```cpp
#include <iostream>
#include <string>
#include <cctype>

int main()
{
	// 英大文字と英小文字からなる文字列
	std::string S;
	std::cin >> S;

	// 各文字について
	for (const auto& c : S)
	{
		// その文字が英大文字であれば出力する
		if (std::isupper(c))
		{
			std::cout << c;
		}
	}

	std::cout << '\n';
}
```
:::


:::details ABC401 A - Status Code
### [ABC401 A - Status Code](https://atcoder.jp/contests/abc401/tasks/abc401_a)
```cpp
#include <iostream>

int main()
{
	// 整数 S
	int S;
	std::cin >> S;

	if ((200 <= S) && (S <= 299)) // S が 200 以上 299 以下の場合
	{
		std::cout << "Success\n";
	}
	else
	{
		std::cout << "Failure\n";
	}
}
```
:::


:::details ABC400 A - ABC400 Party
### [ABC400 A - ABC400 Party](https://atcoder.jp/contests/abc400/tasks/abc400_a)
```cpp
#include <iostream>

int main()
{
	// A 行に並べる
	int A;
	std::cin >> A;

	if ((400 % A) == 0) // 400 人を A 行で隙間なく並べられる場合
	{
		std::cout << (400 / A) << '\n';
	}
	else
	{
		std::cout << "-1\n";
	}
}
```
:::


