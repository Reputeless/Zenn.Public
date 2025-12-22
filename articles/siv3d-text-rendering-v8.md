---
title: "ゲームエンジンを自作する場合のテキスト描画機能"
emoji: "🔠"
type: "tech"
topics: ["siv3d", "cpp"]
published: false
---

> [Siv3D Advent Calendar 2025](https://qiita.com/advent-calendar/2025/siv3d) および [グラフィックス全般 Advent Calendar 2025](https://qiita.com/advent-calendar/2025/graphics) の記事です。

ゲームエンジン・ライブラリを自作する場合に、テキスト描画に関してどのような機能を実装・提供すべきかを整理しました。

## 1. フォント管理
- フォントファイルの読み込みや形式サポートに関する機能

### 1.1 一般的なフォント形式の読み込み
- TrueType (.ttf) / OpenType (.otf) などのロードに対応する

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
		font(U"Hello, Siv3D!").draw(Vec2{ 20, 20 });
	}
}
```
:::


### 1.2 フォントコレクション対応
- TTC/OTC など、1 ファイルに複数書体が含まれる形式に対応する

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
		font1(U"こんにちは Siv3D!（メイリオ）").draw(Vec2{ 20, 20 });
		font2(U"こんにちは Siv3D!（Meiryo UI）").draw(Vec2{ 20, 80 });
	}
}
```
:::


### 1.3 カラーフォント対応
- ビットマップやベクター (COLRv1 など) を含むカラーフォントに対応する

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// COLRv1 形式のカラーフォントをロード
	const Font font{ 64, U"KalniaGlaze-VariableFont_wdth,wght.ttf", U"Bold" };

	while (System::Update())
	{
		font(U"Hello, Siv3D!").draw(Vec2{ 20, 20 });
	}
}
```
:::


### 1.4 Variable Font 対応
- Variable Font の軸パラメータ（Weight, Widthなど）を動的に制御する
- あるいは、既定のパラメータが設定された定義済みのスタイルを選択する

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
		font1(U"Hello, Siv3D!").draw(Vec2{ 200, 20 });
		font2(U"Hello, Siv3D!").draw(Vec2{ 200, 80 });
	}
}
```
:::


### 1.5 埋め込みビットマップ対応
- ベクターフォントに含まれるビットマップデータ（MS ゴシック等の EBDT）の利用有無を制御する

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
		font1(U"こんにちは Siv3D!").draw(Vec2{ 20, 20 }, Palette::Black);
		font2(U"こんにちは Siv3D!").draw(Vec2{ 20, 60 }, Palette::Black);
	}
}
```
:::


### 1.6 フォントメタデータ取得
- ファミリー名、バージョン、グリフ数などの基本情報を取得する

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
- フォントデータ内のグリフ名テーブルを参照し、グリフ名を取得する

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
- プラットフォームに依存せず、共通の見た目のフォントを利用できるようにする
- Noto Sans, Noto Color Emoji, Material Design Icons など、標準的なフォントをエンジンに同梱・利用可能にする

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 標準で同梱される日本語フォントの 1 つ
	const Font fontRegular{ 32, Typeface::CJK_Regular_JP };

	// 標準で同梱されるカラー絵文字フォント
	const Font fontEmoji{ 32, Typeface::ColorEmoji };

	// 標準で同梱されるアイコンフォント
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
- 指定フォントに文字がない場合、自動的に別のフォントを参照する

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
- 一般的なピクセルベースでのグリフ生成

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
	}
}
```
:::


### 2.2 SDF / MSDF 生成
- 拡大に強い Signed Distance Field (SDF) / Multi-channel SDF（MSDF）形式でのグリフ生成・描画

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
	}
}
```
:::


### 2.3 静的テクスチャアトラス
- ビルド時やロード時に、必要な文字一覧からアトラスを事前生成する

### 2.4 動的テクスチャアトラス
- 実行時に必要な文字を順次テクスチャに書き込み、キャッシュを管理する

:::details Siv3D v0.8 での例
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 36, Typeface::CJK_Regular_JP };
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
- 座標の原点指定（左上、中央、右下など）による描画位置の制御

:::details Siv3D v0.8 での例
```cpp

```
:::


### 3.2 ベースライン配置
- ベースラインを基準とした配置制御

:::details Siv3D v0.8 での例
```cpp

```
:::


### 3.3 均等割付
- 指定した幅に合わせて文字間隔を調整し、行頭・行末を揃える

### 3.4 行間制御
- 行送りの倍率を調整する

:::details Siv3D v0.8 での例
```cpp

```
:::


### 3.5 字間制御
- 字間の調整

:::details Siv3D v0.8 での例
```cpp

```
:::


### 3.6 タブ制御
- タブ文字の幅設定およびタブ位置揃え

:::details Siv3D v0.8 での例
```cpp

```
:::


### 3.7 禁則処理・ハイフネーション
- 行頭・行末の禁止文字処理や、欧文の単語分割処理

### 3.8 自動改行
- 指定した矩形幅に合わせて自動的に折り返す

:::details Siv3D v0.8 での例
```cpp

```
:::


### 3.9 省略記号
- 矩形に入り切らない場合、末尾を「...」などで省略表示する

:::details Siv3D v0.8 での例
```cpp

```
:::


### 3.10 縦書き
- 日本語などの縦書きレイアウトと、縦書き用グリフの使用に対応する

:::details Siv3D v0.8 での例
```cpp

```
:::


### 3.11 RTL / BiDi 対応
- アラビア語などの右横書き (RTL) および、混在時の双方向テキスト (BiDi) アルゴリズム

:::details Siv3D v0.8 での例
```cpp

```
:::


### 3.12 合字制御
- fi や ff などの合字機能の On/Off 切り替え

:::details Siv3D v0.8 での例
```cpp

```
:::


### 3.13 ルビ表示
- 文字に対してルビを配置・レンダリングする

### 3.14 自然言語分割
- 日本語の文節など、意味のまとまりで改行候補を算出する
- BudouX などのライブラリを活用できる

https://x.com/Reputeless/status/1706452398947152133

### 3.15 テキスト領域計測
- 実際に描画を行わず、レイアウト後のバウンディングボックスサイズのみを取得する

:::details Siv3D v0.8 での例
```cpp

```
:::




## 4. 文字コード
- 文字データの扱いに関する基本機能

### 4.1 Unicode 完全対応
- サロゲートペアなどを正しく扱う

:::details Siv3D v0.8 での例
```cpp

```
:::


### 4.2 絵文字対応
- カラー絵文字や ZWJ シーケンス（家族の絵文字など合成されるもの）の正しい描画

:::details Siv3D v0.8 での例
```cpp

```
:::


### 4.3 アイコンフォント対応
- 私用領域 (PUA) などに割り当てられたアイコンフォントを正しく扱う

:::details Siv3D v0.8 での例
```cpp

```
:::


### 4.4 グリフ存在確認
- 指定した文字に対応するグリフがフォント内に存在するか確認する

:::details Siv3D v0.8 での例
```cpp

```
:::




## 5. 装飾
- 見た目を装飾する機能

### 5.1 擬似ボールド / イタリック
- フォントデータがない場合に、ジオメトリ操作で太字・斜体を再現する

:::details Siv3D v0.8 での例
```cpp

```
:::


### 5.2 頂点カラー
- 文字全体、または上下左右の頂点ごとの色指定

:::details Siv3D v0.8 での例
```cpp

```
:::


### 5.3 グラデーション
- 文字の上下方向、テキストの横方向などに沿ったグラデーション塗りつぶし

:::details Siv3D v0.8 での例
```cpp

```
:::


### 5.3 アウトライン
- 文字の輪郭線を描画する

:::details Siv3D v0.8 での例
```cpp

```
:::


### 5.4 ドロップシャドウ
- 文字の影を描画する

:::details Siv3D v0.8 での例
```cpp

```
:::


### 5.5 光彩
- 文字の外側に発光表現を加える

:::details Siv3D v0.8 での例
```cpp

```
:::


### 5.6 下線・取り消し線
- 文字の装飾線を描画する

:::details Siv3D v0.8 での例
```cpp

```
:::


### 5.7 インライン画像
- テキスト中に画像を埋め込む


### 5.8 カスタムシェーダー
- 標準の描画シェーダーをユーザー定義のものでオーバーライドする機能

:::details Siv3D v0.8 での例
```cpp

```
:::


## 6. アニメーション・変形
- ゲーム特有の動的な表現機能

### 6.1 タイプライター演出
- 1 文字ずつ時間をずらして表示する

:::details Siv3D v0.8 での例
```cpp

```
:::


### 6.2 文字単位アニメーション
- 個別の文字に対し、位置・回転・スケール・色を動的に操作する

:::details Siv3D v0.8 での例
```cpp

```
:::


### 6.3 鏡文字・反転
- 文字を左右・上下に反転して描画する

:::details Siv3D v0.8 での例
```cpp

```
:::


### 6.4 メッシュ変形
- 円形配置や波打ちなど、テキスト全体または文字単位でメッシュを変形させる

### 6.5 反射
- テキストの下部に鏡面反射のような描画を追加する

:::details Siv3D v0.8 での例
```cpp

```
:::




## 7. 発展的なデータアクセス
- レンダリング以外でのデータ利用

### 7.1 輪郭パス取得
- 文字の輪郭をベクターパスや座標配列として取得する

:::details Siv3D v0.8 での例
```cpp

```
:::


### 7.2 メッシュデータ取得
- グリフを構成する三角形ポリゴンを生成・取得する

:::details Siv3D v0.8 での例
```cpp

```
:::


### 7.3 ビットマップ書き出し
- グリフをビットマップ画像として取得・保存する

:::details Siv3D v0.8 での例
```cpp

```
:::




## 8. その他

### 8.1 バッチング
- 複数の文字をまとめて 1 回のドローコールで描画する

### 8.2 レイアウトキャッシュ
- 計算コストの高い文字配置結果をキャッシュし、再利用する

### 8.3 リッチテキスト
- HTML タグ風や Markdown 風のマークアップによる書式指定をパースし、スタイルを適用する
