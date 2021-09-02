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
`Font` のコンストラクタの第 1 引数にはフォントの基本サイズを指定します。単位はピクセルです。基本サイズはあとから変更できません。1 つの `Font` からさまざまなサイズのテキストを描く方法はのちほど紹介します。

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


## 14.4 フォントの種類
Siv3D には異なる太さの 7 種類の日本語書体と、5 地域向けの CJK（中国語・韓国語・日本語対応）フォント、白黒絵文字フォント、カラー絵文字フォントが同梱されています。Font のコンストラクタの第 2 引数において `Typeface::` で書体を指定することで、それらの書体を利用できます。何も指定しなかった場合 `Typeface::Regular` が選択されます。


|Typeface|説明|
|--|--|
|`Typeface::Thin`|細い日本語フォント|
|`Typeface::Light`|やや細い日本語フォント|
|`Typeface::Regular`|通常日本語フォント|
|`Typeface::Medium`|やや太い日本語フォント|
|`Typeface::Bold`|太い日本語フォント|
|`Typeface::Heavy`|とても太い日本語フォント|
|`Typeface::Black`|最も太い日本語フォント|
|`Typeface::CJK_Regular_JP`|日本語デザインの CJK フォント|
|`Typeface::CJK_Regular_KR`|韓国語デザインの CJK フォント|
|`Typeface::CJK_Regular_SC`|簡体字デザインの CJK フォント|
|`Typeface::CJK_Regular_TC`|台湾繁体字デザインの CJK フォント|
|`Typeface::CJK_Regular_HK`|香港繁体字デザインの CJK フォント|
|`Typeface::MonochromeEmoji`|モノクロ絵文字フォント|
|`Typeface::ColorEmoji`|カラー絵文字フォント|


```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font fontThin{ 36, Typeface::Thin };
	const Font fontLight{ 36, Typeface::Light };
	const Font fontRegular{ 36, Typeface::Regular };
	const Font fontMedium{ 36, Typeface::Medium };
	const Font fontBold{ 36, Typeface::Bold };
	const Font fontHeavy{ 36, Typeface::Heavy };
	const Font fontBlack{ 36, Typeface::Black };

	const Font fontJP{ 36, Typeface::CJK_Regular_JP };
	const Font fontKR{ 36, Typeface::CJK_Regular_KR };
	const Font fontSC{ 36, Typeface::CJK_Regular_SC };
	const Font fontTC{ 36, Typeface::CJK_Regular_TC };
	const Font fontHK{ 36, Typeface::CJK_Regular_HK };

	const Font fontMono{ 36, Typeface::MonochromeEmoji };

	// カラー絵文字フォントは、サイズの指定が無視される仕様
	const Font fontEmoji{ 36, Typeface::ColorEmoji };

	const String s0 = U"Hello, Siv3D!";
	const String s1 = U"こんにちは 你好 안녕하세요 骨曜喝愛遙扇";
	const String s2 = U"🐈🐕🚀";

	while (System::Update())
	{
		fontThin(s0).draw(20, 20);
		fontLight(s0).draw(20, 60);
		fontRegular(s0).draw(20, 100);
		fontMedium(s0).draw(20, 140);
		fontBold(s0).draw(20, 180);
		fontHeavy(s0).draw(20, 220);
		fontBlack(s0).draw(20, 260);

		fontJP(s1).draw(20, 300);
		fontKR(s1).draw(20, 340);
		fontSC(s1).draw(20, 380);
		fontTC(s1).draw(20, 420);
		fontHK(s1).draw(20, 460);

		fontMono(s2).draw(20, 500);
		fontEmoji(s2).draw(20, 540);
	}
}
```


## 14.5 フォールバックフォントの追加
1 つで全ての文字に対応するフォントはありません。様々な言語や字種が交ざるテキストを 1 つの `Font` で表示したい場合は、フォールバックフォントを設定します。フォールバックフォントを設定すると、基本のフォントで描けない文字が見つかったとき、もしフォールバックフォントで描けたら、そのフォントを使います。フォールバックフォントを設定するには、`.addFallback()` で作成済みの `Font` を渡します。フォールバックフォントは何個でも設定でき、先に設定したものが優先して使われます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font fontA{ 36, Typeface::Regular };
	const Font fontB{ 36, Typeface::Regular };
	const Font fontC{ 36, Typeface::Regular };

	const Font fontJP{ 36, Typeface::CJK_Regular_JP };
	const Font fontEmoji{ 36, Typeface::ColorEmoji };

	fontB.addFallback(fontJP);
	fontC.addFallback(fontJP);

	fontC.addFallback(fontEmoji);

	const String s = U"Hello! こんにちは 你好 안녕하세요 🐈🐕🚀";

	while (System::Update())
	{
		fontA(s).draw(20, 20);
		fontB(s).draw(20, 60);
		fontC(s).draw(20, 100);
	}
}
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

