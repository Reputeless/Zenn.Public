---
title: "チュートリアル 14 | フォントを使う"
free: true
---

# 14. フォントを使う
この章では、フォントを使って様々なスタイルのテキストを描く方法を学びます。

## 14.1 Font
前章までテキストの表示に使ってきた `Print` は、フォントのサイズや種類、描画位置に自由度がありませんでした。自由にカスタマイズしたフォントを使ってテキストを描きたいときは `Font` を作成し、描画したい内容を `()` でつなげたあと、`.draw()` または `.drawAt()` します。

`Texture` と同じように、`Font` の作成にはメモリ確保などの実行時負荷がかかります。メインループの中で毎フレーム新しい `Font` を作成するのは避け、作成が 1 回だけになるようにしましょう。
![](/images/doc_v6/tutorial/14/1.png)
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
		font(123, U"ABC").draw(50, 400, ColorF{ 0.5, 1.0, 0.5 });

		font(U"{}/{}/{}"_fmt(2021, 12, 31)).draw(50, 500, ColorF{ 1.0, 0.5, 0.0 });
	}
}
```


## 14.2 改行する
テキストの中に改行文字 `'\n'` が含まれていると、そこで改行されます。
![](/images/doc_v6/tutorial/14/2.png)
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
![](/images/doc_v6/tutorial/14/3.png)
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

![](/images/doc_v6/tutorial/14/4.png)
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
![](/images/doc_v6/tutorial/14/5.png)
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
![](/images/doc_v6/tutorial/14/6.png)
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
| `SpecialFolder::SystemFonts` | (OS):/WINDOWS/Fonts/ | /System/Library/Fonts/ | /usr/share/fonts/ |
| `SpecialFolder::LocalFonts`  | (OS):/WINDOWS/Fonts/ | /Library/Fonts/        | /usr/local/share/fonts/<br>(存在する場合) |
| `SpecialFolder::UserFonts`   | (OS):/WINDOWS/Fonts/ | ~/Library/Fonts/       | /usr/local/share/fonts/<br>(存在する場合) |

![](/images/doc_v6/tutorial/14/7.png)
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

`SIV3D_PLATFORM(WINDOWS)` や `SIV3D_PLATFORM(MACOS)` は Siv3D でプラットフォーム別のコードを書くときに使えるマクロです。


## 14.8 フォントのスタイルを変える
`Font` のコンストラクタに `FontStyle` を指定することで、イタリックやボールドなどのスタイルをフォントに適用できます。

![](/images/doc_v6/tutorial/14/8.png)
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

![](/images/doc_v6/tutorial/14/9.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 32, U"example/font/DotGothic16/DotGothic16-Regular.ttf" };

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

![](/images/doc_v6/tutorial/14/10.png)
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

![](/images/doc_v6/tutorial/14/11.png)
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

![](/images/doc_v6/tutorial/14/12.png)
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
`Font::draw()` に `Rect` または `RectF` を渡すと、テキストをその長方形の内部に収まるように描画します。長方形内にテキストが収まった場合、関数は `true` を返します。一方、テキストがあふれる場合、最後の文字が `…` に置き換えられ、関数は `false` を返します。

![](/images/doc_v6/tutorial/14/13.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 25, Typeface::Bold };
	const String text = U"The quick brown fox jumps over the lazy dog.";

	constexpr Rect rect1{ 50, 20, 200, 100 };
	constexpr Rect rect2{ 50, 160, 300, 100 };
	constexpr Rect rect3{ 50, 300, 400, 100 };

	while (System::Update())
	{
		rect1.draw();
		if (not font(text).draw(rect1.stretched(-10), ColorF{ 0.25 }))
		{
			// 文字が省略されたら赤枠
			rect1.drawFrame(0, 5, Palette::Red);
		}

		rect2.draw();
		if (not font(text).draw(rect2.stretched(-10), ColorF{ 0.25 }))
		{
			// 文字が省略されたら赤枠
			rect2.drawFrame(0, 5, Palette::Red);
		}

		rect3.stretched(10).draw();
		if (not font(text).draw(rect3.stretched(-10), ColorF(0.25)))
		{
			// 文字が省略されたら赤枠
			rect3.drawFrame(0, 5, Palette::Red);
		}
	}
}
```


## 14.14 テキストを 1 文字ずつ表示する
`String` は、`.substr(0, N)` を使うと、0 文字目から N 文字分の文字列を取得できます。N を時間に応じて増やすことで 1 文字ずつテキストが増えていく処理を実現できます。N が実際の文字列の長さをオーバーしてもその分は無視されるので大丈夫です。 

![](/images/doc_v6/tutorial/14/14.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 50, Typeface::Bold };

	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		// 文字カウントを 0.1 秒ごとに増やす
		const size_t length = static_cast<size_t>(Scene::Time() / 0.1);

		// text の文字数以上の length は切り捨てられる
		font(text.substr(0, length)).draw(50, 50);
	}
}
```


## 14.15 文字に影の効果を付ける（2 回描画する手法）
座標をずらして 2回 テキストを描くと、影の効果を簡単に作成できます。`Vec2::movedBy(x, y)` を使うと、指定した値だけ要素を加算した `Vec2` を作成できます。

![](/images/doc_v6/tutorial/14/15.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });

	const Font font{ 100, Typeface::Bold };

	constexpr Vec2 center{ 400, 150 };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		// center から (4, 4) ずらした位置を中心にテキストを描く
		font(text).drawAt(center.movedBy(4, 4), ColorF{ 0.0, 0.5 });

		// center を中心にテキストを描く
		font(text).drawAt(center);
	}
}
```


## 14.16 自由に拡大縮小できるフォントを使う（SDF / MSDF）

これまでの `Font` クラスは、コンストラクタで指定した基本サイズで各文字ごとのビットマップ画像を生成し、それをレンダリングしていました（**ビットマップ方式**）。そのため、基本サイズより大きなサイズでテキストを描画しようとすると、画像がぼやけるという制限がありました。また、輪郭のようなエフェクトを適用することも困難でした。  
一方、**SDF 方式** / **MSDF 方式**は、文字ごとの Distance field 画像を生成し、基本サイズ以上に拡大してもぼやけない手法でテキストをレンダリングできます。SDF / MSDF には影や輪郭などのエフェクトを 1 回の draw で行える仕組みも用意されています。

各方式の利点と欠点を次の表にまとめました。

| レンダリング手法 | 縮小 | 拡大 | 影 | 輪郭 | 実行時負荷 | 備考 |
|--|:--:|:--:|:--:|:--:|:--:|:--|
|`FontMethod::Bitmap`| 〇 | △ | 〇<br>(2 回 draw) | × | 低 | デフォルトの手法 |
|`FontMethod::SDF`| 〇 | 〇 | 〇 | 〇 | 中 | 文字の角が丸くなるなど、細部の情報が失われやすい |
|`FontMethod::MSDF`| ◎ | ◎ | 〇 | 〇 | 高 | SDF より高品質 |

SDF / MSDF フォントで設定する基本サイズは、 Distance Field のサイズに対応します。この値は描画する字形の複雑さに応じて決める必要があります。画数の少ない数字やアルファベット、曲線的でシンプルな字形であれば、40 ピクセル以下の基本サイズでもきれいなテキストをレンダリングできますが、複雑な字形になるほど、小さな Distance Field では描画結果が乱れたり、ノイズが目立つことがあります。かといって大きすぎると描画に時間がかかってしまいます。SDF / MSDF をアプリケーションで使用する際は、テキストの描画結果をチェックし、適切な基本サイズを設定しましょう。

`.draw()` や `.drawAt()`, `.drawBase()` は、文字のサイズを指定できます。各方式について、基本サイズより大きなテキストを描いたときの結果を見てみましょう。

![](/images/doc_v6/tutorial/14/16.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 基本サイズ
	const int32 baseSize = 40;

	const Font font{ baseSize, Typeface::Bold };
	const Font fontSDF{ FontMethod::SDF, baseSize, Typeface::Bold };
	const Font fontMSDF{ FontMethod::MSDF, baseSize, Typeface::Bold };
	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		// 文字のサイズ（指定しない場合は基本サイズで描かれる）
		const double fontSize = 120;

		// 通常（ビットマップ方式）
		font(text).draw(20, 20);
		font(text).draw(fontSize, 20, 50);

		// SDF 方式
		fontSDF(text).draw(20, 220);
		fontSDF(text).draw(fontSize, 20, 250);

		// MSDF 方式
		fontMSDF(text).draw(20, 420);
		fontMSDF(text).draw(fontSize, 20, 450);
	}
}
```


## 14.17 文字に影の効果を付ける（SDF / MSDF）
SDF / MSDF 方式のフォントは、`TextStyle` を `.draw()` や `.drawAt()`, `.drawBase()` に設定することで、簡単なエフェクトを付与できます。文字に影の効果を付けるには `TextStyle::Shadow(影のオフセット, 影の色)` を設定します。

影のオフセットがとても大きく Distance Field の範囲外に及んだ場合、影が途切れてしまいます。それを防ぐには `Font` の `.setBufferThickness(Distance Field の余白のサイズ)` で、Distance Field を大きめに作成しておきます。デフォルトは 2 です。この値を大きくするとメモリ消費量や描画負荷が増加しますが、影や輪郭の効果をより広く適用できるようになります。

![](/images/doc_v6/tutorial/14/17.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const int32 baseSize = 40;
	const Font fontSDF{ FontMethod::SDF, baseSize, Typeface::Bold };
	const Font fontMSDF{ FontMethod::MSDF, baseSize, Typeface::Bold };
	const String text = U"Hello, Siv3D!";

	const int32 bufferThickness = 3;
	fontSDF.setBufferThickness(bufferThickness);
	fontMSDF.setBufferThickness(bufferThickness);

	while (System::Update())
	{
		const Vec2 shadowOffset{ 2, 2 };
		const ColorF shadowColor{ 0.0, 0.5 };
		const double fontSize = 120;

		// SDF 方式
		fontSDF(text).draw(TextStyle::Shadow(shadowOffset, shadowColor), 20, 20);
		fontSDF(text).draw(TextStyle::Shadow(shadowOffset, shadowColor), fontSize, 20, 60);

		// MSDF 方式
		fontMSDF(text).draw(TextStyle::Shadow(shadowOffset, shadowColor), 20, 220);
		fontMSDF(text).draw(TextStyle::Shadow(shadowOffset, shadowColor), fontSize, 20, 260);
	}
}
```


## 14.18 文字に輪郭を付ける（SDF / MSDF）
文字に輪郭の効果を付けるには
- `TextStyle::Outline(輪郭スケール, 輪郭の色)`
- `TextStyle::Outline(内側方向の輪郭スケール, 外側方向の輪郭スケール, 輪郭の色)`

のいずれかを設定します。

文字に輪郭と影、両方の効果を付けるには
- `TextStyle::OutlineShadow(輪郭スケール, 輪郭の色, 影のオフセット, 影の色)`
- `TextStyle::OutlineShadow(内側方向の輪郭スケール, 外側方向の輪郭スケール, 輪郭の色, 影のオフセット, 影の色)`

のいずれかを設定します。

![](/images/doc_v6/tutorial/14/18.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const int32 baseSize = 40;
	const Font fontSDF{ FontMethod::SDF, baseSize, Typeface::Bold };
	const Font fontMSDF{ FontMethod::MSDF, baseSize, Typeface::Bold };
	const String text = U"Hello, Siv3D!";

	const int32 bufferThickness = 3;
	fontSDF.setBufferThickness(bufferThickness);
	fontMSDF.setBufferThickness(bufferThickness);

	while (System::Update())
	{
		const double outlineScale = 0.2;
		const ColorF outlineColor{ 0.0, 0.3, 0.6 };

		const Vec2 shadowOffset{ 2, 2 };
		const ColorF shadowColor{ 0.0, 0.5 };
		const double fontSize = 120;

		// SDF 方式
		fontSDF(text).draw(TextStyle::Outline(outlineScale, outlineColor), 20, 20);
		fontSDF(text).draw(TextStyle::Outline(outlineScale, outlineColor), fontSize, 20, 40);
		fontSDF(text).draw(TextStyle::OutlineShadow(outlineScale, outlineColor, shadowOffset, shadowColor), fontSize, 20, 150);

		// MSDF 方式
		fontMSDF(text).draw(TextStyle::Outline(outlineScale, outlineColor), 20, 300);
		fontMSDF(text).draw(TextStyle::Outline(outlineScale, outlineColor), fontSize, 20, 320);
		fontMSDF(text).draw(TextStyle::OutlineShadow(outlineScale, outlineColor, shadowOffset, shadowColor), fontSize, 20, 430);
	}
}
```


## 14.19 文字単位で自由描画をする（基本）
`Font` の `.getGlyphs(text)` を `for` ループで次のように使用すると、個々の文字を自由に制御して描画するために必要な `Glyph` 型のオブジェクトを文字ごとに取得できます。  
`Glyph` の `.codePoint` はその文字の UTF-32 コードポイントを、`.getOffset()` はペンの位置からさらに必要なオフセットを、`.xAdvance` は次の文字への X 座標の距離を表します。

![](/images/doc_v6/tutorial/14/19.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 50, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		constexpr Vec2 basePos{ 20, 20 };
		Vec2 penPos{ basePos };

		// 文字単位で描画を制御するためのループ
		for (const auto& glyph : font.getGlyphs(text))
		{
			// 改行文字なら
			if (glyph.codePoint == U'\n')
			{
				// ペンの X 座標をリセット
				penPos.x = basePos.x;

				// ペンの Y 座標をフォントの高さ分進める
				penPos.y += font.height();

				continue;
			}

			// 位置に応じて色を変える
			const ColorF color = HSV{ penPos.x };

			// 文字のテクスチャをペンの位置に文字ごとのオフセットを加算して描画
			// FontMethod がビットマップ方式の場合に限り、Math::Round() で整数座標にすると品質が向上
			glyph.texture.draw(Math::Round(penPos + glyph.getOffset()), color);

			// ペンの X 座標を文字の幅の分進める
			penPos.x += glyph.xAdvance;
		}
	}
}
```


## 14.20 文字単位で自由描画をする（応用）
`for (auto [i, value] : Indexed(values))` は次のプログラムを短く書ける機能です。

```cpp
size_t i = 0;
for (const auto& value : values)
{
	++i;
}
```

これを利用して、1 文字ごとに描画する位置をずらしてみましょう。

![](/images/doc_v6/tutorial/14/20.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 50, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		const double t = Scene::Time();
		constexpr Vec2 basePos{ 20, 20 };
		Vec2 penPos{ basePos };

		// 文字単位で描画を制御するためのループ。index には何番目であるかが格納される
		for (auto [index, glyph] : Indexed(font.getGlyphs(text)))
		{
			if (glyph.codePoint == U'\n')
			{
				penPos.x = basePos.x;
				penPos.y += font.height();
				continue;
			}

			const double offsetY = Math::Sin(index * 45_deg + t * 180_deg) * 10;

			glyph.texture.draw(penPos + glyph.getOffset() + Vec2{ 0, offsetY });

			penPos.x += glyph.xAdvance;
		}
	}
}
```

（補足 1）自由描画で使用するフォントが SDF / MSDF 方式の場合、次のように `ScopedCustomShader2D` を作成し、そのオブジェクトが有効なスコープで描画する必要があります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// MSDF フォント
	const Font font{ FontMethod::MSDF, 50, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		const double t = Scene::Time();
		constexpr Vec2 basePos{ 20, 20 };
		Vec2 penPos{ basePos };

		{
			// MSDF フォントの描画のための設定
			const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method()) };

			for (auto [index, glyph] : Indexed(font.getGlyphs(text)))
			{
				if (glyph.codePoint == U'\n')
				{
					penPos.x = basePos.x;
					penPos.y += font.height();
					continue;
				}

				const double offsetY = Math::Sin(index * 45_deg + t * 180_deg) * 10;
				glyph.texture.draw(penPos + glyph.getOffset() + Vec2{ 0, offsetY });
				penPos.x += glyph.xAdvance;
			}
		}
	}
}
```

（補足 2）自由描画で使用するフォントが SDF / MSDF 方式かつ、`TextStyle` を適用する場合はさらに事前設定が必要です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// MSDF フォント
	const Font font{ FontMethod::MSDF, 50, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		const double t = Scene::Time();
		constexpr Vec2 basePos{ 20, 20 };
		Vec2 penPos{ basePos };

		{
			// MSDF フォント + 輪郭描画のための設定
			const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method(), TextStyle::Type::OutlineShadow) };
			const Float4 param{ (0.5f + 0.1f), (0.5f - 0.1f), 2.0f, 2.0f };
			const Float4 outlineColor = ColorF{ 0.8, 0.4, 0.0 }.toFloat4();
			const Float4 shadowColor = ColorF{ 0.0, 0.5 }.toFloat4();
			Graphics2D::Internal::SetSDFParameters({ param, outlineColor, shadowColor });

			for (auto [index, glyph] : Indexed(font.getGlyphs(text)))
			{
				if (glyph.codePoint == U'\n')
				{
					penPos.x = basePos.x;
					penPos.y += font.height();
					continue;
				}

				const double offsetY = Math::Sin(index * 45_deg + t * 180_deg) * 10;
				glyph.texture.draw(penPos + glyph.getOffset() + Vec2{ 0, offsetY });
				penPos.x += glyph.xAdvance;
			}
		}
	}
}
```


## 14.21 縦書きでテキストを描画する
（OpenSiv3D v0.6.1 ではテキストの縦書きに関する機能は未実装です。将来のバージョンで実装予定です）


## 14.22 フォントのプリロード
Siv3D の `Font` は、初めて描く文字の画像を内部でレンダリングしてキャッシュするため、リアルタイムで動作しているゲームの途中で大量のテキストを初めて表示すると、そのフレームの実行時間が長くなり、フレームレートが一瞬低下することがあります。`.preload(text)` を使うと、`text` に含まれる文字を（重複する場合は除去して）内部にあらかじめ用意するため、ゲームの実行中の負荷を抑制できます。  
また、`.getTexture()` を使うと、`Font` の内部にキャッシュされている `Texture` を取得できます。

![](/images/doc_v6/tutorial/14/22.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });
	const Font font{ 30 };
	font.preload(U"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz.,!?0123456789");

	while (System::Update())
	{
		font(U"Hello, Siv3D!").draw(20, 20, ColorF{ 0.25 });

		font.getTexture().draw(20, 100);
	}
}
```

## 14.23 空のフォント
データを持たない空（から）のフォントは何も描きません。フォントファイルの読み込みに失敗したときにも空のフォントが作成されます。  
フォントが空であるかは `if (font.isEmpty())` もしくは `if (not font)` で調べられます。

![](/images/doc_v6/tutorial/14/23.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 初期データを与えないと、空のフォントになる
	Font fontA;

	if (not fontA)
	{
		Print << U"fontA is empty";
	}

	// フォントファイルの読み込みに失敗すると、空のフォントになる
	Font fontB{ 40, U"aaa/bbb.ttf" };

	if (not fontB)
	{
		Print << U"fontB is empty";
	}

	while (System::Update())
	{
		// 何も描かれない
		fontA(U"Hello, Siv3D!").draw(100, 100);

		// 何も描かれない
		fontB(U"Hello, Siv3D!").draw(100, 200);
	}
}
```


## 14.24 フォントの代入
`Font` は次のように `=` 演算子を使って代入できます。

![](/images/doc_v6/tutorial/14/24.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Font font;

	while (System::Update())
	{
		// フォントが空の状態で、左クリックされたら
		if ((not font) && MouseL.down())
		{
			// フォントを作成して代入
			font = Font{ 40 };
		}

		if (font)
		{
			font(U"Helo, Siv3D!").draw(20, 20);
		}
	}
}
```

## 14.25 （サンプル）TextStyle プレビュー
`TextStyle` の効果をプレビューできるサンプルです。マウスの右クリック移動やマウスホイールで視点を変更できます。

![](/images/doc_v6/tutorial/14/25.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 大きいと拡大描画時にきれいになるが、フォントの生成時間・メモリ消費が増える
	constexpr int32 fontSize = 70;

	// このサイズだけ、文字の周囲に輪郭や影のエフェクトを付加できる。フォントの生成時間・メモリ消費が増える
	constexpr int32 bufferThickness = 5;

	// ビットマップ方式では輪郭や影のエフェクトの利用は不可
	const Font fontBitmap{ FontMethod::Bitmap, fontSize, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };

	// SDF 方式
	const Font fontSDF{ FontMethod::SDF, fontSize, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
	fontSDF.setBufferThickness(bufferThickness);

	// MSDF 方式
	const Font fontMSDF{ FontMethod::MSDF, fontSize, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
	fontMSDF.setBufferThickness(bufferThickness);

	bool outline = false;
	bool shadow = false;
	double inner = 0.1, outer = 0.1;
	Vec2 shadowOffset{ 2.0, 2.0 };
	ColorF textColor{ 1.0 };
	ColorF outlineColor{ 0.0 };
	ColorF shadowColor{ 0.0, 0.5 };
	HSV background = ColorF{ 0.8 };

	Camera2D camera{ Scene::Center(), 1.0 };

	while (System::Update())
	{
		Scene::SetBackground(background);

		TextStyle textStyle;
		{
			if (outline && shadow)
			{
				textStyle = TextStyle::OutlineShadow(inner, outer, outlineColor, shadowOffset, shadowColor);
			}
			else if (outline)
			{
				textStyle = TextStyle::Outline(inner, outer, outlineColor);
			}
			else if (shadow)
			{
				textStyle = TextStyle::Shadow(shadowOffset, shadowColor);
			}
		}

		camera.update();
		{
			auto t = camera.createTransformer();
			fontBitmap(U"Siv3D, 渋三次元 (Bitmap)").draw(Vec2{ 100, 250 }, textColor);
			fontSDF(U"Siv3D, 渋三次元 (SDF)").draw(textStyle, Vec2{ 100, 330 }, textColor);
			fontMSDF(U"Siv3D, 渋三次元 (MSDF)").draw(textStyle, Vec2{ 100, 410 }, textColor);
		}

		SimpleGUI::CheckBox(outline, U"Outline", Vec2{ 20, 20 }, 130);
		SimpleGUI::Slider(U"Inner: {:.2f}"_fmt(inner), inner, -0.5, 0.5, Vec2{ 160, 20 }, 120, 120, outline);
		SimpleGUI::Slider(U"Outer: {:.2f}"_fmt(outer), outer, -0.5, 0.5, Vec2{ 160, 60 }, 120, 120, outline);

		SimpleGUI::CheckBox(shadow, U"Shadow", Vec2{ 20, 100 }, 130);
		SimpleGUI::Slider(U"offsetX: {:.1f}"_fmt(shadowOffset.x), shadowOffset.x, -5.0, 5.0, Vec2{ 160, 100 }, 120, 120, shadow);
		SimpleGUI::Slider(U"offsetY: {:.1f}"_fmt(shadowOffset.y), shadowOffset.y, -5.0, 5.0, Vec2{ 160, 140 }, 120, 120, shadow);

		SimpleGUI::Headline(U"Text", Vec2{ 420, 20 });
		SimpleGUI::Slider(U"R", textColor.r, Vec2{ 420, 60 }, 20, 80);
		SimpleGUI::Slider(U"G", textColor.g, Vec2{ 420, 100 }, 20, 80);
		SimpleGUI::Slider(U"B", textColor.b, Vec2{ 420, 140 }, 20, 80);
		SimpleGUI::Slider(U"A", textColor.a, Vec2{ 420, 180 }, 20, 80);

		SimpleGUI::Headline(U"Outline", Vec2{ 540, 20 });
		SimpleGUI::Slider(U"R", outlineColor.r, Vec2{ 540, 60 }, 20, 80, outline);
		SimpleGUI::Slider(U"G", outlineColor.g, Vec2{ 540, 100 }, 20, 80, outline);
		SimpleGUI::Slider(U"B", outlineColor.b, Vec2{ 540, 140 }, 20, 80, outline);
		SimpleGUI::Slider(U"A", outlineColor.a, Vec2{ 540, 180 }, 20, 80, outline);

		SimpleGUI::Headline(U"Shadow", Vec2{ 660, 20 });
		SimpleGUI::Slider(U"R", shadowColor.r, Vec2{ 660, 60 }, 20, 80, shadow);
		SimpleGUI::Slider(U"G", shadowColor.g, Vec2{ 660, 100 }, 20, 80, shadow);
		SimpleGUI::Slider(U"B", shadowColor.b, Vec2{ 660, 140 }, 20, 80, shadow);
		SimpleGUI::Slider(U"A", shadowColor.a, Vec2{ 660, 180 }, 20, 80, shadow);

		SimpleGUI::ColorPicker(background, Vec2{ 780, 20 });
	}
}
```
