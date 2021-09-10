---
title: "チュートリアル 21 | 2D レンダーステート"
free: true
---

# 21. 2D レンダーステート
この章では、2D 描画の設定をカスタマイズして、表現の幅を広げる方法を学びます。

## 21.1 加算ブレンドで描く
`ScopedRenderStates2D` オブジェクトのコンストラクタに `BlendState::Additive` を渡すと、そのオブジェクトのスコープが有効な間、図形や画像が加算ブレンドで描画されます。加算ブレンドでは、背景色に RGB 成分を加算するように描画されるので、重ねて描画した部分が明るくなります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<Vec2> points;

	for (int32 i = 0; i < 400; ++i)
	{
		points << RandomVec2(Scene::Rect());
	}

	bool enabled = true;

	while (System::Update())
	{
		if (enabled)
		{
			// 加算ブレンド有効
			const ScopedRenderStates2D blend{ BlendState::Additive };

			for (const auto& point : points)
			{
				Circle{ point, 20 }.draw(HSV{ (point.y * 100 + point.x * 100), 0.5 });
			}
		}
		else
		{
			// 通常のブレンドモード

			for (const auto& point : points)
			{
				Circle{ point, 20 }.draw(HSV{ (point.y * 100 + point.x * 100), 0.5 });
			}
		}

		SimpleGUI::CheckBox(enabled, U"AdditiveBlend", Vec2{ 20, 20 });
	}
}
```


## 21.2 描画色を加算する
画像や図形を描くときに、本来の色に RGBA 成分を加算して描画するには、`ScopedColorAdd2D` オブジェクトのコンストラクタに、加算したい値を設定します。そのオブジェクトのスコープが有効な間、描画の RGBA 値が加算されます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture textureWindmill{ U"example/windmill.png" };
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };
	ColorF color{ 0.0, 0.0, 0.0, 0.0 };

	while (System::Update())
	{
		{
			// 描画時に色を加算
			const ScopedColorAdd2D colorAdd{ color };

			textureWindmill.draw(40, 20);
			textureSiv3DKun.draw(400, 100);
		}

		SimpleGUI::Slider(U"R", color.r, Vec2{ 620, 20 }, 40);
		SimpleGUI::Slider(U"G", color.g, Vec2{ 620, 60 }, 40);
		SimpleGUI::Slider(U"B", color.b, Vec2{ 620, 100 }, 40);
	}
}
```


## 21.3 描画色を乗算する
画像や図形を描くときに、本来の色に RGBA 成分を乗算して描画するには、`ScopedColorMul2D` オブジェクトのコンストラクタに、乗算したい値を設定します。そのオブジェクトのスコープが有効な間、描画の RGBA 値が乗算されます。

`Texture` の `.draw()` に色を渡すことで乗算の色を設定することもできます（チュートリアル 8.11 参照）。`ScopedColorMul2D` はその設定を一括して行えるものです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture textureWindmill{ U"example/windmill.png" };
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };
	ColorF color{ 1.0, 1.0, 1.0, 1.0 };

	while (System::Update())
	{
		{
			// 描画時に色を乗算
			const ScopedColorMul2D colorMul{ color };

			textureWindmill.draw(40, 20);
			textureSiv3DKun.draw(400, 100);
		}

		SimpleGUI::Slider(U"R", color.r, Vec2{ 620, 20 }, 40);
		SimpleGUI::Slider(U"G", color.g, Vec2{ 620, 60 }, 40);
		SimpleGUI::Slider(U"B", color.b, Vec2{ 620, 100 }, 40);
		SimpleGUI::Slider(U"A", color.a, Vec2{ 620, 140 }, 40);
	}
}
```


## 21.4 テクスチャ拡大縮小時のフィルタを変える
テクスチャを拡大縮小して描画する際に、デフォルトではバイリニア補間によって色が補間されます。ドット感を保ったまま拡大したいときにはサンプラーステート `SamplerState::ClampNearest` を `ScopedRenderStates2D` に設定します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"🐈"_emoji };
	bool bilinear = true;
	double scale = 1.0;

	while (System::Update())
	{
		if (bilinear)
		{
			// バイリニア補間（デフォルト）
			texture.scaled(scale).drawAt(Scene::Center());
		}
		else
		{
			// 最近傍補間
			const ScopedRenderStates2D sampler{ SamplerState::ClampNearest };
			texture.scaled(scale).drawAt(Scene::Center());
		}

		SimpleGUI::Slider(scale, 0.1, 8.0, Vec2{ 20, 20 }, 200);
		SimpleGUI::CheckBox(bilinear, U"Bilinear", Vec2{ 20, 60 });
	}
}
```


## 21.5 ワイヤフレームモードで描画する
`ScopedRenderStates2D` オブジェクトのコンストラクタに `RasterizerState::WireframeCullNone` を渡すと、図形や画像を構成する三角形のワイヤフレームのみが描画されるようになります。


```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture textureWindmill{ U"example/windmill.png" };

	while (System::Update())
	{
		// ワイヤフレーム表示モードに
		const ScopedRenderStates2D rasterizer{ RasterizerState::WireframeCullNone };

		textureWindmill.draw(20, 20);

		Circle{ Scene::Center(), 100 }.draw();

		Shape2D::Star(100, Vec2{ 150, 400 }).draw(Palette::Yellow);
	}
}
```


## 21.6 テクスチャをくり返して描画する
`ScopedRenderStates2D` オブジェクトのコンストラクタにサンプラーステートを渡すことで、テクスチャ描画時に UV 座標が 0.0～1.0 の範囲を超えたときの処理の方法をカスタマイズできます。

`Texture::mapped()` によって、指定したサイズだけテクスチャをくり返しマッピングするような `TextureRegion` を作成できます。それをサンプラーステート `SamplerState::RepeatLinear` が適用されている状態で `.draw()` すると、テクスチャの内容がくり返しマッピングされて描画されます。

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture tree{ U"🌲"_emoji };

	while (System::Update())
	{
		// UV 座標が 0.0～1.0 の範囲を超えたとき、くり返しマッピング
		const ScopedRenderStates2D sampler{ SamplerState::RepeatLinear };

		// シーンのサイズぴったりにマッピングして描画
		tree.mapped(Scene::Size()).draw();
	}
}
```


## 21.7 シザー矩形を設定する
指定した長方形領域以外への描画を行わないようにしたい場合、シザー矩形が便利です。`Graphics2D::SetScissorRect()` で長方形領域を設定し、`.scissorEnable` を `true` にした `RasterizerState` を `ScopedRenderStates2D` で適用すると、シザー矩形が有効になります。

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture textureWindmill{ U"example/windmill.png" };
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };

	// シザー矩形を設定
	Graphics2D::SetScissorRect(Rect{ 100, 100, 300, 200 });

	while (System::Update())
	{
		RasterizerState rs = RasterizerState::Default2D;
		// シザー矩形を有効化
		rs.scissorEnable = true;
		const ScopedRenderStates2D rasterizer{ rs };

		textureWindmill.draw(40, 20);
		textureSiv3DKun.draw(200, 100);
	}
}
```


## 21.8 ビューポートを設定する
`ScopedViewport2D` オブジェクトを作成すると、シーン内に仮想のシーンを作り、新しい描画領域を定義できます。描画時にはビューポートの長方形の左上が (0, 0) の描画座標になり、長方形の範囲外にはみ出たものは描画されなくなります。

ビューポートは描画の座標にしか影響を及ぼしません。マウスカーソルの座標も同様に移動させたい場合には、後述する `Transformer2D` と組み合わせます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture cat{ U"🐈"_emoji };

	while (System::Update())
	{
		const ScopedViewport2D viewport{ 400, 300, 400, 300 };

		Circle{ 200, 150, 200 }.draw();

		cat.draw(0, 0);
	}
}
```


## 21.9 座標変換行列を適用する
`Transformer2D` は、描画やマウスカーソル座標に対して、回転・拡大縮小、座標移動などの座標変換を適用できる、非常に強力で便利な機能です。

座標変換行列を `Mat3x2` によって定義し、`Transformer2D` オブジェクトのコンストラクタに値を設定します。オブジェクトのスコープが有効な間、その行列による座標変換が描画やマウスカーソルに適用されます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture textureWindmill{ U"example/windmill.png", TextureDesc::Mipped };
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png", TextureDesc::Mipped };
	constexpr Circle circle{ 200, 400, 60 };

	size_t index = 0;

	while (System::Update())
	{
		// 何もしない行列
		Mat3x2 mat = Mat3x2::Identity();

		if (index == 0)
		{

		}
		else if (index == 1)
		{
			// シーンの中心を基準に 1.5 倍拡大
			mat = Mat3x2::Scale(1.5, Scene::Center());
		}
		else if (index == 2)
		{
			// (50, 50) 移動
			mat = Mat3x2::Translate(50, 50);
		}
		else if (index == 3)
		{
			// シーンの中心を回転の軸にして 30° 回転
			mat = Mat3x2::Rotate(30_deg, Scene::Center());
		}
		else if (index == 4)
		{
			// シーンの中心を回転の軸にして徐々に回転しながら拡大
			mat = Mat3x2::Rotate(Scene::Time() * 5_deg, Scene::Center())
				.scaled(0.5 + Scene::Time() * 0.03, Scene::Center());
		}

		{
			// 座標変換行列を適用
			const Transformer2D transformer{ mat, TransformCursor::Yes };

			textureWindmill.draw(0, 0);

			textureSiv3DKun.draw(360, 100);

			circle.draw(circle.mouseOver() ? Palette::Red : Palette::Yellow);
		}

		SimpleGUI::RadioButtons(index, { U"Identity", U"Scale", U"Translate", U"Rotate", U"Roatate * Scale" }, Vec2{ 600, 20 });
	}
}
```


## 21.10 マウスカーソルだけに座標変換行列を適用する
ビューポートを使ってミニウィンドウを作成した際など、描画の座標変換は不要でマウスカーソルの座標変換だけ行いたい場合があります。そのようなときは、`Transformer2D` の第 1 引数に `Mat3x2:Identity()` を、第 2 引数にマウスカーソル用の座標変換行列を設定します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		const ScopedViewport2D viewport{ 400, 300, 400, 300 };

		// マウスカーソル座標だけ移動させる
		const Transformer2D transformer{ Mat3x2::Identity(), Mat3x2::Translate(400, 300) };

		Circle{ 200, 150, 200 }.draw();
		Circle{ Cursor::PosF(), 40 }.draw(Palette::Orange);

		if (SimpleGUI::Button(U"Button", Vec2{ 20,20 }))
		{
			Print << U"Pushed";
		}
	}
}
```


## 21.11 座標変換行列を重ねて適用する
`Transformer2D` の効果が適用されているときに新しい `Transformer2D` を作成すると、座標変換の効果が乗算されます。次のプログラムでは、行列の乗算によって複雑な動きを実現しています。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double t = (Scene::Time() * -30_deg);

		const Transformer2D t0{ Mat3x2::Translate(Scene::Center()) };
		Circle{ 0, 0, 40 }.draw(Palette::Orange);
		Circle{ 0, 0, 160 }.drawFrame(2);

		const Transformer2D t1{ Mat3x2::Translate(160, 0).rotated(t) };
		Circle{ 0, 0, 20 }.draw(Palette::Skyblue);
		Circle{ 0, 0, 40 }.drawFrame(2);

		const Transformer2D t2{ Mat3x2::Translate(40, 0).rotated(t * 4) };
		Circle{ 0, 0, 10 }.draw(Palette::Yellow);
	}
}
```


## 21.12 2D カメラ
`Camera2D` を使うと、マウスやキーボードを使った直感的な操作で `Transformer2D` を作成、更新できるようになります。

`Camera2D::update()` では W/A/S/D キーで上下左右移動、↑/↓ キーで拡大縮小、マウス右クリックで自由移動、マウスホイールで拡大縮小の操作を行います。キー操作を無効にしたい場合は `Camera2D` コンストラクタに `CameraControl::Mouse` を渡します。カメラの詳細な挙動は `Camera2DParameters` によってカスタマイズできます。

`Camera2D` の主なメンバ関数は次の通りです。

| 関数 | 説明 |
|--|--|
|`.createTransformer()`| 現在のカメラの設定から `Transformer2D` を作成する |
|`.setTargetCenter(Vec2)`| カメラの中心座標の目標を設定する |
|`.setTargetScale(double)`| カメラのズームアップ倍率の目標を設定する |
|`.jumpTo(Vec2, double)`| カメラの中心座標およびズームアップ倍率を即座に変更する |
|`.update()` | カメラの操作や、目標値への移動を行う |
|`.draw(const ColorF&)`| マウスでのカメラ操作を補助する矢印 UI を表示する |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture cat{ U"🐈"_emoji };

	// 2D カメラ
	// 初期設定: 中心 (0, 0), ズームアップ倍率 1.0
	Camera2D camera{ Vec2{ 0, 0 }, 1.0 };
	//Camera2D camera{ Vec2{ 0, 0 }, 1.0, CameraControl::Mouse }; // マウス操作のみの場合

	while (System::Update())
	{
		// 2D カメラを更新
		camera.update();
		{
			// 2D カメラの設定から Transformer2D を作成
			const auto t = camera.createTransformer();

			for (int32 i = 0; i < 8; ++i)
			{
				Circle{ 0, 0, (50 + i * 50) }.drawFrame(2);
			}

			cat.drawAt(0, 0);

			Shape2D::Star(50, Vec2{ 200, 200 }).draw(Palette::Yellow);
		}

		if (SimpleGUI::Button(U"Jump to center", Vec2{ 20, 20 }))
		{
			// 中心とズームアップ倍率を即座に変更
			camera.jumpTo(Vec2{ 0, 0 }, 1.0);
		}

		if (SimpleGUI::Button(U"Move to center", Vec2{ 20, 60 }))
		{
			// 中心とズームアップ倍率の目標値をセットして、時間をかけて変更する
			camera.setTargetCenter(Vec2{ 0, 0 });
			camera.setTargetScale(1.0);
		}

		// 2D カメラ操作の UI を表示
		camera.draw(Palette::Orange);
	}
}
```


## 21.13 レンダーステートの仕組み
`ScopedRenderStates2D` は、コンストラクタの引数に `BlendState`, `SamplerState`, `RasterizerState` の 3 つを同時に設定できます。`BlendState`, `SamplerState`, `RasterizerState` は、この章で紹介した以外にも多くのパラメータを持ち、様々なカスタマイズが可能です。2D 描画におけるデフォルト値は `BlendState::Default2D`, `SamplerState::Default2D`, `RasterizerState::Default2D` となっています。

### Scoped～ のはたらき
`Scoped～` や `Transformer2D` は、ソースコード上では何も働いていないように見えますが、実際はコンストラクタの中で、コンストラクタに渡された設定を有効化し、自身が破棄されるとき（スコープが終了するとき）には、デストラクタの中で設定を元の状態に戻す処理を行います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		{
			// レンダーステートを変更（元の状態も保存）
			const ScopedRenderStates2D rasterizer{ RasterizerState::WireframeCullNone };

			Circle{ 200, 300, 150 }.draw();

		} // ここで rasterizer のデストラクタが呼び出され、レンダーステートを元の状態に戻す

		Circle{ 600, 300, 150 }.draw();
	}
}
```

