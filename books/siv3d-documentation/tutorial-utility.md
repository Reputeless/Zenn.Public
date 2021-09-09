---
title: "チュートリアル 18 | 便利な関数"
free: true
---

# 18. 便利な関数
この章では、プログラミングに役立つ小さな便利関数を学びます。

## 18.1 最小値、最大値
`Min()`, `Max()` は、渡された引数から最小値、最大値を返します。2 つの引数の型が異なる場合は `Min<size_t>()` のように型を明示的に指定します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<int32> v = { 10, 20, 30 };

	Print << Max(100, 200);
	Print << Max(1.234, -3.456);
	Print << Max<size_t>(v.size(), 10);
	Print << Max({ 11, 44, 22, 55, 33 });

	Print << Min(100, 200);
	Print << Min(1.234, -3.456);
	Print << Min<size_t>(v.size(), 10);
	Print << Min({ 11, 44, 22, 55, 33 });

	while (System::Update())
	{

	}
}
```


## 18.2 指定した範囲に収める
`Clamp(x, min, max)` は、値 `x` を `min` 以上、`max` 以下に収めて返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << Clamp(10, 0, 100);
	Print << Clamp(-10, 0, 100);
	Print << Clamp(110, 0, 100);

	Print << Clamp(9.99, -1.0, 1.0);
	Print << Clamp(-9.99, -1.0, 1.0);
	Print << Clamp(0.0, -1.0, 1.0);

	while (System::Update())
	{

	}
}
```


## 18.3 指定した範囲内かを調べる
`InRange(x, min, max)` は、値 `x` が `min` 以上 `max` 以下であるかを `bool` 型で返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << InRange(10, 0, 100);
	Print << InRange(-10, 0, 100);
	Print << InRange(110, 0, 100);

	Print << InRange(9.99, -1.0, 1.0);
	Print << InRange(-9.99, -1.0, 1.0);
	Print << InRange(0.0, -1.0, 1.0);

	while (System::Update())
	{

	}
}
```


## 18.4 奇数か偶数かを判定する
`IsOdd(n)`, `IsEven(n)` は、それぞれ値 `n` が奇数であるか、偶数であるかを `bool` 型で返します。

Odd → 3 文字 → 奇数、Even → 4 文字 → 偶数、と覚えると簡単です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// IsOdd: 奇数であるか判定
	Print << IsOdd(1);
	Print << IsOdd(0);
	Print << IsOdd(-11);
	Print << IsOdd(9876543210ULL);

	// IsEven: 偶数であるか判定
	Print << IsEven(1);
	Print << IsEven(0);
	Print << IsEven(-11);
	Print << IsEven(9876543210ULL);

	while (System::Update())
	{

	}
}
```


## 18.5 指定した数の範囲をループする
Siv3D では、`for (int32 i = 0; i < N; ++i)` を `for (auto i : step(N))` と短く書ける機能があります（チュートリアル 3.2 参照）。

また、`for (auto i : Range(from, to))` (ただし `from <= to`) は、`for (auto i = from; i <= to; ++i)` の代わりになります。

`for (auto i : Range(from, to, step))` は
- `0 < step` のとき `for (auto i = from; i <= to; i += step)`
- `step < 0` のとき `for (auto i = from; to <= i; i += step)`

の代わりになります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	for (auto i : step(3))
	{
		Print << i;
	}

	Print << U"---";

	for (auto i : Range(5, 10))
	{
		Print << i;
	}

	Print << U"---";

	for (auto i : Range(20, 10, -2))
	{
		Print << i;
	}

	while (System::Update())
	{

	}
}
```


## 18.6 二重ループを 1 つにまとめる

```cpp

```


## 18.7 インデックス付きの range-based for

```cpp

```


## 18.8 絶対値を求める

```cpp

```


## 18.9 差の絶対値を求める

```cpp

```


## 18.10 文字の性質を調べる

```cpp

```


## 18.11 数字に桁区切りを入れる

```cpp

```


## 18.12 任意の場所に簡単にテキストを表示する

```cpp

```


## 18.13 データをコンソール出力する

```cpp

```


## 18.14 データをログ出力する

```cpp

```

