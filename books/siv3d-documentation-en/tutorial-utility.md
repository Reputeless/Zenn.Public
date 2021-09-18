---
title: "チュートリアル 18 | 便利な関数"
free: true
---

# 18. 便利な関数
この章では、Siv3D プログラミングに役立つ小さな便利関数や機能を学びます。

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

「Odd → 3 文字 → 奇数」「Even → 4 文字 → 偶数」と覚えると簡単です。

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
	// 0, 1, 2
	for (auto i : step(3))
	{
		Print << i;
	}

	Print << U"---";

	// 5, 6, 7, 8, 9, 10
	for (auto i : Range(5, 10))
	{
		Print << i;
	}

	Print << U"---";

	// 20, 18, 16, 14, 12, 10
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
`for (auto p : step({size.w, size.h}))` および `for (auto p : step(size))` (`size` は `Size` 型) は、
```cpp
for (int32 y = 0; y < size.h; ++y)
{
	for (int32 x = 0; x < size.w; ++x)
	{
		Point p{ x, y };
	}
}
```
の代わりになります。ただし、コンパイラによっては若干のオーバーヘッドが生じるため、速度が最重要の場面では通常の二重ループを書くことが望ましいです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	for (auto p : step({ 2, 3 }))
	{
		Print << p;
	}

	Print << U"---";

	const Size size{ 2, 4 };

	for (auto p : step(size))
	{
		Print << p;
	}

	Print << U"---";

	const Grid grid{ {10, 20}, {30, 40} };

	for (auto p : step(grid.size()))
	{
		Print << grid[p];
	}

	while (System::Update())
	{

	}
}
```


## 18.7 インデックス付きの range-based for
`Indexed()` を使うと、range-based for ループにおいてインデックス値を使えます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<String> animals = { U"cat", U"dog", U"bird" };

	for (auto [i, animal] : Indexed(animals))
	{
		Print << i << U": " << animal;
	}

	while (System::Update())
	{

	}
}
```


## 18.8 絶対値を求める
`Abs(x)` は `x` の絶対値を返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 絶対値
	Print << Abs(123);
	Print << Abs(-123);
	Print << Abs(3.45);
	Print << Abs(-3.45);

	while (System::Update())
	{

	}
}
```


## 18.9 差の絶対値を求める
`AbsDiff(a, b)` は `a` と `b` の差の絶対値を返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 差の絶対値
	Print << AbsDiff(50, 10);
	Print << AbsDiff(10u, 50u);
	Print << AbsDiff(-2000000000, 2000000000);
	Print << AbsDiff(1.23, -1.23);

	while (System::Update())
	{

	}
}
```


## 18.10 文字の性質を調べる
文字 (ASCII 文字）に関する次のような関数があります。

| 関数 | 説明 |
|--|--|
|`bool IsASCII(char32)`| ASCII 文字であるかを返す |
|`bool IsDigit(char32)`| 10 進数の数字であるかを返す |
|`bool IsLower(char32)`| アルファベットの小文字であるかを返す |
|`bool IsUpper(char32)`| アルファベットの大文字であるかを返す |
|`bool IsAlpha(char32)`| 文字がアルファベットであるかを返す |
|`bool IsAlnum(char32)`| 文字がアルファベットもしくは数字であるかを返す |
|`bool IsXdigit(char32)`| 文字が 16 進数の数字であるかを返す |
|`bool IsControl(char32)`| 文字が制御文字であるかを返す |
|`bool IsBlank(char32)`| 文字が空白文字 (`' '`, `'\t'`, および全角空白) であるかを返す |
|`bool IsSpace(char32)`| 文字が空白類文字 (`' '`, `'\t'`, `'\n'`, `'\v'`, `'\f'`, `'\r'`, および全角空白) であるかを返す |
|`bool IsPrint(char32)`| 文字が印字可能文字であるかを返す |
|`char32 ToLower(char32)`| アルファベットの大文字を小文字にする |
|`char32 ToUpper(char32)`| アルファベットの小文字を大文字にする |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << IsASCII(U'A') << U' ' << IsASCII(U'あ');
	Print << IsUpper(U'A') << U' ' << IsUpper(U'a');
	Print << IsAlnum(U'4') << U' ' << IsAlnum(U'#');
	Print << IsSpace(U' ') << U' ' << IsSpace(U'-');
	Print << ToLower(U'A') << U' ' << ToLower(U'a');

	while (System::Update())
	{

	}
}
```


## 18.11 数字に桁区切りを入れる
`ThousandsSeparate(x)` は、数値 `x` に桁区切りを入れて文字列にした結果を返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 桁区切りを入れる
	Print << ThousandsSeparate(123456789);
	Print << ThousandsSeparate(-123456789);
	Print << ThousandsSeparate(1234567.89);

	while (System::Update())
	{

	}
}
```


## 18.12 任意の場所に簡単にテキストを表示する
`PutText(s, pos)` は、文字列 `s` を座標 `pos` を中心に描きます。`Print` と同じフォントが使われます。`Print` と異なり画面に残り続けることはありません。

`PutText(s, Arg::topLeft = pos)` で座標を左上基準にすることもできます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// テキストを表示
		PutText(DateTime::Now().format(), Scene::Center());
		PutText(U"Hello, Siv3D!", Arg::topLeft = Cursor::Pos());
	}
}
```


## 18.13 データをコンソール出力する
`Console` に向かって、出力の記号 `<<` で値を送ると、その値がコンソール出力されます。Windows の場合はコマンドプロンプトに出力されます。出力データが大量にある場合や、データをクリップボードにコピーしたい際に便利です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10,20,30,40,50 };

	Print << v;

	// コンソール出力
	Console << v;

	Console << U"Hello, Siv3D!";

	while (System::Update())
	{

	}
}
```


## 18.14 データをログ出力する
`Logger` に向かって、出力の記号 `<<` で値を送ると、その値がログ出力されます。Windows の場合は Visual Studio の「出力」ウィンドウに出力されます。出力データが大量にある場合に便利です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10,20,30,40,50 };

	Print << v;

	// ログ出力
	Logger << v;

	Logger << U"Hello, Siv3D!";

	while (System::Update())
	{

	}
}
```


## 18.15 数学定数
Siv3D には次のような数学定数が用意されています。

| 名前 | 型 | 説明 | 値 (実際の `double`, `float` 型より高い精度の桁数で表示) |
|--|--|--|--|
| `Math::E` | `double` | 自然対数の底 | 2.718281828459045235360287471352662498 |
| `Math::Log2E` | `double` | 2 を底とする e の対数 | 1.442695040888963407359924681001892137 |
| `Math::Log10E` | `double` | 10 を底とする e の対数 | 0.434294481903251827651128918916605082 |
| `Math::Pi` | `double` | π（円周率） | 3.141592653589793238462643383279502884 |
| `Math::QuarterPi` | `double` | π/4 | 0.785398163397448309615660845819875721 |
| `Math::OneThirdPi` | `double` | π/3 | 1.047197551196597746154214461093167628 |
| `Math::HalfPi` | `double` | π/2 | 1.570796326794896619231321691639751442 |
| `Math::TwoPi` | `double` | 2π | 6.283185307179586476925286766559005768 |
| `Math::InvTwoPi` | `double` | 1/(2π) | 0.159154943091895335768883763372514362 |
| `Math::InvPi` | `double` | 1/π | 0.318309886183790671537767526745028724 |
| `Math::InvSqrtPi` | `double` | 1/√π | 0.564189583547756286948079451560772586 |
| `Math::Ln2` | `double` | 2 の自然対数 | 0.693147180559945309417232121458176568 |
| `Math::Ln10` | `double` | 10 の自然対数 | 2.302585092994045684017991454684364208 |
| `Math::Sqrt2` | `double` | √2 | 1.414213562373095048801688724209698078 |
| `Math::Sqrt3` | `double` | √3 | 1.732050807568877293527446341505872366 |
| `Math::InvSqrt2` | `double` | 1/√2 | 0.707106781186547524400844362104849039 |
| `Math::InvSqrt3` | `double` | 1/√3 | 0.577350269189625764509148780501957456 |
| `Math::EGamma` | `double` | オイラーの定数 | 0.577215664901532860606512090082402431 |
| `Math::Phi` | `double` | 黄金数 (φ) | 1.618033988749894848204586834365638117 |
| `Math::QNaN` | `double` | Quiet NaN | QNaN |
| `Math::NaN` | `double` | Signaling NaN | SNaN |
| `Math::Inf` | `double` | Inf | Inf |
| `Math::PiF` | `float` | π（円周率） | 3.141592653589793238462643383279502884 |
| `Math::QuarterPiF` | `float` | π/4 | 0.785398163397448309615660845819875721 |
| `Math::OneThirdPiF` | `float` | π/3 | 1.047197551196597746154214461093167628 |
| `Math::HalfPiF` | `float` | π/2 | 1.570796326794896619231321691639751442 |
| `Math::TwoPiF` | `float` | 2π | 6.283185307179586476925286766559005768 |
| `Math::InvTwoPiF` | `float` | 1/(2π) | 0.159154943091895335768883763372514362 |
| `Math::InvPiF` | `float` | 1/π | 0.318309886183790671537767526745028724 |
| `Math::InvSqrtPiF` | `float` | 1/√π | 0.564189583547756286948079451560772586 |
| `Math::QNaNF` | `float` | Quiet NaN | QNaN |
| `Math::NaNF` | `float` | Signaling NaN | SNaN |
| `Math::InfF` | `float` | Inf | Inf |


## 18.16 エラー
Siv3D のプログラムでエラーを伝える例外を簡単に送出したい場合、`Error` クラスを使うと便利です。

（Windows 版）Visual Studio において、例外の発生箇所を IDE 上で表示する手順は、[テクニック | 生産性の向上](https://zenn.dev/reputeless/books/siv3d-documentation/viewer/productivity)を参照してください。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"aaa.png" };

	if (not texture)
	{
		// 例外を送出する
		throw Error{ U"Failed to load `aaa.png`" };
	}

	while (System::Update())
	{

	}
}
```

