---
title: "<algorithm> [🚧執筆中🚧 🟢C++20 対応]"
free: true
---

アルゴリズムライブラリは、ある範囲の要素に対して検索、カウント、ソート、変更などの操作を行う機能を提供します。

C++20 以降では、新たに `std::ranges::` 名前空間に、既存の `std::` 名前空間のものよりも強化された機能が提供されています。新しい C++ を使える場合は、`std::ranges::` 名前空間で提供される機能を使うことで、より便利なコードや、わかりやすいエラーメッセージを得ることができます。


# 1. 最小値と最大値

## 1.1 二つの値のうち小さいほうの値を得る [🟢C++20]
- `std:min(a, b)` および `std::ranges::min(a, b)` は、`a` と `b` のうち小さいほうの値への const 参照を返します。
- 二つの値が等しい場合は `a` への const 参照を返します。
- `std:min(a, b)` において `a` と `b` が異なる型である場合、`std::min<Type>(a, b)` のように型 `Type` を明示的に指定することで、一方が `Type` に変換されます。
- `std::ranges::min(a, b)` において `a` と `b` が異なる型である場合、`std::ranges::min(a, static_cast<Type>(b))` のように型をそろえます。

```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	{
		int a = 30, b = 60;
		std::cout << std::ranges::min(a, b) << '\n';
	}

	{
		double a = 0.5, b = -10.5;
		std::cout << std::ranges::min(a, b) << '\n';
	}

	{
		char a = 'a', b = 'z';
		std::cout << std::ranges::min(a, b) << '\n';
	}

	{
		std::string a = "apple", b = "bird";
		std::cout << std::ranges::min(a, b) << '\n';
	}

	{
		int a = 30;
		std::size_t b = 60;
		std::cout << std::ranges::min(a, static_cast<int>(b)) << '\n';
	}

	{
		int a = 30;
		std::size_t b = 60;
		std::cout << std::ranges::min(static_cast<size_t>(a), b) << '\n';
	}
}
```
```txt:出力
30
-10.5
a
apple
30
30
```


## 1.2 二つの値のうち大きいほうの値を得る [🟢C++20]
- `std::max(a, b)` および `std::ranges::max(a, b)` は、`a` と `b` のうち大きいほうの値への const 参照を返します。
- 二つの値が等しい場合は `a` への const 参照を返します。
- `std::max(a, b)` において `a` と `b` が異なる型である場合、`std::max<Type>(a, b)` のように型 `Type` を明示的に指定することで、一方が `Type` に変換されます。
- `std::ranges::max(a, b)` において `a` と `b` が異なる型である場合、`std::ranges::max(a, static_cast<Type>(b))` のように型をそろえます。

```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	{
		int a = 30, b = 60;
		std::cout << std::ranges::max(a, b) << '\n';
	}

	{
		double a = 0.5, b = -10.5;
		std::cout << std::ranges::max(a, b) << '\n';
	}

	{
		char a = 'a', b = 'z';
		std::cout << std::ranges::max(a, b) << '\n';
	}

	{
		std::string a = "apple", b = "bird";
		std::cout << std::ranges::max(a, b) << '\n';
	}

	{
		int a = 30;
		std::size_t b = 60;
		std::cout << std::ranges::max(a, static_cast<int>(b)) << '\n';
	}

	{
		int a = 30;
		std::size_t b = 60;
		std::cout << std::ranges::max(static_cast<size_t>(a), b) << '\n';
	}
}
```
```txt:出力
60
0.5
z
bird
60
60
```


## 1.3 三つ以上の値から最小値を得る [🟢C++20]
- `std::min({ ... })` および `std::ranges::min({ ... })` は、リスト `{ ... }` 内の要素の最小値を返します。
- リスト内の要素は同じ型である必要があります。

```cpp
#include <iostream>
#include <algorithm>

int main()
{
	{
		int a = 30, b = 60, c = 50;
		std::cout << std::ranges::min({ a, b, c }) << '\n';
	}

	{
		double a = 0.1, b = 0.2, c = 0.3, d = 0.4;
		std::cout << std::ranges::min({ a, b, c, d }) << '\n';
	}
}
```
```txt:出力
30
0.1
```


## 1.4 三つ以上の値から最大値を得る [🟢C++20]
- `std::max({ ... })` および `std::ranges::max({ ... })` は、リスト `{ ... }` 内の要素の最大値を返します。
- リスト内の要素は同じ型である必要があります。

```cpp
#include <iostream>
#include <algorithm>

int main()
{
	{
		int a = 30, b = 60, c = 50;
		std::cout << std::ranges::max({ a, b, c }) << '\n';
	}

	{
		double a = 0.1, b = 0.2, c = 0.3, d = 0.4;
		std::cout << std::ranges::max({ a, b, c, d }) << '\n';
	}
}
```
```txt:出力
60
0.4
```


## 1.5 空でない範囲の中から最小の要素を得る [🟢C++20]
- 空でない範囲の中から最小の要素を得るには、`std::ranges::min(range)` を使います。
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
	}

	{
		std::deque<double> d = { 0.1, 0.2, 0.3, 0.4 };
		std::cout << std::ranges::min(d) << '\n'; // 0.1
	}

	{
		std::string s = "atcoder";
		std::cout << std::ranges::min(s) << '\n'; // 'a'
	}

	{
		std::vector<int> v = { 9, 8, 7, 6, 5, 4, 3, 2, 1 };
		std::cout << std::ranges::min(v | std::views::take(3)) << '\n'; // 最初の三つの範囲の最小値: 7
	}
}
```
```txt:出力
30
0.1
a
7
```


## 1.6 空でない範囲の中から最大の要素を得る [🟢C++20]
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
		std::cout << std::ranges::max(v) << '\n'; // 60
	}

	{
		std::deque<double> d = { 0.1, 0.2, 0.3, 0.4 };
		std::cout << std::ranges::max(d) << '\n'; // 0.4
	}

	{
		std::string s = "atcoder";
		std::cout << std::ranges::max(s) << '\n'; // 't'
	}

	{
		std::vector<int> v = { 2, 4, 6, 8, 10 };
		std::cout << std::ranges::max(v | std::views::take(3)) << '\n'; // 最初の三つの範囲の最大値: 6
	}
}
```
```txt:出力
60
0.4
t
6
```


## 1.7 配列の中から最小の要素とその位置を得る [🟢C++20]
- `std::min_element(irFirst, itLast)`, `std::ranges::min_element(irFirst, itLast)` および `std::ranges::min_element(range)` は、範囲 `[itFirst, itLast)` または `range` の中で最小の要素の位置を指すイテレータを返します。
- 範囲の要素数が 0 の場合、範囲の終端イテレータを返します。
- イテレータに `*` を付けると、そのイテレータが指す値にアクセスできます。
- イテレータの指す値が、配列の何番目にあるかを整数値で得るには、`std::distance(itFirst, itLast)` または `std::ranges::distance(itFirst, itLast)` に、範囲の先頭イテレータと、戻り値のイテレータを渡すことで、その間の距離を求めます。

> - `min_element` の計算量: $O(N)$
> - `distance` の計算量: イテレータがランダムアクセスイテレータであれば $O(1)$, それ以外の場合は $O(N)$

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


## 1.8 配列の中から最大の要素とその位置を得る [🟢C++20]
- `std::max_element(irFirst, itLast)`, `std::ranges::max_element(irFirst, itLast)` および `std::ranges::max_element(range)` は、範囲 `[itFirst, itLast)` または `range` の中で最大の要素の位置を指すイテレータを返します。
- 範囲の要素数が 0 の場合、範囲の終端イテレータを返します。
- イテレータに `*` を付けると、そのイテレータが指す値にアクセスできます。
- イテレータの指す値が、配列の何番目にあるかを整数値で得るには、`std::distance(itFirst, itLast)` または `std::ranges::distance(itFirst, itLast)` に、範囲の先頭イテレータと、戻り値のイテレータを渡すことで、その間の距離を求めます。

> - `max_element` の計算量: $O(N)$
> - `distance` の計算量: イテレータがランダムアクセスイテレータであれば $O(1)$, それ以外の場合は $O(N)$

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


## 1.7 二つの値から小さいほうの値と大きいほうの値を一度に得る [🟢C++20]
- `std::minmax(a, b)` は、小さいほうの値、大きいほうの値それぞれへの const 参照を組にした `std::pair` を返します。
- `std::ranged::minmax(a, b)` は、小さいほうの値、大きいほうの値それぞれへを格納した `std::ranges::min_max_result` を返します。
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


## 1.8 三つ以上の値から最小値と最大値を一度に得る [🟢C++20]
- `std::minmax({ ... })` は、最小の値、最大の値それぞれへの const 参照を組にした `std::pair` を返します。
- `std::ranged::minmax({ ... })` は、最小の値、最大の値それぞれを格納した `std::ranges::min_max_result` を返します。
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


## 1.9 空でない範囲の中から最小の要素と最大の要素を一度に得る [🟢C++20]
- `std::ranged::minmax(range)` は、空でない範囲 `range` の中の最小値、最大値それぞれを格納した `std::ranges::min_max_result` を返します。
- 戻り値を構造化束縛で受け取ることができます。
- `range` の要素数が 0 の場合、実行時エラーになります。

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


## 1.10 配列の中から最小の要素と最大の要素、およびそれらの位置を一度に得る [🟢C++20]
- `std::minmax_element(irFirst, itLast)` は、範囲 `[itFirst, itLast)` の中で最小の値、最大の値それぞれへのイテレータを組にした `std::pair` を返します。
- `std::ranges::minmax_element(irFirst, itLast)` および `std::ranges::minmax_element(range)` は、範囲 `range` の中で最小の値、最大の値それぞれへのイテレータを組にした `std::minmax_element_result` を返します。
- 範囲の要素数が 0 の場合、どちらも範囲の終端イテレータになります。
- イテレータに `*` を付けると、そのイテレータが指す値にアクセスできます。
- イテレータの指す値が、配列の何番目にあるかを整数値で得るには、`std::distance(itFirst, itLast)` または `std::ranges::distance(itFirst, itLast)` に、範囲の先頭イテレータと、戻り値のイテレータを渡すことで、その間の距離を求めます。

> - `minmax_element` の計算量: $O(N)$
> - `distance` の計算量: イテレータがランダムアクセスイテレータであれば $O(1)$, それ以外の場合は $O(N)$

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <string>

int main()
{
	{
		std::vector<int> numbers = { -5, 10, -30, 20, 50, 0 };
		auto minmax = std::ranges::minmax_element(numbers);
		std::cout << "min: " << *minmax.min << ", max: " << *minmax.max << '\n'; // min: -30, max: 50
	}

	{
		std::vector<std::string> words = { "cat", "apple", "bird", "dog" };
		auto [itMin, itMax] = std::ranges::minmax_element(words);
		std::cout << "min: " << *itMin << ", max: " << *itMax << '\n'; // min: apple, max: dog
		std::cout << std::ranges::distance(words.begin(), itMin) << ", " << std::ranges::distance(words.begin(), itMax) << '\n'; // 1, 3
	}
}
```
```txt:出力
min: -30, max: 50
min: apple, max: dog
1, 3
```


## 1.11 ある値を、指定した最小値と最大値の範囲に収める [🟢C++20]
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

## 2.1 全ての要素が条件を満たすかを調べる
- `std::all_of(itFirst, itLast, unaryPred)` あるいは `std::ranges::all_of(itFirst, itLast, unaryPred)`, `std::ranges::all_of(range, unaryPred)` は、範囲 `[itFirst, itLast)`　または `range` にある要素すべてが条件 `unaryPred` を満たしているかを `bool` 型の値で返します。
- 範囲が空の場合は `true` を返します。
- `unaryPred` は、要素に対して条件を満たすかを返す関数や関数オブジェクトです。

> - `all_of` の計算量: $O(N)$

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
		std::cout << std::ranges::all_of(v, IsOK) << '\n'; // ラムダ式を使う
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
		std::cout << std::ranges::any_of(v, IsNG) << '\n';
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
		std::cout << std::ranges::none_of(v, IsNG) << '\n';
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

> - `count` の計算量: $O(N)$

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

> - `find` の計算量: $O(N)$
> - `distance` の計算量: イテレータがランダムアクセスイテレータであれば $O(1)$, それ以外の場合は $O(N)$

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

> - `find_if` の計算量: $O(N)$
> - `distance` の計算量: イテレータがランダムアクセスイテレータであれば $O(1)$, それ以外の場合は $O(N)$

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

