---
title: "チュートリアル 08 | 画像を描く"
free: true
---

# 8. 画像を描く
この章では、絵文字やアイコン、画像ファイルから読み込んだ画像を画面に描く方法を学びます。

## 8.1 絵文字を描画する
画面に画像を描きたいときは `Texture` を作成し、`.draw()` または `.drawAt()` します。`Texture` は、画像ファイルから画像データを読み込んで作成したり、プログラムで作成した画像データから作成したり、Siv3D が標準で提供している絵文字やアイコンコレクションから作成することができます。

この章の最初では、絵文字コレクションから好きな絵文字を選んで `Texture` を作成し、それを描画するプログラムを書いてみましょう。`Texture` の作成にはメモリ確保などの実行時負荷がかかります。メインループの中で毎フレーム新しい `Texture` を作成するのは避け、作成が 1 回だけで済むようにしましょう。

絵文字コレクションから `Texture` を作成するには、`Texture` のコンストラクタ引数に `U"🐈"_emoji` のように絵文字を渡します。

### Texture::drawAt()
`.drawAt()` では、テクスチャの中心をどこに据えるかをシーンの座標で指定します。絵文字やアイコンを描く場合はこの指定方法が便利です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 🐈 の絵文字からテクスチャを作成
	const Texture texture{ U"🐈"_emoji };

	while (System::Update())
	{
		// テクスチャを座標 (0, 0) を中心に描画
		texture.drawAt(0, 0);

		// テクスチャを画面中央を中心に描画
		texture.drawAt(Scene::Center());
	}
}
```

### Texture::draw()
`.draw()` では、テクスチャの左上をどこに据えるかをシーンの座標で指定します。背景画像や UI などを描くときにはこの指定方法が便利な場合があります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 🐈 の絵文字からテクスチャを作成
	const Texture texture{ U"🐈"_emoji };

	while (System::Update())
	{
		// テクスチャを座標 (0, 0) から描画
		texture.draw(0, 0);

		// テクスチャを画面中心から描画
		texture.draw(Scene::Center());
	}
}
```

### Siv3D で使える絵文字の一覧
OpenSiv3D で使える絵文字は約 3,600 種類あります。絵文字は [emojipedia](https://emojipedia.org/) の Categories から探すのが便利です。 OpenSiv3D v0.6.0 はオープンソースの絵文字フォント Noto Color Emoji (Android 12.0 版) を内蔵しているので、Siv3D アプリはどのプラットフォームでも同じ見た目の絵文字を表示できます。

## 8.2 テクスチャを拡大縮小して描画する

### Texture::scaled()
`Texture::scaled()` に拡大縮小倍率を指定すると、`Texture` にサイズ情報が付加された `TextureRegion` を作成できます。`TextureRegion` は `Texture` のように `.draw()` または `.drawAt()` できます。`Texture` を作成するのと異なり、既存の `Texture` から `TextureRegion` を作成するのは非常に軽い実行時負荷です。サンプルでは示していませんが、縦横で異なる倍率に設定することもできます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	const Texture dog{ U"🐕"_emoji };

	while (System::Update())
	{
		// 2 倍に拡大して描画
		cat.scaled(2.0).drawAt(200, 300);

		// 1.5 倍に拡大して描画
		cat.scaled(1.5).drawAt(400, 300);

		// 半分のサイズに縮小して描画
		dog.scaled(0.5).drawAt(600, 300);
	}
}
```

### Texture::resized()
`Texture::resized()` は、拡大縮小後のサイズを倍率ではなくピクセル単位で指定します。Siv3D 標準の絵文字のサイズは幅が 136 ピクセルなので、136 より大きい数を指定すると拡大、小さい数を指定すると縮小表示になります。サンプルでは示していませんが、縦横で異なるサイズに設定することもできます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	const Texture dog{ U"🐕"_emoji };

	while (System::Update())
	{
		// 300px に拡大して描画
		cat.resized(300).drawAt(200, 300);

		// 200px に拡大して描画
		cat.resized(200).drawAt(400, 300);

		// 20px に縮小して描画
		dog.resized(20).drawAt(600, 300);
	}
}
```

## 8.3 テクスチャを回転して描画する
`Texture::rotated()` または `Texture::rotatedAt()` によって、テクスチャに回転情報を付加した `TexturedQuad` を作成できます。`TexturedQuad` は `Texture` のように `.draw()` または `.drawAt()` できます。`TextureRegion` と同様、`TexturedQuad` を作成するのは非常に軽い実行時負荷です。

### Texture::rotated()
テクスチャの中心を軸にして回転します。回転角度はラジアンで指定します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	while (System::Update())
	{
		// 毎秒 90° 時計回りに回転
		cat.rotated(Scene::Time() * 90_deg).drawAt(Scene::Center());
	}
}
```

### Texture::roatedAt()
テクスチャ上の指定した座標を軸にして回転します。`Vec2{ 0, 0 }` を指定すればテクスチャの左上が軸になります。回転角度はラジアンで指定します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	while (System::Update())
	{
		// 画像中の (40, 20) を軸に回転させて描画
		cat.rotatedAt(Vec2{ 40, 20 }, Scene::Time() * 90_deg).draw(Scene::Center());
	}
}
```

## 8.4 テクスチャを上下・左右反転して描画する
`Texture::flipped()` で上下反転、`Texture::mirrored()` で左右反転した `TextureRegion` を作成できます。それぞれ、引数をとらずに反転する関数、引数の `bool` 値が `true` のときだけ反転する関数の 2 種類のオーバーロードがあります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	while (System::Update())
	{
		// 上下反転
		cat.flipped().drawAt(200, 300);

		// 左右反転
		cat.mirrored().drawAt(400, 300);

		// 反転するかを bool 値で指定するオーバーロード
		cat.mirrored(false).drawAt(600, 300);
	}
}
```

## 8.5 アイコンを描画する
アイコンコレクションから `Texture` を作成するには、`Texture` のコンストラクタ引数にアイコンオブジェクトを渡します。アイコンは全部で約 7,000 種類用意されています。

`Texture` のコンストラクタには、[Font Awesome アイコン一覧](https://fontawesome.com/icons?d=gallery&s=brands,solid&m=free) または [Material Design Icons](https://pictogrammers.github.io/@mdi/font/5.4.55/) で調べられる 16 進数コードに `_icon` を付けた値と、アイコンの基本サイズ（ピクセル）を渡します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.92 });

	// 歯車 (f013) のアイコン
	const Texture iconConfig{ 0xf013_icon, 80 };

	// 南京錠 (f023) のアイコン
	const Texture iconLock{ 0xf023_icon, 80 };

	// 拡大鏡 (f00e) のアイコン
	const Texture iconZoom{ 0xf00e_icon, 80 };

	while (System::Update())
	{
		iconConfig.drawAt(200, 300, ColorF{ 0.25 });

		iconLock.scaled(1.5).drawAt(400, 300, ColorF{ 0.25 });

		iconZoom.drawAt(600, 300, ColorF{ 0.25 });
	}
}
```

## 8.6 画像ファイルを読み込んで描画する
画像ファイルから `Texture` を作成するには、`Texture` のコンストラクタ引数に、読み込みたい画像ファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 風車の画像
	const Texture textureWindmill{ U"example/windmill.png" };

	// Siv3D くん（Siv3D の公式マスコットキャラクター）の画像
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };

	while (System::Update())
	{
		textureWindmill.draw(40, 20);

		textureSiv3DKun.draw(400, 100);
	}
}
```

### 対応している画像フォーマット
OpenSiv3D v0.6.0 では、9 種類の画像フォーマットの読み込みがサポートされています。

| フォーマット   | 拡張子             | 対応状況       |
|----------|-----------------|:----------:|
| PNG      | png             | ✔          |
| JPEG     | jpg/jpeg        | ✔          |
| BMP      | bmp             | ✔          |
| SVG      | svg             | ✔          |
| GIF      | gif             | ✔          |
| TGA      | tga             | ✔          |
| PPM      | ppm/pgm/pbm/pnm | ✔          |
| WebP     | webp            | ✔          |
| TIFF     | tif/tiff        | ✔          |
| JPEG2000 | jp2             | (将来のバージョン) |
| DDS      | dds             | (将来のバージョン) |


## 8.7 ミップマップの作成





## 8.8 テクスチャの一部を描画する
テクスチャの全部ではなく、特定の長方形の領域だけを描画したい場合は `Texture::operator()` で、テクスチャ上の描画したい領域を指定して `TextureRegion` を作成します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture textureWindmill{ U"example/windmill.png" };

	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };

	while (System::Update())
	{
		// 画像の (250, 100) から幅 200, 高さ 150 の長方形部分
		textureWindmill(250, 100, 200, 150).draw(40, 20);

		// 画像の (100, 0) から幅 100, 高さ 150 の長方形部分
		textureSiv3DKun(100, 0, 100, 150).draw(400, 100);
	}
}
```


## 8.9 図形の形に合わせてテクスチャを描く



## 8.10 テクスチャを透過させる




## 8.11 動画をテクスチャとして扱う
`VideoTexture` を使うと、動画ファイルを `Texture` のように扱えます。`VideoTexture` は毎フレーム `.advance()` を呼ぶことで再生位置を進めることができます。

対応する動画フォーマットはプラットフォームによって異なりますが、MP4 ファイルは Windows, macOS, Linux でサポートされています。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const VideoTexture videoTexture{ U"example/video/river.mp4", Loop::Yes };

	while (System::Update())
	{
		// 動画の時間を進める (デフォルトでは Scece::DeltaTime() 秒)
		videoTexture.advance();

		videoTexture
			.scaled(0.5).draw();

		videoTexture
			.scaled(0.5)
			.rotated(Scene::Time() * 30_deg)
			.drawAt(Cursor::Pos());
	}
}
```

## 8.12 テクスチャのサイズ


## 8.13 空のテクスチャ


## 8.14 テクスチャの代入



