---
title: "チュートリアル 33 | 画像処理"
free: true
---

# 33. 画像処理
この章では、画像処理プログラミングのための機能と、その結果をシーンに表示する方法を学びます。

`Texture` クラスで読み込んだ画像データは GPU のメモリ上に配置されるため、C++ プログラムで簡単にアクセスすることができません。一方 `Image` クラスで読み込んだ画像データはメインメモリ上に配置されるため、通常の `Array` や `Grid` のように、C++ プログラムで簡単にアクセスしたり中身を変更したりできます。ただし、`Image` にはシーンに画像を表示する機能は無く、表示する場合は `Texture` もしくは、本章で登場する `DynamicTexture` クラスを使う必要があります。


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

次のサンプルでは、画像の任意の位置のピクセルカラーを取得し表示します。

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


## 33.2

```cpp

```


## 33.3

```cpp

```


## 33.4

```cpp

```


## 33.6

```cpp

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
