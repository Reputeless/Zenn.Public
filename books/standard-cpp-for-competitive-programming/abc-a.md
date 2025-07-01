---
title: "ABC A 問題 C++ 解法"
free: true
---

- AtCoder Beginner Contest (ABC) A 問題
- 標準 C++ 機能を効果的に活用したクリーンな解答コードです
- マークの意味:
	- 🟢 C++20 の機能を活用
	- 🟣 C++23 の機能を活用

## ABC410～

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
		// 目標タスク A 個、実際に完了したタスク B 個
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

	// 時と分をまとめて 1 つの整数に変換して、比較しやすくする
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

	for (int i = 0; i < N; ++i)
	{
		int a;
		std::cin >> a;

		// i が偶数のとき、a を合計に加える
		if ((i % 2) == 0)
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


## ABC390～ABC399

:::details ABC399 A - Hamming Distance
### [ABC399 A - Hamming Distance](https://atcoder.jp/contests/abc399/tasks/abc399_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 長さ N の文字列
	int N;
	std::cin >> N;

	std::string S, T;
	std::cin >> S >> T;

	// ハミング距離
	int count = 0;

	for (int i = 0; i < N; ++i)
	{
		if (S[i] != T[i])
		{
			++count;
		}
	}

	std::cout << count << '\n';
}
```
:::


:::details ABC398 A - Doors in the Center
### [ABC398 A - Doors in the Center](https://atcoder.jp/contests/abc398/tasks/abc398_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 長さ N の文字列
	int N;
	std::cin >> N;

	// すべて '-' で埋められた長さ N の文字列を作成する
	std::string s(N, '-');

	if ((N % 2) == 0) // N が偶数の場合
	{
		// 中央の 2 文字を '=' に置き換える
		s[N / 2 - 1] = '=';
		s[N / 2] = '=';
	}
	else // N が奇数の場合
	{
		// 中央の 1 文字を '=' に置き換える
		s[N / 2] = '=';
	}

	std::cout << s << '\n';
}
```
:::


:::details ABC397 A - Thermometer
### [ABC397 A - Thermometer](https://atcoder.jp/contests/abc397/tasks/abc397_a)
```cpp
#include <iostream>

int main()
{
	// 体温 X
	double X;
	std::cin >> X;

	if (38.0 <= X) // 高熱
	{
		std::cout << "1\n";
	}
	else if (37.5 <= X) // 発熱
	{
		std::cout << "2\n";
	}
	else // 平熱
	{
		std::cout << "3\n";
	}
}
```
:::


:::details ABC396 A - Triple Four
### [ABC396 A - Triple Four](https://atcoder.jp/contests/abc396/tasks/abc396_a)
```cpp
#include <iostream>

int main()
{
	// 長さ N の整数列
	int N;
	std::cin >> N;

	// 現在続いている整数
	int current = 0;

	// その連続個数
	int count = 0;

	for (int i = 0; i < N; ++i)
	{
		// 新しい整数
		int a;
		std::cin >> a;

		if (a == current) // 前回の整数と同じ場合
		{
			// 連続個数を増やす
			++count;

			if (count == 3)
			{
				// 3 回連続した場合 Yes を出力して終了する
				std::cout << "Yes\n";
				return 0;
			}
		}
		else // 前回の整数と異なる場合
		{
			// リセットする
			current = a;

			// 連続個数を 1 にする（現在の整数の分）
			count = 1;
		}
	}

	std::cout << "No\n";
}
```
:::


:::details ABC395 A - Strictly Increasing?
### [ABC395 A - Strictly Increasing?](https://atcoder.jp/contests/abc395/tasks/abc395_a)
```cpp
#include <iostream>

int main()
{
	// 長さ N の正整数列
	int N;
	std::cin >> N;

	// 前回の整数
	int last = 0;

	for (int i = 0; i < N; ++i)
	{
		// 新しい整数
		int a;
		std::cin >> a;

		// 新しい整数が前回の整数以下で、狭義単調増加が成り立たない場合
		if (a <= last)
		{
			std::cout << "No\n";
			return 0;
		}

		// 前回の整数を新しい整数に更新する
		last = a;
	}

	std::cout << "Yes\n";
}
```
:::


:::details ABC394 A - 22222
### [ABC394 A - 22222](https://atcoder.jp/contests/abc394/tasks/abc394_a)
```cpp

```
:::


:::details ABC393 A - Poisonous Oyster
### [ABC393 A - Poisonous Oyster](https://atcoder.jp/contests/abc393/tasks/abc393_a)
```cpp

```
:::


:::details ABC392 A - Shuffled Equation
### [ABC392 A - Shuffled Equation](https://atcoder.jp/contests/abc392/tasks/abc392_a)
```cpp

```
:::


:::details ABC391 A - Lucky Direction
### [ABC391 A - Lucky Direction](https://atcoder.jp/contests/abc391/tasks/abc391_a)
```cpp

```
:::


:::details ABC390 A - 12435
### [ABC390 A - 12435](https://atcoder.jp/contests/abc390/tasks/abc390_a)
```cpp

```
:::


## ABC380～ABC389

:::details ABC389 A - 9x9
### [ABC389 A - 9x9](https://atcoder.jp/contests/abc389/tasks/abc389_a)
```cpp

```
:::


:::details ABC388 A - ?UPC
### [ABC388 A - ?UPC](https://atcoder.jp/contests/abc388/tasks/abc388_a)
```cpp

```
:::


:::details ABC387 A - Happy New Year 2025
### [ABC387 A - Happy New Year 2025](https://atcoder.jp/contests/abc387/tasks/abc387_a)
```cpp

```
:::


:::details ABC386 A - Full House 2
### [ABC386 A - Full House 2](https://atcoder.jp/contests/abc386/tasks/abc386_a)
```cpp

```
:::


:::details ABC385 A - Equally
### [ABC385 A - Equally](https://atcoder.jp/contests/abc385/tasks/abc385_a)
```cpp

```
:::


:::details ABC384 A - aaaadaa
### [ABC384 A - aaaadaa](https://atcoder.jp/contests/abc384/tasks/abc384_a)
```cpp

```
:::


:::details ABC383 A - Humidifier 1
### [ABC383 A - Humidifier 1](https://atcoder.jp/contests/abc383/tasks/abc383_a)
```cpp

```
:::


:::details ABC382 A - Daily Cookie
### [ABC382 A - Daily Cookie](https://atcoder.jp/contests/abc382/tasks/abc382_a)
```cpp

```
:::


:::details ABC381 A - 11/22 String
### [ABC381 A - 11/22 String](https://atcoder.jp/contests/abc381/tasks/abc381_a)
```cpp

```
:::


:::details ABC380 A - 123233
### [ABC380 A - 123233](https://atcoder.jp/contests/abc380/tasks/abc380_a)
```cpp

```
:::


