---
title: "チュートリアル 14 | フォントを使う"
free: true
---

# 14. フォントを使う
この章では、フォントを使って様々なスタイルのテキストを描く方法を学びます。

## 14.1 Font
前章までテキストの表示に使ってきた `Print` は、フォントのサイズや種類、描画位置に自由度がありませんでした。自由にカスタマイズしたフォントを使ってテキストを描きたいときは `Font` を作成し、描画したい内容を `()` でつなげたあと、`.draw()` または `.drawAt()` します。

`Texture` と同じように、`Font` の作成にはメモリ確保などの実行時負荷がかかります。メインループの中で毎フレーム新しい `Font` を作成するのは避け、作成が 1 回だけになるようにしましょう。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 基本サイズ 50 のフォントを作成
	const Font font{ 50 };

	while (System::Update())
	{
		// 左上位置 (20, 20) からテキストを描く
		font(U"Hello, Siv3D!").draw(20, 20);

		// テキストの中心座標が画面の中心になるようにテキストを描く
		font(U"C++").drawAt(Scene::Center(), Palette::Skyblue);

		// 文字列以外を渡すと Format される
		font(Cursor::Pos()).draw(50, 300);

		// 複数渡すと、それぞれを Format した文字列をつなげる
		font(123, U"ABC").draw(50, 400);

		font(U"{}/{}/{}"_fmt(2021, 12, 31)).draw(50, 500);
	}
}
```


## 14.2 改行する
テキストの中に改行文字 `'\n'` が含まれていると、そこで改行されます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 50 };

	while (System::Update())
	{
		font(U"Hello,\nSiv3D\n\n!!!").draw(20, 20);
	}
}
```


## 14.3 フォントの基本サイズ
`Font` のコンストラクタの第 1 引数にはフォントの基本サイズを指定します。単位はピクセルです。基本サイズはあとから変更できません。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 基本サイズ 20 のフォント
	const Font font20{ 20 };

	// 基本サイズ 40 のフォント
	const Font font40{ 40 };

	// 基本サイズ 60 のフォント
	const Font font60{ 60 };

	// 基本サイズ 80 のフォント
	const Font font80{ 80 };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font20(text).draw(20, 20);

		font40(text).draw(20, 60);

		font60(text).draw(20, 120);

		font80(text).draw(20, 200);
	}
}
```


## 14.4 

```cpp

```


## 14.5 

```cpp

```


## 14.6 

```cpp

```


## 14.7 

```cpp

```


## 14.8 

```cpp

```


## 14.9 

```cpp

```


## 14.10

```cpp

```


## 14.11

```cpp

```


## 14.12 

```cpp

```


## 14.13

```cpp

```


## 14.14

```cpp

```


## 14.15

```cpp

```


## 14.16

```cpp

```

