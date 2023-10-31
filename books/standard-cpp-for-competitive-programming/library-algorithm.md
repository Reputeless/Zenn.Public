---
title: "<algorithm> (C++17 以前)"
free: false
---

# 1. 最小値と最大値

## 1.1 二つの値のうち小さいほうの値を得る
- `std::min(a, b)` は、`a` と `b` のうち小さいほうの値への const 参照を返します
- 同じ場合は `a` への const 参照を返します
- `a` と `b` が違う型の場合、`std::min<Type>(a, b)` のように型 `Type` を明示的に指定します
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	int a = 30, b = 60;
	std::cout << std::min(a, b) << '\n'; // 小さいほうの値を出力

	double c = 0.5, d = -10.5;
	std::cout << std::min(c, d) << '\n'; // 小さいほうの値を出力

	char e = 'a', f = 'z';
	std::cout << std::min(e, f) << '\n'; // 小さいほうの値を出力

	std::string g = "apple", h = "bird";
	std::cout << std::min(g, h) << '\n'; // 小さいほうの値を出力

	// std::size_t と int なので <> で明示的に型を指定
	std::cout << std::min<std::size_t>(g.size(), 1) << '\n'; // 小さいほうの値を出力
	
	// 1 を std::size_t 型にするのもあり
	std::cout << std::min(g.size(), std::size_t(1)) << '\n'; // 小さいほうの値を出力
	
	// 整数オーバーフローの心配が無ければ、これもあり
	std::cout << std::min(static_cast<int>(g.size()), 1) << '\n'; // 小さいほうの値を出力
}
```
```txt:出力
30
-10.5
a
apple
1
1
1
```

## 1.2 二つの値のうち大きいほうの値を得る
- `std::max(a, b)` は、`a` と `b` のうち大きいほうの値への const 参照を返します
- 同じ場合は `a` への const 参照を返します
- `a` と `b` が違う型の場合、`std::max<Type>(a, b)` のように型 `Type` を明示的に指定します
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	int a = 30, b = 60;
	std::cout << std::max(a, b) << '\n'; // 大きいほうの値を出力

	double c = 0.5, d = -10.5;
	std::cout << std::max(c, d) << '\n'; // 大きいほうの値を出力

	char e = 'a', f = 'z';
	std::cout << std::max(e, f) << '\n'; // 大きいほうの値を出力

	std::string g = "apple", h = "bird";
	std::cout << std::max(g, h) << '\n'; // 大きいほうの値を出力

	// std::size_t と int なので <> で明示的に型を指定
	std::cout << std::max<std::size_t>(g.size(), 1) << '\n'; // 大きいほうの値を出力
	
	// 1 を std::size_t 型にするのもあり
	std::cout << std::max(g.size(), std::size_t(1)) << '\n'; // 大きいほうの値を出力
	
	// 整数オーバーフローの心配が無ければ、これもあり
	std::cout << std::max(static_cast<int>(g.size()), 1) << '\n'; // 大きいほうの値を出力
}
```
```txt:出力
60
0.5
z
bird
5
5
5
```

## 1.3 三つ以上の値から最小値を得る
- `std::min({ ... })` は、リスト `{ ... }` 内の要素の最小値を返します
- リスト内の要素は同じ型である必要があります
```cpp
#include <iostream>
#include <algorithm>

int main()
{
	int a = 30, b = 60, c = 50;
	std::cout << std::min({ a, b, c }) << '\n'; // 最小値を出力

	double d = 0.1, e = 0.2, f = 0.3, g = 0.4;
	std::cout << std::min({ d, e, f, g, -0.1 }) << '\n'; // 最小値を出力
}
```
```txt:出力
30
-0.1
```

## 1.4 三つ以上の値から最大値を得る
- `std::max({ ... })` は、リスト `{ ... }` 内の要素の最大値を返します
- リスト内の要素は同じ型である必要があります
```cpp
#include <iostream>
#include <algorithm>

int main()
{
	int a = 30, b = 60, c = 50;
	std::cout << std::max({ a, b, c }) << '\n'; // 最大値を出力

	double d = 0.1, e = 0.2, f = 0.3, g = 0.4;
	std::cout << std::max({ d, e, f, g, -0.1 }) << '\n'; // 最大値を出力
}
```
```txt:出力
60
0.4
```

## 1.5 配列の中から最小の要素とその位置を得る
- `std::min_element(itFirst, itLast)` は、範囲 `[itFirst, itLast)` の中で最小の要素の位置を指すイテレータを返します
- イテレータに `*` を付けると、その位置の値にアクセスできます
- イテレータの指す値が配列の何番目にあるかを整数値で得たい場合は、`std::distance(itFirst, itLast)` に、範囲の先頭のイテレータと、`std::min_element()` が返したイテレータを渡し、その間の距離を求めます
> - `std::min_element(itFirst, itLast)` の計算量: $O(N)$
> - `std::distance(itFirst, itLast)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { -5, 10, -30, 20, 50, 0 };	
	// 位置（何番目）の情報が不要な場合、関数の戻り値に直接 * を使うと短く書ける
	int minNumber = *std::min_element(numbers.begin(), numbers.end());
	std::cout << minNumber << '\n'; // 最小値を出力
	// イテレータを取得し、値と位置（何番目）を調べる
	auto it = std::min_element(numbers.begin(), numbers.end());
	std::cout << *it << " : " << std::distance(numbers.begin(), it) << '\n'; // 最小値とその位置を出力

	std::cout << "---\n";

	std::vector<std::string> words = { "cat", "apple", "bird", "dog" };
	auto it2 = std::min_element(words.begin(), words.end());
	std::cout << *it2 << " : " << std::distance(words.begin(), it2) << '\n'; // 最小値とその位置を出力

	std::cout << "---\n";

	std::string s = "computer";
	std::cout << *std::min_element(s.begin(), s.end()) << '\n'; // 最小値を出力
}
```
```txt:出力
-30
-30 : 2
---
apple : 1
---
c
```

## 1.6 配列の中から最大の要素とその位置を得る
- `std::max_element(itFirst, itLast)` は、範囲 `[itFirst, itLast)` の中で最大の要素の位置を指すイテレータを返します
- イテレータに `*` を付けると、その位置の値にアクセスできます
- イテレータの指す値が配列の何番目にあるかを整数値で得たい場合は、`std::distance(itFirst, itLast)` に、範囲の先頭のイテレータと、`std::max_element()` が返したイテレータを渡し、その間の距離を求めます
> - `std::max_element(itFirst, itLast)` の計算量: $O(N)$
> - `std::distance(itFirst, itLast)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { -5, 10, -30, 20, 50, 0 };	
	// 位置（何番目）の情報が不要な場合、関数の戻り値に直接 * を使うと短く書ける
	int minNumber = *std::max_element(numbers.begin(), numbers.end());
	std::cout << minNumber << '\n'; // 最大値を出力
	// イテレータを取得し、値と位置（何番目）を調べる
	auto it = std::max_element(numbers.begin(), numbers.end());
	std::cout << *it << " : " << std::distance(numbers.begin(), it) << '\n'; // 最大値とその位置を出力

	std::cout << "---\n";

	std::vector<std::string> words = { "cat", "apple", "bird", "dog" };
	auto it2 = std::max_element(words.begin(), words.end());
	std::cout << *it2 << " : " << std::distance(words.begin(), it2) << '\n'; // 最大値とその位置を出力

	std::cout << "---\n";

	std::string s = "computer";
	std::cout << *std::max_element(s.begin(), s.end()) << '\n'; // 最大値を出力
}
```
```txt:出力
50
50 : 4
---
dog : 3
---
u
```

## 1.7 二つの値から小さいほうの値と大きいほうの値を一度に得る
- `std::minmax(a, b)` は、小さいほうの値への const 参照、大きいほうの値への const 参照を組にした `std::pair` を返します
- `std::pair`の `.first` が小さいほうの値で、`.second` が大きいほうの値です
- 個別に `std::min(a, b)`, `std::max(a, b)` を呼ぶよりも大小比較の回数を減らせる利点があります
- `std::pair` を構造化束縛で受け取ることもできます
```cpp
#include <iostream>
#include <algorithm>

int main()
{
	int a = 100, b = -20;
	auto p = std::minmax(a, b);
	std::cout << "min: " << p.first << '\n'; // 小さいほうの値を出力
	std::cout << "max: " << p.second << '\n'; // 大きいほうの値を出力

	// 構造化束縛を利用する場合
	auto [min, max] = std::minmax(a, b);
	std::cout << "min: " << min << '\n'; // 小さいほうの値を出力
	std::cout << "max: " << max << '\n'; // 大きいほうの値を出力
}
```
```txt:出力
min: -20
max: 100
min: -20
max: 100
```

## 1.8 三つ以上の値から最小値と最大値を一度に得る
- `std::minmax({ ... })` は、リスト `{ ... }` 内の要素の最小値、最大値を組にした `std::pair` を返します
- `std::pair`の `.first` が小さいほうの値で、`.second` が大きいほうの値です
- 個別に `std::min({ ... })`, `std::max({ ... })` を呼ぶよりも大小比較の回数を減らせる利点があります
- `std::pair` を構造化束縛で受け取ることもできます
```cpp
#include <iostream>
#include <algorithm>

int main()
{
	int a = 100, b = -20, c = -50, d = 120;
	auto p = std::minmax({ a, b, c, d });
	std::cout << "min: " << p.first << '\n'; // 最小値を出力
	std::cout << "max: " << p.second << '\n'; // 最大値を出力

	// 構造化束縛を利用する場合
	auto [min, max] = std::minmax({ a, b, c, d });
	std::cout << "min: " << min << '\n'; // 最小値を出力
	std::cout << "max: " << max << '\n'; // 最大値を出力
}
```
```txt:出力
min: -50
max: 120
min: -50
max: 120
```

## 1.9 配列の中から最小の要素と最大の要素、およびそれらの位置を一度に得る
- `std::minmax_element(itFirst, itLast)` は、範囲 `[itFirst, itLast)` の中で最小の要素、最大の要素を指すイテレータを組にした `std::pair` を返します
- `std::pair`の `.first` が小さいほうの値で、`.second` が大きいほうの値です
- 個別に `std::min_element(itFirst, itLast)`, `std::max_element(itFirst, itLast)` を呼ぶよりも大小比較の回数を減らせる利点があります
- `std::pair` を構造化束縛で受け取ることもできます
- イテレータに `*` を付けると、その位置の値にアクセスできます
- イテレータの指す値が配列の何番目にあるかを整数値で得たい場合は、`std::distance(itFirst, itLast)` に、範囲の先頭のイテレータと、`std::minmax_element()` が返したイテレータを渡し、その間の距離を求めます
> - `std::minmax_element(itFirst, itLast)` の計算量: $O(N)$
> - `std::distance(itFirst, itLast)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { -5, 10, -30, 20, 50, 0 };
	// イテレータのペアを取得し、値と位置（何番目）を調べる
	auto p = std::minmax_element(numbers.begin(), numbers.end());
	std::cout << "min: " << *p.first << " : " << std::distance(numbers.begin(), p.first) << '\n'; // 最小値とその位置を出力
	std::cout << "max: " << *p.second << " : " << std::distance(numbers.begin(), p.second) << '\n'; // 最大値とその位置を出力

	std::cout << "---\n";

	std::vector<std::string> words = { "cat", "apple", "bird", "dog" };
	auto[itMin, itMax] = std::minmax_element(words.begin(), words.end());
	std::cout << "min: " << *itMin << " : " << std::distance(words.begin(), itMin) << '\n'; // 最小値とその位置を出力
	std::cout << "max: " << *itMax << " : " << std::distance(words.begin(), itMax) << '\n'; // 最大値とその位置を出力
}
```
```txt:出力
min: -30 : 2
max: 50 : 4
---
min: apple : 1
max: dog : 3
```

## 1.10 ある値を、指定した最小値と最大値の範囲に収める
- `std::clamp(value, min, max)` は、値 `value` を `min` 以上 `max` 以下の範囲に収めた値を返します
- `value` が範囲内であれば `value` を、`min` 未満なら `min` を、`max` より大きいなら `max` を返します
- `value`, `min`, `max` の型がそれぞれ違う場合、`std::clamp<Type>(value, min, max)` のように型 `Type` を明示的に指定します
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	int a = 50;
	std::cout << std::clamp(a, 0, 100) << '\n'; // a を 0 以上 100 以下に収める
	std::cout << std::clamp(a, 0, 10) << '\n'; // a を 0 以上 10 以下に収める
	std::cout << std::clamp(a, 100, 200) << '\n'; // a を 100 以上 200 以下に収める

	std::cout << "---\n";

	std::size_t b = 50;
	// std::size_t と int が混在しているため <> で明示的に型を指定
	std::cout << std::clamp<std::size_t>(b, 0, 100) << '\n'; // b を 0 以上 100 以下に収める
	std::cout << std::clamp<std::size_t>(b, 0, 10) << '\n'; // b を 0 以上 10 以下に収める
	std::cout << std::clamp<std::size_t>(b, 100, 200) << '\n'; // b を 100 以上 200 以下に収める
}
```
```txt:出力
50
10
100
---
50
10
100
```


# 2. 範囲に対する検索操作

## 2.1 すべての要素が条件を満たすか調べる
- `std::all_of(itFirst, itLast, unaryPred)` は、範囲 `[itFirst, itLast)` にある要素すべてが条件 `unaryPred` を満たしているかを `bool` 型の値で返します
- 範囲が空の場合は `true` を返します
- `unaryPred` は、要素に対して条件を満たすかを返す関数や関数オブジェクトです
> - `std::all_of(itFirst, itLast, unaryPred)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<double> a = { 36.2, 36.6, 36.9, 40.1, 37.2 };
	std::vector<double> b = { 36.2, 36.6, 36.9 };
	std::vector<double> c;

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	// 全員 37.5℃ 未満？
	std::cout << std::all_of(a.begin(), a.end(), [](double t) { return t < 37.5; }) << '\n';
	std::cout << std::all_of(b.begin(), b.end(), [](double t) { return t < 37.5; }) << '\n';
	std::cout << std::all_of(c.begin(), c.end(), [](double t) { return t < 37.5; }) << '\n';
}
```
```txt:出力
false
true
true
```

## 2.2 条件を満たす要素があるか調べる
- `std::any_of(itFirst, itLast, unaryPred)` は、範囲 `[itFirst, itLast)` に条件 `unaryPred` を満たす要素が 1 つでもあるかを `bool` 型の値で返します
- 範囲が空の場合は `false` を返します
- `unaryPred` は、要素に対して条件を満たすかを返す関数や関数オブジェクトです
> - `std::any_of(itFirst, itLast, unaryPred)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<double> a = { 36.2, 36.6, 36.9, 40.1, 37.2 };
	std::vector<double> b = { 36.2, 36.6, 36.9 };
	std::vector<double> c;

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	// 37.5℃ 以上の人がいる？
	std::cout << std::any_of(a.begin(), a.end(), [](double t) { return 37.5 <= t; }) << '\n';
	std::cout << std::any_of(b.begin(), b.end(), [](double t) { return 37.5 <= t; }) << '\n';
	std::cout << std::any_of(c.begin(), c.end(), [](double t) { return 37.5 <= t; }) << '\n';
}
```
```txt:出力
true
false
false
```

## 2.3 条件を満たす要素が存在しないかを調べる
- `std::none_of(itFirst, itLast, unaryPred)` は、範囲 `[itFirst, itLast)` に条件 `unaryPred` を満たす要素が 0 個であるかを `bool` 型の値で返します
- 範囲が空の場合は `true` を返します
- `unaryPred` は、要素に対して条件を満たすかを返す関数や関数オブジェクトです
> - `std::none_of(itFirst, itLast, unaryPred)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<double> a = { 36.2, 36.6, 36.9, 40.1, 37.2 };
	std::vector<double> b = { 36.2, 36.6, 36.9 };
	std::vector<double> c;

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	// 37.5℃ 以上の人は 0 人？
	std::cout << std::none_of(a.begin(), a.end(), [](double t) { return 37.5 <= t; }) << '\n';
	std::cout << std::none_of(b.begin(), b.end(), [](double t) { return 37.5 <= t; }) << '\n';
	std::cout << std::none_of(c.begin(), c.end(), [](double t) { return 37.5 <= t; }) << '\n';
}
```
```txt:出力
false
true
true
```

## 2.4 指定した値と同じ要素の個数を数える
- `std::count(itFirst, itLast, value)` は、範囲 `[itFirst, itLast)` に存在する、`value` と等しい要素の個数を返します
> - `std::count(itFirst, itLast, value)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> coins = { 10, 10, 50, 100, 1, 10, 500, 10 };
	// 10 の個数
	std::cout << std::count(coins.begin(), coins.end(), 10) << '\n';
	// 5 の個数
	std::cout << std::count(coins.begin(), coins.end(), 5) << '\n';

	std::cout << "---\n";

	std::vector<std::string> words = { "apple", "bird", "apple", "cat" };
	// "apple" の個数
	std::size_t n = std::count(words.begin(), words.end(), "apple");
	std::cout << n << '\n';
	// "dog の個数
	std::cout << std::count(words.begin(), words.end(), "dog") << '\n';
}
```
```txt:出力
4
0
---
2
0
```

## 2.5 条件を満たす要素の個数を数える
- `std::count_if(itFirst, itLast, unaryPred)` は、範囲 `[itFirst, itLast)` に存在する、条件 `unaryPred` を満たす要素の個数を返します
- `unaryPred` は、要素に対して条件を満たすかを返す関数や関数オブジェクトです
> - `std::count_if(itFirst, itLast, unaryPred)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> coins = { 10, 10, 50, 100, 1, 10, 500, 10 };
	// 10 以下の要素の個数
	std::cout << std::count_if(coins.begin(), coins.end(), [](int n){ return n <= 10; }) << '\n';
	// 1 または 5 の個数
	std::cout << std::count_if(coins.begin(), coins.end(), [](int n){ return (n == 1) || (n == 5); }) << '\n';

	std::cout << "---\n";

	std::vector<std::string> words = { "apple", "bird", "apple", "cat" };
	// 3 文字の単語の個数
	std::size_t n = std::count_if(words.begin(), words.end(), [](const std::string& s) { return s.size() == 3; });
	std::cout << n << '\n';
	// 4 文字以下の単語の個数
	std::cout << std::count_if(words.begin(), words.end(), [](const std::string& s) { return s.size() <= 4; }) << '\n';
}
```
```txt:出力
5
1
---
1
2
```

## 2.6 指定した値と等しい要素が最初に現れる位置を調べる
![](https://storage.googleapis.com/zenn-user-upload/merltfiqqm7jouov7jnz4kvxewt7)
- `std::find(itFirst, itLast, value)` は、範囲 `[itFirst, itLast)` の中で `value` と等しい最初の要素の位置のイテレータを返します
- 要素の中に `value` が見つからなかった場合は `itLast` を返します
- イテレータの指す値が配列の何番目にあるかを整数値で得たい場合は、`std::distance(itFirst, itLast)` に、範囲の先頭のイテレータと、`std::find()` が返したイテレータを渡し、その間の距離を求めます
> - `std::find(itFirst, itLast, value)` の計算量: $O(N)$
> - `std::distance(itFirst, itLast)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };
	// 4 を検索
	auto it = std::find(numbers.begin(), numbers.end(), 4);
	if (it == numbers.end()) // itLast と同じなら見つからなかったということ
	{
		std::cout << "not found\n";
	}
	else
	{
		// std::distance で 2 つの位置の距離を計算
		std::cout << "pos: " << std::distance(numbers.begin(), it) << '\n';
	}

	// 0 を検索
	it = std::find(numbers.begin(), numbers.end(), 0);
	if (it == numbers.end()) // itLast と同じなら見つからなかったということ
	{
		std::cout << "not found\n";
	}
	else
	{
		// std::distance で 2 つの位置の距離を計算
		std::cout << "pos: " << std::distance(numbers.begin(), it) << '\n';
	}

	std::cout << "---\n";

	std::vector<std::string> words = { "apple", "cat", "bird", "apple" };
	// "apple" を検索
	auto it2 = std::find(words.begin(), words.end(), "apple");
	if (it2 == words.end()) // itLast と同じなら見つからなかったということ
	{
		std::cout << "not found\n";
	}
	else
	{
		// std::distance で 2 つの位置の距離を計算
		std::cout << "pos: " << std::distance(words.begin(), it2) << '\n';
	}

	// "dog" を検索
	it2 = std::find(words.begin(), words.end(), "dog");
	if (it2 == words.end()) // itLast と同じなら見つからなかったということ
	{
		std::cout << "not found\n";
	}
	else
	{
		// std::distance で 2 つの位置の距離を計算
		std::cout << "pos: " << std::distance(words.begin(), it2) << '\n';
	}
}
```
```txt:出力
pos: 4
not found
---
pos: 0
not found
```

## 2.7 条件を満たす要素が最初に現れる位置を調べる
![](https://storage.googleapis.com/zenn-user-upload/y41i3iwzyzxs1pyxq7l2ym3bbzkx)
- `std::find_if(itFirst, itLast, unaryPred)` は、範囲 `[itFirst, itLast)` の中で、条件 `unaryPred` を満たす最初の要素の位置のイテレータを返します
- 要素の中に `value` が見つからなかった場合は `itLast` を返します
- イテレータの指す値が配列の何番目にあるかを整数値で得たい場合は、`std::distance(itFirst, itLast)` に、範囲の先頭のイテレータと、`std::find_if()` が返したイテレータを渡し、その間の距離を求めます
> - `std::find_if(itFirst, itLast, unaryPred)` の計算量: $O(N)$
> - `std::distance(itFirst, itLast)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };
	// 偶数を検索
	auto it = std::find_if(numbers.begin(), numbers.end(), [](int n) { return (n % 2 == 0); });
	if (it == numbers.end()) // itLast と同じなら見つからなかったということ
	{
		std::cout << "not found\n";
	}
	else
	{
		// std::distance で 2 つの位置の距離を計算
		std::cout << "pos: " << std::distance(numbers.begin(), it) << '\n';
	}

	// 負の数を検索
	it = std::find_if(numbers.begin(), numbers.end(), [](int n) { return (n < 0); });
	if (it == numbers.end()) // itLast と同じなら見つからなかったということ
	{
		std::cout << "not found\n";
	}
	else
	{
		// std::distance で 2 つの位置の距離を計算
		std::cout << "pos: " << std::distance(numbers.begin(), it) << '\n';
	}

	std::cout << "---\n";

	std::vector<std::string> words = { "apple", "cat", "bird", "apple" };
	// 6 文字の単語を検索
	auto it2 = std::find_if(words.begin(), words.end(), [](const std::string& s) { return (s.size() == 6); });
	if (it2 == words.end()) // itLast を同じなら見つからなかったということ
	{
		std::cout << "not found\n";
	}
	else
	{
		// std::distance で 2 つの位置の距離を計算
		std::cout << "pos: " << std::distance(words.begin(), it2) << '\n';
	}

	// "bird" または "cat" を検索
	it2 = std::find_if(words.begin(), words.end(), [](const std::string& s) { return (s == "bird") || (s == "cat"); });
	if (it2 == words.end()) // itLast を同じなら見つからなかったということ
	{
		std::cout << "not found\n";
	}
	else
	{
		// std::distance で 2 つの位置の距離を計算
		std::cout << "pos: " << std::distance(words.begin(), it2) << '\n';
	}
}
```
```txt:出力
pos: 3
not found
---
not found
pos: 1
```


# 3. 範囲に対する一般的な操作

## 3.1 範囲の要素を指定した値で埋める
- `std::fill(itFirst, itLast, value)` は、イテレータで指定した範囲 `[itFirst, itLast)` のすべての要素を `value` にします
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { 1, 2, 4, 8 };
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	// 全要素を -1 にする
	std::fill(numbers.begin(), numbers.end(), -1);
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	std::vector<std::string> words = { "apple", "bird", "cat" };
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';

	// 全要素を "dog" にする
	std::fill(words.begin(), words.end(), "dog");
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
1 2 4 8
-1 -1 -1 -1
---
apple bird cat
dog dog dog
```

## 3.2 指定した値と等しい要素をすべて削除する
![](https://storage.googleapis.com/zenn-user-upload/14n9ju6cs33n08nhzgt2guxhvfrs)  
![](https://storage.googleapis.com/zenn-user-upload/9bnh7kb5j8n1auxnlqx0lvjakmpz)  
![](https://storage.googleapis.com/zenn-user-upload/265t7mqtqmoln39qis2v6gtp645t)  
- `std::remove(itFirst, itLast, value)` は、イテレータで指定した範囲 `[itFirst, itLast)` について、前半が、`value` に等しい要素を除外した有効範囲となるよう並びかえ、それ以降は無効範囲とし、有効範囲の終端イテレータを返します
- 有効範囲に残る要素の前後関係は元の順序が維持されます
- 無効範囲の要素がどうなっているかは未規定で、値を読み取っても意味はありません（除外した要素や古い要素がゴミとして残っています）
- `std::vector` の `.erase(itFirst, itLast)` は、イテレータで指定した範囲 `[itFirst, itLast)` の要素を配列から削除し、その分だけ配列の要素数を縮小します
- `std::vector` の `.erase(itFirst, itLast)` において、`itFirst` を `std::remove()` が返したイテレータ、`itLast` を配列の終端イテレータにすることで、配列から指定した値と等しい要素をすべて削除できます
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };
	std::cout << "size: " << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	// 配列から 3 を削除
	numbers.erase(std::remove(numbers.begin(), numbers.end(), 3), numbers.end());
	std::cout << "size: " << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	std::vector<std::string> words = { "apple", "bird", "cat", "apple", "dog" };
	std::cout << "size: " << words.size() << '\n';
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';

	// 配列から "apple" を削除
	words.erase(std::remove(words.begin(), words.end(), "apple"), words.end());
	std::cout << "size: " << words.size() << '\n';
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
size: 10
5 3 3 2 4 2 4 3 5 1 
size: 7
5 2 4 2 4 5 1 
---
size: 5
apple bird cat apple dog 
size: 3
bird cat dog 
```


## 3.3 条件を満たす要素をすべて削除する
![](https://storage.googleapis.com/zenn-user-upload/0eawt9k48qk0dsztugf6894is2g9)  
![](https://storage.googleapis.com/zenn-user-upload/5c076a59mn7r67r3lmc686eifow5)  
![](https://storage.googleapis.com/zenn-user-upload/10f3zfxoorsu6wdqgbgc8dmjl216)  
- `std::remove_if(itFirst, itLast, unaryPred)` は、イテレータで指定した範囲 `[itFirst, itLast)` について、前半が、`unaryPred` を満たす要素を除外した有効範囲となるよう並びかえ、それ以降は無効範囲とし、有効範囲の終端イテレータを返します
- 有効範囲に残る要素の前後関係は元の順序が維持されます
- 無効範囲の要素がどうなっているかは未規定で、値を読み取っても意味はありません（除外した要素や古い要素がゴミとして残っています）
- `std::vector` の `.erase(itFirst, itLast)` は、イテレータで指定した範囲 `[itFirst, itLast)` の要素を配列から削除し、その分だけ配列の要素数を縮小します
- `std::vector` の `.erase(itFirst, itLast)` において、`itFirst` を `std::remove_if()` が返したイテレータ、`itLast` を配列の終端イテレータにすることで、配列から条件を満たす要素をすべて削除できます
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };
	std::cout << "size: " << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	// 配列から偶数を削除
	numbers.erase(std::remove_if(numbers.begin(), numbers.end(), [](int n){ return (n % 2 == 0); }), numbers.end());
	std::cout << "size: " << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	std::vector<std::string> words = { "apple", "bird", "cat", "apple", "dog" };
	std::cout << "size: " << words.size() << '\n';
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';

	// 配列から 3 文字の単語を削除
	words.erase(std::remove_if(words.begin(), words.end(), [](const std::string& s){ return (s.size() == 3); }), words.end());
	std::cout << "size: " << words.size() << '\n';
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
size: 10
5 3 3 2 4 2 4 3 5 1 
size: 6
5 3 3 3 5 1 
---
size: 5
apple bird cat apple dog 
size: 3
apple bird apple 
```

## 3.4 範囲の要素を逆順にする
- `std::reverse(itFirst, itLast)` は、イテレータで指定した範囲 `[itFirst, itLast)` にある要素を逆順にします
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { 1, 3, 5, 2, 4, 0 };
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	// 順序を逆順に
	std::reverse(numbers.begin(), numbers.end());
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	std::string word = "computer";
	std::cout << word << '\n';

	// 順序を反転
	std::reverse(word.begin(), word.end());
	std::cout << word << '\n';
}
```
```txt:出力
1 3 5 2 4 0
0 4 2 5 3 1
---
computer
retupmoc
```

## 3.5 要素を回転させる
![](https://storage.googleapis.com/zenn-user-upload/00ubzrhcws3ywiv75aplss1u2tkj)  
![](https://storage.googleapis.com/zenn-user-upload/4xyfcvmfvg63ulzjgvnoc7pwn29r)  
![](https://storage.googleapis.com/zenn-user-upload/h39ztens5lp1erqgrudaomemgy9i)  
- `std::rotate(itFirst, itMiddle, itLast)` は、イテレータで指定した範囲 `[itFirst, itLast)` について、イテレータ `itMiddle` で指した要素が先頭の要素になるよう、左方向に回転させて並びかえます
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	// 3 個分、左に回転
	std::rotate(numbers.begin(), numbers.begin() + 3, numbers.end());
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	std::string word = "computer";
	std::cout << word << '\n';

	// 5 個分、左に回転
	std::rotate(word.begin(), word.begin() + 5, word.end());
	std::cout << word << '\n';
}
```
```txt:出力
0 1 2 3 4 5 6 7 8 9
3 4 5 6 7 8 9 0 1 2
---
computer
tercompu
```

## 3.6 同じ値が隣同士にならないよう要素を削除する
![](https://storage.googleapis.com/zenn-user-upload/ju1t0e4osdok3jw0g78dfjuvu4em)  
![](https://storage.googleapis.com/zenn-user-upload/oal49bx7uj5j3pc46so8nx3ngnwm)  
![](https://storage.googleapis.com/zenn-user-upload/ajffs25zekco5k7e1poxetj6but4)  
- `std::unique(itFirst, itLast)` は、イテレータで指定した範囲 `[itFirst, itLast)` について、前半が、隣同士で重複する要素を除外した有効範囲となるよう並びかえ、それ以降は無効範囲とし、有効範囲の終端イテレータを返します
- 有効範囲に残る要素の前後関係は元の順序が維持されます
- 無効範囲の要素がどうなっているかは未規定で、値を読み取っても意味はありません（除外した要素や古い要素がゴミとして残っています）
- `std::vector` の `.erase(itFirst, itLast)` は、イテレータで指定した範囲 `[itFirst, itLast)` の要素を配列から削除し、その分だけ配列の要素数を縮小します
- `std::vector` の `.erase(itFirst, itLast)` において、`itFirst` を `std::unique()` が返したイテレータ、`itLast` を配列の終端イテレータにすることで、配列から隣同士で重複する要素を削除できます
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { 1, 1, 1, 3, 4, 3, 3, 2, 0, 2 };
	std::cout << "size: " << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	// 隣同士で重複する要素を削除
	numbers.erase(std::unique(numbers.begin(), numbers.end()), numbers.end());
	std::cout << "size: " << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	std::string word = "communication";
	std::cout << "size: " << word.size() << '\n';
	std::cout << word << '\n';

	// 隣同士で重複する要素を削除
	word.erase(std::unique(word.begin(), word.end()), word.end());
	std::cout << "size: " << word.size() << '\n';
	std::cout << word << '\n';
}
```
```txt:出力
size: 10
1 1 1 3 4 3 3 2 0 2
size: 7
1 3 4 3 2 0 2
---
size: 13
communication
size: 12
comunication
```

## 3.7 配列から重複する要素を無くす
![](https://storage.googleapis.com/zenn-user-upload/8ad2ohrp1h314mi1ae53hundxxg6)  
![](https://storage.googleapis.com/zenn-user-upload/wmw38txnyspy7lhguqp5ievf3nln)  
![](https://storage.googleapis.com/zenn-user-upload/zdcgymqoz7tlorn7nq3bpsb7sbcw)  
![](https://storage.googleapis.com/zenn-user-upload/2n8y9c8nlcrk1iz2q7ciicnqqtps)  
- ある配列について、ソートしてから「3.6 同じ値が隣同士にならないよう要素を削除する」の操作を行うと、重複する要素を配列から削除できます
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { 1, 1, 1, 3, 4, 3, 3, 2, 0, 2 };
	std::cout << "size: " << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	// ソート
	std::sort(numbers.begin(), numbers.end());
	std::cout << "size: " << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	// 隣同士で重複する要素を削除
	numbers.erase(std::unique(numbers.begin(), numbers.end()), numbers.end());
	std::cout << "size: " << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	std::string word = "communication";
	std::cout << "size: " << word.size() << '\n';
	std::cout << word << '\n';

	// ソート
	std::sort(word.begin(), word.end());
	std::cout << "size: " << word.size() << '\n';
	std::cout << word << '\n';

	// 隣同士で重複する要素を削除
	word.erase(std::unique(word.begin(), word.end()), word.end());
	std::cout << "size: " << word.size() << '\n';
	std::cout << word << '\n';
}
```
```txt:出力
size: 10
1 1 1 3 4 3 3 2 0 2
size: 10
0 1 1 1 2 2 3 3 3 4
size: 5
0 1 2 3 4
---
size: 13
communication
size: 13
acciimmnnootu
size: 8
acimnotu
```


# 4. 範囲に対するソート

## 4.1 要素を小さい順にソートする
![](https://storage.googleapis.com/zenn-user-upload/pm5dghn9gr65rhybj6mk3u45t39o)
- `std::sort(itFirst, itLast)` は、範囲 `[itFirst, itLast)` にある要素を、小さい順になるようにソートします
- イテレータはランダムアクセスイテレータである必要があります
> - `std::sort(itFirst, itLast)` の計算量:  $O(N log N)$
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { 1, 5, 0, 2, 4, 6, 3, 9, 8, 7 };
	// 小さい順にソート
	std::sort(numbers.begin(), numbers.end());
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	std::vector<std::string> words = { "bird", "cat", "dog", "apple" };
	// 小さい順にソート
	std::sort(words.begin(), words.end());
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
0 1 2 3 4 5 6 7 8 9 
---
apple bird cat dog 
```

## 4.2 要素を大きい順にソートする
![](https://storage.googleapis.com/zenn-user-upload/ubuylg828bh5vefi0ilq8h8fr3q0)
- `std::sort(itFirst, itLast, comp)` は、範囲 `[itFirst, itLast)` にある要素を、`comp` による比較の結果でソートします
- `comp` を `std::greater<>{}` にすることで、大きい順になるようにソートできます
- イテレータはランダムアクセスイテレータである必要があります
> - `std::sort(itFirst, itLast, comp)` の計算量:  $O(N log N)$
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <functional> // std::greater のため

int main()
{
	std::vector<int> numbers = { 1, 5, 0, 2, 4, 6, 3, 9, 8, 7 };
	// 大きい順にソート
	std::sort(numbers.begin(), numbers.end(), std::greater<>{});
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	std::vector<std::string> words = { "bird", "cat", "dog", "apple" };
	// 大きい順にソート
	std::sort(words.begin(), words.end(), std::greater<>{});
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
9 8 7 6 5 4 3 2 1 0 
---
dog cat bird apple 
```

`itFirst`, `itLast` にリバースイテレータを使う、次のような短い書き方も可能です。通常のソートと誤読しないよう注意しましょう。
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <functional> // std::greater のため

int main()
{
	std::vector<int> numbers = { 1, 5, 0, 2, 4, 6, 3, 9, 8, 7 };
	// 大きい順にソート
	std::sort(numbers.rbegin(), numbers.rend());
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	std::vector<std::string> words = { "bird", "cat", "dog", "apple" };
	// 大きい順にソート
	std::sort(words.rbegin(), words.rend());
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
9 8 7 6 5 4 3 2 1 0 
---
dog cat bird apple 
```

## 4.3 上位 N 個までを求めるソートをする
![](https://storage.googleapis.com/zenn-user-upload/piahsffvr41zhkm6b79507xvo7e0)
- `std::partial_sort(itFirst, itMiddle, itLast)` は、範囲 `[itFirst, itLast)` にある要素をソートし、結果として `[itFirst, itMiddle)` の範囲に上位 `(itMiddle - itFirst)` 個の要素がソート済みで並ぶようにし、それ以降についてはソートが未完了のままにすることで、`std::sort(itFirst, itLast)` より計算回数を少なくします
> - `std::partial_sort(itFirst, itMiddle, itLast)` の計算量:  $O(N log N)$

::: message
使用されているソートアルゴリズムの関係で、全体に対して大きい部分に `std::partial_sort()` を使う場合、`std::sort()` よりも遅いことがあります。その場合は、代わりに `std::nth_element()` 後に `std::sort()` による部分的なソートを行うのが効果的です。下記が参考記事です。
- [sort() vs. partial_sort() vs. nth_element() + sort() in C++ STL](https://www.geeksforgeeks.org/sort-vs-partial_sort-vs-nth_element-sort-in-c-stl/), GeeksforGeeks
:::
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { 1, 5, 0, 2, 4, 6, 3, 9, 8, 7 };
	// 上位 4 個まで小さい順にソート
	std::partial_sort(numbers.begin(), numbers.begin() + 4, numbers.end());
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	std::vector<std::string> words = { "bird", "cat", "dog", "apple" };
	// 上位 2 個まで小さい順にソート
	std::partial_sort(words.begin(), words.begin() + 2, words.end());
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力（例）
0 1 2 3 5 6 4 9 8 7 
---
apple bird dog cat 
```

## 4.4 N 番目に小さい要素を求める
![](https://storage.googleapis.com/zenn-user-upload/hwom96sdwa49wi3tsv5il3ijrouk)
- `std::nth_element(itFirst, itNth, itLast)` は、範囲 `[itFirst, itLast)` にある要素を不完全にソートし、結果として `itNth` の位置に、完全なソートを行ったときと同じ要素が置かれるようにし、それより前はその要素より小さく、それ以降はその要素より大きい要素が並ぶということだけ保証する、部分的なソートを行います
- 上位 N 番目の要素を求めたいだけの時、`std::sort(itFirst, itLast)` や `std::partial_sort(itFirst, itNth, itLast)` を使うより計算量を小さくできます
> - `std::nth_element(itFirst, itNth, itLast)` の計算量:  平均 $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::vector<int> numbers = { 1, 5, 0, 2, 4, 6, 3, 9, 8, 7 };
	// 4 番目に小さい要素を探す
	std::nth_element(numbers.begin(), numbers.begin() + 3, numbers.end());
	std::cout << "4th : " << numbers[3] << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	std::vector<std::string> words = { "bird", "cat", "dog", "apple", "egg" };
	// 2 番目に小さい要素を探す
	std::nth_element(words.begin(), words.begin() + 1, words.end());
	std::cout << "2nd : " << words[1] << '\n';
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
4th : 3
1 0 2 3 4 5 6 9 8 7 
---
2nd : bird
apple bird dog cat egg 
```


# 5. ソート済みの範囲に対する二分探索

## 5.1 ある値を範囲に挿入するとして、ソートされた状態を維持できる最も左の位置 (lower_bound) を二分探索で取得する
- `std::lower_bound(itFirst, itLast, value)` は、ソート済みの範囲 `[itFirst, itLast)` に対して値 `value` を挿入するとして、ソートされた状態を壊さない最も左の位置を二分探索し、その位置を指すイテレータを返します
- 何番目かを整数値で得たい場合は、`std::distance(itFirst, itLast)` に、範囲の先頭のイテレータと `std::lower_bound()` が返したイテレータを渡して、その間の距離を求めます
> - `std::lower_bound(itFirst, itLast, value)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(\log N)$, そうでなければ $O(N)$
> - `std::distance(itFirst, itLast)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$

::: message
`std::lower_bound()` に渡すイテレータがランダムアクセスイテレータでない場合（具体的には `std::set` や `std::multiset` のイテレータ）、関数内部でのイテレータの移動の処理に線形時間を要するため、計算量は $O(N)$ に悪化します。代わりにそれぞれのコンテナのメンバ関数 `std::set::lower_bound()`, `std::multiset::lower_bound()` を使えば、データ構造の特性に合わせた実装になっているため、計算量は $O(\log N)$ になります。
:::

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	// ソート済みの状態
	std::vector<int> coins = { 1, 5, 10, 50, 100, 500 };

	auto it = std::lower_bound(coins.begin(), coins.end(), 0);
	std::cout << "(0): " << std::distance(coins.begin(), it) << '\n';

	it = std::lower_bound(coins.begin(), coins.end(), 1);
	std::cout << "(1): " << std::distance(coins.begin(), it) << '\n';

	it = std::lower_bound(coins.begin(), coins.end(), 2);
	std::cout << "(2): " << std::distance(coins.begin(), it) << '\n';

	it = std::lower_bound(coins.begin(), coins.end(), 5);
	std::cout << "(5): " << std::distance(coins.begin(), it) << '\n';

	it = std::lower_bound(coins.begin(), coins.end(), 499);
	std::cout << "(499): " << std::distance(coins.begin(), it) << '\n';

	it = std::lower_bound(coins.begin(), coins.end(), 500);
	std::cout << "(500): " << std::distance(coins.begin(), it) << '\n';

	it = std::lower_bound(coins.begin(), coins.end(), 501);
	std::cout << "(501): " << std::distance(coins.begin(), it) << '\n';

	std::cout << "---\n";

	// ソート済みの状態
	std::vector<std::string> words = { "apple", "bird", "cat" };

	auto it2 = std::lower_bound(words.begin(), words.end(), "alarm");
	words.insert(it2, "alarm");

	it2 = std::lower_bound(words.begin(), words.end(), "banana");
	words.insert(it2, "banana");

	it2 = std::lower_bound(words.begin(), words.end(), "dog");
	words.insert(it2, "dog");

	// ソート済みの状態が保たれていることを確認
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
(0): 0
(1): 0
(2): 1
(5): 1
(499): 5
(500): 5
(501): 6
---
alarm apple banana bird cat dog 
```

## 5.2 ある値を範囲に挿入するとして、ソートされた状態を維持できる最も右の位置 (upper_bound) を二分探索で取得する
- `std::upper_bound(itFirst, itLast, value)` は、ソート済みの範囲 `[itFirst, itLast)` に対して値 `value` を挿入するとして、ソートされた状態を壊さない最も右の位置を二分探索し、その位置を指すイテレータを返します
- 何番目かを整数値で得たい場合は、`std::distance(itFirst, itLast)` に、範囲の先頭のイテレータと `std::upper_bound()` が返したイテレータを渡して、その間の距離を求めます
> - `std::upper_bound(itFirst, itLast, value)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(\log N)$, そうでなければ $O(N)$
> - `std::distance(itFirst, itLast)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$

::: message
`std::upper_bound()` に渡すイテレータがランダムアクセスイテレータでない場合（具体的には `std::set` や `std::multiset` のイテレータ）、関数内部でのイテレータの移動の処理に線形時間を要するため、計算量は $O(N)$ に悪化します。代わりにそれぞれのコンテナのメンバ関数 `std::set::upper_bound()`, `std::multiset::upper_bound()` を使えば、データ構造の特性に合わせた実装になっているため、計算量は $O(\log N)$ になります。
:::

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	// ソート済みの状態
	std::vector<int> coins = { 1, 5, 10, 50, 100, 500 };
	
	auto it = std::upper_bound(coins.begin(), coins.end(), 0);
	std::cout << "(0): " << std::distance(coins.begin(), it) << '\n';

	it = std::upper_bound(coins.begin(), coins.end(), 1);
	std::cout << "(1): " << std::distance(coins.begin(), it) << '\n';

	it = std::upper_bound(coins.begin(), coins.end(), 2);
	std::cout << "(2): " << std::distance(coins.begin(), it) << '\n';

	it = std::upper_bound(coins.begin(), coins.end(), 5);
	std::cout << "(5): " << std::distance(coins.begin(), it) << '\n';

	it = std::upper_bound(coins.begin(), coins.end(), 499);
	std::cout << "(499): " << std::distance(coins.begin(), it) << '\n';

	it = std::upper_bound(coins.begin(), coins.end(), 500);
	std::cout << "(500): " << std::distance(coins.begin(), it) << '\n';

	it = std::upper_bound(coins.begin(), coins.end(), 501);
	std::cout << "(501): " << std::distance(coins.begin(), it) << '\n';

	std::cout << "---\n";

	// ソート済みの状態
	std::vector<std::string> words = { "apple", "bird", "cat" };

	auto it2 = std::upper_bound(words.begin(), words.end(), "alarm");
	words.insert(it2, "alarm");

	it2 = std::upper_bound(words.begin(), words.end(), "banana");
	words.insert(it2, "banana");

	it2 = std::upper_bound(words.begin(), words.end(), "dog");
	words.insert(it2, "dog");

	// ソート済みの状態が保たれていることを確認
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
(0): 0
(1): 1
(2): 1
(5): 2
(499): 5
(500): 6
(501): 6
---
alarm apple banana bird cat dog 
```

## 5.3 lower_bound と upper_bound の結果を同時に取得する
- `std::equal_range(itFirst, itLast, value)` は、`std::lower_bound(itFirst, itLast, value)` と `std::upper_bound(itFirst, itLast, value)` の結果を `std::pair` で返します
- 探索時に情報を共有できるため、`std::lower_bound(itFirst, itLast, value)`, `std::upper_bound(itFirst, itLast, value)` を個別に呼ぶよりも定数倍高速化されます
> - `std::equal_range(itFirst, itLast, value)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(\log N)$, そうでなければ $O(N)$
> - `std::distance(itFirst, itLast)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$

::: message
`std::equal_range()` に渡すイテレータがランダムアクセスイテレータでない場合（具体的には `std::set` や `std::multiset` のイテレータ）、関数内部でのイテレータの移動の処理に線形時間を要するため、計算量は $O(N)$ に悪化します。代わりにそれぞれのコンテナのメンバ関数 `std::set::equal_range()`, `std::multiset::equal_range()` を使えば、データ構造の特性に合わせた実装になっているため、計算量は $O(\log N)$ になります。
:::

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// ソート済みの状態
	std::vector<int> coins = { 1, 1, 5, 10, 10, 10, 500 };

	auto p = std::equal_range(coins.begin(), coins.end(), 0);
	std::cout << "(0): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';

	p = std::equal_range(coins.begin(), coins.end(), 1);
	std::cout << "(1): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';

	p = std::equal_range(coins.begin(), coins.end(), 2);
	std::cout << "(2): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';

	p = std::equal_range(coins.begin(), coins.end(), 5);
	std::cout << "(5): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';

	p = std::equal_range(coins.begin(), coins.end(), 10);
	std::cout << "(10): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';

	p = std::equal_range(coins.begin(), coins.end(), 500);
	std::cout << "(500): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';

	p = std::equal_range(coins.begin(), coins.end(), 501);
	std::cout << "(501): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';
}
```
```txt:出力
(0): 0 -> 0
(1): 0 -> 2
(2): 2 -> 2
(5): 2 -> 3
(10): 3 -> 6
(500): 6 -> 7
(501): 7 -> 7
```

# 6. ソート済みの二つの範囲に対する集合演算や操作

## 6.1 二つの集合の少なくとも片方に存在する要素からなる集合（和集合）を得る
- `std::set_union(itFirst1, itLast1, itFirst2, itLast2, itOut)` は、ソート済みの範囲 `[itFirst1, itLast1)` および `[itFirst2, itLast2)` の少なくとも片方に存在する要素からなる集合（和集合）の要素を `itOut` に出力していきます
- `{0, 1}` と `{1, 2}` の和集合は `{0, 1, 2}` です
- `{0, 0, 1, 1}` と `{1, 2}` の和集合は `{0, 0, 1, 1, 2}` です
- `std::back_inserter()` と組み合わせることで、和集合の結果を別の配列に格納することができます
- `itOut` に出力される要素の順序はソート済みです
> - `std::set_union(itFirst1, itLast1, itFirst2, itLast2, itOut)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// 両方ともソート済みの状態
	std::vector<int> a = { 0, 1, 2, 3 };
	std::vector<int> b = { 2, 3, 4, 5 };
	// 結果を格納する配列
	std::vector<int> c;

	// a と b の和集合を得る
	std::set_union(a.begin(), a.end(), b.begin(), b.end(), std::back_inserter(c));
	for (const auto& n : c)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	// 両方ともソート済みの状態
	std::vector<int> d = { 0, 0, 1, 1, 2, 2 };
	std::vector<int> e = { 1, 2, 2, 3, 3 };
	// 結果を格納する配列
	std::vector<int> f;

	// d と e の和集合を得る
	std::set_union(d.begin(), d.end(), e.begin(), e.end(), std::back_inserter(f));
	for (const auto& n : f)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
0 1 2 3 4 5
---
0 0 1 1 2 2 3 3
```

## 6.2 二つの集合の両方に存在する要素からなる集合（積集合）を得る
- `std::set_intersection(itFirst1, itLast1, itFirst2, itLast2, itOut)` は、ソート済みの範囲 `[itFirst1, itLast1)` および `[itFirst2, itLast2)` の両方に存在する要素からなる集合（積集合）の要素を `itOut` に出力していきます
- `{0, 1}` と `{1, 2}` の積集合は `{1}` です
- `{0, 1}` と `{2, 3}` の積集合は `{}` です
- `{0, 0, 1, 1, 2, 2}` と `{1, 1, 2}` の積集合は `{1, 1, 2}` です
- `std::back_inserter()` と組み合わせることで、積集合の結果を別の配列に格納することができます
- `itOut` に出力される要素の順序はソート済みです
> - `std::set_intersection(itFirst1, itLast1, itFirst2, itLast2, itOut)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// 両方ともソート済みの状態
	std::vector<int> a = { 0, 1, 2, 3 };
	std::vector<int> b = { 2, 3, 4, 5 };
	// 結果を格納する配列
	std::vector<int> c;

	// a と b の積集合を得る
	std::set_intersection(a.begin(), a.end(), b.begin(), b.end(), std::back_inserter(c));
	for (const auto& n : c)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	// 両方ともソート済みの状態
	std::vector<int> d = { 0, 0, 1, 1, 2, 2 };
	std::vector<int> e = { 1, 2, 2, 3, 3 };
	// 結果を格納する配列
	std::vector<int> f;

	// d と e の積集合を得る
	std::set_intersection(d.begin(), d.end(), e.begin(), e.end(), std::back_inserter(f));
	for (const auto& n : f)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
2 3
---
1 2 2
```

## 6.3 二つの集合について、前者の中から後者に属する要素を取り除いた集合（差集合）を得る
- `std::set_difference(itFirst1, itLast1, itFirst2, itLast2, itOut)` は、ソート済みの範囲 `[itFirst1, itLast1)` および `[itFirst2, itLast2)` について、前者の中から後者に属する要素を取り除いた集合（差集合）の要素を `itOut` に出力していきます
- `{0, 1, 2, 3}` と `{1, 2, 4, 5}` の差集合は `{0, 3}` です
- `{0, 1}` と `{0, 1}` の積集合は `{}` です
- `{0, 0, 1, 1, 2, 2}` と `{1, 1, 2}` の積集合は `{0, 0, 2}` です
- `std::back_inserter()` と組み合わせることで、差集合の結果を別の配列に格納することができます
- `itOut` に出力される要素の順序はソート済みです
> - `std::set_difference(itFirst1, itLast1, itFirst2, itLast2, itOut)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// 両方ともソート済みの状態
	std::vector<int> a = { 0, 1, 2, 3 };
	std::vector<int> b = { 2, 3, 4, 5 };
	// 結果を格納する配列
	std::vector<int> c;

	// a と b の差集合を得る
	std::set_difference(a.begin(), a.end(), b.begin(), b.end(), std::back_inserter(c));
	for (const auto& n : c)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	// 両方ともソート済みの状態
	std::vector<int> d = { 0, 0, 1, 1, 2, 2 };
	std::vector<int> e = { 1, 2, 2, 3, 3 };
	// 結果を格納する配列
	std::vector<int> f;

	// d と e の差集合を得る
	std::set_difference(d.begin(), d.end(), e.begin(), e.end(), std::back_inserter(f));
	for (const auto& n : f)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
0 1
---
0 0 1
```

## 6.4 二つの集合のどちらか片方にしか存在しない要素からなる集合（対称差集合）を得る
- `std::set_symmetric_difference(itFirst1, itLast1, itFirst2, itLast2, itOut)` は、ソート済みの範囲 `[itFirst1, itLast1)` および `[itFirst2, itLast2)` について、どちらか片方にしか存在しない要素からなる集合（対称差集合）の要素を `itOut` に出力していきます
- `{0, 1, 2, 3}` と `{1, 2, 4, 5}` の対称差集合は `{0, 4, 5}` です
- `{0, 1}` と `{0, 1}` の対称差集合は `{}` です
- `{0, 0, 1, 1, 2, 2}` と `{1, 1, 2}` の対称差集合は `{0, 0, 2}` です
- `std::back_inserter()` と組み合わせることで、対称差集合の結果を別の配列に格納することができます
- `itOut` に出力される要素の順序はソート済みです
> - `std::set_symmetric_difference(itFirst1, itLast1, itFirst2, itLast2, itOut)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// 両方ともソート済みの状態
	std::vector<int> a = { 0, 1, 2, 3 };
	std::vector<int> b = { 2, 3, 4, 5 };
	// 結果を格納する配列
	std::vector<int> c;

	// a と b の対称差集合を得る
	std::set_symmetric_difference(a.begin(), a.end(), b.begin(), b.end(), std::back_inserter(c));
	for (const auto& n : c)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	// 両方ともソート済みの状態
	std::vector<int> d = { 0, 0, 1, 1, 2, 2 };
	std::vector<int> e = { 1, 2, 2, 3, 3 };
	// 結果を格納する配列
	std::vector<int> f;

	// d と e の対称差集合を得る
	std::set_symmetric_difference(d.begin(), d.end(), e.begin(), e.end(), std::back_inserter(f));
	for (const auto& n : f)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
0 1 4 5
---
0 0 1 3 3
```

## 6.5 二つのソート済みの集合をマージした、ソート済みの集合を得る
- `std::merge(itFirst1, itLast1, itFirst2, itLast2, itOut)` は、ソート済みの範囲 `[itFirst1, itLast1)` および `[itFirst2, itLast2)` の要素をまとめた集合の要素を `itOut` に出力していきます
- `{0, 1, 2, 2}` と `{0, 1, 3, 4, 5}` をマージした集合は `{0, 0, 1, 1, 2, 2, 3, 4, 5}` です
- マージ後の要素数は 2 つの範囲の要素数の合計になります
- `std::back_inserter()` と組み合わせることで、マージした集合の結果を別の配列に格納することができます
- `itOut` に出力される要素の順序はソート済みです
> - `std::merge(itFirst1, itLast1, itFirst2, itLast2, itOut)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// 両方ともソート済みの状態
	std::vector<int> a = { 0, 1, 2, 3 };
	std::vector<int> b = { 2, 3, 4, 5 };
	// 結果を格納する配列
	std::vector<int> c;

	// a と b をマージした集合を得る
	std::merge(a.begin(), a.end(), b.begin(), b.end(), std::back_inserter(c));
	for (const auto& n : c)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	// 両方ともソート済みの状態
	std::vector<int> d = { 0, 0, 1, 1, 2, 2 };
	std::vector<int> e = { 1, 2, 2, 3, 3 };
	// 結果を格納する配列
	std::vector<int> f;

	// d と e をマージした集合を得る
	std::merge(d.begin(), d.end(), e.begin(), e.end(), std::back_inserter(f));
	for (const auto& n : f)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
0 1 2 2 3 3 4 5
---
0 0 1 1 1 2 2 2 2 3 3
```


# 7. 順列

## 7.1 順列を作成する
- `std::next_permutaion(itFirst, itLast)` は、範囲 `[itFirst, itLast)` について、辞書順で次にくる順列になるよう要素を並びかえます
- 次の順列が存在する場合は `true`, それ以外の場合は `false` を返します
- ソート済みの範囲から始め、`do while()` と組み合わせることで、すべての順列を列挙できます
> - `std::next_permutaion(itFirst, itLast)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	// すべての順列を列挙するためにはソート済みの状態から始める
	std::vector<int> v = { 1, 2, 3, 4 };
	do
	{
		for (const auto& n : v)
		{
			std::cout << n << ' ';
		}
		std::cout << '\n';
	} while (std::next_permutation(v.begin(), v.end())); // 辞書順で次の順列へ

	std::cout << "---\n";

	// すべての順列を列挙するためにはソート済みの状態から始める
	std::string s = "abc";
	do
	{
		std::cout << s << '\n';
	} while (std::next_permutation(s.begin(), s.end())); // 辞書順で次の順列へ
}
```
```txt:出力
1 2 3 4
1 2 4 3
1 3 2 4
1 3 4 2
1 4 2 3
1 4 3 2
2 1 3 4
2 1 4 3
2 3 1 4
2 3 4 1
2 4 1 3
2 4 3 1
3 1 2 4
3 1 4 2
3 2 1 4
3 2 4 1
3 4 1 2
3 4 2 1
4 1 2 3
4 1 3 2
4 2 1 3
4 2 3 1
4 3 1 2
4 3 2 1
---
abc
acb
bac
bca
cab
cba
```

