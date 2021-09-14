---
title: "チュートリアル 33 | 画像処理"
free: true
---

# 33. 画像処理
この章では、画像処理プログラミングのための機能と、その結果をシーンに表示する方法を学びます。

`Texture` クラスで読み込んだ画像データは GPU のメモリ上に配置されるため、C++ プログラムで簡単にアクセスすることができません。一方 `Image` クラスで読み込んだ画像データはメインメモリ上に配置されるため、通常の `Array` や `Grid` のように、C++ プログラムで簡単にアクセスしたり中身を変更したりできます。ただし、`Image` にはシーンに画像を表示する機能は無く、表示する場合は `Texture` もしくは、本章で登場する `DynamicTexture` クラスを使う必要があります。

|  | Image | DyanmicTexture | Texture |
|--|:--:|:--:|:--:|
| データの格納場所 | メインメモリ | GPU メモリ | GPU メモリ |
| 内容の更新 | ✔ | ✔<br>`.fill()` を使う | |
| 描画 | | ✔ | ✔ |
| CPU からのアクセス | ✔ | | |
| GPU (シェーダ) からのアクセス | | ✔ | ✔ |


## 33.1 Image クラスの基本
Siv3D で画像データを扱うときは `Image` クラスを使うのが便利です。`Image` クラスでは、画像データを二次元配列クラス `Gird` のようなインタフェースで扱えます。

`Image` クラスは実質的には次のような構造です。
```cpp
// 説明のため簡略化
class Image
{
	Array<Color> m_data;
	uint32 m_width;
	uint32 m_height;
};
```

`Image` 型の変数 `image` に対する、インデックスによる要素へのアクセス `image[y][x] = value;` は、内部では `m_data[y * m_width + x] = value;` になります。また、`Point` 型の値 `pos` による `image[pos] = value;` は、`m_data[pos.y * m_width + pos.x] = value;` になります。

なお、`Color` 型は r, g, b, a の各色を `uint8` 型で保持する、4 バイトの構造体です。

```cpp
// 説明のため簡略化
struct Color
{
	uint8 r;
	uint8 g;
	uint8 b;
	uint8 a;
};
```

画像ファイルから `Image` を作成するには、`Image` のコンストラクタ引数に、読み込みたい画像ファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します。

また、`Texture` のコンストラクタに `Image` を渡すことで、`Image` が保持する画像データからテクスチャを作成することができます。

次のサンプルでは、マウスカーソルで選択した、画像の任意の位置のピクセル色を取得し表示します。

![](/images/doc_v6/tutorial/33/1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 画像ファイルから Image を作成
	const Image image{ U"example/windmill.png" };

	// Image から　Texture を作成
	const Texture texture{ image };

	while (System::Update())
	{
		const Point pos = Cursor::Pos();

		if (InRange(pos.x, 0, image.width() - 1)
			&& InRange(pos.y, 0, image.height() - 1))
		{
			// マウスカーソルの位置にあるピクセルの色を取得
			const Color pixelColor = image[pos];

			Rect{ 500, 20, 40 }.draw(pixelColor);

			PutText(U"{}"_fmt(pixelColor), Arg::topLeft(560, 20));
		}

		texture.draw();
	}
}
```


## 33.2 プログラムで画像を作成する
`Image` のコンストラクタに幅、高さ、色を渡して、画像を作成することができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Image image{ 400, 300, Color{ 63, 127, 255 } };

	const Texture texture{ image };

	while (System::Update())
	{
		texture.draw();
	}
}
```

なお、`Texture` のコンストラクタに `Image` を渡したときには、画像データが `Texture` にコピーされるので、`Texture` を作成したあとで `Image` を破棄しても問題ありません。上記のプログラムでは、メインループ中に使われない `image` がメモリを消費したままなので、次のようにするとメモリ消費量を減らせます。

```cpp
# include <Siv3D.hpp>

Image MakeImage()
{
	return Image{ 400, 300, Color{ 63, 127, 255 } };
}

void Main()
{
	const Texture texture{ MakeImage() };

	while (System::Update())
	{
		texture.draw();
	}
}
```


## 33.3 ピクセルを編集する
`Image` が持つ画像データの幅は `.width()`, 高さは `.height()`, 幅と高さは `.size()` で取得できます。次のようなループで、`Image` 内のすべてのピクセルにアクセスできます。

```cpp
# include <Siv3D.hpp>

Image MakeImage()
{
	Image image{ 400, 300 };

	for (int32 y = 0; y < image.height(); ++y)
	{
		for (int32 x = 0; x < image.width(); ++x)
		{
			image[y][x] = ColorF{ (x / 399.0), (y / 299.0), 1.0 };
		}
	}

	return image;
}

void Main()
{
	const Texture texture{ MakeImage() };

	while (System::Update())
	{
		texture.draw();
	}
}
```

次のように `step(Size)` を使って、ループを 1 つにまとめることもできます。

```cpp
# include <Siv3D.hpp>

Image MakeImage()
{
	Image image{ 400, 300 };

	for (auto p : step(image.size()))
	{
		image[p] = ColorF{ (p.x / 399.0), (p.y / 299.0), 1.0 };
	}

	return image;
}

void Main()
{
	const Texture texture{ MakeImage() };

	while (System::Update())
	{
		texture.draw();
	}
}
```


## 33.4 range-based for
range-based for を使って、すべてのピクセルにアクセスすることもできます。

```cpp
# include <Siv3D.hpp>

Image MakeImage()
{
	Image image{ U"example/windmill.png" };

	for (auto& pixel : image)
	{
		// R 成分と B 成分を入れ替える
		std::swap(pixel.r, pixel.b);
	}

	return image;
}

void Main()
{
	const Texture texture{ MakeImage() };

	while (System::Update())
	{
		texture.draw();
	}
}
```


## 33.5 画像を保存する
`Image` の画像データを画像ファイルとして保存するには `.save(path)` を使います。画像の保存形式は、`path` の拡張子から自動的に適切なものが選択されます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Image image{ U"example/windmill.png" };

	for (auto& pixel : image)
	{
		std::swap(pixel.r, pixel.b);
	}

	// 画像を保存
	image.save(U"tutorial1.png");

	while (System::Update())
	{

	}
}
```


## 33.6 ダイアログでファイル名を指定して画像を保存する
`Image` の画像データを、ダイアログでファイル名を指定して画像ファイルとして保存するには `.saveWithDialog()` を使います。`.save()`　同様、画像の保存形式は、`path` の拡張子から自動的に適切なものが選択されます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Image image{ U"example/windmill.png" };

	for (auto& pixel : image)
	{
		std::swap(pixel.r, pixel.b);
	}

	// ダイアログでファイル名を指定して画像を保存
	image.saveWithDialog();

	while (System::Update())
	{

	}
}
```


## 33.7

```cpp

```


## 33.8

```cpp

```


## 33.9

```cpp

```


## 33.10

```cpp

```


## 33.11

```cpp

```
