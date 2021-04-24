---
title: "<numeric>"
free: true
---

# 1. 範囲の集計

## 1.1 配列の要素の和（合計）を計算する
- `std::accumulate(itFirst, itLast, init)` は、初期値 `init` にイテレータで指定した範囲 `[itFirst, itLast)` の値を先頭から順に `+` で足していった結果を返します
- つまり、`init` が `0` なら、指定した範囲の要素の合計を計算できます
- この関数の戻り値の型は、配列の要素の型とは関係なく `init` の型で決まるため、`double` 型で合計を求める場合は `init` を `double` 型である `0.0` にする必要があるので注意してください
```cpp
#include <iostream>
#include <vector>
#include <numeric>

int main()
{
	std::vector<int> vi = { 1, 2, 3, 4, 5 };
	int a = std::accumulate(vi.begin(), vi.end(), 0);
	std::cout << a << '\n';

	std::vector<double> vd = { 0.1, 0.2, 0.3, 0.4, 0.5 };
	double b = std::accumulate(vd.begin(), vd.end(), 0.0); // double 型で計算するために 0.0 にする
	std::cout << b << '\n';
}
```
```txt:出力
15
1.5
```

## 1.2 配列の要素の積を計算する
- `std::accumulate(itFirst, itLast, init, binaryOp)` は、初期値 `init` にイテレータで指定した範囲 `[itFirst, itLast)` の値を先頭から順に `binaryOp` による計算を繰り返した結果を返します
- `binaryOp` を `std::multiplies<>{}`, `init` を 1 にすることで、指定した範囲の要素の積を得られます
- この関数の戻り値の型は、配列の要素の型とは関係なく `init` の型で決まるため、`double` 型で積を求める場合は `init` を `double` 型である `1.0` にする必要があるので注意してください

```cpp
#include <iostream>
#include <vector>
#include <numeric>

int main()
{
	std::vector<int> vi = { 1, 2, 3, 4, 5 };
	int a = std::accumulate(vi.begin(), vi.end(), 1, std::multiplies<>{});
	std::cout << a << '\n';

	std::vector<double> vd = { 0.1, 0.2, 0.3, 0.4, 0.5 };
	double b = std::accumulate(vd.begin(), vd.end(), 1.0, std::multiplies<>{}); // double 型で計算するために 1.0 にする
	std::cout << b << '\n';
}
```
```txt:出力
120
0.0012
```

## 1.3 累積和を求める
- `std::partial_sum(itFirst, itLast, itOut)` は、イテレータで指定した範囲 `[itFirst, itLast)` の先頭からの累積和を `itOut` に出力していきます
- `std::back_inserter()` と組み合わせることで、累積和の結果を別の配列に格納することができます
```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <iterator> // std::back_inserter() のため

int main()
{
	std::vector<int> v = { 1, 2, 3, 4, 5 };
	std::vector<int> sums = { 0 }; // 最初の要素は 0

	// 累積和の結果を sums の末尾に随時追加
	std::partial_sum(v.begin(), v.end(), std::back_inserter(sums));

	for (const auto& sum : sums)
	{
		std::cout << sum << '\n';
	}
}
```
```txt:出力
0
1
3
6
10
15
```

# 2. 数列の作成

## 2.1 配列の要素を 1 ずつ増えていく値で埋める
- `std::iota(itFirst, itEnd, value)` は、イテレータで指定した範囲 `[itFirst, itLast)` を `value` から 1 ずつ増えていく値で埋めます
```cpp
#include <iostream>
#include <vector>
#include <numeric>

int main()
{
	std::vector<int> numbers(10); // 10 個の要素数
	std::iota(numbers.begin(), numbers.end(), 0); // 0, 1, ... をセット
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}

	std::cout << "---\n";

	std::iota(numbers.begin(), numbers.end(), -5); // -5, -4, ... をセット
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}
}
```
```txt:出力
0
1
2
3
4
5
6
7
8
9
---
-5
-4
-3
-2
-1
0
1
2
3
4
```

## 2.2 配列 / 文字列を `'a'` ～ `'z'` で埋める
- `std::iota(itFirst, itEnd, value)` において、範囲の要素数をアルファベットと同じ 26 個にして、`value` を `'a'` にすると、`'a'` ～ `'z'` で範囲を埋めることができます
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <numeric>

int main()
{
	std::vector<char> chars(26); // 26 個の要素数
	std::iota(chars.begin(), chars.end(), 'a'); // 'a', 'b', ... をセット
	for (const auto& ch : chars)
	{
		std::cout << ch;
	}
	std::cout << '\n';

	std::string s(26, 'a'); // 26 個の 'a'
	std::iota(s.begin(), s.end(), 'a'); // 'a', 'b', ... をセット
	std::cout << s << '\n';
}
```
```txt:出力
abcdefghijklmnopqrstuvwxyz
abcdefghijklmnopqrstuvwxyz
```

# 3. 最大公約数と最小公倍数

## 3.1 最大公約数を求める
- `std::gcd(a, b)` は、整数 `a`, `b` の最大公約数 (greatest common divisor) を返します
```cpp
#include <iostream>
#include <numeric>

int main()
{
	std::cout << std::gcd(100, 150) << '\n';
}
```
```txt:出力
50
```

## 3.2 最小公倍数を求める
- `std::lcm(a, b)` は、整数 `a`, `b` の最小公倍数 (least common multiple) を返します
```cpp
#include <iostream>
#include <numeric>

int main()
{
	std::cout << std::lcm(100, 150) << '\n';
}
```
```txt:出力
300
```
