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
Siv3D には異なる太さの 7 種類の日本語フォントと、5 地域向けの CJK（中国語・韓国語・日本語対応）フォント、白黒絵文字フォント、カラー絵文字フォントが同梱されています。`Font` のコンストラクタにおいて `Typeface::` で書体を指定することで、それらの書体を利用できます。何も指定しなかった場合 `Typeface::Regular` が選択されます。


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

	// fontB にフォールバックフォントを 1 つ追加
	fontB.addFallback(fontJP);

	// fontC にフォールバックフォントを 2 つ追加
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


## 14.6 フォントファイルからフォントを読み込んで使う
コンピュータ上にあるフォントファイルから `Font` を作成するには、`Font` のコンストラクタに、読み込みたいフォントファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（App フォルダ）を基準とする相対パスか、絶対パスを使用します。リリース用のアプリを作るときには、のちの章で説明する「リソース」パスの使用を推奨します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// RocknRollOne-Regular.ttf をロードして使う
	const Font font{ 50, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"Hello, Siv3D!\nこんにちは！").draw(20, 20);
	}
}
```


## 14.7 PC にインストールされているフォントを使う
PC にインストールされているフォントは OS ごとに特殊なフォルダに保存されています。そのフォルダのパスを `FileSystem::GetFolderPath()` で取得し、フォントファイル名とつなげることで、ファイルパスを構築できます。`FileSystem::GetFolderPath()` に渡す `SpecialFolder` の種類と OS によって取得できるパスの対応表は次の通りです。

|                            | Windows             | macOS                  | Linux       |
|----------------------------|:---------------------:|:------------------------:|:-------------:|
| SpecialFolder::SystemFonts | (OS):/WINDOWS/Fonts/ | /System/Library/Fonts/ | /usr/share/fonts/ |
| SpecialFolder::LocalFonts  | (OS):/WINDOWS/Fonts/ | /Library/Fonts/        | /usr/local/share/fonts/<br>(存在する場合) |
| SpecialFolder::UserFonts   | (OS):/WINDOWS/Fonts/ | ~/Library/Fonts/       | /usr/local/share/fonts/<br>(存在する場合) |

```cpp
# include <Siv3D.hpp>

void Main()
{
# if SIV3D_PLATFORM(WINDOWS)

	const Font font{ 60, FileSystem::GetFolderPath(SpecialFolder::SystemFonts) + U"arial.ttf" };

# elif SIV3D_PLATFORM(MACOS)

	const Font font{ 60, FileSystem::GetFolderPath(SpecialFolder::SystemFonts) + U"Helvetica.dfont" };

# endif

	while (System::Update())
	{
# if SIV3D_PLATFORM(WINDOWS)

		font(U"Arial").draw(20, 40);

# elif SIV3D_PLATFORM(MACOS)

		font(U"Helvetica").draw(20, 40);

# endif
	}
}
```


## 14.8 フォントのスタイルを変える
`Font` のコンストラクタに `FontStyle` を指定することで、イタリックやボールドなどのスタイルをフォントに適用できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 50, Typeface::Regular };

	// ボールド
	const Font fontBold{ 50, Typeface::Regular, FontStyle::Bold };

	// イタリック
	const Font fontItalic{ 50, Typeface::Regular, FontStyle::Italic };

	// ボールド・イタリック
	const Font fontBoldItalic{ 50, Typeface::Regular, FontStyle::BoldItalic };

	const String text = U"Hello, Siv3D! こんにちは。";

	while (System::Update())
	{
		font(text).draw(20, 20);

		fontBold(text).draw(20, 70);

		fontItalic(text).draw(20, 120);

		fontBoldItalic(text).draw(20, 170);
	}
}
```


## 14.9 ビットマップフォントを使う
ビットマップフォントはフォントスタイルに `FontStyle::Bitmap` を指定することで、フィルタリングされずドット感を保つことができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// RocknRollOne-Regular.ttf をロードして使う
	const Font font{ 32, U"example/font/DotGothic16/DotGothic16-Regular.ttf" };

	// RocknRollOne-Regular.ttf をロードして使う
	const Font fontB{ 32, U"example/font/DotGothic16/DotGothic16-Regular.ttf", FontStyle::Bitmap };

	const String text = U"Hello, Siv3D! こんにちは。";

	while (System::Update())
	{
		font(text).draw(20, 20, Palette::Black);

		fontB(text).draw(20, 60, Palette::Black);
	}
}
```


## 14.10 ベースラインを指定してテキストを描く
文字のベースラインの開始位置を指定して描画したい場合は `.drawBase()` を使います。異なるサイズや種類のフォントを、ベースラインをそろえて描画できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font20{ 20 };
	const Font font30{ 30, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
	const Font font50{ 50 };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		// ベースラインがそろわない
		font20(text).draw(20, 100);
		font30(text).draw(160, 100);
		font50(text).draw(380, 100);

		Rect{ 0, 400, 800, 10 }.draw(ColorF{ 0.3 });

		// (20, 400) がベースラインの開始位置になるようテキストを描画
		font20(text).drawBase(20, 400);

		// (160, 400) がベースラインの開始位置になるようテキストを描画
		font30(text).drawBase(160, 400);

		// (380, 400) がベースラインの開始位置になるようテキストを描画
		font50(text).drawBase(380, 400);
	}
}
```


## 14.11 テキスト描画の基準位置をカスタマイズする
左上や中心以外にも、描画座標の基準点を設定できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 50 };
	constexpr Vec2 pos{ 400,300 };
	const String text = U"Hello, Siv3D!";
	size_t index = 0;

	while (System::Update())
	{
		SimpleGUI::RadioButtons(index,
			{ U"topLeft", U"bottomLeft", U"bottomRight", U"bottomCenter", U"leftCenter", U"center" },
			Vec2{20,20});

		Circle{ pos, 2 }.draw(Palette::Red);

		if (index == 0)
		{
			font(text).draw(pos);
		}
		else if (index == 1)
		{
			// 左下を基準にする
			font(text).draw(Arg::bottomLeft = pos);
		}
		else if (index == 2)
		{
			// 右下を基準にする
			font(text).draw(Arg::bottomRight = pos);
		}
		else if (index == 3)
		{
			// 下辺中央を基準にする
			font(text).draw(Arg::bottomCenter = pos);
		}
		else if (index == 4)
		{
			// 左辺中央を基準
			font(text).draw(Arg::leftCenter = pos);
		}
		else
		{
			// 中央を基準
			font(text).drawAt(pos);
		}
	}
}
```


## 14.12 テキストが表示される領域を調べる
`Font` の `.draw()` や `.drawAt()` は、描画された領域を `RectF` 型で返します。また、`.region()` や `.regionAt()` を使うと、描画なしでその領域を取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 50 };
	const String text = U"Hello, Siv3D!";
	constexpr Vec2 pos{ 20, 20 };

	// font を使って text を pos の位置に描画したときのテキストの領域を取得
	const RectF rect = font(text).region(pos);

	while (System::Update())
	{
		// 描画領域の長方形を事前に塗りつぶす
		rect.draw(Palette::Skyblue);

		// 長方形の上にテキストを描く
		font(text).draw(pos, ColorF{ 0.25 });

		// テキストの領域を
		font(text)
			.drawAt(Scene::Center())
			.stretched(40, 0)	// 横に広げて
			.shearedX(20)		// 平行四辺形にして
			.drawFrame(2);		// 枠を描く
	}
}
```


## 14.13 指定した長方形の中にテキストを描く

```cpp

```


## 14.14 テキストを 1 文字ずつ表示する

```cpp

```


## 14.15 文字に影の効果を付ける（2 回描画する手法）

```cpp

```


## 14.16 自由に拡大縮小できるフォントを使う（SDF / MSDF）

```cpp

```


## 14.17 文字に影の効果を付ける（SDF / MSDF）

```cpp

```


## 14.18 文字に輪郭を付ける（SDF / MSDF）

```cpp

```


## 14.19 文字単位で描画を制御する（基本）

```cpp

```


## 14.20 文字単位で描画を制御する（応用）

```cpp

```


## 14.21 空のフォント

```cpp

```


## 14.22 フォントの代入

```cpp

```

