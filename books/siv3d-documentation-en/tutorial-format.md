---
title: "Tutorial 10 | Formatting and Parsing"
free: true
---

# 10. Formatting and Parsing
この章では、数値データを文字列に変換する方法と、文字列を数値データに変換する方法を学びます。

## 10.1 数値から文字列への変換
`Format()` を使うと、文字列への変換に対応したあらゆるデータを `String` に変換できます。
![](/images/doc_v6/tutorial/10/1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const String a = Format(12345);
	Print << a;

	const String b = Format(true);
	Print << b;

	const String c = Format(1.23456789);
	Print << c;

	const String d = Format(Vec2{ 11, 22 });
	Print << d;

	const Array<int32> values = { 3, 4, 5, 6 };
	const String e = Format(values);
	Print << e;

	const std::array<ColorF, 3> colors =
	{
		ColorF{ 1.0 , 0.0, 0.0 },
		ColorF{ 0.0 , 1.0, 0.0 },
		ColorF{ 0.0 , 0.0, 1.0 },
	};
	const String f = Format(colors);
	Print << f;

	const String g = Format(Rect{ 30, 50, 100, 50 });
	Print << g;

	// (復習) Print は String でなくても使える
	Print << 12345;
	Print << colors;
	Print << Rect{ 30, 50, 100, 50 };

	while (System::Update())
	{

	}
}
```


## 10.2 フォーマット指定子を使った、数値から文字列への変換
文字列リテラルのあとに `_fmt()` サフィックスを付けると、文字列リテラル内に記述した `{}` という箇所に、`( )` 内に記述した引数が文字列化されて挿入されます。

### 10.2.1 基本
![](/images/doc_v6/tutorial/10/2.1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"Siv{}D"_fmt(3);

	Print << U"{}/{}/{}"_fmt(2020, 12, 31);

	Print << U"Hello, {}!"_fmt(U"Siv3D");

	Print << U"position: {}, color: {}"_fmt(Point{ 23, 45 }, ColorF{ 0.7, 0.8, 0.9 });

	// '{', '}' を使いたい場合は "{{", "}}" を使う 
	Print << U"{{abc}} {}"_fmt(123);

	while (System::Update())
	{

	}
}
```


### 10.2.2 インデックスの指定
`{0}`, `{1}` のようにインデックスを記述して、`_fmt()` 内の対応する引数を指定できます。
![](/images/doc_v6/tutorial/10/2.2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"{2}/{1}/{0}"_fmt(2021, 12, 31);

	Print << U"{0}/{1}/{2}"_fmt(2021, 12, 31);

	Print << U"C{0}{0}"_fmt(U'+');

	Print << U"{0} - {1} - {0}"_fmt(U"Tokyo", U"Osaka");

	while (System::Update())
	{

	}
}
```


### 10.2.3 小数点以下の桁数
小数点以下 N 桁を変換したい場合は `{:.Nf}` と記述します。指定しなかった場合は、格納されている値の最大精度の桁数で変換されます。
![](/images/doc_v6/tutorial/10/2.3.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"{}"_fmt(Math::Pi);

	Print << U"{:.3f}"_fmt(Math::Pi);

	// インデックス指定と組み合わせる場合、インデックス値は : の左
	Print << U"{1} ≒ {0:.6f}"_fmt(Math::Pi, U"π");

	Print << U"{}"_fmt(12345.678);

	Print << U"{:.3f}"_fmt(12345.678);

	Print << U"{:.6f}"_fmt(12345.678);

	Print << U"{}"_fmt(9876543.21);

	Print << U"{:.0f}"_fmt(9876543.21);

    // Vec2 型にも使える
	Print << U"{}"_fmt(Vec2{ 1.111, 2.222 });
	Print << U"{:.1f}"_fmt(Vec2{ 1.111, 2.222 });

	while (System::Update())
	{

	}
}
```


### 10.2.4 パディング
N 文字の幅になるよう、文字の左にパティング文字 c を挿入したい場合は `{:c>N}`, 右に挿入したい場合は `{:c<N}`, 左右に均等に挿入したい場合は `{:c^N}` と記述します。`c` を省略した場合は半角スペースが使われます。
![](/images/doc_v6/tutorial/10/2.4.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"{:0>5}"_fmt(3);
	Print << U"{:>5}"_fmt(3);

	Print << U"{:>6}"_fmt(100);
	Print << U"{:*>6}"_fmt(100);
	Print << U"{:<6}"_fmt(100);
	Print << U"{:*<6}"_fmt(100);
	Print << U"{:^6}"_fmt(100);
	Print << U"{:*^6}"_fmt(100);

	Print << U"{:?>6}"_fmt(U"aaa");
	Print << U"{:?>6}"_fmt(U"aaabbb");
	Print << U"{:?>6}"_fmt(U"aaabbbccc");

	while (System::Update())
	{

	}
}
```


### 10.2.5 基数
`{:X}` は大文字の十六進数、`{:x}` は小文字の十六進数、`{:o}` は八進数、`{:b}` は二進数に変換します。`#` を付けるとプレフィックスが付きます。
![](/images/doc_v6/tutorial/10/2.5.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"{:X}"_fmt(255);
	Print << U"{:x}"_fmt(255);
	Print << U"{:o}"_fmt(255);
	Print << U"{:b}"_fmt(255);
	Print << U"{:#X}"_fmt(255);
	Print << U"{:#x}"_fmt(255);
	Print << U"{:#o}"_fmt(255);
	Print << U"{:#b}"_fmt(255);

	while (System::Update())
	{

	}
}
```


### 10.2.6 符号
`{:+}` は正の値に + 記号を付加し、`{: }` は正の値に半角空白を付加します。
![](/images/doc_v6/tutorial/10/2.6.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
    Print << U"{}/{}"_fmt(-123, 123);
    Print << U"{:+}/{:+}"_fmt(-123, 123);
    Print << U"{: }/{: }"_fmt(-123, 123);
    Print << U"{}/{}"_fmt(0.5, -0.5);
    Print << U"{:+}/{:+}"_fmt(0.5, -0.5);
    Print << U"{: }/{: }"_fmt(0.5, -0.5);

    while (System::Update())
    {

    }
}
```

### 10.2.7 そのほかの変換指定子
変換指定子について、さらに知りたい場合は [{fmt} Format String Syntax](https://fmt.dev/latest/syntax.html) を参照してください。


## 10.3 文字列から数値への変換
`Parse`, `ParseOr`, `ParseOpt` 関数テンプレートを使うと、文字列を指定した型の値に変換できます。変換するにはその型がパースに対応している必要があります。配列のパースはサポートされていません。

### 10.3.1 Parse
`Parse<Type>(s)` 関数テンプレートは、引数の文字列 `s` を `Type` 型の値に変換し、失敗した場合は例外 `ParseError` を投げます。
![](/images/doc_v6/tutorial/10/3.1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const int32 a = Parse<int32>(U"123");
	const double b = Parse<double>(U"-3.14159");
	const Point c = Parse<Point>(U"(10, 20)");

	Print << a;
	Print << b;
	Print << c;

	try
	{
		const Point d = Parse<Point>(U"(0,0)");
		const Point e = Parse<Point>(U"(20, 40)");
		const Point f = Parse<Point>(U"123"); // 失敗して例外を投げる
	}
	catch (const ParseError& e)
	{
		// 例外の詳細を取得して表示
		Print << e.what();
	}

	while (System::Update())
	{

	}
}
```


### 10.3.2 ParseOr
`ParseOr<Type>(s, defaultValue)` 関数テンプレートは、引数の文字列 `s` を `Type` 型の値に変換し、失敗した場合は `defaultValue` を返します。
![](/images/doc_v6/tutorial/10/3.2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const int32 a = ParseOr<int32>(U"123", -1);
	const int32 b = ParseOr<int32>(U"???", -1); // 失敗して defaultValue を返す
	const ColorF c = ParseOr<ColorF>(U"123", ColorF{ 0.0, 0.0 }); // 失敗して defaultValue を返す
	const Circle d = ParseOr<Circle>(U"(400, 300, 100)", Circle{ 0, 0, 0 });

	Print << a;
	Print << b;
	Print << c;
	Print << d;

	while (System::Update())
	{

	}
}
```


### 10.3.3 ParseOpt
`ParseOpt<Type>(s)` 関数テンプレートは、引数の文字列 `s` を `Type` 型の値に変換し、オプショナル型で返します。変換に失敗した場合は無効値を返します。
![](/images/doc_v6/tutorial/10/3.3.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Optional<int32> a = ParseOpt<int32>(U"123");
	const Optional<int32> b = ParseOpt<int32>(U"???"); // 失敗して無効値を返す
	const Optional<ColorF> c = ParseOpt<ColorF>(U"123"); // 失敗して無効値を返す
	const Optional<Circle> d = ParseOpt<Circle>(U"(400, 300, 100)");

	if (a)
	{
		Print << U"a: " << * a;
	}

	if (b)
	{
		Print << U"b: " << *b;
	}

	if (c)
	{
		Print << U"c: " << *c;
	}

	if (d)
	{
		Print << U"d: " << *d;
	}

	while (System::Update())
	{

	}
}
```
