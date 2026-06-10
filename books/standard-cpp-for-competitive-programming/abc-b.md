---
title: "ABC B 問題 C++ 解法"
free: true
---

- AtCoder Beginner Contest（ABC）B 問題
- C++ 標準ライブラリの機能を効果的に活用した、クリーンな C++ コードによる模範解答集です
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
<!-- 

## ABC450～

:::details ABC459 B - 459
### [ABC459 B - 459](https://atcoder.jp/contests/abc459/tasks/abc459_b)
```cpp

```
:::

:::details ABC458 B - Count Adjacent Cells
### [ABC458 B - Count Adjacent Cells](https://atcoder.jp/contests/abc458/tasks/abc458_b)
```cpp

```
:::

:::details ABC457 B - Arrays
### [ABC457 B - Arrays](https://atcoder.jp/contests/abc457/tasks/abc457_b)
```cpp

```
:::

:::details ABC456 B - 456
### [ABC456 B - 456](https://atcoder.jp/contests/abc456/tasks/abc456_b)
```cpp

```
:::

:::details ABC455 B - Spiral Galaxy
### [ABC455 B - Spiral Galaxy](https://atcoder.jp/contests/abc455/tasks/abc455_b)
```cpp

```
:::

:::details ABC454 B - Mapping
### [ABC454 B - Mapping](https://atcoder.jp/contests/abc454/tasks/abc454_b)
```cpp

```
:::

:::details ABC453 B - Sensor Data Logging
### [ABC453 B - Sensor Data Logging](https://atcoder.jp/contests/abc453/tasks/abc453_b)
```cpp

```
:::

:::details ABC452 B - Draw Frame
### [ABC452 B - Draw Frame](https://atcoder.jp/contests/abc452/tasks/abc452_b)
```cpp

```
:::

:::details ABC451 B - Personnel Change
### [ABC451 B - Personnel Change](https://atcoder.jp/contests/abc451/tasks/abc451_b)
```cpp

```
:::

:::details ABC450 B - Split Ticketing
### [ABC450 B - Split Ticketing](https://atcoder.jp/contests/abc450/tasks/abc450_b)
```cpp

```
:::

 -->
