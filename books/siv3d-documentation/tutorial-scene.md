---
title: "チュートリアル 15 | シーンとウィンドウ"
free: true
---

# 15. シーンとウィンドウ

Siv3D では、図形やテクスチャ、テキストなどを `.draw()` すると、「シーン」と呼ばれる仮想の画面に描画されます。`System::Update()` 内でシーンの画像がウィンドウに転送されることで、ユーザは描画された結果を目にすることができます。

![](/images/doc_v6/tutorial/15/0.png)

これらの処理は自動的に行われるため、前章までは特に意識することなく `.draw()` した内容をウィンドウに表示していました。この章ではシーンやウィンドウの仕組みを掘り下げます。

この章は Siv3D の内部の仕組みも説明するため長い文章になっていますが、開発におけるほとんどのニーズは 15.3 の `Window::Resize()` だけで満たせるため、最初からすべてを理解する必要はありません。

## 15.1 三つのサイズ
Siv3D プログラムにおける画面表示を理解するには、「3 つのサイズ」を知る必要があります。

#### ① シーンのサイズ
`.draw()` などの描画や `Cursor::Pos()` などのマウスカーソル座標のプログラムの基準になる、独立した 1 つのシーンのサイズです。`Scene::Size()` で取得できます。デフォルトでは後述する仮想ウィンドウサイズと一致します。Siv3D の機能を使ってスクリーンショットを撮影したときは、この解像度で保存されます。

#### ② 仮想ウィンドウサイズ
ユーザのデスクトップ上における、ウィンドウの**クライアント領域**（タイトルバーやフレームを除く、描画が行われる領域）の見かけのサイズです。`Window::GetState().virtualSize` で取得できます。この仮想ウィンドウサイズに OS 設定の拡大縮小倍率 (150%, 200% など) を乗算したものが、後述する実ウィンドウサイズになります。

※ Linux 版では OS 設定の拡大縮小倍率に未対応で、仮想ウィンドウサイズと実ウィンドウサイズは同じになります（OpenSiv3D v0.6.10 現在）

#### ③ 実ウィンドウサイズ（フレームバッファサイズ）
モニタ上のピクセル数で計測した、ウィンドウのクライアント領域のサイズです。`Window::GetState().frameBufferSize` で取得できます。


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
シーンのサイズと実ウィンドウサイズが異なる場合、シーン画像はアスペクト比を保ったまま、クライアント領域にフィットするよう、適切に拡大または縮小されて表示されます。

次のように Siv3D が OS 設定の拡大縮小倍率の差を吸収することで、プログラマは 800x600 のシーンにグラフィックスを描くことだけに専念できます。

| OS 設定の拡大縮小倍率 | シーンのサイズ | 仮想ウィンドウサイズ | 実ウィンドウサイズ |
|--|--|--|--|
| 100% | 800x600 | 800x600 | 800x600 |
| 125% | 800x600 | 800x600 | 1000x750 |
| 150% | 800x600 | 800x600 | 1200x900 |
| 200% | 800x600 | 800x600 | 1600x1200 |


## 15.2 シーンのリサイズモード
ユーザがウィンドウをマウスでリサイズしたり、ウィンドウをリサイズする関数を呼んだりすると、仮想ウィンドウサイズと実ウィンドウサイズの 2 つが変化します。そして、これらのウィンドウサイズに応じてシーンのサイズも更新されます。どのようにシーンのサイズを更新するかを決めるのが、シーンの**リサイズモード**です。リサイズモードは次の 3 種類があります。デフォルトでは、仮想ウィンドウサイズと一致するようにシーンのサイズを更新するモード `ResizeMode::Virtual` になっています。

|リサイズモード|説明|
|--|--|
|`ResizeMode::Virtual` | 仮想ウィンドウサイズと一致するようにシーンのサイズを更新します（デフォルト） |
|`ResizeMode::Actual` | 実ウィンドウサイズと一致するようにシーンのサイズを更新します |
|`ResizeMode::Keep` | シーンのサイズを更新しません |

リサイズモードは `Scene::SetResizeMode(ResizeMode)` で設定します。現在のリサイズモードは `Scene::GetResizeMode()` で取得できます。


## 15.3 ウィンドウのリサイズ
`Window::Resize(int 幅, int 高さ)` または `Window::Resize(Size サイズ)` で、仮想ウィンドウサイズを変更できます。これにともない実ウィンドウサイズも変更され、リサイズモードに応じてシーンのサイズも更新されます。

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

リサイズモード `ResizeMode::Keep` が適用されている場合、`Window::Resize()` を行ってもシーンのサイズは変化しません。シーンのサイズと実ウィンドウサイズのアスペクト比が異なる場合、クライアント領域の左右もしくは上下に**レターボックス**と呼ばれる余白領域が生じます。

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
シーンのサイズと実ウィンドウサイズのアスペクト比が異なる際に、クライアント領域の左右もしくは上下に生じる余白領域をレターボックスと言います。レターボックスの色を変更するには `Scene::SetLetterbox()` で色を指定します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// レターボックスの色を変更
	Scene::SetLetterbox(ColorF{ 0.8, 0.9, 1.0 });

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


## 15.6 シーンのサイズだけを変更する
リサイズモードを `ResizeMode::Keep` にした状態で、`Scene::Resize()` を使うと、ウィンドウサイズはそのままでシーンのサイズだけを変更できます。

シーンと実ウィンドウサイズが異なるときは、`Cursor::Pos()` の代わりに `Cursor::PosF()` を使うと、シーンの拡大縮小に合わせた `Vec2` 型のマウスカーソル座標を取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);

	// シーンを 1600x1200 にリサイズ
	Scene::Resize(1600, 1200);

	while (System::Update())
	{
		ClearPrint();
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"scene size: " << Scene::Size();

		// マウスカーソルの座標を Vec2 型で取得
		Print << Cursor::PosF();

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


## 15.7 シーンが実ウィンドウに転送される際のフィルタを変更する
シーンのサイズと実ウィンドウサイズが異なる場合、シーン画像はアスペクト比を保ったまま、クライアント領域にフィットするよう、適切に拡大または縮小されて表示されますが、拡大縮小時に用いるテクスチャフィルタは 2 種類存在し、`Scene::SetTextureFilter()` で変更できます。

低解像度のシーンを、最近傍法による補間 `TextureFilter::Nearest` フィルタで拡大すると、なめらかにフィルタリングされずにドット感を保ったまま拡大できます。デフォルトでは、バイリニア補間 `TextureFilter::Linear` が使用されます。

| テクスチャフィルタ | 説明 |
|--|--|
|`TextureFilter::Linear`| 画像をバイリニア補間します（デフォルト） |
|`TextureFilter::Nearest`| 画像を最近傍法で補間します |

![](/images/doc_v6/tutorial/15/7.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);
	Scene::Resize(200, 150);

	// 拡大縮小時に最近傍法で補間
	Scene::SetTextureFilter(TextureFilter::Nearest);

	const Texture texture{ U"🐈"_emoji };

	while (System::Update())
	{
		Circle{ 120, 75, 50 }.draw();

		texture.draw();
	}
}
```


## 15.8 ウィンドウの枠を消す
ウィンドウの枠を非表示にするには、`Window::SetStyle()` で `WindowStyle::Frameless` を設定します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウの枠を非表示にする
	Window::SetStyle(WindowStyle::Frameless);

	while (System::Update())
	{
		Circle{ Cursor::Pos(), 100 }.draw();
	}
}
```


## 15.9 ウィンドウのタイトルを変更する
ウィンドウのタイトルを変更するには、`Window::SetTitle()` に文字列や値を渡します。デバッグビルド時には、タイトルのほかに、"(Debug Build)" という文字列や、フレームレート、ウィンドウのサイズ、シーンのサイズ等が合わせて表示されます。

:::message
ウィンドウタイトルの変更は時間のかかる処理なので、毎フレーム `Window::SetTitle()` に異なる文字列を設定することは避けてください。既に設定されているタイトルと同じタイトルを設定した場合には何もしないので、コストは発生しません。
:::

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウのタイトルを変更する
	Window::SetTitle(U"My Game");

	while (System::Update())
	{

	}
}
```


## 15.10 ウィンドウをスクリーンの中心に移動する
`Window::Centering()` を使うと、ウィンドウを、現在ウィンドウがあるモニタのワークエリアの中心に移動できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Center", Vec2{ 20, 20 }))
		{
			// ウィンドウを中心に移動
			Window::Centering();
		}
	}
}
```


## 15.11 ウィンドウを移動させる
`Window::SetPos()` を使うと、ウィンドウを指定した位置に移動できます。現在のウィンドウの位置は `Window::GetPos()` で取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// スクリーン上のウィンドウの位置を表示
		Print << Window::GetPos();

		if (SimpleGUI::Button(U"(0, 0)", Vec2{ 200, 20 }))
		{
			// ウィンドウをスクリーンの (0, 0) に移動
			Window::SetPos(0, 0);
		}

		if (SimpleGUI::Button(U"(200, 200)", Vec2{ 300, 20 }))
		{
			// ウィンドウをスクリーンの (200, 200) に移動
			Window::SetPos(200, 200);
		}
	}
}
```


## 15.12 ウィンドウを最小化 / 最大化する
プログラムによってウィンドウを最小化するには `Window::Minimize()` を、最大化するには `Window::Maximize()` を呼びます。ウィンドウを最大化する場合、ウィンドウスタイルが `WindowStyle::Sizable` である必要があります。最小化 / 最大化したウィンドウを以前のサイズに戻すときは `Window::Restore()` を呼びます。

ウィンドウが最小化されているかは `Window::GetState().minimized` で、最大化されているかは `Window::GetState().maximized` で取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウの最大化を可能にする
	Window::SetStyle(WindowStyle::Sizable);

	// 最小化中にカウントする変数
	int32 count = 0;

	while (System::Update())
	{
		ClearPrint();
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"scene size: " << Scene::Size();
		Print << U"minimized: " << count;
		Print << U"maximized: " << Window::GetState().maximized;

		if (Window::GetState().minimized)
		{
			++count;
		}

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

		if (SimpleGUI::Button(U"Minimize", Vec2{ 300, 20 }))
		{
			// ウィンドウを最小化
			Window::Minimize();
		}

		if (SimpleGUI::Button(U"Maximize", Vec2{ 300, 60 }))
		{
			// ウィンドウを最大化
			Window::Maximize();
		}

		if (SimpleGUI::Button(U"Restore", Vec2{ 300, 100 }))
		{
			// 最小化 / 最大化されたウィンドウを元のサイズに戻す
			Window::Restore();
		}
	}
}
```


## 15.13 モニタの情報を得る
接続されているモニタの情報の一覧を取得するには `System::EnumerateMonitors()` を使います。結果は `Array<MonitorInfo>` 型で得られます。

`MonitorInfo` 型のメンバ変数は次の通りです。

| 変数 | 説明 |
|--|--|
| `String name` | ディスプレイの名前 |
| `String id` | ディスプレイ ID |
| `String displayDeviceName` | 内部的に使われているディスプレイの名前 |
| `Rect displayRect` | ディスプレイ全体の位置とサイズ |
| `Rect workArea` | タスクバーなどを除いた利用可能な領域の位置とサイズ |
| `Size fullscreenResolution` | フルスクリーン時の解像度 |
| `bool isPrimary` | メインディスプレイである場合 `true`, それ以外の場合は `false` |
| `Optional<Size> sizeMillimeter` | 物理的なサイズ (mm), 取得できなかった場合は `none` |
| `Optional<double> scaling` | UI の拡大倍率。取得できなかった場合は `none` |
| `Optional<double> refreshRate` | リフレッシュレート。取得できなかった場合は `none` |

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 接続されているモニタの情報一覧を取得
	const Array<MonitorInfo> monitors = System::EnumerateMonitors();

	for (const auto& monitor : monitors)
	{
		Print << U"name: " << monitor.name;
		Print << U"displayRect: " << monitor.displayRect << U" workArea: " << monitor.workArea;
		Print << U"fullscreenResolution: " << monitor.fullscreenResolution << U" sizeMillimeter: " << monitor.sizeMillimeter;
		Print << U"scaling: " << monitor.scaling << U" refreshRate: " << monitor.refreshRate;
		Print << U"isPrimary: " << monitor.isPrimary;
		Print << U"-----";
	}

	while (System::Update())
	{

	}
}
```

現在のプログラムのウィンドウが存在する**モニタのインデックス**を取得するには `System::GetCurrentMonitorIndex()` を使います。このインデックスは `System::EnumerateMonitors()` の戻り値の配列に対応します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << System::GetCurrentMonitorIndex();
	}
}
```

## 15.14 フルスクリーンモードにする
アプリケーションを**フルスクリーンモード**にするには `Window::SetFullscreen(true)` を呼びます。ウィンドウモードに戻すには `Window::SetFullscreen(false)` を呼びます。サンプルコードでは扱っていませんが、`Window::SetFullscreen(true)` の第 2 引数には、フルスクリーンで表示する先のモニタのインデックスを指定できます。

アプリケーションがフルスクリーンモードであるかは `Window::GetState().fullscreen` で取得できます。

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
		Print << U"fullscreen: " << Window::GetState().fullscreen;

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

		if (Window::GetState().fullscreen)
		{
			if (SimpleGUI::Button(U"Window mode", Vec2{ 300, 20 }))
			{
				// ウィンドウモードにする
				Window::SetFullscreen(false);
			}
		}
		else
		{
			if (SimpleGUI::Button(U"Fullscreen mode", Vec2{ 300, 20 }))
			{
				// フルスクリーンモードにする
				Window::SetFullscreen(true);
			}
		}
	}
}

```


## 15.15 (Windows 版) 全画面モードにする
Windows 版では、アプリケーションの実行中に Alt + Enter キーを押すことで**全画面モード**にできます。挙動としてはほぼフルスクリーンモードですが、シーンのリサイズモードが `Resize::Keep` に設定され、シーンのサイズが変化しません。フルスクリーンモードや解像度の変更に対応していないアプリケーションを、全画面で大きく表示して実行したいときに役立ちます。

この挙動は `Window::SetToggleFullscreenEnabled(false)` で無効化できます。

