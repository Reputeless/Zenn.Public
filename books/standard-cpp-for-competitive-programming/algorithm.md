---
title: "<algorithm>"
free: true
---

アルゴリズムライブラリは、ある範囲の要素に対して検索、カウント、ソート、変更などの操作を行う機能を提供します。

C++20 以降では、新たに `std::ranges::` 名前空間に、既存の `std::` 名前空間のものよりも強化された機能が提供されています。新しい C++ を使える場合は、`std::ranges::` 名前空間で提供される機能を使うことで、より便利なコードや、わかりやすいエラーメッセージを得ることができます。


# 1. 最小値と最大値

## 1.1 二つの値のうち小さいほう・大きいほうの値を得る
- `std:min(a, b)` および `std::ranges::min(a, b)` は、`a` と `b` のうち小さいほうの値への const 参照を返します。
- `std::max(a, b)` および `std::ranges::max(a, b)` は、`a` と `b` のうち大きいほうの値への const 参照を返します。
- 二つの値が等しい場合は `a` への const 参照を返します。
- `std:min(a, b)` や `std::max(a, b)` において `a` と `b` が異なる型である場合、`std::min<Type>(a, b)` のように型 `Type` を明示的に指定することで、一方が `Type` に変換されます。
- `std::ranges::min(a, b)` や `std::ranges::max(a, b)` において `a` と `b` が異なる型である場合、`std::ranges::min(a, static_cast<Type>(b))` のように型をそろえます。

```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	{
		int a = 30, b = 60;
		std::cout << std::ranges::min(a, b) << '\n';
		std::cout << std::ranges::max(a, b) << '\n';
	}

	{
		double a = 0.5, b = -10.5;
		std::cout << std::ranges::min(a, b) << '\n';
		std::cout << std::ranges::max(a, b) << '\n';
	}

	{
		std::string a = "apple", b = "bird";
		std::cout << std::ranges::min(a, b) << '\n';
		std::cout << std::ranges::max(a, b) << '\n';
	}

	{
		int a = 30;
		std::size_t b = 60;
		std::cout << std::ranges::min(a, static_cast<int>(b)) << '\n';
		std::cout << std::ranges::max(a, static_cast<int>(b)) << '\n';
	}
}
```
```txt:出力
30
60
-10.5
0.5
apple
bird
30
60
```


## 1.2 三つ以上の値から最小値・最大値を得る
- `std::min({ ... })` および `std::ranges::min({ ... })` は、リスト `{ ... }` 内の要素の最小値を返します。
- `std::max({ ... })` および `std::ranges::max({ ... })` は、リスト `{ ... }` 内の要素の最大値を返します。
- リスト内の要素は同じ型である必要があります。

```cpp
#include <iostream>
#include <algorithm>

int main()
{
	{
		int a = 30, b = 60, c = 50;
		std::cout << std::ranges::min({ a, b, c }) << '\n';
		std::cout << std::ranges::max({ a, b, c }) << '\n';
	}

	{
		double a = 0.1, b = 0.2, c = 0.3, d = 0.4;
		std::cout << std::ranges::min({ a, b, c, d }) << '\n';
		std::cout << std::ranges::max({ a, b, c, d }) << '\n';
	}
}
```
```txt:出力
30
60
0.1
0.4
```


## 1.3 空でない範囲の中から最小・最大の要素を得る [🟢C++20]
- 空でない範囲の中から最小の要素を得るには、`std::ranges::min(range)` を使います。
- 空でない範囲の中から最大の要素を得るには、`std::ranges::max(range)` を使います。
- `range` の要素数が 0 の場合、実行時エラーになります。

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <deque>
#include <string>
#include <ranges>

int main()
{
	{
		std::vector<int> v = { 30, 60, 50 };
		std::cout << std::ranges::min(v) << '\n'; // 30
		std::cout << std::ranges::max(v) << '\n'; // 60
	}

	{
		std::deque<double> d = { 0.1, 0.2, 0.3, 0.4 };
		std::cout << std::ranges::min(d) << '\n'; // 0.1
		std::cout << std::ranges::max(d) << '\n'; // 0.4
	}

	{
		std::string s = "atcoder";
		std::cout << std::ranges::min(s) << '\n'; // 'a'
		std::cout << std::ranges::max(s) << '\n'; // 't'
	}

	{
		std::vector<int> v = { 9, 8, 7, 6, 5, 4, 3, 2, 1 };
		std::cout << std::ranges::min(v | std::views::take(3)) << '\n'; // 最初の三つの範囲の最小値: 7
		std::cout << std::ranges::max(v | std::views::drop(3)) << '\n'; // 最初の三つを除いた範囲の最大値: 6
	}
}
```
```txt:出力
30
60
0.1
0.4
a
t
7
6
```


## 1.4 配列の中から最小の要素とその位置を得る
- `std::min_element(irFirst, itLast)`, `std::ranges::min_element(irFirst, itLast)` および `std::ranges::min_element(range)` は、範囲 `[itFirst, itLast)` または `range` の中で最小の要素の位置を指すイテレータを返します。
- 範囲の要素数が 0 の場合、範囲の終端イテレータを返します。
- イテレータに `*` を付けると、そのイテレータが指す値にアクセスできます。
- イテレータの指す値が、配列の何番目にあるかを整数値で得るには、`std::distance(itFirst, itLast)` または `std::ranges::distance(itFirst, itLast)` に、範囲の先頭イテレータと、戻り値のイテレータを渡すことで、その間の距離を求めます。

> - `min_element()` の計算量: $O(N)$
> - `distance()` の計算量: イテレータがランダムアクセスイテレータであれば $O(1)$, それ以外の場合は $O(N)$

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <string>
#include <ranges>

int main()
{
	// 最小値だけを得る
	{
		std::vector<int> v = { -5, 10, -30, 20, 50, 0 };
		std::cout << *std::ranges::min_element(v) << '\n'; // -30
	}

	{
		std::string s = "computer";
		std::cout << *std::ranges::min_element(s) << '\n'; // 'c'
	}

	{
		std::string s = "computer";
		std::cout << *std::ranges::min_element((s.begin() + 1), s.end()) << '\n'; // 最初の一つを除いた範囲の最小値: 'e'
	}

	{
		std::string s = "computer";
		std::cout << *std::ranges::min_element(s | std::views::drop(1)) << '\n'; // 最初の一つを除いた範囲の最小値: 'e'
	}

	// 最小値と、その位置（何番目）を得る
	{
		std::vector<int> v = { -5, 10, -30, 20, 50, 0 };
		auto it = std::ranges::min_element(v); // イテレータを得る
		std::cout << *it << " at "
			<< std::ranges::distance(v.begin(), it) << '\n'; // -30 at 2
	}

	{
		std::vector<std::string> v = { "cat", "apple", "bird", "dog" };
		auto it = std::ranges::min_element(v); // イテレータを得る
		std::cout << *it << " at "
			<< std::ranges::distance(v.begin(), it) << '\n'; // apple at 1
	}
}
```
```txt:出力
-30
c
e
e
-30 at 2
apple at 1
```


## 1.5 配列の中から最大の要素とその位置を得る
- `std::max_element(irFirst, itLast)`, `std::ranges::max_element(irFirst, itLast)` および `std::ranges::max_element(range)` は、範囲 `[itFirst, itLast)` または `range` の中で最大の要素の位置を指すイテレータを返します。
- 範囲の要素数が 0 の場合、範囲の終端イテレータを返します。
- イテレータに `*` を付けると、そのイテレータが指す値にアクセスできます。
- イテレータの指す値が、配列の何番目にあるかを整数値で得るには、`std::distance(itFirst, itLast)` または `std::ranges::distance(itFirst, itLast)` に、範囲の先頭イテレータと、戻り値のイテレータを渡すことで、その間の距離を求めます。

> - `max_element()` の計算量: $O(N)$
> - `distance()` の計算量: イテレータがランダムアクセスイテレータであれば $O(1)$, それ以外の場合は $O(N)$

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <string>
#include <ranges>

int main()
{
	// 最小値だけを得る
	{
		std::vector<int> v = { -5, 10, -30, 20, 50, 0 };
		std::cout << *std::ranges::max_element(v) << '\n'; // 50
	}

	{
		std::string s = "computer";
		std::cout << *std::ranges::max_element(s) << '\n'; // 'u'
	}

	{
		std::string s = "computer";
		std::cout << *std::ranges::max_element((s.begin() + 1), s.end()) << '\n'; // 最初の一つを除いた範囲の最大値: 'u'
	}

	{
		std::string s = "computer";
		std::cout << *std::ranges::max_element(s | std::views::take(3)) << '\n'; // 最初の三つの範囲の最大値: 'o'
	}

	// 最大値と、その位置（何番目）を得る
	{
		std::vector<int> v = { -5, 10, -30, 20, 50, 0 };
		auto it = std::ranges::max_element(v); // イテレータを得る
		std::cout << *it << " at "
			<< std::ranges::distance(v.begin(), it) << '\n'; // 50 at 4
	}

	{
		std::vector<std::string> v = { "cat", "apple", "bird", "dog" };
		auto it = std::ranges::max_element(v); // イテレータを得る
		std::cout << *it << " at "
			<< std::ranges::distance(v.begin(), it) << '\n'; // dog at 3
	}
}
```
```txt:出力
50
u
u
o
50 at 4
dog at 3
```


## 1.6 二つの値から小さいほうの値と大きいほうの値を一度に得る
- `std::minmax(a, b)` は、小さいほうの値、大きいほうの値それぞれへの const 参照を組にした `std::pair` を返します。
- `std::ranges::minmax(a, b)` は、小さいほうの値、大きいほうの値それぞれへを格納した `std::ranges::min_max_result` を返します。
- `min(a, b)`, `max(a, b)` を 1 回ずつ実行するよりも、比較の回数を減らせる利点があります。
- 戻り値を構造化束縛で受け取ることができます。

```cpp
#include <iostream>
#include <algorithm>

int main()
{
	{
		int a = 30, b = 60;
		auto minmax = std::ranges::minmax(a, b);
		std::cout << "min: " << minmax.min << ", max: " << minmax.max << '\n'; // min: 30, max: 60
	}

	{
		double a = 0.5, b = -10.5;
		auto [min, max] = std::ranges::minmax(a, b); // 構造化束縛を使う場合
		std::cout << "min: " << min << ", max: " << max << '\n'; // min: -10.5, max: 0.5
	}
}
```
```txt:出力
min: 30, max: 60
min: -10.5, max: 0.5
```


## 1.7 三つ以上の値から最小値と最大値を一度に得る
- `std::minmax({ ... })` は、最小の値、最大の値それぞれへの const 参照を組にした `std::pair` を返します。
- `std::ranges::minmax({ ... })` は、最小の値、最大の値それぞれを格納した `std::ranges::min_max_result` を返します。
- リスト内の要素は同じ型である必要があります。
- `min(a, b)`, `max(a, b)` を 1 回ずつ実行するよりも、比較の回数を減らせる利点があります。
- 戻り値を構造化束縛で受け取ることができます。

```cpp
#include <iostream>
#include <algorithm>

int main()
{
	{
		int a = 30, b = 60, c = 50;
		auto minmax = std::ranges::minmax({ a, b, c });
		std::cout << "min: " << minmax.min << ", max: " << minmax.max << '\n'; // min: 30, max: 60
	}

	{
		double a = 0.1, b = 0.2, c = 0.3, d = 0.4;
		auto [min, max] = std::ranges::minmax({ a, b, c, d }); // 構造化束縛を使う場合
		std::cout << "min: " << min << ", max: " << max << '\n'; // min: 0.1, max: 0.4
	}
}
```
```txt:出力
min: 30, max: 60
min: 0.1, max: 0.4
```


## 1.8 空でない範囲の中から最小の要素と最大の要素を一度に得る [🟢C++20]
- `std::ranges::minmax(range)` は、空でない範囲 `range` の中の最小値、最大値それぞれを格納した `std::ranges::min_max_result` を返します。
- `range` の要素数が 0 の場合、実行時エラーになります。
- 戻り値を構造化束縛で受け取ることができます。

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <string>
#include <ranges>

int main()
{
	{
		std::vector<int> v = { 30, 60, 50 };
		auto minmax = std::ranges::minmax(v);
		std::cout << "min: " << minmax.min << ", max: " << minmax.max << '\n'; // min: 30, max: 60
	}

	{
		std::string s = "atcoder";
		auto [min, max] = std::ranges::minmax(s);
		std::cout << "min: " << min << ", max: " << max << '\n'; // min: a, max: t
	}

	{
		std::vector<int> v = { 2, 4, 6, 8, 10 };
		auto [min, max] = std::ranges::minmax(v | std::views::take(3)); // 最初の三つの範囲の最小値と最大値
		std::cout << "min: " << min << ", max: " << max << '\n'; // min: 2, max: 6
	}
}
```
```txt:出力
min: 30, max: 60
min: a, max: t
min: 2, max: 6
```


## 1.9 配列の中から最小の要素と最大の要素、およびそれらの位置を一度に得る
- `std::minmax_element(irFirst, itLast)` は、範囲 `[itFirst, itLast)` の中で最小の値、最大の値それぞれへのイテレータを組にした `std::pair` を返します。
- `std::ranges::minmax_element(irFirst, itLast)` および `std::ranges::minmax_element(range)` は、範囲 `range` の中で最小の値、最大の値それぞれへのイテレータを組にした `std::minmax_element_result` を返します。
- 範囲の要素数が 0 の場合、どちらも範囲の終端イテレータになります。
- 戻り値を構造化束縛で受け取ることができます。
- イテレータに `*` を付けると、そのイテレータが指す値にアクセスできます。
- イテレータの指す値が、配列の何番目にあるかを整数値で得るには、`std::distance(itFirst, itLast)` または `std::ranges::distance(itFirst, itLast)` に、範囲の先頭イテレータと、戻り値のイテレータを渡すことで、その間の距離を求めます。

> - `minmax_element()` の計算量: $O(N)$
> - `distance()` の計算量: イテレータがランダムアクセスイテレータであれば $O(1)$, それ以外の場合は $O(N)$

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <string>

int main()
{
	{
		std::vector<int> v = { -5, 10, -30, 20, 50, 0 };
		auto minmax = std::ranges::minmax_element(v);
		std::cout << "min: " << *minmax.min << ", max: " << *minmax.max << '\n'; // min: -30, max: 50
	}

	{
		std::vector<std::string> v = { "cat", "apple", "bird", "dog" };
		auto [itMin, itMax] = std::ranges::minmax_element(v);
		std::cout << "min: " << *itMin << ", max: " << *itMax << '\n'; // min: apple, max: dog
		std::cout << std::ranges::distance(v.begin(), itMin) << ", " << std::ranges::distance(v.begin(), itMax) << '\n'; // 1, 3
	}
}
```
```txt:出力
min: -30, max: 50
min: apple, max: dog
1, 3
```


## 1.10 ある値を、指定した最小値と最大値の範囲に収めた値を得る
- `std::clamp(value, min, max)` および `std::ranges::clamp(value, min, max)` は、値 `value` を `min` 以上 `max` 以下の範囲に収めた値を返します。
- `value` が範囲内であれば `value` を、`min` 未満なら `min` を、`max` より大きいなら `max` を返します。

```cpp
#include <iostream>
#include <algorithm>

int main()
{
	{
		int a = 50;
		std::cout << std::ranges::clamp(a, 0, 100) << '\n'; // a を 0 以上 100 以下に収める
		std::cout << std::ranges::clamp(a, 0, 10) << '\n'; // a を 0 以上 10 以下に収める
		std::cout << std::ranges::clamp(a, 100, 200) << '\n'; // a を 100 以上 200 以下に収める
	}

	{
		std::size_t b = 50;
		std::cout << std::ranges::clamp(static_cast<int>(b), 0, 10) << '\n'; // b を 0 以上 10 以下に収める
	}
}
```
```txt:出力
50
10
100
10
```


# 2. 範囲に対する検索

## 2.1 すべての要素が条件を満たすかを調べる
- `std::all_of(itFirst, itLast, unaryPred)` あるいは `std::ranges::all_of(itFirst, itLast, unaryPred)`, `std::ranges::all_of(range, unaryPred)` は、範囲 `[itFirst, itLast)`　または `range` にある要素すべてが条件 `unaryPred` を満たしているかを `bool` 型の値で返します。
- 範囲が空の場合は `true` を返します。
- `unaryPred` は、要素に対して条件を満たすかを返す関数や関数オブジェクトです。

> - `all_of()` の計算量: $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <ranges>

bool IsOK(double t)
{
	return (t < 37.5);
}

int main()
{
	std::cout << std::boolalpha;

	{
		std::vector<double> v = { 36.2, 36.6, 36.9, 40.1, 37.2 };
		std::cout << std::ranges::all_of(v, [](double t) { return (t < 37.5); }) << '\n'; // ラムダ式を使う
	}

	{
		std::vector<double> v = { 36.2, 36.6, 36.9, 40.1, 37.2 };
		std::cout << std::ranges::all_of(v, IsOK) << '\n'; // 関数を使う
	}

	{
		std::vector<double> v = { 36.2, 36.6, 36.9, 40.1, 37.2 };
		std::cout << std::ranges::all_of((v | std::views::take(3)), IsOK) << '\n';
	}

	{
		std::vector<double> v;
		std::cout << std::ranges::all_of(v, IsOK) << '\n';
	}
}
```
```txt:出力
false
false
true
true
```

##  2.2 条件を満たす要素があるかを調べる
- `std::any_of(itFirst, itLast, unaryPred)` あるいは `std::ranges::any_of(itFirst, itLast, unaryPred)`, `std::ranges::any_of(range, unaryPred)` は、範囲 `[itFirst, itLast)`　または `range` にある要素のうち、少なくとも 1 つが条件 `unaryPred` を満たしているかを `bool` 型の値で返します。
- 範囲が空の場合は `false` を返します。
- `unaryPred` は、要素に対して条件を満たすかを返す関数や関数オブジェクトです。

> - `any_of()` の計算量: $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <ranges>

bool IsNG(double t)
{
	return (37.5 <= t);
}

int main()
{
	std::cout << std::boolalpha;

	{
		std::vector<double> v = { 36.2, 36.6, 36.9, 40.1, 37.2 };
		std::cout << std::ranges::any_of(v, [](double t) { return (37.5 <= t); }) << '\n'; // ラムダ式を使う
	}

	{
		std::vector<double> v = { 36.2, 36.6, 36.9, 40.1, 37.2 };
		std::cout << std::ranges::any_of(v, IsNG) << '\n'; // 関数を使う
	}

	{
		std::vector<double> v = { 36.2, 36.6, 36.9, 40.1, 37.2 };
		std::cout << std::ranges::any_of((v | std::views::take(3)), IsNG) << '\n';
	}

	{
		std::vector<double> v;
		std::cout << std::ranges::any_of(v, IsNG) << '\n';
	}
}
```
```txt:出力
true
true
false
false
```


## 2.3 条件を満たす要素が存在しないかを調べる
- `std::none_of(itFirst, itLast, unaryPred)` あるいは `std::ranges::none_of(itFirst, itLast, unaryPred)`, `std::ranges::none_of(range, unaryPred)` は、範囲 `[itFirst, itLast)`　または `range` にある要素のうち、どの要素も条件 `unaryPred` を満たしていないかを `bool` 型の値で返します。
- 範囲が空の場合は `true` を返します。
- `unaryPred` は、要素に対して条件を満たすかを返す関数や関数オブジェクトです。

> - `none_of()` の計算量: $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <ranges>

bool IsNG(double t)
{
	return (37.5 <= t);
}

int main()
{
	std::cout << std::boolalpha;

	{
		std::vector<double> v = { 36.2, 36.6, 36.9, 40.1, 37.2 };
		std::cout << std::ranges::none_of(v, [](double t) { return (37.5 <= t); }) << '\n'; // ラムダ式を使う
	}

	{
		std::vector<double> v = { 36.2, 36.6, 36.9, 40.1, 37.2 };
		std::cout << std::ranges::none_of(v, IsNG) << '\n'; // 関数を使う
	}

	{
		std::vector<double> v = { 36.2, 36.6, 36.9, 40.1, 37.2 };
		std::cout << std::ranges::none_of((v | std::views::take(3)), IsNG) << '\n';
	}

	{
		std::vector<double> v;
		std::cout << std::ranges::none_of(v, IsNG) << '\n';
	}
}
```
```txt:出力
false
false
true
true
```


## 2.4 指定した値と等しい要素の個数を数える
- `std::count(itFirst, itLast, value)` および `std::ranges::count(itFirst, itLast, value)`, `std::ranges::count(range, value)` は、範囲 `[itFirst, itLast)` または `range` に存在する、`value` と等しい要素の個数を返します。

> - `count()` の計算量: $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <ranges>

int main()
{
	{
		std::vector<int> v = { 10, 10, 50, 100, 1, 10, 500, 10 };
		std::cout << std::ranges::count(v, 10) << '\n';
	}

	{
		std::vector<std::string> v = { "apple", "bird", "apple", "cat" };
		std::cout << std::ranges::count(v, "apple") << '\n';
	}

	{
		std::vector<int> v = { 10, 10, 50, 100, 1, 10, 500, 10 };
		std::cout << std::ranges::count((v | std::views::take(3)), 10) << '\n';
	}
}
```
```txt:出力
4
2
2
```


## 2.5 条件を満たす要素の個数を数える
- `std::count_if(itFirst, itLast, unaryPred)` および `std::ranges::count_if(itFirst, itLast, unaryPred)`, `std::ranges::count_if(range, unaryPred)` は、範囲 `[itFirst, itLast)` または `range` に存在する、条件 `unaryPred` を満たす要素の個数を返します。

> - `count_if` の計算量: $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <ranges>

int main()
{
	{
		std::vector<int> v = { 10, 10, 50, 100, 1, 10, 500, 10 };
		std::cout << std::ranges::count_if(v, [](int n) { return (n <= 10); }) << '\n';
	}

	{
		std::vector<std::string> v = { "apple", "bird", "apple", "cat" };
		std::cout << std::ranges::count_if(v, [](const std::string& s) { return s.size() == 3; }) << '\n';
	}

	{
		std::vector<int> v = { 10, 10, 50, 100, 1, 10, 500, 10 };
		std::cout << std::ranges::count_if((v | std::views::take(3)), [](int n) { return (n <= 10); }) << '\n';
	}
}
```
```txt:出力
5
1
2
```


## 2.6 指定した値と等しい要素が最初に現れる位置を調べる

![](https://storage.googleapis.com/zenn-user-upload/merltfiqqm7jouov7jnz4kvxewt7)

- `std::find(itFirst, itLast, value)` および `std::ranges::find(itFirst, itLast, value)`, `std::ranges::find(range, value)` は、範囲 `[itFirst, itLast)` または `range` の中で `value` と等しい最初の要素の位置のイテレータを返します。
- 見つからなかった場合、範囲の終端イテレータを返します。
- イテレータに `*` を付けると、そのイテレータが指す値にアクセスできます。
- イテレータの指す値が、配列の何番目にあるかを整数値で得るには、`std::distance(itFirst, itLast)` または `std::ranges::distance(itFirst, itLast)` に、範囲の先頭イテレータと、戻り値のイテレータを渡すことで、その間の距離を求めます。

> - `find()` の計算量: $O(N)$
> - `distance()` の計算量: イテレータがランダムアクセスイテレータであれば $O(1)$, それ以外の場合は $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };
		
		auto it = std::ranges::find(v, 4);

		if (it != v.end())
		{
			std::cout << "Found " << *it << " at " << std::ranges::distance(v.begin(), it) << '\n';
		}
		else
		{
			std::cout << "Not found\n";
		}
	}

	{
		std::vector<int> v = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };
		
		auto it = std::ranges::find(v, 0);

		if (it != v.end())
		{
			std::cout << "Found " << *it << " at " << std::ranges::distance(v.begin(), it) << '\n';
		}
		else
		{
			std::cout << "Not found\n";
		}
	}

	{
		std::vector<std::string> v = { "apple", "bird", "apple", "cat" };

		auto it = std::ranges::find(v, "apple");
		
		if (it != v.end())
		{
			std::cout << "Found " << *it << " at " << std::ranges::distance(v.begin(), it) << '\n';
		}
		else
		{
			std::cout << "Not found\n";
		}
	}
}
```
```txt:出力
Found 4 at 4
Not found
Found apple at 0
```


## 2.7 条件を満たす要素が最初に現れる位置を調べる

![](https://storage.googleapis.com/zenn-user-upload/y41i3iwzyzxs1pyxq7l2ym3bbzkx)

- `std::find_if(itFirst, itLast, unaryPred)` および `std::ranges::find_if(itFirst, itLast, unaryPred)`, `std::ranges::find_if(range, unaryPred)` は、範囲 `[itFirst, itLast)` または `range` の中で、条件 `unaryPred` を満たす最初の要素の位置のイテレータを返します。
- 見つからなかった場合、範囲の終端イテレータを返します。
- `unaryPred` は、要素に対して条件を満たすかを返す関数や関数オブジェクトです。
- イテレータに `*` を付けると、そのイテレータが指す値にアクセスできます。
- イテレータの指す値が、配列の何番目にあるかを整数値で得るには、`std::distance(itFirst, itLast)` または `std::ranges::distance(itFirst, itLast)` に、範囲の先頭イテレータと、戻り値のイテレータを渡すことで、その間の距離を求めます。

> - `find_if()` の計算量: $O(N)$
> - `distance()` の計算量: イテレータがランダムアクセスイテレータであれば $O(1)$, それ以外の場合は $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };
		
		auto it = std::ranges::find_if(v, [](int n) { return (n % 2 == 0); });

		if (it != v.end())
		{
			std::cout << "Found " << *it << " at " << std::ranges::distance(v.begin(), it) << '\n';
		}
		else
		{
			std::cout << "Not found\n";
		}
	}

	{
		std::vector<int> v = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };
		
		auto it = std::ranges::find_if(v, [](int n) { return (n < 0); });

		if (it != v.end())
		{
			std::cout << "Found " << *it << " at " << std::ranges::distance(v.begin(), it) << '\n';
		}
		else
		{
			std::cout << "Not found\n";
		}
	}

	{
		std::vector<std::string> v = { "apple", "bird", "apple", "cat" };

		auto it = std::ranges::find_if(v, [](const std::string& s) { return (s.size() < 4); });
		
		if (it != v.end())
		{
			std::cout << "Found " << *it << " at " << std::ranges::distance(v.begin(), it) << '\n';
		}
		else
		{
			std::cout << "Not found\n";
		}
	}
}
```
```txt:出力
Found 2 at 3
Not found
Found cat at 3
```


# 3. 範囲に対する一般的な操作

## 3.1 範囲の要素を指定した値で埋める
- `std::fill(itFirst, itLast, value)` および `std::ranges::fill(itFirst, itLast, value)`, `std::ranges::fill(range, value)` は、範囲 `[itFirst, itLast)` または `range` のすべての要素を `value` にします。

> - `fill()` の計算量: $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <ranges>

int main()
{
	{
		std::vector<int> v = { 1, 2, 4, 8 };

		// 全要素を 0 にする
		std::ranges::fill(v, 0);
		
		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::vector<std::string> v = { "apple", "bird", "cat", "dog", "elephant" };

		// 最後の二つ以外の要素を "hello" にする
		std::ranges::fill((v | std::views::drop(2)), "hello");

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}
}
```
```txt:出力
0 0 0 0
apple bird hello hello hello
```


## 3.2 指定した値と等しい要素をすべて削除する (1) [🟢C++20]
- `std::erase(container, value)` は、コンテナ `container` にある、`value` と等しい要素をすべて削除します。
- ここでのコンテナは `std::vector`, `std::deque`, `std::string` などです。

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };

		// 3 と等しい要素を削除する
		std::erase(v, 3);

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::vector<std::string> v = { "apple", "bird", "cat", "apple" };

		// "apple" と等しい要素を削除する
		std::erase(v, "apple");
	
		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::string s = "programming";

		// 'm' と等しい要素を削除する
		std::erase(s, 'm');

		std::cout << s << '\n';
	}
}
```
```txt:出力
5 2 4 2 4 5 1
bird cat
prograing
```

## 3.3 指定した値と等しい要素をすべて削除する (2)

![](https://storage.googleapis.com/zenn-user-upload/14n9ju6cs33n08nhzgt2guxhvfrs)  
![](https://storage.googleapis.com/zenn-user-upload/9bnh7kb5j8n1auxnlqx0lvjakmpz)  
![](https://storage.googleapis.com/zenn-user-upload/265t7mqtqmoln39qis2v6gtp645t)

- `std::remove(itFirst, itLast)` は、範囲 `[itFirst, itLast)` の要素について、前半が、`value` に等しい要素を除外した有効範囲となるよう並びかえ、それ以降は無効範囲とし、有効範囲の終端イテレータを返します。
- `std::ranges::remove(itFirst, itLast)` および `std::ranges::remove(range)` は、範囲 `[itFirst, itLast)` または `range` の要素について、`value` に等しい要素を除外した有効範囲となるよう並びかえ、それ以降は無効範囲とし、無効範囲のサブレンジを返します。
- 有効範囲に残る要素の前後関係は元の順序が維持されます。
- 無効範囲の要素がどうなっているかは未規定で、値を読み取っても意味はありません（除外した要素や古い要素がゴミとして残っています）。
- `std::vector` の `.erase(itFirst, itLast)` は、イテレータで指定した範囲 `[itFirst, itLast)` の要素を配列から削除し、その分だけ配列の要素数を縮小します。
- `std::vector` の `.erase(itFirst, itLast)` において、`remove()` が返したイテレータを使うことで、無効範囲を削除することができます。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// std::remove
	{
		std::vector<int> v = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };

		// 3 と等しい要素を削除する
		v.erase(std::remove(v.begin(), v.end(), 3), v.end());

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	// std::ranges::remove
	{
		std::vector<int> v = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };

		// 3 と等しい要素を削除する
		auto subrange = std::ranges::remove(v, 3);
		v.erase(subrange.begin(), subrange.end());

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}
}
```
```txt:出力
5 2 4 2 4 5 1
5 2 4 2 4 5 1
```


## 3.4 条件を満たす要素をすべて削除する (1) [🟢C++20]
- `std::erase_if(container, unaryPred)` は、コンテナ `container` にある、条件 `unaryPred` を満たす要素をすべて削除します。
- ここでのコンテナは `std::vector`, `std::deque`, `std::string` などです。
- `unaryPred` は、要素に対して条件を満たすかを返す関数や関数オブジェクトです。

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };

		// 偶数の要素を削除する
		std::erase_if(v, [](int x) { return (x % 2 == 0); });

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::vector<std::string> v = { "apple", "bird", "cat", "apple" };

		// 4 文字以上の要素を削除する
		std::erase_if(v, [](const std::string& s) { return (4 <= s.size()); });
	
		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::string s = "programming";

		// 'a' と 'o' を削除する
		std::erase_if(s, [](char c) { return ((c == 'a') || (c == 'o')); });

		std::cout << s << '\n';
	}
}
```
```txt:出力
5 3 3 3 5 1
cat
prgrmming
```

## 3.5 条件を満たす要素をすべて削除する (2)

![](https://storage.googleapis.com/zenn-user-upload/0eawt9k48qk0dsztugf6894is2g9)  
![](https://storage.googleapis.com/zenn-user-upload/5c076a59mn7r67r3lmc686eifow5)  
![](https://storage.googleapis.com/zenn-user-upload/10f3zfxoorsu6wdqgbgc8dmjl216)  

- `std::remove_if(itFirst, itLast, unaryPred)` は、範囲 `[itFirst, itLast)` の要素について、前半が、条件 `unaryPred` を満たさない要素を除外した有効範囲となるよう並びかえ、それ以降は無効範囲とし、有効範囲の終端イテレータを返します。
- `std::ranges::remove_if(itFirst, itLast, unaryPred)` および `std::ranges::remove_if(range, unaryPred)` は、範囲 `[itFirst, itLast)` または `range` の要素について、条件 `unaryPred` を満たさない要素を除外した有効範囲となるよう並びかえ、それ以降は無効範囲とし、無効範囲のサブレンジを返します。
- 有効範囲に残る要素の前後関係は元の順序が維持されます。
- 無効範囲の要素がどうなっているかは未規定で、値を読み取っても意味はありません（除外した要素や古い要素がゴミとして残っています）。
- `std::vector` の `.erase(itFirst, itLast)` は、イテレータで指定した範囲 `[itFirst, itLast)` の要素を配列から削除し、その分だけ配列の要素数を縮小します。
- `std::vector` の `.erase(itFirst, itLast)` において、`remove_if()` が返したイテレータを使うことで、無効範囲を削除することができます。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// std::remove_if
	{
		std::vector<int> v = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };

		// 偶数の要素を削除する
		v.erase(std::remove_if(v.begin(), v.end(), [](int n) { return (n % 2 == 0); }), v.end());

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	// std::ranges::remove_if
	{
		std::vector<int> v = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };

		// 偶数の要素を削除する
		auto subrange = std::ranges::remove_if(v, [](int n) { return (n % 2 == 0); });
		v.erase(subrange.begin(), subrange.end());

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}
}
```
```txt:出力
5 3 3 3 5 1
5 3 3 3 5 1
```


## 3.6 範囲の要素を逆順にする
- `std::reverse(itFirst, itLast)` および `std::ranges::reverse(itFirst, itLast)`, `std::ranges::reverse(range)` は、範囲 `[itFirst, itLast)` または `range` の要素を逆順に並び替えます。

> - `reverse()` の計算量: $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 1, 3, 5, 2, 4, 0 };

		// 逆順に並び替える
		std::ranges::reverse(v);

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::vector<std::string> v = { "apple", "bird", "cat", "dog", "elephant" };

		// 逆順に並び替える
		std::ranges::reverse(v);

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::string s = "atcoder";

		// 逆順に並び替える
		std::ranges::reverse(s);

		std::cout << s << '\n';
	}
}
```
```txt:出力
0 4 2 5 3 1
elephant dog cat bird apple
redocta
```


## 3.7 指定した要素を別の値で置き換える
- `std::replace(itFirst, itLast, oldValue, newValue)` および `std::ranges::replace(itFirst, itLast, oldValue, newValue)`, `std::ranges::replace(range, oldValue, newValue)` は、範囲 `[itFirst, itLast)` または `range` の要素のうち、`oldValue` と等しい要素を `newValue` に置き換えます。

> - `replace()` の計算量: $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };

		std::ranges::replace(v, 3, 30);

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::vector<std::string> v = { "apple", "bird", "apple", "cat" };

		std::ranges::replace(v, "apple", "orange");

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::string s = "atcoder";

		std::ranges::replace(s, 'o', 'i');

		std::cout << s << '\n';
	}
}
```
```txt:出力
5 30 30 2 4 2 4 30 5 1
orange bird orange cat
atcider
```


## 3.8 指定した条件を満たす要素を別の値で置き換える
- `std::replace_if(itFirst, itLast, unaryPred, newValue)` および `std::ranges::replace_if(itFirst, itLast, unaryPred, newValue)`, `std::ranges::replace_if(range, unaryPred, newValue)` は、範囲 `[itFirst, itLast)` または `range` の要素のうち、条件 `unaryPred` を満たす要素を `newValue` に置き換えます。
- `unaryPred` は、要素に対して条件を満たすかを返す関数や関数オブジェクトです。

> - `replace_if()` の計算量: $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 5, 3, 3, 2, 4, 2, 4, 3, 5, 1 };

		// 偶数をすべて -1 に置き換える
		std::ranges::replace_if(v, [](int n) { return (n % 2 == 0); }, -1);

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::vector<std::string> v = { "apple", "bird", "apple", "cat" };

		// 4 文字以下の要素をすべて "banana" に置き換える
		std::ranges::replace_if(v, [](const std::string& s) { return (s.size() <= 4); }, "banana");

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::string s = "atcoder";

		// 'a' と 'o' をすべて 'i' に置き換える
		std::ranges::replace_if(s, [](char c) { return ((c == 'a') || (c == 'o')); }, 'i');

		std::cout << s << '\n';
	}
}
```
```txt:出力
5 3 3 -1 -1 -1 -1 3 5 1
apple banana apple banana
itcider
```


## 3.9 等しい値が隣同士にならないよう要素を削除する

![](https://storage.googleapis.com/zenn-user-upload/ju1t0e4osdok3jw0g78dfjuvu4em)  
![](https://storage.googleapis.com/zenn-user-upload/oal49bx7uj5j3pc46so8nx3ngnwm)  
![](https://storage.googleapis.com/zenn-user-upload/ajffs25zekco5k7e1poxetj6but4)  


- `std::unique(itFirst, itLast)` は、範囲 `[itFirst, itLast)` の要素について、前半が、隣同士で重複する要素を除外した有効範囲となるよう並びかえ、それ以降は無効範囲とし、有効範囲の終端イテレータを返します。
- `std::ranges::unique(itFirst, itLast)` および `std::ranges::unique(range)` は、範囲 `[itFirst, itLast)` または `range` の要素について、前半が、隣同士で重複する要素を除外した有効範囲となるよう並びかえ、それ以降は無効範囲とし、無効範囲のサブレンジを返します。
- 有効範囲に残る要素の前後関係は元の順序が維持されます。
- 無効範囲の要素がどうなっているかは未規定で、値を読み取っても意味はありません（除外した要素や古い要素がゴミとして残っています）。
- `std::vector` の `.erase(itFirst, itLast)` は、イテレータで指定した範囲 `[itFirst, itLast)` の要素を配列から削除し、その分だけ配列の要素数を縮小します。
- `std::vector` の `.erase(itFirst, itLast)` において、`unique()` が返したイテレータを使うことで、無効範囲を削除することができます。

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	// std::unique
	{
		std::vector<int> v = { 1, 1, 1, 3, 4, 3, 3, 2, 0, 2 };

		// 隣同士で重複する要素を除外する
		v.erase(std::unique(v.begin(), v.end()), v.end());

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	// std::ranges::unique
	{
		std::vector<int> v = { 1, 1, 1, 3, 4, 3, 3, 2, 0, 2 };

		// 隣同士で重複する要素を除外する
		auto subrange = std::ranges::unique(v);
		v.erase(subrange.begin(), subrange.end());

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::string s = "communication";

		auto subrange = std::ranges::unique(s);
		s.erase(subrange.begin(), subrange.end());

		std::cout << s << '\n';
	}
}
```
```txt:出力
1 3 4 3 2 0 2
1 3 4 3 2 0 2
comunication
```


## 3.10 配列から重複する要素を無くす
![](https://storage.googleapis.com/zenn-user-upload/8ad2ohrp1h314mi1ae53hundxxg6)  
![](https://storage.googleapis.com/zenn-user-upload/wmw38txnyspy7lhguqp5ievf3nln)  
![](https://storage.googleapis.com/zenn-user-upload/zdcgymqoz7tlorn7nq3bpsb7sbcw)  
![](https://storage.googleapis.com/zenn-user-upload/2n8y9c8nlcrk1iz2q7ciicnqqtps)  

- 配列において、ソートしてから「3.7 等しい値が隣同士にならないよう要素を削除する」を行うことで、重複する要素を配列から削除できます。

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 1, 1, 1, 3, 4, 3, 3, 2, 0, 2 };

		std::ranges::sort(v);

		auto result = std::ranges::unique(v);

		v.erase(result.begin(), result.end());

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::string s = "communication";

		std::ranges::sort(s);

		auto result = std::ranges::unique(s);

		s.erase(result.begin(), result.end());

		std::cout << s << '\n';
	}
}
```
```txt:出力
0 1 2 3 4
acimnotu
```


## 3.11 要素の前半と後半を入れ替える

![](https://storage.googleapis.com/zenn-user-upload/00ubzrhcws3ywiv75aplss1u2tkj)  
![](https://storage.googleapis.com/zenn-user-upload/4xyfcvmfvg63ulzjgvnoc7pwn29r)  
![](https://storage.googleapis.com/zenn-user-upload/h39ztens5lp1erqgrudaomemgy9i)  

- `std::rotate(itFirst, itMiddle, itLast)` および `std::ranges::rotate(itFirst, itMiddle, itLast)`, `std::ranges::rotate(range, itMiddle)` は、範囲 `[itFirst, itLast)` または `range` の要素を、`itMiddle` で指定した位置を境に、前半と後半を入れ替えます。

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };

		// 0 1 2|3 4 5 6 7 8 9 
		// 2 と 3 の間を境に、前半と後半を入れ替える
		std::ranges::rotate(v, (v.begin() + 3));

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::string s = "atcoder";

		// at|coder
		// 't' と 'c' の間を境に、前半と後半を入れ替える
		std::ranges::rotate(s, (s.begin() + 2));

		std::cout << s << '\n';
	}
}
```
```txt:出力
3 4 5 6 7 8 9 0 1 2
coderat
```


# 4. 範囲に対するソート

## 4.1 配列の要素を昇順にソートする
- `std::sort(itFirst, itLast)` および `std::ranges::sort(itFirst, itLast)`, `std::ranges::sort(range)` は、範囲 `[itFirst, itLast)` または `range` の要素を昇順にソートします。
- イテレータはランダムアクセスイテレータである必要があります。

> - `sort()` の計算量: $O(N \log N)$

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 1, 5, 0, 2, 4, 6, 3, 9, 8, 7 };

		// 昇順にソートする
		std::ranges::sort(v);

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::string s = "atcoder";

		// 昇順にソートする
		std::ranges::sort(s);

		std::cout << s << '\n';
	}
}
```
```txt:出力
0 1 2 3 4 5 6 7 8 9
acdeort
```


## 4.2 配列の要素を降順にソートする
- `std::sort(itFirst, itLast, comp)` および `std::ranges::sort(itFirst, itLast, comp)`, `std::ranges::sort(range, comp)` は、範囲 `[itFirst, itLast)` または `range` の要素を、`comp` による比較の結果でソートします。
-  `comp` を `std::greater{}` または `std::ranges::greater{}`（大きいほう優先）にすることで、降順にソートできます。
- イテレータはランダムアクセスイテレータである必要があります。

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <functional> // greater のため

int main()
{
	{
		std::vector<int> v = { 1, 5, 0, 2, 4, 6, 3, 9, 8, 7 };

		// 降順にソートする
		std::ranges::sort(v, std::ranges::greater{});

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::string s = "atcoder";

		// 降順にソートする
		std::ranges::sort(s, std::ranges::greater{});

		std::cout << s << '\n';
	}
}
```
```txt:出力
9 8 7 6 5 4 3 2 1 0
troedca
```


## 4.3 配列の要素を、特定のメンバ変数だけを見てソートする [🟢C++20]
- `std::ranges::sort(itFirst, itLast, comp, projection)` および `std::ranges::sort(range, comp, projection)` は、範囲 `[itFirst, itLast)` または `range` の要素を `projection` でプロジェクションした値を、`comp` による比較の結果でソートします。
- `comp` に `{}` を指定すると、デフォルトの比較関数が使われます。
- `projection` に、`&Person::age` のようにメンバ変数を指定すると、そのメンバ変数の値をプロジェクションします。

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <utility>

struct Person
{
	std::string name;
	int age;
};

int main()
{
	{
		std::vector<Person> v = {
			{ "Alice", 25 },
			{ "Bob", 30 },
			{ "Charlie", 20 },
			{ "Dave", 15 },
			{ "Eve", 18 },
		};

		// 年齢の昇順にソートする
		std::ranges::sort(v, {}, &Person::age);

		for (const auto& e : v)
		{
			std::cout << e.name << "(" << e.age << ") ";
		}
		std::cout << '\n';
	}

	{
		std::vector<std::pair<std::string, int>> v = {
			{ "Alice", 25 },
			{ "Bob", 30 },
			{ "Charlie", 20 },
			{ "Dave", 15 },
			{ "Eve", 18 },
		};

		// 年齢の降順にソートする
		std::ranges::sort(v, std::ranges::greater{}, &std::pair<std::string, int>::second);

		for (const auto& e : v)
		{
			std::cout << e.first << "(" << e.second << ") ";
		}
		std::cout << '\n';
	}
}
```
```txt:出力
Dave(15) Eve(18) Charlie(20) Alice(25) Bob(30)
Bob(30) Alice(25) Charlie(20) Eve(18) Dave(15)
```


## 4.4 配列がソートされているかを調べる
- `std::is_sorted(itFirst, itLast)` および `std::ranges::is_sorted(itFirst, itLast)`, `std::ranges::is_sorted(range)` は、範囲 `[itFirst, itLast)` または `range` が昇順にソートされているかを調べます。
- `std::is_sorted()` には `comp` を指定することもできます。
- `std::ranges::is_sorted()` には `comp` と `projection` を指定することもできます。

> - `is_sorted()` の計算量: $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <utility>

int main()
{
	std::cout << std::boolalpha;

	{
		std::vector<int> v = { 5, 1, 3, 2, 4 };

		std::cout << std::ranges::is_sorted(v) << '\n';

		std::ranges::sort(v);

		std::cout << std::ranges::is_sorted(v) << '\n';
	}

	{
		std::vector<std::pair<std::string, int>> v = {
			{ "Alice", 25 },
			{ "Bob", 30 },
			{ "Charlie", 20 },
			{ "Dave", 15 },
			{ "Eve", 18 },
		};

		std::cout << std::ranges::is_sorted(v, std::ranges::greater{}, &std::pair<std::string, int>::second) << '\n';

		// 年齢の降順にソートする
		std::ranges::sort(v, std::ranges::greater{}, &std::pair<std::string, int>::second);

		std::cout << std::ranges::is_sorted(v, std::ranges::greater{}, &std::pair<std::string, int>::second) << '\n';
	}
}
```
```txt:出力
false
true
false
true
```


## 4.5 配列の要素について、上位 N 個までソートをする

![](https://storage.googleapis.com/zenn-user-upload/piahsffvr41zhkm6b79507xvo7e0)

- `std::partial_sort(itFirst, itMiddle, itLast)` および `std::ranges::partial_sort(itFirst, itMiddle, itLast)`, `std::ranges::partial_sort(range, itMiddle)` は、範囲 `[itFirst, itLast)` または `range` の要素をソートし、結果として `[itFirst, itMiddle)` の範囲に上位 `(itMiddle - itFirst)` 個の要素がソート済みで並ぶようにし、それ以降についてはソートが未完了のままにすることで、`sort()` より計算量を小さくします。

> - `partial_sort()` の計算量: $O(N \log M)$

::: message
使用されているアルゴリズムの都合上、N が全体に対して大部分である場合は `sort()` よりも効率が悪くなる場合があります。その際は、代わりの手法として、`nth_element()` のあとに `sort()` による部分的なソートを行うと効率的です。下記が参考になります。

- [sort() vs. partial_sort() vs. nth_element() + sort() in C++ STL](https://www.geeksforgeeks.org/sort-vs-partial_sort-vs-nth_element-sort-in-c-stl/), GeeksforGeeks
:::


```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 1, 5, 0, 2, 4, 6, 3, 9, 8, 7 };

		// 上位 4 個まで昇順にソートする
		std::ranges::partial_sort(v, (v.begin() + 4));

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::string s = "atcoder";

		// 上位 2 個まで昇順にソートする
		std::ranges::partial_sort(s, (s.begin() + 2));

		std::cout << s << '\n';
	}
}
```
```txt:出力例
0 1 2 3 5 6 4 9 8 7
actoder
```


## 4.6 配列で N 番目に小さい要素を求める

![](https://storage.googleapis.com/zenn-user-upload/hwom96sdwa49wi3tsv5il3ijrouk)

- `std::nth_element(itFirst, itNth, itLast)` および `std::ranges::nth_element(itFirst, itNth, itLast)`, `std::ranges::nth_element(range, itNth)` は、範囲 `[itFirst, itLast)` または `range` にある要素を不完全にソートし、結果として `itNth` の位置に、完全なソートを行ったときと同じ要素が置かれるようにし、それより前はその要素より小さく、それ以降はその要素より大きい要素が並ぶということだけ保証する、部分的なソートを行います。
- 上位 N 番目の要素を求めたいだけの時、`sort()` や `partial_sort()` を使うより計算量を小さくできます。

> - `nth_element()` の計算量: 平均 $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 1, 5, 0, 2, 4, 6, 3, 9, 8, 7 };

		// 4 番目に小さい要素を探す
		std::ranges::nth_element(v, (v.begin() + 3));

		std::cout << "4th : " << v[3] << '\n';

		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}

	{
		std::string s = "qwertyuiopasdfghjklzxcvbnmqwertyuiopasdfghjklzxcvbnm";

		// 2 番目に小さい要素を探す
		std::ranges::nth_element(s, (s.begin() + 1));

		std::cout << "2nd : " << s[1] << '\n';

		std::cout << s << '\n';

		// 完全なソート結果と比べる
		std::ranges::sort(s);

		std::cout << s << '\n';
	}
}
```
```txt:出力例
4th : 3
0 1 2 3 4 5 6 7 8 9
2nd : a
aabbccddeeffgghhiijjkkllmmnnooppqqusystrwwvrtuxyvxzz
aabbccddeeffgghhiijjkkllmmnnooppqqrrssttuuvvwwxxyyzz
```


# 5. ソート済みの範囲に対する二分探索

## 5.1 ある値を範囲に挿入するとして、ソートされた状態を維持できる最も左の位置 (lower_bound) を二分探索で取得する
- `std::lower_bound(itFirst, itLast, value)` および `std::ranges::lower_bound(itFirst, itLast, value)`, `std::ranges::lower_bound(range, value)` は、範囲 `[itFirst, itLast)` または `range` において、`value` を挿入するとして、ソートされた状態を壊さない最も左の位置のイテレータを返します。
- イテレータの指す値が、配列の何番目にあるかを整数値で得るには、`std::distance(itFirst, itLast)` または `std::ranges::distance(itFirst, itLast)` に、範囲の先頭イテレータと、戻り値のイテレータを渡すことで、その間の距離を求めます。

> - `lower_bound()` の計算量: イテレータがランダムアクセスイテレータの場合 $O(\log N)$, そうでなければ $O(N)$
> - `distance()` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$

::: message
`lower_bound()` に渡すイテレータがランダムアクセスイテレータでない場合（具体的には `std::set` や `std::multiset` のイテレータ）、関数内部でのイテレータの移動の処理に線形時間を要するため、計算量は $O(N)$ に悪化します。それぞれのコンテナのメンバ関数 `std::set::lower_bound()`, `std::multiset::lower_bound()` であれば、データ構造に合わせた実装になっているため、計算量は $O(\log N)$ です。
:::

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		// ソート済みの状態
		std::vector<int> v = { 1, 5, 10, 50, 100, 500 };

		auto it = std::ranges::lower_bound(v, 0);
		std::cout << "(0): " << std::ranges::distance(v.begin(), it) << '\n';

		it = std::ranges::lower_bound(v, 1);
		std::cout << "(1): " << std::ranges::distance(v.begin(), it) << '\n';

		it = std::ranges::lower_bound(v, 2);
		std::cout << "(2): " << std::ranges::distance(v.begin(), it) << '\n';

		it = std::ranges::lower_bound(v, 5);
		std::cout << "(5): " << std::ranges::distance(v.begin(), it) << '\n';

		it = std::ranges::lower_bound(v, 499);
		std::cout << "(499): " << std::ranges::distance(v.begin(), it) << '\n';

		it = std::ranges::lower_bound(v, 500);
		std::cout << "(500): " << std::ranges::distance(v.begin(), it) << '\n';

		it = std::ranges::lower_bound(v, 501);
		std::cout << "(501): " << std::ranges::distance(v.begin(), it) << '\n';
	}

	{
		std::vector<std::string> v = { "apple", "bird", "cat" };

		auto it = std::ranges::lower_bound(v, "alarm");
		v.insert(it, "alarm");

		it = std::ranges::lower_bound(v, "banana");
		v.insert(it, "banana");

		it = std::ranges::lower_bound(v, "dog");
		v.insert(it, "dog");

		// ソート済みの状態が保たれていることを確認する
		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}
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
alarm apple banana bird cat dog
```


## 5.2 ある値を範囲に挿入するとして、ソートされた状態を維持できる最も右の位置 (upper_bound) を二分探索で取得する
- `std::upper_bound(itFirst, itLast, value)` および `std::ranges::upper_bound(itFirst, itLast, value)`, `std::ranges::upper_bound(range, value)` は、範囲 `[itFirst, itLast)` または `range` において、`value` を挿入するとして、ソートされた状態を壊さない最も右の位置のイテレータを返します。
- イテレータの指す値が、配列の何番目にあるかを整数値で得るには、`std::distance(itFirst, itLast)` または `std::ranges::distance(itFirst, itLast)` に、範囲の先頭イテレータと、戻り値のイテレータを渡すことで、その間の距離を求めます。

> - `upper_bound()` の計算量: イテレータがランダムアクセスイテレータの場合 $O(\log N)$, そうでなければ $O(N)$
> - `distance()` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$

::: message
`upper_bound()` に渡すイテレータがランダムアクセスイテレータでない場合（具体的には `std::set` や `std::multiset` のイテレータ）、関数内部でのイテレータの移動の処理に線形時間を要するため、計算量は $O(N)$ に悪化します。それぞれのコンテナのメンバ関数 `std::set::upper_bound()`, `std::multiset::upper_bound()` であれば、データ構造に合わせた実装になっているため、計算量は $O(\log N)$ です。
:::

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		// ソート済みの状態
		std::vector<int> v = { 1, 5, 10, 50, 100, 500 };

		auto it = std::ranges::upper_bound(v, 0);
		std::cout << "(0): " << std::ranges::distance(v.begin(), it) << '\n';

		it = std::ranges::upper_bound(v, 1);
		std::cout << "(1): " << std::ranges::distance(v.begin(), it) << '\n';

		it = std::ranges::upper_bound(v, 2);
		std::cout << "(2): " << std::ranges::distance(v.begin(), it) << '\n';

		it = std::ranges::upper_bound(v, 5);
		std::cout << "(5): " << std::ranges::distance(v.begin(), it) << '\n';

		it = std::ranges::upper_bound(v, 499);
		std::cout << "(499): " << std::ranges::distance(v.begin(), it) << '\n';

		it = std::ranges::upper_bound(v, 500);
		std::cout << "(500): " << std::ranges::distance(v.begin(), it) << '\n';

		it = std::ranges::upper_bound(v, 501);
		std::cout << "(501): " << std::ranges::distance(v.begin(), it) << '\n';
	}

	{
		std::vector<std::string> v = { "apple", "bird", "cat" };

		auto it = std::ranges::upper_bound(v, "alarm");
		v.insert(it, "alarm");

		it = std::ranges::upper_bound(v, "banana");
		v.insert(it, "banana");

		it = std::ranges::upper_bound(v, "dog");
		v.insert(it, "dog");

		// ソート済みの状態が保たれていることを確認する
		for (const auto& e : v)
		{
			std::cout << e << ' ';
		}
		std::cout << '\n';
	}
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
alarm apple banana bird cat dog
```


## 5.3 lower_bound と upper_bound の結果を同時に取得する
- `std::equal_range(itFirst, itLast, value)` および `std::ranges::equal_range(itFirst, itLast, value)`, `std::ranges::equal_range(range, value)` は、範囲 `[itFirst, itLast)` または `range` において、`value` を挿入するとして、ソートされた状態を壊さない最も左の位置のイテレータと、最も右の位置のイテレータの組を返します。
- `std::equal_range(itFirst, itLast, value)` はイテレータの `std::pair` を返します。
- `std::ranges::equal_range(itFirst, itLast, value)` および `std::ranges::equal_range(range, value)` はサブレンジを返します。

> - `equal_range()` の計算量: イテレータがランダムアクセスイテレータの場合 $O(\log N)$, そうでなければ $O(N)$

::: message
`equal_range()` に渡すイテレータがランダムアクセスイテレータでない場合（具体的には `std::set` や `std::multiset` のイテレータ）、関数内部でのイテレータの移動の処理に線形時間を要するため、計算量は $O(N)$ に悪化します。それぞれのコンテナのメンバ関数 `std::set::equal_range()`, `std::multiset::equal_range()` であれば、データ構造に合わせた実装になっているため、計算量は $O(\log N)$ です。
:::

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	{
		// ソート済みの状態
		std::vector<int> v = { 1, 1, 5, 10, 10, 10, 500 };

		auto p = std::ranges::equal_range(v, 0);
		std::cout << "(0): " << std::ranges::distance(v.begin(), p.begin())
			<< " - " << std::ranges::distance(v.begin(), p.end()) << '\n';

		p = std::ranges::equal_range(v, 1);
		std::cout << "(1): " << std::ranges::distance(v.begin(), p.begin())
			<< " - " << std::ranges::distance(v.begin(), p.end()) << '\n';

		p = std::ranges::equal_range(v, 2);
		std::cout << "(2): " << std::ranges::distance(v.begin(), p.begin())
			<< " - " << std::ranges::distance(v.begin(), p.end()) << '\n';

		p = std::ranges::equal_range(v, 5);
		std::cout << "(5): " << std::ranges::distance(v.begin(), p.begin())
			<< " - " << std::ranges::distance(v.begin(), p.end()) << '\n';

		p = std::ranges::equal_range(v, 10);
		std::cout << "(10): " << std::ranges::distance(v.begin(), p.begin())
			<< " - " << std::ranges::distance(v.begin(), p.end()) << '\n';

		p = std::ranges::equal_range(v, 500);
		std::cout << "(500): " << std::ranges::distance(v.begin(), p.begin())
			<< " - " << std::ranges::distance(v.begin(), p.end()) << '\n';

		p = std::ranges::equal_range(v, 501);
		std::cout << "(501): " << std::ranges::distance(v.begin(), p.begin())
			<< " - " << std::ranges::distance(v.begin(), p.end()) << '\n';
	}
}
```
```txt:出力
(0): 0 - 0
(1): 0 - 2
(2): 2 - 2
(5): 2 - 3
(10): 3 - 6
(500): 6 - 7
(501): 7 - 7
```


## 5.4 指定した値と等しい要素が存在するかを調べる
- `std::binary_search(itFirst, itLast, value)` および `std::ranges::binary_search(itFirst, itLast, value)`, `std::ranges::binary_search(range, value)` は、範囲 `[itFirst, itLast)` または `range` において、`value` と等しい要素が存在するかを `bool` 型で返します。

> - `binary_search()` の計算量: イテレータがランダムアクセスイテレータの場合 $O(\log N)$, そうでなければ $O(N)$

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::cout << std::boolalpha;

	{
		std::vector<int> v = { 1, 1, 5, 10, 10, 10, 500 };

		std::cout << std::ranges::binary_search(v, 10) << '\n'; // true
		std::cout << std::ranges::binary_search(v, 20) << '\n'; // false
		std::cout << std::ranges::binary_search(v, 30) << '\n'; // false
	}

	std::cout << "---\n";

	{
		std::string s = "atcoder";
		std::ranges::sort(s);

		std::cout << std::ranges::binary_search(s, 'a') << '\n'; // true
		std::cout << std::ranges::binary_search(s, 'b') << '\n'; // false
		std::cout << std::ranges::binary_search(s, 'c') << '\n'; // true
	}
}
```
```txt:出力
true
false
false
---
true
false
true
```


# 6. ソート済みの二つの範囲に対する集合演算

## 6.1 指定した集合が部分集合であるかを調べる
- `std::includes(itFirst1, itLast1, itFirst2, itLast2)` および `std::ranges::includes(itFirst1, itLast1, itFirst2, itLast2)`, `std::ranges::includes(range1, range2)` は、範囲 `[itFirst2, itLast2)` または `range2` が、範囲 `[itFirst1, itLast1)` または `range1` の部分集合であるかを `bool` 型で返します。

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::cout << std::boolalpha;

	{
		std::vector<int> v1 = { 1, 2, 3, 8, 9, 10 };
		std::vector<int> v2 = { 1, 10 };
		std::vector<int> v3 = { 1, 2, 3, 4 };

		std::cout << std::ranges::includes(v1, v2) << '\n'; // true
		std::cout << std::ranges::includes(v1, v3) << '\n'; // false
	}

	std::cout << "---\n";

	{
		std::string s1 = "abcdef";
		std::string s2 = "ac";
		std::string s3 = "abcdefg";

		std::cout << std::ranges::includes(s1, s2) << '\n'; // true
		std::cout << std::ranges::includes(s1, s3) << '\n'; // false
	}
}
```
```txt:出力
true
false
---
true
false
```


# 7. 順列

## 7.1 順列を作成する
- `std::next_permutation(itFirst, itLast)` および `std::ranges::next_permutation(itFirst, itLast)`, `std::ranges::next_permutation(range)` は、範囲 `[itFirst, itLast)` または `range` の要素を、辞書順で次の順列に並び替えます。
- `std::next_permutation(itFirst, itLast)` は、新しい順列が以前の順列よりも辞書順であとにある場合 `true`, 順列が終端に達して最初の順列に戻った場合 `false` を返します。
- `std::ranges::next_permutation(itFirst, itLast)` および `std::ranges::next_permutation(range)` は、`bool` 型の代わりに `std::ranges::next_permutation_result` 型を返します。これは `{ itLast, bool found }` のようなクラスです。新しい順列が以前の順列よりも辞書順であとにある場合、`found` は `true`, 順列が終端に達して最初の順列に戻った場合 `false` になります。
- すべての順列を列挙するためにはソート済みの状態から始めます。

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	{
		// ソート済みの状態から始める
		std::vector<int> v = { 1, 2, 3, 4 };
		
		do
		{
			for (const auto& n : v)
			{
				std::cout << n << ' ';
			}
			std::cout << '\n';
		
		} while (std::ranges::next_permutation(v).found);
	}

	{
		// ソート済みの状態から始める
		std::string s = "abc";

		do
		{
			std::cout << s << '\n';

		} while (std::ranges::next_permutation(s).found);
	}
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
abc
acb
bac
bca
cab
cba
```
