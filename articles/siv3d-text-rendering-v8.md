---
title: "ゲームエンジンを自作する場合のテキスト描画機能"
emoji: "🔠"
type: "tech"
topics: ["siv3d", "cpp"]
published: true
---

> [Siv3D Advent Calendar 2025](https://qiita.com/advent-calendar/2025/siv3d) および [グラフィックス全般 Advent Calendar 2025](https://qiita.com/advent-calendar/2025/graphics) の記事です。

ゲームエンジン / ライブラリを自作する際に、**テキスト描画**についてどのような機能を実装・提供すべきかを整理しました。

## 1. フォント管理
- フォントファイルの読み込みや形式サポートに関する機能

### 1.1 一般的なフォント形式の読み込み
- デザインの自由度を確保するため、標準的な TTF / OTF 形式の読み込みに対応する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/1.1.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Font{ 基本サイズ, フォントファイル名 }
	const Font font{ 32, U"RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"Hello, Siv3D!").draw(Vec2{ 20, 20 }, ColorF{ 0.1 });
	}
}
```
:::


### 1.2 フォントコレクション対応
- システムフォントや複数ウェイトを含むアセットを扱えるよう、TTC / OTC など、1 ファイルに複数書体が含まれる形式に対応する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/1.2.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Font{ 基本サイズ, フォントファイル名, フォントインデックス }
	const Font font1{ 32, U"meiryo.ttc", 0 };
	const Font font2{ 32, U"meiryo.ttc", 2 };

	while (System::Update())
	{
		font1(U"こんにちは Siv3D!（メイリオ）").draw(Vec2{ 20, 20 }, ColorF{ 0.1 });
		font2(U"こんにちは Siv3D!（Meiryo UI）").draw(Vec2{ 20, 80 }, ColorF{ 0.1 });
	}
}
```
:::


### 1.3 カラーフォント対応
- チャットや演出における表現力を高めるため、色情報を持つフォントの描画をサポートする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/1.3.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// COLRv1 形式のカラーフォントをロード
	const Font font1{ 64, U"KalniaGlaze-VariableFont_wdth,wght.ttf", U"Bold" };
	const Font font2{ 64, Typeface::ColorEmoji };

	while (System::Update())
	{
		font1(U"Hello, Siv3D!").draw(Vec2{ 20, 20 }, ColorF{ 0.1 });
		font2(U"🍎🍊🍇").draw(Vec2{ 20, 100 });
	}
}
```
:::


### 1.4 Variable Font 対応
- アプリ容量を削減しつつ動的なウェイト変更などの演出を可能にするため、Variable Font の軸制御や、定義済みスタイルの選択に対応する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/1.4.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const FilePath fontPath = U"Inter-VariableFont_opsz,wght.ttf";

	// フォントファイルに含まれる全ての定義済みスタイルと Variation Axis の情報を表示
	for (const auto& face : Font::GetFaces(fontPath))
	{
		Print << face.styleName;

		for (const auto& axis : face.variationAxes)
		{
			Print << U"\t{}: {}"_fmt(axis.name, axis.value);
		}
	}

	// Font{ 基本サイズ, フォントファイル名, 定義済みスタイル名 }
	const Font font1{ 48, fontPath, U"Medium" };
	const Font font2{ 48, fontPath, U"Black" };

	while (System::Update())
	{
		font1(U"Hello, Siv3D!").draw(Vec2{ 200, 20 }, ColorF{ 0.1 });
		font2(U"Hello, Siv3D!").draw(Vec2{ 200, 80 }, ColorF{ 0.1 });
	}
}
```
:::


### 1.5 埋め込みビットマップ対応
- レトロな表現や低解像度での視認性を確保するため、フォントに内包されたビットマップデータの利用を選択可能にする
- 例えば、MS ゴシックには 16px などの小さいサイズ向けのビットマップグリフが含まれている

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/1.5.png)
*拡大図*

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Font{ 基本サイズ, フォントファイル名, フォントスタイル }
	const Font font1{ 16, U"msgothic.ttc" };
	const Font font2{ 16, U"msgothic.ttc", FontStyle::Bitmap };

	while (System::Update())
	{
		font1(U"こんにちは Siv3D!").draw(Vec2{ 20, 20 }, ColorF{ 0.1 });
		font2(U"こんにちは Siv3D!").draw(Vec2{ 20, 60 }, ColorF{ 0.1 });
	}
}
```
:::


### 1.6 フォントメタデータ取得
- ユーザーによるフォント選択 UI や MOD ツール等の実装を支援するため、フォント名、グリフ数、対応スタイルなどの内部情報を取得可能にする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/1.6.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// フォントファイルのメタ情報を表示
	for (const auto& face : Font::GetFaces(U"RocknRollOne-Regular.ttf"))
	{
		Print << U"Family Name: " << face.familyName;
		Print << U"Style Name: " << face.styleName;
		Print << U"PostScript Name: " << face.postscriptName;
		Print << U"Version: " << face.version;
		Print << U"Number of Glyphs: " << face.numGlyphs;
		Print << U"Units per EM: " << face.unitsPerEM;
		Print << U"Is Bold: " << (face.isBold ? U"Yes" : U"No");
		Print << U"Is Italic: " << (face.isItalic ? U"Yes" : U"No");
		Print << U"Is Scalable: " << (face.isScalable ? U"Yes" : U"No");
		Print << U"Is Variable: " << (face.isVariable ? U"Yes" : U"No");
		Print << U"Has Color: " << (face.hasColor ? U"Yes" : U"No");
	}

	while (System::Update())
	{

	}
}
```
:::


### 1.7 グリフ名取得
- アイコンフォント等を直感的に扱えるよう、コードポイントではなく文字名での指定や検索を可能にする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/1.7.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 48, Typeface::Icon_MaterialDesign };

	// グリフインデックス 0 から 19 までのグリフ名を表示
	for (GlyphIndex i = 0; i < 20; ++i)
	{
		Print << font.getGlyphNameByGlyphIndex(i);
	}

	while (System::Update())
	{

	}
}
```
:::


### 1.8 デフォルトフォント内蔵
- プロトタイピングの高速化や、環境間の表示差異解消のため、標準的なフォントセットをエンジンに同梱する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/1.8.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Siv3D において標準で同梱される日本語フォントの 1 つ
	const Font fontRegular{ 32, Typeface::CJK_Regular_JP };

	// Siv3D において標準で同梱されるカラー絵文字フォント
	const Font fontEmoji{ 32, Typeface::ColorEmoji };

	// Siv3D において標準で同梱されるアイコンフォント
	const Font fontIcon{ 32, Typeface::Icon_MaterialDesign };

	while (System::Update())
	{
		fontRegular(U"こんにちは Siv3D!").draw(Vec2{ 20, 20 }, ColorF{ 0.1 });
		fontEmoji(U"😀😃😄😁😆😅😂🤣😊😇").draw(Vec2{ 20, 70 });
		fontIcon(U"\U000F0493\U000F0787\U000F018C").draw(Vec2{ 20, 120 }, ColorF{ 0.1 });

	}
}
```
:::


### 1.9 フォールバックシステム
- 多言語対応や異体字表示における「豆腐文字」を防ぐため、不足グリフを代替フォントから自動補完する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/1.9.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font0{ 36, Typeface::Medium };
	const Font font1{ 36, Typeface::Medium };
	const Font font2{ 36, Typeface::Medium };

	const Font fontCJK{ 36, Typeface::CJK_Regular_JP };
	const Font fontEmoji{ 36, Typeface::ColorEmoji };

	// font1 にはフォールバックフォントを 1 つ追加する
	font1.addFallback(fontCJK);

	// font2 にはフォールバックフォントを 2 つ追加する
	font2.addFallback(fontCJK);
	font2.addFallback(fontEmoji);

	const String text = U"Hello! こんにちは 你好 안녕하세요 🐈🐕🚀";

	while (System::Update())
	{
		font0(U"font0:\n" + text).draw(Vec2{ 40, 40 }, ColorF{ 0.1 });
		font1(U"font1:\n" + text).draw(Vec2{ 40, 200 }, ColorF{ 0.1 });
		font2(U"font2:\n" + text).draw(Vec2{ 40, 360 }, ColorF{ 0.1 });
	}
}
```
:::



## 2. ラスタライズ
- 文字をどのように描画用データに変換・保持するかの機能

### 2.1 ビットマップラスタライズ
- 小さいサイズでの可読性とドット単位の正確な UI 表示を実現するため、基本的なピクセルベースの描画を提供する
- 拡大表示するとぼやける、巨大なサイズでのキャッシュはメモリを圧迫するなどの欠点がある

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/2.1.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::Bitmap, 40, Typeface::Bold };
	const String text = U"こんにちは Siv3D!";

	while (System::Update())
	{
		font(text).draw(40, Vec2{ 20, 20 }, ColorF{ 0.1 });
		font(text).draw(160, Vec2{ 20, 60 }, ColorF{ 0.1 });
		font(text).draw(280, Vec2{ 20, 200 }, ColorF{ 0.1 });
	}
}
```
:::


### 2.2 SDF / MSDF 生成
- アニメーションでの拡大表示や 3D 空間での表示に耐えうる高品質な描画を実現するため、距離場を用いたレンダリングに対応する
- 小さな解像度のテクスチャだけで、どれだけ拡大してもクッキリと滑らかに描画される
- Multi-channel SDF（MSDF）形式が有効

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/2.2.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 40, Typeface::Bold };
	const String text = U"こんにちは Siv3D!";

	while (System::Update())
	{
		font(text).draw(40, Vec2{ 20, 20 }, ColorF{ 0.1 });
		font(text).draw(160, Vec2{ 20, 60 }, ColorF{ 0.1 });
		font(text).draw(280, Vec2{ 20, 200 }, ColorF{ 0.1 });
	}
}
```
:::


### 2.3 静的テクスチャアトラス
- パフォーマンスを最大化するため、使用文字が確定している場合にテクスチャを事前生成する機能を提供する

### 2.4 動的テクスチャアトラス
- チャットやユーザー入力など予測不能なテキストに対応しつつメモリ効率を維持するため、実行時に必要なグリフをキャッシュする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/2.4.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 40, Typeface::Bold };
	const String text = U"Siv3D（シブスリーディー）は、音や画像、AI を使ったゲームやアプリを、モダンな C++ コードで楽しく簡単に開発できるオープンソースのフレームワークです。";

	while (System::Update())
	{
		font(text).draw(Rect{ 1280, 720 }.stretched(-20), ColorF{ 0.1 });

		// テクスチャアトラスを表示
		const auto& texture = font.getTexture();
		Rect{ 20, 240, texture.size() }.draw(ColorF{ 0.0 });
		texture.draw(Vec2{ 20, 240 });
	}
}
```
:::




## 3. レイアウト
- 文字をどこに、どのように配置するかを決定する機能

### 3.1 アンカー配置
- 解像度が異なる環境での UI 崩れを防ぐため、端や中央を基準とした相対的な配置指定を可能にする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/3.1.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		font(U"TopLeft").draw(40, Arg::topLeft(20, 20), ColorF{ 0.1 });
		font(U"TopRight").draw(40, Arg::topRight(780, 20), ColorF{ 0.1 });

		font(U"BottomLeft").draw(40, Arg::bottomLeft(20, 580), ColorF{ 0.1 });
		font(U"BottomRight").draw(40, Arg::bottomRight(780, 580), ColorF{ 0.1 });

		Rect{ 200, 100, 400, 100 }.draw(ColorF{ 0.8, 0.9, 1.0 });
		font(U"MiddleLeft").draw(20, Arg::middleLeft(200, 150), ColorF{ 0.1 });
		font(U"MiddleRight").draw(20, Arg::middleRight(600, 150), ColorF{ 0.1 });

		font(U"Center").draw(40, Arg::center(400, 300), ColorF{ 0.1 });
	}
}
```
:::


### 3.2 ベースライン配置
- サイズの異なるフォントや異種のフォントが混在しても整った見た目を維持するため、ベースラインに合わせた配置制御を行う

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/3.2.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font1{ FontMethod::MSDF, 48, U"RocknRollOne-Regular.ttf" };
	const Font font2{ FontMethod::MSDF, 48, Typeface::Bold };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		// ベースラインがそろわない
		font1(text).draw(30, Vec2{ 40, 100 }, ColorF{ 0.1 });
		font2(text).draw(20, Vec2{ 280, 100 }, ColorF{ 0.1 });
		font2(text).draw(50, Vec2{ 440, 100 }, ColorF{ 0.1 });

		Rect{ 0, 300, 800, 10 }.draw(Palette::Skyblue);

		// (40, 400) がベースラインの開始位置になるようテキストを描画
		font1(text).drawBase(30, Vec2{ 40, 300 }, ColorF{ 0.1 });
		Circle{ 40, 300 , 5 }.drawFrame(2, Palette::Red);

		// (280, 400) がベースラインの開始位置になるようテキストを描画
		font2(text).drawBase(20, Vec2{ 280, 300 }, ColorF{ 0.1 });
		Circle{ 280, 300 , 5 }.drawFrame(2, Palette::Red);

		// (440, 400) がベースラインの開始位置になるようテキストを描画
		font2(text).drawBase(50, Vec2{ 440, 300 }, ColorF{ 0.1 });
		Circle{ 440, 300 , 5 }.drawFrame(2, Palette::Red);
	}
}
```
:::


### 3.3 均等割付
- 長文ドキュメントの品質を高めるため、行の左右を揃える雑誌のようなレイアウト処理を実装する

### 3.4 行間制御
- 読みやすさの調整や限られたスペースへの格納を容易にするため、行送りの倍率や間隔を制御可能にする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/3.4.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	String text;
	for (int32 i = 0; i < 5; ++i)
	{
		text += U"こんにちは、Siv3D！\n";
	}

	while (System::Update())
	{
		// 通常の行間（1.0）
		font(text).draw(TextStyle{ .lineSpacing = 1.0 }, 30, Vec2{ 40, 40 }, ColorF{ 0.1 });

		// 行間を少し広げる（1.2）
		font(text).draw(TextStyle{ .lineSpacing = 1.2 }, 30, Vec2{ 400, 40 }, ColorF{ 0.1 });
	}
}
```
:::


### 3.5 字間制御
- 演出のため、文字ごとの間隔を動的に調整可能にする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/3.5.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"こんにちは、Siv3D！";

	while (System::Update())
	{
		// 字間を狭める（-1.5px）
		font(text).draw(TextStyle{ .characterSpacing = -1.5 }, 32, Vec2{ 40, 40 }, ColorF{ 0.1 });

		// 通常の字間（0.0）
		font(text).draw(TextStyle{ .characterSpacing = 0.0 }, 32, Vec2{ 40, 100 }, ColorF{ 0.1 });

		// 字間を少し広げる（2px）
		font(text).draw(TextStyle{ .characterSpacing = 2.0 }, 32, Vec2{ 40, 160 }, ColorF{ 0.1 });

		// 字間を広げる（4px）
		font(text).draw(TextStyle{ .characterSpacing = 4.0 }, 32, Vec2{ 40, 220 }, ColorF{ 0.1 });
	}
}
```
:::


### 3.6 タブ制御
- プログラムコードの表示においてインデントを揃えるため、タブ文字の幅制御に対応する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/3.6.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"int main()\n{\n\tint n = 0;\n}";

	while (System::Update())
	{
		font.setTabSize(4);
		font(text).draw(32, Vec2{ 40, 40 }, ColorF{ 0.1 });

		font.setTabSize(8);
		font(text).draw(32, Vec2{ 40, 280 }, ColorF{ 0.1 });
	}
}
```
:::


### 3.7 禁則処理・ハイフネーション
- 言語ごとのルールに基づいた自動調整を行い、行頭・行末での不自然な区切りを防ぎ、高品質なテキスト表示を担保する

### 3.8 自動改行
- 可変長のテキストを吹き出しやウィンドウ内に収めるため、指定した矩形幅に応じた自動折り返し処理を実装する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/3.8.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox jumps over the lazy dog.";
	const Rect rect{ 40, 40, 420, 120 };

	while (System::Update())
	{
		rect.draw();
		font(text).draw(24, rect.stretched(-20), ColorF{ 0.1 });
	}
}
```
:::


### 3.9 省略記号
- 表示領域不足を明示し UI の破綻を防ぐため、テキスト末尾を「...」などで自動省略する機能を提供する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/3.9.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox jumps over the lazy dog.";
	const Rect rect{ 40, 40, 240, 120 };

	while (System::Update())
	{
		rect.draw();
		font(text).draw(24, rect.stretched(-20), ColorF{ 0.1 });
	}
}
```
:::


### 3.10 縦書き
- 日本語特有の和風表現や小説的演出をサポートするため、縦書きレイアウトおよび専用グリフへの置換に対応する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/3.10.png)]

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		font(U"「横」書きの、\n文章の表示。").draw(Vec2{ 40, 40 }, ColorF{ 0.1 });
		font(ReadingDirection::TopToBottom, U"「縦」書きの、\n文章の表示。").draw(Vec2{ 700, 40 }, ColorF{ 0.1 });
	}
}
```
:::


### 3.11 RTL / BiDi 対応
- ラビア語圏などへのグローバル展開を可能にするため、右横書き（RTL）および双方向テキストの混在処理（BiDi）に対応する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/3.11.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, U"NotoSansArabic-Regular.ttf"};

	while (System::Update())
	{
		// Siv3D v0.8 は RTL のみ対応。BiDi には未対応
		font(ReadingDirection::RightToLeft, U"هذا نصّ عربيّ للاختبار فقط.").draw(Vec2{ 40, 40 }, ColorF{ 0.1 });
	}
}
```
:::


### 3.12 合字制御
- デザイン性と演出上の都合（1 文字ずつの表示など）を両立するため、合字機能の有効・無効を切り替え可能にする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/3.12.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		// デフォルトでは合字が有効
		font(U"Effect").draw(48, Vec2{ 40, 40 }, ColorF{ 0.1 });

		// 合字を無効にしてグリフを処理
		DrawableText{ font, U"Effect", EnableLigatures::No }.draw(48, Vec2{ 40, 100 }, ColorF{ 0.1 });
	}
}
```
:::


### 3.13 ルビ表示
- 難読語を含むコンテンツのアクセシビリティを向上させるため、文字に対するふりがなの自動配置をサポートする

### 3.14 自然言語分割
- 文節途中での不自然な改行を防ぎ読みやすさを向上させるため、意味のまとまりに基づいた改行位置判定を行う
- 日本語では BudouX などのライブラリを活用できる

https://x.com/Reputeless/status/1706452398947152133

### 3.15 テキスト領域計測
- 動的な UI 調整を行うため、描画前にテキストのバウンディングボックスを取得可能にする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/3.15.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		// テキストの領域を取得
		const RectF region = font(U"Hello, Siv3D!").region(36, Vec2{ 40, 40 });

		region.draw(ColorF{ 0.9 });

		font(U"Hello, Siv3D!").draw(36, Vec2{ 40, 40 }, ColorF{ 0.1 });
	}
}
```
:::




## 4. 文字コード
- 文字データの扱いに関する基本機能

### 4.1 Unicode 完全対応
- 多言語文字や特殊な漢字によるバグを防ぐため、サロゲートペアを含む Unicode 仕様を正しく処理する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/4.1.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::CJK_Regular_JP };

	while (System::Update())
	{
		font(U"𩸽").draw(48, Vec2{ 40, 40 }, ColorF{ 0.1 });
	}
}
```
:::


### 4.2 絵文字対応
- ZWJ シーケンスなど複数のコードで構成される絵文字の描画に対応する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/4.2.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ 48, Typeface::ColorEmoji };

	while (System::Update())
	{
		font(U"🍎🍊🇦🇺🏄🏾‍♀️👨‍👩‍👧‍👧").draw(48, Vec2{ 40, 40 });
	}
}
```
:::


### 4.3 アイコンフォント対応
- 画像素材なしで軽量かつ高品質な UI アイコンを利用できるよう、私用領域（PUA）等の特殊マッピングに対応する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/4.3.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Icon_MaterialDesign };

	while (System::Update())
	{
		font(U"\U000F0493\U000F0787\U000F018C").draw(48, Vec2{ 40, 40 }, ColorF{ 0.1 });
	}
}
```
:::


### 4.4 グリフ存在確認
- 未収録文字による表示不具合を回避し、ロバストな UI を構築するため、フォント内のグリフ有無を事前に判定可能にする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/4.4.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::CJK_Regular_JP };

	Print << font.hasGlyph(U"𰻞");

	while (System::Update())
	{
		font(U"𰻞𰻞麺").draw(48, Vec2{ 40, 40 }, ColorF{ 0.1 });
	}
}
```
:::




## 5. 装飾
- 見た目を装飾する機能。複数の装飾を組み合わせて使用できるようにする

### 5.1 擬似ボールド / イタリック
- 専用データがないフォントでも強調表現を行えるよう、ジオメトリ操作による太字・斜体の生成機能を提供する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/5.1.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, U"RocknRollOne-Regular.ttf" };
	const Font fontBold{ FontMethod::MSDF, 48, U"RocknRollOne-Regular.ttf", FontStyle::Bold };
	const Font fontItalic{ FontMethod::MSDF, 48, U"RocknRollOne-Regular.ttf", FontStyle::Italic };

	while (System::Update())
	{
		font(U"こんにちは Siv3D!").draw(Vec2{ 20, 20 }, ColorF{ 0.1 });
		fontBold(U"こんにちは Siv3D! (Bold)").draw(Vec2{ 20, 100 }, ColorF{ 0.1 });
		fontItalic(U"こんにちは Siv3D! (Italic)").draw(Vec2{ 20, 180 }, ColorF{ 0.1 });
	}
}
```
:::


### 5.2 カラー
- 情報の重要度やゲームの状態を直感的に伝えるため、テキスト全体の RGBA カラー指定に対応する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/5.2.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, U"RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"こんにちは Siv3D!").draw(Vec2{ 20, 20 }, ColorF{ 0.1 });
		font(U"こんにちは Siv3D!").draw(Vec2{ 20, 100 }, ColorF{ 0.0, 0.7, 0.4 });
		font(U"こんにちは Siv3D!").draw(Vec2{ 20, 180 }, ColorF{ 0.0, 0.4, 0.7 });
	}
}
```
:::


### 5.3 グラデーション
- タイトルロゴや演出などでリッチな質感を表現するため、文字単位や全体へのグラデーション塗りに対応する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/5.3.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, U"RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"こんにちは Siv3D!").draw(Vec2{ 20, 20 },
			TextEffect::VerticalGradient{ ColorF{ 0.9, 0.3, 0.3 }, ColorF{ 0.3, 0.3, 0.9 } });

		font(U"こんにちは Siv3D!").draw(Vec2{ 20, 100 },
			TextEffect::HorizontalGradient{ ColorF{ 0.9, 0.3, 0.3 }, ColorF{ 0.3, 0.3, 0.9 }, 20, (20 + font(U"こんにちは Siv3D!").region().w) });
	}
}
```
:::


### 5.4 アウトライン
- 背景色と同化することを防ぎ視認性を確保するため、文字の縁取り（袋文字）描画を実装する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/5.4.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, 4, U"RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"こんにちは Siv3D!").draw(TextStyle::Outline(0.0, 0.25, ColorF{ 0.0 }), Vec2{ 20, 20 }, ColorF{ 0.3, 0.9, 0.9 });

		font(U"こんにちは Siv3D!").draw(TextStyle::Outline(0.0, 0.1, ColorF{ 0.0, 0.5, 0.0 }), Vec2{ 20, 100 },
			TextEffect::VerticalGradient{ ColorF{ 1.6 }, ColorF{ 0.0, 0.6, 0.3 } });
	}
}
```
:::


### 5.5 ドロップシャドウ
- 奥行きを与えるため、文字の影を描画する機能を提供する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/5.5.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, 4, U"RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"こんにちは Siv3D!").draw(TextStyle::Shadow(Vec2{ 4, 4 }, ColorF{ 0.0 }), Vec2{ 20, 20 }, ColorF{ 1.0 });

		font(U"こんにちは Siv3D!").draw(TextStyle::OutlineShadow(0.0, 0.05, Vec2{ 0, 4 }, ColorF{ 0.0 }), Vec2{ 20, 100 },
			TextEffect::VerticalGradient{ ColorF{ 1.6 }, ColorF{ 0.0, 0.6, 0.3 } });
	}
}
```
:::


### 5.6 光彩
- 強調や発光表現を行うため、文字周囲へのぼかし発光エフェクトを実装する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/5.6.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, 6, U"RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"こんにちは Siv3D!").draw(TextStyle::Glow(0.8), Vec2{ 20, 20 }, ColorF{ 0.3, 0.5, 0.8 });
		font(U"こんにちは Siv3D!").draw(Vec2{ 20, 20 }, ColorF{ 1.0 });

		font(U"こんにちは Siv3D!").draw(TextStyle::Glow(0.25), Vec2{ 20, 100 }, ColorF{ 0.2 });
		font(U"こんにちは Siv3D!").draw(Vec2{ 20, 100 },
			TextEffect::VerticalGradient{ ColorF{ 1.6 }, ColorF{ 0.0, 0.6, 0.3 } });

		Rect{ 0, 180, 800, 80 }.draw(ColorF{ 0.0 });
		font(U"こんにちは Siv3D!").draw(TextStyle::Glow(0.8), Vec2{ 20, 180 }, ColorF{ 1.0, 0.8, 0.0 });
		font(U"こんにちは Siv3D!").draw(Vec2{ 20, 180 }, ColorF{ 0.0 });
	}
}
```
:::


### 5.7 下線・取り消し線
- ハイパーリンクやステータス変化（達成済みなど）を視覚化するため、文字への装飾線描画に対応する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/5.7.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, U"RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		const RectF rect1 = font(U"こんにちは Siv3D!").draw(Vec2{ 20, 20 }, ColorF{ 0.1 });
		rect1.bottom().withOffsetY(-6).draw(3, ColorF{ 0.0 });

		const RectF rect2 = font(U"こんにちは Siv3D!").draw(Vec2{ 20, 100 }, ColorF{ 0.1 });
		rect2.middleHorizontal().withOffsetY(3).draw(3, ColorF{ 0.0 });

		const RectF rect3 = font(U"こんにちは Siv3D!").draw(Vec2{ 20, 180 }, ColorF{ 0.1 });
		rect3.middleHorizontal().draw(3, ColorF{ 0.0 });
		rect3.middleHorizontal().withOffsetY(6).draw(3, ColorF{ 0.0 });
	}
}
```
:::


### 5.8 インライン画像
- チュートリアル等で操作説明をスムーズに行うため、テキストの途中にアイコン画像等を埋め込む機能を提供する


### 5.9 カスタムシェーダ
- 標準機能では表現できないグリッチや燃焼などの特殊演出を実現するため、描画シェーダのオーバーライドを可能にする

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, U"RocknRollOne-Regular.ttf" };
	const PixelShader pixelShader{ ... }; // カスタムシェーダ

	while (System::Update())
	{
		{
			const ScopedCustomShader2D shader{ pixelShader };
			font(U"こんにちは Siv3D!").draw(TextStyle::CustomTextFontShader(), Vec2{ 20, 20 }, ColorF{ 0.1 });
		}
	}
}
```
:::


## 6. アニメーション・変形
- ゲーム特有の動的な表現機能

### 6.1 タイプライター演出
- 会話シーンで読むペースを制御し臨場感を出すため、時間経過に伴う文字の順次表示をサポートする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/6.1.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, U"RocknRollOne-Regular.ttf" };
	const String text = U"こんにちは Siv3D!";

	Stopwatch sw{ StartImmediately::Yes };

	while (System::Update())
	{
		// 100ms ごとに表示する文字数が増える
		const int32 count = (sw.ms() / 100);
		font(text.subview(0, count)).draw(Vec2{ 20, 20 }, ColorF{ 0.1 });
	}
}
```
:::


### 6.2 文字単位アニメーション
- 感情表現や動的な演出を強化するため、個別の文字に対する位置・回転・色の操作を可能にする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/6.2.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

class TextWave : public TextEffect::BasicTextEffect
{
public:

	using TextEffect::BasicTextEffect::BasicTextEffect;

	void draw(const TextureRegion& textureRegion, const GlyphContext& glyphContext) const override
	{
		const double offsetY = (Math::Sin(glyphContext.index * 25_deg) * -30);
		const Vec2 pos = glyphContext.pos.withOffsetY(offsetY);
		textureRegion.draw(pos, m_color);
	}
};

class ZebraColor : public ITextEffect
{
public:

	using ITextEffect::ITextEffect;

	void draw(const TextureRegion& textureRegion, const GlyphContext& glyphContext) const override
	{
		const ColorF color{ IsEven(glyphContext.index) ? 0.1 : 1.0};
		textureRegion.draw(glyphContext.pos, color);
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, U"RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"The quick brown fox jumps over the lazy dog.").draw(Vec2{ 20, 60 }, TextWave{ ColorF{ 0.1, 0.5, 0.4 } });
		font(U"The quick brown fox jumps over the lazy dog.").draw(Vec2{ 20, 160 }, ZebraColor{});
	}
}
```
:::


### 6.3 鏡文字・反転
- 鏡面世界や特殊なパズル演出を手軽に実装するため、行列計算なしでの文字反転描画を提供する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/6.3.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, U"RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"左右反転あいうえお").draw(Vec2{ 40, 40 }, TextEffect::FlipX{ ColorF{ 0.1, 0.5, 0.4 } });
		font(U"上下反転あいうえお").draw(Vec2{ 40, 100 }, TextEffect::VerticalScale(-1.0, 0.5, ColorF{ 0.4, 0.5, 0.1 }));
	}
}
```
:::


### 6.4 メッシュ変形
- UI デザインの自由度を高めるため、円形配置や波打ちなど矩形に縛られないテキスト変形に対応する

### 6.5 反射
- 高級感の演出などのため、文字下部への鏡面反射描画をサポートする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/6.5.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.1 });
	const Font font{ FontMethod::MSDF, 48, 4, Typeface::Bold };

	while (System::Update())
	{
		font(U"Hello, Siv3D!").draw(Vec2{ 40, 40 },
			TextEffect::Reflection{ 0.5, 0.5, 0.0, ColorF{ 0.2, 0.8, 1.0 } });
	}
}
```
:::




## 7. 発展的なデータアクセス
- レンダリング以外でのデータ利用

### 7.1 輪郭パス取得
- 輪郭に沿ったエフェクト描画への応用のため、グリフのアウトラインデータの取得に対応する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/7.1.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

Array<LineString> ToLineStrings(const Vec2& basePos, const Array<OutlineGlyph>& glyphs)
{
	Array<LineString> lines;

	Vec2 penPos{ basePos };

	for (const auto& glyph : glyphs)
	{
		for (const auto& ring : glyph.rings)
		{
			lines << ring.movedBy(penPos + glyph.getOffset());
		}

		penPos.x += glyph.advance;
	}

	return lines;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.1 });
	const Font font{ 80, Typeface::CJK_Regular_JP };

	const String text = U"こんにちは、Siv3D!";
	const Array<LineString> lines = ToLineStrings(Vec2{ 20, 20 }, font.renderOutlines(text));

	while (System::Update())
	{
		for (size_t i = 0; i < lines.size(); ++i)
		{
			lines[i].drawClosed(2, HSV{ (i * 50) });
		}
	}
}
```
:::


### 7.2 メッシュデータ取得
- 特殊なエフェクトや 3D テキスト表現、物理演算への応用のため、グリフを構成する三角形ポリゴンを取得可能にする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/refs/heads/main/zenn/text2025/7.2.png)

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

Array<Polygon> ToPolygons(const Vec2& basePos, const Array<PolygonGlyph>& glyphs)
{
	Array<Polygon> polygons;
	Vec2 penPos{ basePos };

	for (const auto& glyph : glyphs)
	{
		for (const auto& polygon : glyph.polygons)
		{
			polygons << polygon.movedBy(penPos + glyph.getOffset());
		}

		penPos.x += glyph.advance;
	}

	return polygons;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 80, Typeface::CJK_Regular_JP };
	const String text = U"こんにちは、Siv3D!";
	const Array<Polygon> polygons = ToPolygons(Vec2{ 20, 20 }, font.renderPolygons(text));

	while (System::Update())
	{
		for (const auto& polygon : polygons)
		{
			polygon.draw();
			polygon.drawWireframe(1, ColorF{ 0.1 });
		}
	}
}
```
:::


### 7.3 ビットマップ書き出し
- 画像処理やテクスチャ生成への応用のため、グリフやテキストのレンダリング結果をビットマップ画像として取得・保存可能にする

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 80, Typeface::CJK_Regular_JP };

	// 「あ」のビットマップを保存
	font.renderBitmap(U'あ').image.save(U"あ.png");

	while (System::Update())
	{

	}
}
```
:::



## 8. その他
- パフォーマンスや開発効率に関わるインフラ部分

### 8.1 バッチング
- 大量のテキスト表示における描画負荷を低減するため、ドローコールを自動的に結合する最適化を行う

### 8.2 レイアウトキャッシュ
- 静的なテキスト表示の CPU 負荷を低減するため、計算コストの高いレイアウト結果を再利用可能にする

### 8.3 リッチテキスト
- データドリブンでのテキスト管理効率化のため、HTML や Markdown のようなマークアップによる書式指定とパース機能を提供する

---

これ以外にも取り上げるべき機能があれば、コメントで教えてください！

