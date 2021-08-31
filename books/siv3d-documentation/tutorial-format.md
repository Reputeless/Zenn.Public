---
title: "チュートリアル 10 | 文字列と数値の変換"
free: true
---

# 10. 文字列と数値の変換
この章では、数値データを文字列に変換する方法と、文字列を数値データに変換する方法を学びます。

## 10.1 数値から文字列への変換
`Format()` を使うと、文字列への変換に対応したあらゆるデータを `String` に変換できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String a = Format(12345);

	const String b = Format(true);

	const String c = Format(1.23456789);

	const String d = Format(Vec2{ 11, 22 });

	const Array<int32> values = { 3, 4, 5, 6 };
	const String e = Format(values);

	const std::array<ColorF, 3> colors =
	{
		ColorF{ 1.0 , 0.0, 0.0 },
		ColorF{ 0.0 , 1.0, 0.0 },
		ColorF{ 0.0 , 0.0, 1.0 },
	};
	const String f = Format(colors);

	const String g = Format(Rect{ 30, 50, 100, 50 });

	Print << a;
	Print << b;
	Print << c;
	Print << d;
	Print << e;
	Print << f;
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


```cpp

```


### 10.2.2 小数点以下の桁数


```cpp

```


### 10.2.3 インデックスの指定


```cpp

```


### 10.2.4 パディング


```cpp

```


### 10.2.5 基数


```cpp

```


### 10.2.6 符号


```cpp

```


## 10.3 文字列から数値への変換


### 10.3.1 Parse


```cpp

```


### 10.3.2 ParseOr


```cpp

```


### 10.3.3 ParseOpt


```cpp

```


### 10.3.4 ParseError の捕捉


```cpp

```

