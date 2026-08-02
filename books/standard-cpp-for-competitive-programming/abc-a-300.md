---
title: "ABC A 問題 C++ 解法 | ABC300～399"
free: true
---

- **AtCoder Beginner Contest（ABC）A 問題**
- C++ 標準ライブラリの機能を効果的に活用した、クリーンな C++ コードによる模範解答集
- **ABC400～ABC499** は前ページ
- 🟢 → C++20 の機能を使用 / 🟣 → C++23 の機能を使用

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

	for (int i = 0; i < (N - 2); ++i)
	{
		// 3 個連続して同じ整数があるかを調べる
		if ((A[i] == A[i + 1]) && (A[i + 1] == A[i + 2]))
		{
			std::cout << "Yes\n";
			return 0;
		}
	}

	std::cout << "No\n";
}
```
- 別解
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

		// 新しい整数が前回の整数以下で, 狭義単調増加が成り立たない場合
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


:::details ABC394 A - 22222 🟢
### [ABC394 A - 22222](https://atcoder.jp/contests/abc394/tasks/abc394_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	std::string S;
	std::cin >> S;

	// S に含まれる '2' の個数を数えて, その個数の '2' からなる文字列を出力する
	std::cout << std::string(std::ranges::count(S, '2'), '2') << '\n';
}
```
:::


:::details ABC393 A - Poisonous Oyster
### [ABC393 A - Poisonous Oyster](https://atcoder.jp/contests/abc393/tasks/abc393_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 高橋君, 青木君の状態
	std::string S1, S2;
	std::cin >> S1 >> S2;

	// それぞれがお腹を壊したか
	const bool s1 = (S1 == "sick");
	const bool s2 = (S2 == "sick");

	if (s1 && s2) // 2 人ともお腹を壊した
	{
		std::cout << "1\n";
	}
	else if (s1) // 高橋君だけお腹を壊した
	{
		std::cout << "2\n";
	}
	else if (s2) // 青木君だけお腹を壊した
	{
		std::cout << "3\n";
	}
	else // 2 人ともお腹を壊していない
	{
		std::cout << "4\n";
	}
}
```
:::


:::details ABC392 A - Shuffled Equation
### [ABC392 A - Shuffled Equation](https://atcoder.jp/contests/abc392/tasks/abc392_a)
```cpp
#include <iostream>

int main()
{
	int a, b, c;
	std::cin >> a >> b >> c;

	// 並び替えパターンをすべてチェックし, 1 つでも条件を満たす場合
	if (((a * b) == c) || ((a * c) == b) || ((b * c) == a))
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


:::details ABC391 A - Lucky Direction
### [ABC391 A - Lucky Direction](https://atcoder.jp/contests/abc391/tasks/abc391_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 方角
	std::string D;
	std::cin >> D;

	// 各文字について反対の方角の文字を出力する
	for (const auto& c : D)
	{
		if (c == 'N')
		{
			std::cout << 'S';
		}
		else if (c == 'S')
		{
			std::cout << 'N';
		}
		else if (c == 'E')
		{
			std::cout << 'W';
		}
		else
		{
			std::cout << 'E';
		}
	}

	std::cout << '\n';
}
```
:::


:::details ABC390 A - 12435
### [ABC390 A - 12435](https://atcoder.jp/contests/abc390/tasks/abc390_a)
```cpp
#include <iostream>
#include <vector>

int main()
{
	// (1, 2, 3, 4, 5) を並び替えた整数列
	std::vector<int> A(5);
	for (auto& a: A)
	{
		std::cin >> a;
	}

	if ((A == std::vector{ 2, 1, 3, 4, 5 })		// 1 と 2 を入れ替えたパターン
		|| (A == std::vector{ 1, 3, 2, 4, 5 })	// 2 と 3 を入れ替えたパターン 
		|| (A == std::vector{ 1, 2, 4, 3, 5 })	// 3 と 4 を入れ替えたパターン
		|| (A == std::vector{ 1, 2, 3, 5, 4 }))	// 4 と 5 を入れ替えたパターン
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


## ABC380～ABC389

:::details ABC389 A - 9x9
### [ABC389 A - 9x9](https://atcoder.jp/contests/abc389/tasks/abc389_a)
```cpp
#include <iostream>

int main()
{
	int a, c;
	char b;
	
	// 最初の整数は a に読み込まれる
	// 次の 'x' は b に読み込まれる
	// 最後の整数は c に読み込まれる
	std::cin >> a >> b >> c;

	std::cout << (a * c) << '\n';
}
```
:::


:::details ABC388 A - ?UPC
### [ABC388 A - ?UPC](https://atcoder.jp/contests/abc388/tasks/abc388_a)
```cpp
#include <iostream>

int main()
{
	// 先頭の 1 文字を読み込む
	char c;
	std::cin >> c;

	std::cout << c << "UPC\n";
}
```
:::


:::details ABC387 A - Happy New Year 2025
### [ABC387 A - Happy New Year 2025](https://atcoder.jp/contests/abc387/tasks/abc387_a)
```cpp
#include <iostream>

int main()
{
	// 正整数 A, B
	int A, B;
	std::cin >> A >> B;

	// A + B の二乗を出力する
	std::cout << ((A + B) * (A + B)) << '\n';
}
```
:::


:::details ABC386 A - Full House 2
### [ABC386 A - Full House 2](https://atcoder.jp/contests/abc386/tasks/abc386_a)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// 4 枚のカードの整数
	std::vector<int> C(4);
	for (auto& c : C)
	{
		std::cin >> c;
	}

	std::ranges::sort(C);

	// ツーペアであるかを判定する
	if ((C[0] == C[1]) && (C[2] == C[3]) && (C[0] != C[2]))
	{
		// ツーペアであればフルハウスを作れる
		std::cout << "Yes\n";
		return 0;
	}

	// 先頭 3 枚でスリーカードであるかを判定する
	if ((C[0] == C[1]) && (C[1] == C[2]) && (C[3] != C[0]))
	{
		// スリーカードであればフルハウスを作れる
		std::cout << "Yes\n";
		return 0;
	}

	// 末尾 3 枚でスリーカードであるかを判定する
	if ((C[1] == C[2]) && (C[2] == C[3]) && (C[0] != C[3]))
	{
		// スリーカードであればフルハウスを作れる
		std::cout << "Yes\n";
		return 0;
	}

	// それ以外はフルハウスを作れない
	std::cout << "No\n";
}
```
:::


:::details ABC385 A - Equally
### [ABC385 A - Equally](https://atcoder.jp/contests/abc385/tasks/abc385_a)
```cpp
#include <iostream>

int main()
{
	// 3 つの整数
	int A, B, C;
	std::cin >> A >> B >> C;

	// すべてのグループ分けパターンで 1 つでも成り立つかを調べる
	if (((A + B) == C)
		|| ((A + C) == B)
		|| ((B + C) == A)
		|| ((A == B) && (B == C)))
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


:::details ABC384 A - aaaadaa 🟢
### [ABC384 A - aaaadaa](https://atcoder.jp/contests/abc384/tasks/abc384_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	// 長さ N の文字列
	int N;
	std::cin >> N;

	// 2 つの英小文字
	char c1, c2;
	std::cin >> c1 >> c2;

	// 文字列
	std::string S;
	std::cin >> S;

	// S の要素のうち, c1 でないものを c2 に置き換える
	std::ranges::replace_if(S, [c1](char c) { return (c != c1); }, c2);

	std::cout << S << '\n';
}
```
:::


:::details ABC383 A - Humidifier 1
### [ABC383 A - Humidifier 1](https://atcoder.jp/contests/abc383/tasks/abc383_a)
```cpp
#include <iostream>

int main()
{
	// N 回水を追加する
	int N;
	std::cin >> N;

	// 現在の水の量
	int water = 0;

	// 最後に水を追加した時刻
	int lastTime = 0;

	for (int i = 0; i < N; ++i)
	{
		// 時刻 T に V リットルの水を追加
		int T, V;
		std::cin >> T >> V;

		// 時間経過に応じて水の量を減らす（0 未満にはならない）
		water = std::max(0, (water - (T - lastTime)));

		// 水を追加する
		water += V;

		// 最後に水を追加した時刻を更新する
		lastTime = T;
	}

	std::cout << water << '\n';
}
```
:::


:::details ABC382 A - Daily Cookie 🟢
### [ABC382 A - Daily Cookie](https://atcoder.jp/contests/abc382/tasks/abc382_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	// N 個の箱, D 日間
	int N, D;
	std::cin >> N >> D;

	// 各箱の状態（'@' がクッキー入り, '.' が空箱）
	std::string S;
	std::cin >> S;

	// 初期の空き箱の個数に, D 日間で新たに空き箱になる個数を加える
	std::cout << (std::ranges::count(S, '.') + D) << '\n';
}
```
:::


:::details ABC381 A - 11/22 String
### [ABC381 A - 11/22 String](https://atcoder.jp/contests/abc381/tasks/abc381_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 文字列の長さ N
	int N;
	std::cin >> N;

	// 文字列 S
	std::string S;
	std::cin >> S;

	// 偶数文字では不成立
	if ((N % 2) == 0)
	{
		std::cout << "No\n";
		return 0;
	}

	// 長さ N の正しい 11/22 文字列を作る
	std::string target;
	target += std::string((N / 2), '1');
	target += '/';
	target += std::string((N / 2), '2');

	std::cout << ((S == target) ? "Yes\n" : "No\n");
}
```
:::


:::details ABC380 A - 123233 🟢
### [ABC380 A - 123233](https://atcoder.jp/contests/abc380/tasks/abc380_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	// 6 桁の正整数 N
	std::string N;
	std::cin >> N;

	// 条件を満たす場合, ソートすれば "122333" になる
	std::ranges::sort(N);

	std::cout << ((N == "122333") ? "Yes\n" : "No\n");
}
```
:::


## ABC370～ABC379

:::details ABC379 A - Cyclic
### [ABC379 A - Cyclic](https://atcoder.jp/contests/abc379/tasks/abc379_a)
```cpp
#include <iostream>

int main()
{
	// 整数の各桁
	char a, b, c;
	std::cin >> a >> b >> c;

	std::cout << b << c << a << ' ' << c << a << b << '\n';
}
```
:::


:::details ABC378 A - Pairing 🟢
### [ABC378 A - Pairing](https://atcoder.jp/contests/abc378/tasks/abc378_a)
```cpp
#include <iostream>
#include <set>

int main()
{
	// 所持しているボールの色を格納するセット
	std::set<int> set;

	for (int i = 0; i < 4; ++i)
	{
		// 新しいボールの色
		int a;
		std::cin >> a;

		// 既に同じ色のボールを持っている場合
		if (set.contains(a))
		{
			// その色のボールをセットから削除する
			set.erase(a);
		}
		else // まだ持っていない色のボールの場合
		{
			// セットにその色のボールを追加する
			set.insert(a);
		}
	}

	// （4 - 現在持っているボール）を 2 で割った値が捨てた回数
	std::cout << ((4 - set.size()) / 2) << '\n';
}
```
:::


:::details ABC377 A - Rearranging ABC 🟢
### [ABC377 A - Rearranging ABC](https://atcoder.jp/contests/abc377/tasks/abc377_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	// 長さ 3 の英大文字列
	std::string S;
	std::cin >> S;

	// ソートして "ABC" になるか確認すれば OK
	std::ranges::sort(S);

	std::cout << ((S == "ABC") ? "Yes\n" : "No\n");
}
```
:::


:::details ABC376 A - Candy Button
### [ABC376 A - Candy Button](https://atcoder.jp/contests/abc376/tasks/abc376_a)
```cpp
#include <iostream>

int main()
{
	// ボタンを N 回押す, 経過時間 C 秒未満は飴をもらえない
	int N, C;
	std::cin >> N >> C;

	// 最後に飴をもらった時刻（初回に必ずもらえるよう -1000 に設定）
	int lastT = -1000;

	// 飴の数
	int count = 0;

	for (int i = 0; i < N; ++i)
	{
		// ボタンを押した時刻
		int T;
		std::cin >> T;

		if (C <= (T - lastT))
		{
			// 飴をもらえる
			++count;

			// 最後に飴をもらった時刻を更新する
			lastT = T;
		}
	}
	
	std::cout << count << '\n';
}
```
:::


:::details ABC375 A - Seats
### [ABC375 A - Seats](https://atcoder.jp/contests/abc375/tasks/abc375_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// N 個の座席
	int N;
	std::cin >> N;

	// 座席の状態（#: 人が座っている, .: 座っていない）
	std::string S;
	std::cin >> S;

	// 左右に人が座っている空席の個数
	int count = 0;

	for (int i = 1; i < (N - 1); ++i)
	{
		if ((S[i] == '.') && (S[i - 1] == '#') && (S[i + 1] == '#'))
		{
			++count;
		}
	}

	std::cout << count << '\n';
}
```
:::


:::details ABC374 A - Takahashi san 2 🟢
### [ABC374 A - Takahashi san 2](https://atcoder.jp/contests/abc374/tasks/abc374_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 英小文字のみからなる文字列
	std::string S;
	std::cin >> S;

	// 文字列 S が "san" で終わっているかを調べる
	std::cout << (S.ends_with("san") ? "Yes\n" : "No\n");
}
```
:::


:::details ABC373 A - September
### [ABC373 A - September](https://atcoder.jp/contests/abc373/tasks/abc373_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 条件を満たす S の個数
	int count = 0;

	for (int i = 1; i <= 12; ++i)
	{
		std::string S;
		std::cin >> S;

		// S の長さが i
		if (S.size() == i)
		{
			++count;
		}
	}

	std::cout << count << '\n';
}
```
:::


:::details ABC372 A - delete .
### [ABC372 A - delete .](https://atcoder.jp/contests/abc372/tasks/abc372_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 英小文字および . からなる文字列
	std::string S;
	std::cin >> S;

	// 文字列 S の各要素について
	for (const auto& c : S)
	{
		// . でなければ出力する
		if (c != '.')
		{
			std::cout << c;
		}
	}

	std::cout << '\n';
}
```
:::


:::details ABC371 A - Jiro
### [ABC371 A - Jiro](https://atcoder.jp/contests/abc371/tasks/abc371_a)
```cpp
#include <iostream>

int main()
{
	// 3 人の年齢関係
	char sAB, sAC, sBC;
	std::cin >> sAB >> sAC >> sBC;

	if (sAB != sAC) // 「A < B && A > C」または「A > B && A < C」の場合
	{
		std::cout << "A\n";
	}
	else if (sAB == sBC) // 「A < B && B < C」または「A > B && B > C」の場合
	{
		std::cout << "B\n";
	}
	else // それ以外の場合
	{
		std::cout << "C\n";
	}
}
```
:::


:::details ABC370 A - Raise Both Hands
### [ABC370 A - Raise Both Hands](https://atcoder.jp/contests/abc370/tasks/abc370_a)
```cpp
#include <iostream>

int main()
{
	// 左手, 右手を挙げているか（0: 挙げていない, 1: 挙げている）
	int L, R;
	std::cin >> L >> R;

	if ((L == 1) && (R == 0)) // 左手のみを挙げている場合
	{
		std::cout << "Yes\n";
	}
	else if ((L == 0) && (R == 1)) // 右手のみを挙げている場合
	{
		std::cout << "No\n";
	}
	else // それ以外の場合
	{
		std::cout << "Invalid \n";
	}
}
```
:::


## ABC360～ABC369

:::details ABC369 A - 369
### [ABC369 A - 369](https://atcoder.jp/contests/abc369/tasks/abc369_a)
```cpp
#include <iostream>

int main()
{
	// 整数 A, B
	int A, B;
	std::cin >> A >> B;

	if (A == B)
	{
		// x, A, B
		std::cout << "1\n";
	}
	else if (((A - B) % 2) == 0)
	{
		// x, A, B
		// A, y, B
		// A, B, z
		std::cout << "3\n";
	}
	else
	{
		// x, A, B
		// A, B, y
		std::cout << "2\n";
	}
}
```
:::


:::details ABC368 A - Cut 🟢
### [ABC368 A - Cut](https://atcoder.jp/contests/abc368/tasks/abc368_a)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// N 枚のカード, K 枚取り出す
	int N, K;
	std::cin >> N >> K;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	// 終端から K 枚の位置で、前半と後半を入れ替える
	std::ranges::rotate(A, (A.end() - K));

	for (const auto& a : A)
	{
		std::cout << a << ' ';
	}
}
```
:::


:::details ABC367 A - Shout Everyday
### [ABC367 A - Shout Everyday](https://atcoder.jp/contests/abc367/tasks/abc367_a)
```cpp
#include <iostream>

int main()
{
	// A 時になると叫ぶ, 高橋君は B 時に就寝して C 時に起床する
	int A, B, C;
	std::cin >> A >> B >> C;

	// B 時が C 時より前の場合
	if (B < C)
	{
		// A が就寝中の時間帯（B～C）の間にあれば "No"
		std::cout << ((B < A) && (A < C) ? "No\n" : "Yes\n");
	}
	else
	{
		// A が起床中の時間帯（C～B）の間にあれば "Yes"
		std::cout << ((C < A) && (A < B) ? "Yes\n" : "No\n");
	}
}
```
:::


:::details ABC366 A - Election 2
### [ABC366 A - Election 2](https://atcoder.jp/contests/abc366/tasks/abc366_a)
```cpp
#include <iostream>

int main()
{
	// 有効票 N（奇数）, 高橋 T 票, 青木 A 票
	int N, T, A;
	std::cin >> N >> T >> A;

	// どちらかが過半数を獲得していれば
	if (((N / 2) < T) || ((N / 2) < A))
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


:::details ABC365 A - Leap Year
### [ABC365 A - Leap Year](https://atcoder.jp/contests/abc365/tasks/abc365_a)
```cpp
#include <iostream>

int main()
{
	// 西暦 Y 年
	int Y;
	std::cin >> Y;

	// うるう年の判定
	const bool isLeapYear = (((Y % 4 == 0) && (Y % 100 != 0)) || (Y % 400 == 0));

	std::cout << (isLeapYear ? "366\n" : "365\n");
}
```
:::


:::details ABC364 A - Glutton Takahashi
### [ABC364 A - Glutton Takahashi](https://atcoder.jp/contests/abc364/tasks/abc364_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// N 個の料理
	int N;
	std::cin >> N;

	// 連続して食べた甘い料理の数
	int sweetCount = 0;

	for (int i = 0; i < N; ++i)
	{
		// 甘い料理が 2 つ連続した場合、次の料理を断念する
		if (sweetCount == 2)
		{
			// 断念したということは完食失敗
			std::cout << "No\n";
			return 0;
		}

		std::string S;
		std::cin >> S;

		if (S == "sweet")
		{
			++sweetCount;
		}
		else
		{
			sweetCount = 0;
		}
	}

	std::cout << "Yes\n";
}
```
:::


:::details ABC363 A - Piling Up
### [ABC363 A - Piling Up](https://atcoder.jp/contests/abc363/tasks/abc363_a)
```cpp
#include <iostream>

int main()
{
	// 現在のレート
	int R;
	std::cin >> R;

	std::cout << (100 - R % 100) << '\n';
}
```
:::


:::details ABC362 A - Buy a Pen
### [ABC362 A - Buy a Pen](https://atcoder.jp/contests/abc362/tasks/abc362_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	// 赤色 R 円, 緑色 G 円, 青色 B 円
	int R, G, B;
	std::cin >> R >> G >> B;

	// 嫌いな色 C
	std::string C;
	std::cin >> C;

	if (C == "Red")
	{
		std::cout << std::min(G, B) << '\n';
	}
	else if (C == "Green")
	{
		std::cout << std::min(R, B) << '\n';
	}
	else
	{
		std::cout << std::min(R, G) << '\n';
	}
}
```
:::


:::details ABC361 A - Insert
### [ABC361 A - Insert](https://atcoder.jp/contests/abc361/tasks/abc361_a)
```cpp
#include <iostream>

int main()
{
	// 長さ N の整数列, K 要素目の直後に X を 1 つ挿入
	int N, K, X;
	std::cin >> N >> K >> X;

	// 1 から N
	for (int i = 1; i <= N; ++i)
	{
		int A;
		std::cin >> A;

		std::cout << A << ' ';

		// K 要素目なら X を追加で出力する
		if (i == K)
		{
			std::cout << X << ' ';
		}
	}
}
```
:::


:::details ABC360 A - A Healthy Breakfast
### [ABC360 A - A Healthy Breakfast](https://atcoder.jp/contests/abc360/tasks/abc360_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	// 皿の並べ方（R: ご飯, M: 味噌汁, S: サラダ）
	std::string S;
	std::cin >> S;

	// R の位置が M の位置よりも前にある場合
	if (S.find('R') < S.find('M'))
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


## ABC350～ABC359

:::details ABC359 A - Count Takahashi
### [ABC359 A - Count Takahashi](https://atcoder.jp/contests/abc359/tasks/abc359_a)
```cpp
#include <iostream>

int main()
{
	// N 個の文字列, "Takahashi" をカウント
	int N;
	std::cin >> N;

	int count = 0;

	for (int i = 0; i < N; ++i)
	{
		std::string S;
		std::cin >> S;

		if (S == "Takahashi")
		{
			++count;
		}
	}

	std::cout << count << '\n';
}
```
:::

:::details ABC358 A - Welcome to AtCoder Land
### [ABC358 A - Welcome to AtCoder Land](https://atcoder.jp/contests/abc358/tasks/abc358_a)
```cpp
#include <iostream>

int main()
{
	// S が "AtCoder", T が "Land" であるかを判定
	std::string S, T;
	std::cin >> S >> T;

	if ((S == "AtCoder") && (T == "Land"))
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

:::details ABC357 A - Sanitize Hands
### [ABC357 A - Sanitize Hands](https://atcoder.jp/contests/abc357/tasks/abc357_a)
```cpp
#include <iostream>

int main()
{
	// N 人の宇宙人, M 本の手を消毒できる消毒液
	int N, M;
	std::cin >> N >> M;

	// 消毒を完遂できる宇宙人の数
	int count = 0;

	// 各宇宙人について
	for (int i = 0; i < N; ++i)
	{
		// H 本の手
		int H;
		std::cin >> H;

		if (H <= M)
		{
			++count;
			M -= H;
		}
		else
		{
			break;
		}
	}

	std::cout << count << '\n';
}
```
:::

:::details ABC356 A - Subsegment Reverse 🟢
### [ABC356 A - Subsegment Reverse](https://atcoder.jp/contests/abc356/tasks/abc356_a)
```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <algorithm>

int main()
{
	// 正の整数 N, L, R
	int N, L, R;
	std::cin >> N >> L >> R;

	std::vector<int> A(N);
	
	// 1 から N までの整数を順に格納
	std::iota(A.begin(), A.end(), 1);

	// 区間 [L, R] の要素を反転
	std::ranges::reverse((A.begin() + L - 1), (A.begin() + R));

	for (const auto& a : A)
	{
		std::cout << a << ' ';
	}

	std::cout << '\n';
}
```
:::

:::details ABC355 A - Who Ate the Cake?
### [ABC355 A - Who Ate the Cake?](https://atcoder.jp/contests/abc355/tasks/abc355_a)
```cpp
#include <iostream>

int main()
{
	// A は犯人でない, B も犯人でない
	int A, B;
	std::cin >> A >> B;

	if (A != B)
	{
		std::cout << (6 - (A + B)) << '\n';
	}
	else
	{
		std::cout << "-1\n";
	}
}
```
:::

:::details ABC354 A - Exponential Plant
### [ABC354 A - Exponential Plant](https://atcoder.jp/contests/abc354/tasks/abc354_a)
```cpp
#include <iostream>

int main()
{
	// 高橋君の身長 H
	int H;
	std::cin >> H;

	int day = 0;
	int plantHeight = 0;

	for (;;)
	{
		// 2^day だけ植物が成長する
		plantHeight += (1 << day);

		++day;

		if (H < plantHeight)
		{
			std::cout << day << '\n';
			break;
		}
	}
}
```
:::

:::details ABC353 A - Buildings
### [ABC353 A - Buildings](https://atcoder.jp/contests/abc353/tasks/abc353_a)
```cpp
#include <iostream>

int main()
{
	// N 個のビル
	int N;
	std::cin >> N;

	// 左から 1 番目のビルの高さ H1
	int H1;
	std::cin >> H1;

	for (int i = 1; i < N; ++i)
	{
		// i + 1 番目のビルの高さ
		int H;
		std::cin >> H;

		if (H1 < H)
		{
			std::cout << (i + 1) << '\n';
			return 0;
		}
	}

	std::cout << "-1\n";
}
```
:::

:::details ABC352 A - AtCoder Line
### [ABC352 A - AtCoder Line](https://atcoder.jp/contests/abc352/tasks/abc352_a)
```cpp
#include <iostream>
#include <algorithm>

int main()
{
	// N 個の駅, 駅 X から駅 Y まで移動, 駅 Z に止まるか
	int N, X, Y, Z;
	std::cin >> N >> X >> Y >> Z;

	if ((std::min(X, Y) < Z) && (Z < std::max(X, Y)))
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

:::details ABC351 A - The bottom of the ninth
### [ABC351 A - The bottom of the ninth](https://atcoder.jp/contests/abc351/tasks/abc351_a)
```cpp
#include <iostream>

int main()
{
	// A チームの合計点
	int aScore = 0;

	for (int i = 0; i < 9; ++i)
	{
		int A;
		std::cin >> A;
		aScore += A;
	}

	// B チームの合計点
	int bScore = 0;

	for (int i = 0; i < 8; ++i)
	{
		int B;
		std::cin >> B;
		bScore += B;
	}

	std::cout << (aScore - bScore + 1) << '\n';
}
```
:::

:::details ABC350 A - Past ABCs
### [ABC350 A - Past ABCs](https://atcoder.jp/contests/abc350/tasks/abc350_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string S;
	std::cin >> S;

	const int n = ((S[3] - '0') * 100 + (S[4] - '0') * 10 + (S[5] - '0'));

	if ((1 <= n) && (n <= 349) && (n != 316))
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


## ABC340～ABC349

:::details ABC349 A - Zero Sum Game
### [ABC349 A - Zero Sum Game](https://atcoder.jp/contests/abc349/tasks/abc349_a)
```cpp
#include <iostream>

int main()
{
	// N 人
	int N;
	std::cin >> N;

	// A_1 ～ A_(N - 1) の合計点
	int sum = 0;

	for (int i = 1; i < N; ++i)
	{
		int A;
		std::cin >> A;
		sum += A;
	}

	std::cout << (0 - sum) << '\n';
}
```
:::

:::details ABC348 A - Penalty Kick
### [ABC348 A - Penalty Kick](https://atcoder.jp/contests/abc348/tasks/abc348_a)
```cpp
#include <iostream>

int main()
{
	// N 回のペナルティキック
	int N;
	std::cin >> N;

	for (int i = 1; i <= N; ++i)
	{
		// 3 の倍数は失敗, それ以外は成功
		if (i % 3 == 0)
		{
			std::cout << 'x';
		}
		else
		{
			std::cout << 'o';
		}
	}

	std::cout << '\n';
}
```
:::

:::details ABC347 A - Divisible
### [ABC347 A - Divisible](https://atcoder.jp/contests/abc347/tasks/abc347_a)
```cpp
#include <iostream>

int main()
{
	// 長さ N の数列, K の倍数の要素を抽出し K で割った商を出力
	int N, K;
	std::cin >> N >> K;

	for (int i = 0; i < N; ++i)
	{
		int a;
		std::cin >> a;

		if (a % K == 0)
		{
			std::cout << (a / K) << ' ';
		}
	}

	std::cout << '\n';
}
```
:::

:::details ABC346 A - Adjacent Product
### [ABC346 A - Adjacent Product](https://atcoder.jp/contests/abc346/tasks/abc346_a)
```cpp
#include <iostream>
#include <vector>

int main()
{
	// N 個の整数
	int N;
	std::cin >> N;

	std::vector<int> A(N);

	for (auto& a : A)
	{
		std::cin >> a;
	}

	for (int i = 0; i < (N - 1); ++i)
	{
		std::cout << (A[i] * A[i + 1]) << ' ';
	}

	std::cout << '\n';
}
```
:::

:::details ABC345 A - Leftrightarrow
### [ABC345 A - Leftrightarrow](https://atcoder.jp/contests/abc345/tasks/abc345_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string S;
	std::cin >> S;

	// 期待される連続した '=' の個数
	const int equalLength = (S.size() - 2);

	if ((S.front() == '<') &&
		(S.back() == '>') &&
		(S.substr(1, equalLength) == std::string(equalLength, '=')))
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

:::details ABC344 A - Spoiler
### [ABC344 A - Spoiler](https://atcoder.jp/contests/abc344/tasks/abc344_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string S;
	std::cin >> S;

	// 削除対象であるかを示すフラグ
	bool inside = false;

	for (const auto& c : S)
	{
		if (c == '|')
		{
			inside = !inside;
		}
		else if (!inside)
		{
			std::cout << c;
		}
	}

	std::cout << '\n';
}
```
:::

:::details ABC343 A - Wrong Answer
### [ABC343 A - Wrong Answer](https://atcoder.jp/contests/abc343/tasks/abc343_a)
```cpp
#include <iostream>

int main()
{
	int A, B;
	std::cin >> A >> B;

	for (int i = 0; i <= 9; ++i)
	{
		if (i != (A + B))
		{
			std::cout << i << '\n';
			break;
		}
	}
}
```
:::

:::details ABC342 A - Yay! 🟢
### [ABC342 A - Yay!](https://atcoder.jp/contests/abc342/tasks/abc342_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	std::string S;
	std::cin >> S;

	// ソート結果の先頭と末尾より 2 種類の文字を取得
	std::string S2 = S;
	std::ranges::sort(S2);
	const char c1 = S2.front();
	const char c2 = S2.back();

	// S2[1] でないほうの文字が 1 回のみ出現する文字
	if (c1 != S2[1]) // 1 回のみ出現するのは c1
	{
		std::cout << (S.find(c1) + 1) << '\n';
	}
	else // 1 回のみ出現するのは c2
	{
		std::cout << (S.find(c2) + 1) << '\n';
	}
}
```
:::

:::details ABC341 A - Print 341
### [ABC341 A - Print 341](https://atcoder.jp/contests/abc341/tasks/abc341_a)
```cpp
#include <iostream>

int main()
{
	int N;
	std::cin >> N;

	for (int i = 0; i < N; ++i)
	{
		std::cout << "10";
	}

	std::cout << "1\n";
}
```
:::

:::details ABC340 A - Arithmetic Progression
### [ABC340 A - Arithmetic Progression](https://atcoder.jp/contests/abc340/tasks/abc340_a)
```cpp
#include <iostream>

int main()
{
	// 初項 A, 末項 B, 公差 D
	int A, B, D;
	std::cin >> A >> B >> D;

	for (int i = A; i <= B; i += D)
	{
		std::cout << i << ' ';
	}

	std::cout << '\n';
}
```
:::


## ～ABC339

:::details ABC339 A - TLD
### [ABC339 A - TLD ](https://atcoder.jp/contests/abc339/tasks/abc339_a)
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string S;
	std::cin >> S;

	std::cout << S.substr(S.rfind('.') + 1) << '\n';
}
```
:::

