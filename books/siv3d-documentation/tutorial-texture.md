---
title: "チュートリアル 08 | 画像を描く"
free: true
---

# 8. 画像を描く
この章では、絵文字やアイコン、画像ファイルから読み込んだ画像をシーンに描く方法を学びます。

## 8.1 絵文字を描画する
シーンに画像を描きたいときは `Texture` を作成し、`.draw()` または `.drawAt()` します。`Texture` は、次のような方法で作成できます。

- 画像ファイルから画像データを読み込む
- プログラムで作成した画像データ (`Image`) から作成する
- Siv3D が標準で提供する絵文字コレクションから作成する
- Siv3D が標準で提供するアイコンコレクションから作成する

`Texture` はテクスチャのリソースをデストラクタで自動的に解放するため、リソースの解放処理を明示的に書く必要はありません。

まず最初に、絵文字コレクションから好きな絵文字を選んで `Texture` を作成し、それを描画するプログラムを書いてみましょう。`Texture` の作成にはメモリ確保などの実行時負荷がかかります。メインループの中で毎フレーム新しい `Texture` を作成するのは避け、作成が 1 回だけで済むようにしましょう。

絵文字コレクションから `Texture` を作成するには、`Texture` のコンストラクタ引数に `U"🐈"_emoji` のように絵文字を渡します。

### Texture::drawAt()
`.drawAt()` では、テクスチャの中心をどこに据えるかをシーンの座標で指定します。絵文字やアイコンを描く場合はこの指定方法が便利です。
![](/images/doc_v6/tutorial/8/1.1.png)
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

		// テクスチャを座標 (200, 200) を中心に描画
		texture.drawAt(200, 200);

		// テクスチャを、シーン中央を中心にして描画
		texture.drawAt(Scene::Center());
	}
}
```

### Texture::draw()
`.draw()` では、テクスチャの左上をどこに据えるかをシーンの座標で指定します。背景画像や UI などを描くときにはこの指定方法が便利な場合があります。
![](/images/doc_v6/tutorial/8/1.2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 🐈 の絵文字からテクスチャを作成
	const Texture texture{ U"🐈"_emoji };

	while (System::Update())
	{
		// テクスチャを座標 (400, 0) から描画
		texture.draw(400, 0);

		// 座標を指定しない場合 (0, 0) から描画
		texture.draw();

		// テクスチャをシーン中心から描画
		texture.draw(Scene::Center());
	}
}
```

### Siv3D で使える絵文字の一覧
Siv3D で使える絵文字は約 3,600 種類あります。絵文字を探すときは [emojipedia](https://emojipedia.org/) の Categories から調べるのが便利です。 OpenSiv3D v0.6.7 はオープンソースの絵文字フォント Noto Color Emoji (Unicode 14.0 版) を内蔵しているので、Siv3D アプリはどのプラットフォームでも同じ見た目の絵文字を表示できます。

## 8.2 テクスチャを拡大縮小して描画する

### Texture::scaled()
`Texture::scaled()` に拡大縮小倍率を指定すると、`Texture` にサイズ情報が付加された `TextureRegion` を作成できます。`TextureRegion` は `Texture` のように `.draw()` または `.drawAt()` できます。`Texture` を作成するのと異なり、既存の `Texture` から `TextureRegion` を作成するのは非常に軽い実行時負荷です。サンプルでは示していませんが、縦横で異なる倍率に設定することもできます。
![](/images/doc_v6/tutorial/8/2.1.png)
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
![](/images/doc_v6/tutorial/8/2.2.png)
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
![](/images/doc_v6/tutorial/8/3.1.png)
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
![](/images/doc_v6/tutorial/8/3.2.png)
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
![](/images/doc_v6/tutorial/8/4.png)
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
アイコンコレクションから `Texture` を作成するには、`Texture` のコンストラクタ引数にアイコンオブジェクトを渡します。アイコンは全部で約 7,700 種類用意されています。

`Texture` のコンストラクタには、[Font Awesome アイコン一覧](https://fontawesome.com/icons?d=gallery&s=brands,solid&m=free) または [Material Design Icons](https://pictogrammers.github.io/@mdi/font/6.5.95/) で調べられる 16 進数コードに `_icon` を付けた値と、アイコンの基本サイズ（ピクセル）を渡します。
![](/images/doc_v6/tutorial/8/5.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.92 });

	// 歯車 (f013) のアイコン
	const Texture icon0{ 0xf013_icon, 40 };

	// 南京錠 (f023) のアイコン
	const Texture icon1{ 0xf023_icon, 80 };

	// 拡大鏡 (f00e) のアイコン
	const Texture icon2{ 0xf00e_icon, 120 };

	while (System::Update())
	{
		icon0.drawAt(200, 300, ColorF{ 0.25 });

		icon1.drawAt(400, 300, ColorF{ 0.25 });

		icon2.drawAt(600, 300, ColorF{ 0.25 });
	}
}
```


## 8.6 画像ファイルを読み込んで描画する
画像ファイルから `Texture` を作成するには、`Texture` のコンストラクタ引数に、読み込みたい画像ファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します。
![](/images/doc_v6/tutorial/8/6.png)
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
OpenSiv3D v0.6.7 では、9 種類の画像フォーマットの読み込みがサポートされています。

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
テクスチャの作成時に**ミップマップ**を作成するオプションを設定すると、そのテクスチャを縮小して表示する際の画質や実行時性能が改善されます。ミップマップとは、あらかじめきれいに縮小された画像を、テクスチャ作成時に生成しておく技術のことです。プログラムがテクスチャを縮小表示する際に、必要に応じてミップマップテクスチャが参照されます。

ミップマップを作成する場合、テクスチャのロード時間が若干の増加し、テクスチャが消費するメモリも 30% ほど増加します。それでも、画像の縮小表示が行われる多くのケースで、ミップマップ作成は最適な選択肢になります。

絵文字やアイコンから Texture を作る場合はデフォルトでミップマップが作成されます。一方、画像ファイルから `Texture` を作成する場合は、コンストラクタの第 2 引数でミップマップのありなしを選択します。デフォルトではミップマップを作成しない `TextureDesc::Unmipped` になっていて、明示的に `TextureDesc::Mipped` を指定したときにだけミップマップを作成します。

次のサンプルで、ミップマップの効果を確認できます。ミップマップを作成していない左のテクスチャは、サイズの変化に伴いちらちらしたノイズが発生しますが、ミップマップを作成する右のテクスチャではそれが抑えられていることがわかります。
https://youtu.be/ZsviU-05d-M
```cpp
# include <Siv3D.hpp>

void Main()
{
	// ミップマップを作成しない設定
	const Texture unmipped{ U"example/windmill.png" };

	// ミップマップを作成する設定
	const Texture mipped{ U"example/windmill.png", TextureDesc::Mipped };

	while (System::Update())
	{
		const double scale = (0.02 + Periodic::Triangle0_1(10s) * 0.4);

		unmipped.scaled(scale).drawAt(300, 300);

		mipped.scaled(scale).drawAt(500, 300);
	}
}
```


## 8.8 テクスチャの一部を描画する
テクスチャの全部ではなく、特定の長方形の領域だけを描画したい場合は `Texture::operator()` で、テクスチャ上の描画したい領域を指定して `TextureRegion` を作成します。
![](/images/doc_v6/tutorial/8/8.png)
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


## 8.9 テクスチャのサイズ
テクスチャのサイズを `Size` 型（`Point` 型のエイリアス) で取得するには `.size()` を使います。横のサイズだけ、縦のサイズだけを取得したい場合は、それぞれ `.width()`, `.height()` を使います。
![](/images/doc_v6/tutorial/8/9.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"example/windmill.png" };

	Print << texture.size();

	Print << texture.width();

	Print << texture.height();

	while (System::Update())
	{
		Rect{ 0, 0, texture.size() }.draw();
	}
}
```


## 8.10 図形の形に合わせてテクスチャを描く
`Rect` や `RectF`, `Circle`, `Quad`, `RoundRect`, `Polygon` に、テクスチャ全体やテクスチャの一部領域を貼り付けて描くことができます。
![](/images/doc_v6/tutorial/8/10.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture textureWindmill{ U"example/windmill.png", TextureDesc::Mipped };
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png", TextureDesc::Mipped };

	const RoundRect roundRect{ 430, 50, 100, 100, 25 };
	const Circle circle{ 480, 240, 50 };
	const Polygon hexagon = Shape2D::Hexagon(60, Vec2{ 480, 380 });

	while (System::Update())
	{
		// テクスチャを長方形に貼り付けて描画
		Rect{ 50, 50, 350, 400 }(textureWindmill).draw();

		roundRect.draw(HSV{ 0, 0.5, 1.0 });
		// テクスチャの (90, 5) から幅 110 高さ 110 の領域を、roundRect に貼り付けて描画
		roundRect(textureSiv3DKun(90, 5, 110, 110)).draw();

		circle.draw(HSV{ 120, 0.5, 1.0 });
		// テクスチャの (85, 0) から幅 120 高さ 120 の領域を、circle に貼り付けて描画
		circle(textureSiv3DKun(85, 0, 120, 120)).draw();

		hexagon.draw(HSV{ 240, 0.5, 1.0 });
		// Polygon に対し、(515, 560) を画像の中心とするようにテクスチャを貼り付けて描画
		hexagon.toBuffer2D(Arg::center(515, 560), textureSiv3DKun.size())
			.draw(textureSiv3DKun);
	}
}
```


## 8.11 テクスチャを透過させる
テクスチャの `.draw()`, `.drawAt()` の引数に色を渡すと、その色で乗算して描画されます。アルファ成分に応じてテクスチャは透過します。
https://youtu.be/uRIwKBklWQ8
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture textureWindmill{ U"example/windmill.png" };

	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };

	while (System::Update())
	{
		textureWindmill.draw(40, 20);

		// アルファ成分を 0.0～1.0 の間で変化させる
		textureSiv3DKun.draw(400, 100, ColorF{ 1.0, Periodic::Sine0_1(2s) });
	}
}
```


## 8.12 空のテクスチャ
データを持たない空（から）のテクスチャは、16x16 の黄色い画像になります。画像ファイルの読み込みに失敗したときにも空のテクスチャが作成されます。

テクスチャが空であるかは `if (texture.isEmpty())` もしくは `if (not texture)` で調べられます。
![](/images/doc_v6/tutorial/8/12.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 初期データを与えないと、空のテクスチャになる
	Texture textureA;

	if (not textureA)
	{
		Print << U"textureA is empty";
	}

	// 画像ファイルの読み込みに失敗すると、空のテクスチャになる
	Texture textureB{ U"aaa/bbb.png" };

	if (not textureB)
	{
		Print << U"textureB is empty";
	}

	while (System::Update())
	{
		textureA.draw(0, 0);

		textureB.draw(400, 0);
	}
}
```


## 8.13 テクスチャの代入
`Texture` は次のように `=` 演算子を使って代入できます。
![](/images/doc_v6/tutorial/8/13.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Texture texture;

	while (System::Update())
	{
		// テクスチャが空の状態で、左クリックされたら
		if ((not texture) && MouseL.down())
		{
			// テクスチャを作成して代入
			texture = Texture{ U"example/windmill.png" };
		}

		if (texture)
		{
			texture.draw();
		}
	}
}
```

