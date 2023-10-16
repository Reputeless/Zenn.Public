---
title: "<numeric> [🟢C++20 対応]"
free: true
---

# 1. 範囲の集計

## 1.1 配列の要素の和を計算する（順不同）
- `std::reduce(itFirst, itLast)` は、イテレータで指定した範囲 `[itFirst, itLast)` の値を**順不同で** `+` で足していった結果（合計）を返します。
	- 一般的な実装では、`std::reduce` に並列実行のための追加のパラメータ（本資料では扱わない）を指定しない限り順番は前後しませんが、保証はされていません。
	- 整数の合計を求める用途など、順番が保証されていなくても問題ない場合に使います。
- この関数の戻り値の型は、配列の要素の型によって決まります。

```cpp
#include <iostream>
#include <vector>
#include <numeric>

int main()
{
	std::vector<int> v1 = { 1, 2, 3, 4, 5 };
	int a = std::reduce(v1.begin(), v1.end());
	std::cout << a << '\n';

	std::vector<double> v2 = { 0.1, 0.2, 0.3, 0.4, 0.5 };
	double b = std::reduce(v2.begin(), v2.end());
	std::cout << b << '\n';
}
```
```txt:出力
15
1.5
```

## 1.2 配列の要素の和を、型を指定して計算する（順不同）
- `std::reduce(itFirst, itLast, init)` は、イテレータで指定した範囲 `[itFirst, itLast)` の値を、`init` に**順不同で** `+` で足していった結果（合計）を返します。
	- 一般的な実装では、`std::reduce` に並列実行のための追加のパラメータ（本資料では扱わない）を指定しない限り順番は前後しませんが、保証はされていません。
	- 整数の合計を求める用途など、順番が保証されていなくても問題ない場合に使います。
- この関数の戻り値の型は、`init` の型によって決まります。
- `int` 型の整数の和を `long long` 型で計算したいときに `init` を `long long` 型である `0LL` にします。


```cpp
#include <iostream>
#include <vector>
#include <numeric>

int main()
{
	std::vector<int> v(10, 500000000);
	
	int a = std::reduce(v.begin(), v.end()); // int 型で計算する
	std::cout << a << '\n';

	long long b = std::reduce(v.begin(), v.end(), 0LL); // long long 型で計算する
	std::cout << b << '\n';
}
```
```txt:出力
705032704
5000000000
```


## 1.3 配列の要素の和を、型を指定して計算する（順序を保証）
- `std::accumulate(itFirst, itLast, init)` は、初期値 `init` にイテレータで指定した範囲 `[itFirst, itLast)` の値を**先頭から順に** `+` で足していった結果を返します。
- つまり、`init` が `0` なら、指定した範囲の要素の合計を計算できます。
- この関数の戻り値の型は、配列の要素の型とは関係なく `init` の型で決まるため、`double` 型で合計を求める場合は `init` を `double` 型である `0.0` にする必要があるので注意してください。

```cpp
#include <iostream>
#include <vector>
#include <numeric>

int main()
{
	std::vector<std::string> v = { "a", "b", "c", "d", "e", "f" };
	
	std::string a = std::reduce(v.begin(), v.end());
	std::cout << a << '\n'; // abcdef になることが保証されない（defabc になるかもしれない）

	std::string b = std::accumulate(v.begin(), v.end(), std::string());
	std::cout << b << '\n'; // abcdef になることが保証される
}
```
```txt:出力例
abcdef
abcdef
```

```cpp
#include <iostream>
#include <vector>
#include <numeric>

int main()
{
	std::vector<double> v(107, 0.1);

	double a = std::accumulate(v.begin(), v.end(), 0); // int 型で計算する
	std::cout << a << '\n';

	double b = std::accumulate(v.begin(), v.end(), 0.0); // double 型で計算する
	std::cout << b << '\n';
}
```
```txt:出力
0
10.7
```


## 1.4 累積和を求める
- `std::partial_sum(itFirst, itLast, itOut)` は、イテレータで指定した範囲 `[itFirst, itLast)` の先頭からの累積和を `itOut` に出力していきます。
- `std::back_inserter()` と組み合わせることで、累積和の結果を別の配列に格納することができます。

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <iterator> // std::back_inserter() のため

int main()
{
	std::vector<int> v = { 1, 2, 3, 4, 5 };
	std::vector<int> sums = { 0 }; // 最初の要素として 0 を入れておく

	// 累積和の結果を sums の末尾に都度追加していく
	std::partial_sum(v.begin(), v.end(), std::back_inserter(sums));

	for (const auto& sum : sums)
	{
		std::cout << sum << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
0 1 3 6 10 15
```


# 2. 数列の作成

## 2.1 配列の要素を 1 ずつ増えていく値で埋める
- `std::iota(itFirst, itLast, value)` は、イテレータで指定した範囲 `[itFirst, itLast)` を `value` から 1 ずつ増えていく値で埋めます。

```cpp
#include <iostream>
#include <vector>
#include <numeric>

int main()
{
	std::vector<int> numbers(10);
	
	std::iota(numbers.begin(), numbers.end(), 0); // 0, 1, ... をセットする
	
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
0 1 2 3 4 5 6 7 8 9
```

## 2.2 配列や文字列を `'a'` ～ `'z'` で埋める
- `std::iota(itFirst, itLast, value)` において、範囲の要素数をアルファベットと同じ 26 個にして、`value` を `'a'` にすると、`'a'` ～ `'z'` で範囲を埋めることができます。

### `std::vector<char>` の場合

```cpp
#include <iostream>
#include <vector>
#include <numeric>

int main()
{
	std::vector<char> v(26);

	std::iota(v.begin(), v.end(), 'a'); // 'a', 'b', ... をセットする
	
	for (const auto& ch : v)
	{
		std::cout << ch;
	}

	std::cout << '\n';
}
```
```txt:出力
abcdefghijklmnopqrstuvwxyz
```

### `std::string` の場合

```cpp
#include <iostream>
#include <string>
#include <numeric>

int main()
{
	std::string s(26, 'a'); // 26 個の 'a' で初期化する

	std::iota(s.begin(), s.end(), 'a'); // 'a', 'b', ... をセットする
	
	std::cout << s << '\n';
}
```
```txt:出力
abcdefghijklmnopqrstuvwxyz
```


# 3. 最大公約数と最小公倍数

## 3.1 最大公約数を求める
- `std::gcd(a, b)` は、整数 `a`, `b` の最大公約数 (greatest common divisor) を返します。

```cpp
#include <iostream>
#include <numeric>

int main()
{
	std::cout << std::gcd(100, 150) << '\n';

	std::cout << std::gcd(1234567890LL, 9876543210LL) << '\n';
}
```
```txt:出力
50
90
```

## 3.2 最小公倍数を求める
- `std::lcm(a, b)` は、整数 `a`, `b` の最小公倍数 (least common multiple) を返します。

```cpp
#include <iostream>
#include <numeric>

int main()
{
	std::cout << std::lcm(100, 150) << '\n';

	std::cout << std::lcm(1234567890LL, 9876543210LL) << '\n';
}
```
```txt:出力
300
135480701236261410
```

