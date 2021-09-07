---
title: "チュートリアル 15 | シーンとウィンドウ"
free: true
---

# 15. シーンとウィンドウ

Siv3D では、図形やテクスチャ、テキストなどを `.draw()` すると、「シーン」と呼ばれる仮想の画面に描画されます。`System::Update()` 内でシーンの画像がウィンドウに転送されることで、ユーザは描画された結果を目にすることができます。

![](/images/doc_v6/tutorial/15/0.png)

これらの処理は自動的に行われるため、前章までは特に意識することなく `.draw()` した内容をウィンドウに表示していました。この章ではシーンやウィンドウの仕組みを掘り下げます。

## 15.1 三つのサイズ
Siv3D プログラムにおける画面表示を理解するには、「3 つのサイズ」を知る必要があります。

#### ① シーンのサイズ
`.draw()` などの描画や `Cursor::Pos()` などのマウスカーソル座標のプログラムの基準になる、独立した 1 つのシーンのサイズです。`Scene::Size()` で取得できます。デフォルトでは後述する仮想ウィンドウサイズと一致します。スクリーンショットを撮影したときはこの解像度で保存されます。

#### ② 仮想ウィンドウサイズ
ユーザのデスクトップ上における、ウィンドウのクライアント領域（タイトルやバーやフレームを除く、描画が行われる領域）の見かけのサイズです。`Window::GetState().virtualSize` で取得できます。この仮想ウィンドウサイズに OS 設定の拡大縮小倍率 (150%, 200% など) をかけたものが、後述する実ウィンドウサイズになります。

※ Linux 版では OS 設定の拡大縮小倍率に未対応で、仮想ウィンドウサイズと実ウィンドウサイズが一致します（OpenSiv3D v0.6.0 現在）

#### ③ 実ウィンドウサイズ（フレームバッファサイズ）
モニタ上のピクセル数で計測したウィンドウのクライアント領域のサイズです。`Window::GetState().frameBufferSize` で取得できます。


```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"scene size: " << Scene::Size();
	}
}
```

#### 3 つのサイズのデフォルト値
次のように Siv3D が OS 設定の拡大縮小倍率の差を吸収することで、プログラマは 800x600 のシーンにグラフィックスを描くことに専念できます。

| OS 設定の拡大縮小倍率 | シーンのサイズ | 仮想ウィンドウサイズ | 実ウィンドウサイズ |
|--|--|--|--|
| 100% | 800x600 | 800x600 | 800x600 |
| 125% | 800x600 | 800x600 | 1000x750 |
| 150% | 800x600 | 800x600 | 1200x900 |
| 200% | 800x600 | 800x600 | 1600x1200 |


## 15.2 シーンのリサイズモード
ユーザがウィンドウをマウスでリサイズしたり、ウィンドウをリサイズする関数を呼んだりすると、仮想ウィンドウサイズおよび実ウィンドウサイズが変化します。これらのウィンドウサイズに応じてシーンの新しいサイズを決定するのが、シーンの**リサイズモード**です。リサイズモードは次の 3 種類があります。

|リサイズモード|説明|
|--|--|
|`ResizeMode::Virtual` | 仮想ウィンドウサイズに合わせてシーンのサイズを変更します（デフォルト） |
|`ResizeMode::Actual` | 実ウィンドウサイズに合わせてシーンのサイズを変更します |
|`ResizeMode::Keep` | シーンのサイズを変更しません |

リサイズモードは `Scene::SetResizeMode(ResizeMode)` で設定します。現在のリサイズモードは `Scene::GetResizeMode()` で取得できます。


## 15.3 ウィンドウのリサイズ
`Window::Resize(int 幅, int 高さ)` または `Window::Resize(Size サイズ)` で、仮想ウィンドウサイズを変更できます。これにともない実ウィンドウサイズも変更され、リサイズモードによってはシーンのサイズも更新されます。

デフォルトのリサイズモード `ResizeMode::Virtual` が適用されている場合、`Window::Resize()` で指定した仮想ウィンドウサイズが新しいシーンのサイズになります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1000, 600);

	while (System::Update())
	{
		ClearPrint();
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"scene size: " << Scene::Size();

		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ x * 100, y * 100, 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}
	}
}
```

リサイズモード `ResizeMode::Keep` が適用されている場合、`Window::Resize()` シーンのサイズは変化しません。シーンのサイズと実ウィンドウサイズのアスペクト比が異なる場合、クライアント領域の左右もしくは上下に**レターボックス**と呼ばれる余白領域が生じます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);
	Window::Resize(1000, 600);

	while (System::Update())
	{
		ClearPrint();
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"scene size: " << Scene::Size();

		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ x * 100, y * 100, 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}
	}
}
```

## 15.4 ウィンドウを手動でリサイズできるようにする
`Window::SetStyle(WindowStyle::Sizable)` を設定すると、ウィンドウをつかんでリサイズできるようになります。ユーザの操作によって仮想ウィンドウサイズ / 実ウィンドウサイズが変更されたとき、リサイズモードに応じてシーンのサイズも更新されます。

デフォルトのリサイズモード `ResizeMode::Virtual` が適用されている場合、仮想ウィンドウサイズが新しいシーンのサイズになります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::SetStyle(WindowStyle::Sizable);

	while (System::Update())
	{
		ClearPrint();
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"scene size: " << Scene::Size();

		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ x * 100, y * 100, 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}
	}
}
```

リサイズモード `ResizeMode::Keep` が適用されている場合、ウィンドウを手動でリサイズしてもシーンのサイズは変化しません。シーンのサイズと実ウィンドウサイズのアスペクト比が異なる場合、クライアント領域の左右もしくは上下にレターボックスが生じます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);
	Window::SetStyle(WindowStyle::Sizable);

	while (System::Update())
	{
		ClearPrint();
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"scene size: " << Scene::Size();

		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ x * 100, y * 100, 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}
	}
}
```


## 15.5 レターボックスの色を変更する

```cpp

```


## 15.6 シーンのサイズだけを変更する

```cpp

```


## 15.7 シーンが実ウィンドウに送られる際のフィルタを変更する

```cpp

```


## 15.8 ウィンドウの枠を消す

```cpp

```


## 15.9 ウィンドウのタイトルを変更する

```cpp

```


## 15.10 ウィンドウをスクリーンの中心に移動する

```cpp

```


## 15.11 ウィンドウを最小化する

```cpp

```


## 15.12 ウィンドウを最大化する

```cpp

```


## 15.13 モニタの情報を得る

```cpp

```


## 15.14 フルスクリーンにする

```cpp

```

