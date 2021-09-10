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

```cpp

```


## 21.9 座標変換行列を適用する

```cpp

```


## 21.10 座標変換行列を重ねて適用する

```cpp

```


## 21.11 2D カメラ

```cpp

```


## 21.12 レンダーステートの仕組み
`ScopedRenderStates2D` は、コンストラクタの引数に `BlendState`, `SamplerState`, `RasterizerState` の 3 つを同時に設定できます。`BlendState`, `SamplerState`, `RasterizerState` は、この章で紹介した以外にも多くのパラメータを持ち、様々なカスタマイズが可能です。2D 描画におけるデフォルト値は `BlendState::Default2D`, `SamplerState::Default2D`, `RasterizerState::Default2D` となっています。

### Scoped～ のはたらき
`Scoped～` や `Transformer2D` は、ソースコード上では何も働いていないように見えます。しかし、実際はコンストラクタで、指定された設定を有効化し、自身が破棄されるとき（スコープが終了するとき）には、デストラクタで設定を元の状態に戻す処理を行います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		{
			// ワイヤフレーム表示
			const ScopedRenderStates2D rasterizer{ RasterizerState::WireframeCullNone };

			Circle{ 200, 300, 150 }.draw();

		} // ここで rasterizer のデストラクタが呼び出され、レンダーステートが初期状態に

		Circle{ 600, 300, 150 }.draw();
	}
}
```

