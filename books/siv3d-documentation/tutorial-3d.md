---
title: "チュートリアル 36 | 3D 形状を描く"
free: true
---

# 36. 3D 形状を描く
この章では、3D 空間に形状やモデルを描画する方法を学びます。

![](/images/doc_v6/tutorial/36/0.png)

これまでの 2D 描画は**ガンマ色空間** (sRGB 色空間) で行われてきました。画像ファイル中のデータは基本的にガンマ色空間で記録されており、ガンマ色空間で色を出力すれば、モニタ上でその色を正確に再現できます。しかし、ガンマ色空間における明るさは非線形で、色に 0.5 を乗算して数字の上では半分になっても、物理的な輝度が正確に半分なるわけではないという欠点があります。ガンマ色空間のこのような非線形な性質は、陰影などを扱う 3D 描画で正確なレンダリングを行う上で障壁となります。そのため、3D 描画は**リニア色空間**で行います。

まず、リニア色空間で 3D 描画を行うためのレンダーテクスチャを作成します。リニア色空間ではとくに小さい値の精度が重要なため、テクスチャフォーマットとして `TextureFormat::R8G8B8A8_Unorm_SRGB` か、必要に応じて `TexturePixelFormat::R16G16B16A16_Float` などを指定します。

リニア色空間に色を書き込む際は、`ColorF` の `.removeSRGBCurve()` でガンマ色空間をリニア色空間に補正（sRGB カーブを除去）した色を使います。数字上は元の色より暗くなりますが、これはのちに 3D 描画の結果を 2D 描画に転送する際のリニア色空間→ガンマ色空間への補正 (sRGB カーブの付加) によって元の色に復元されます。

`Texture` に格納されている画像データはガンマ色空間なので、これもリニア色空間での作業には不適です。`Texture` のコンストラクタの `TextureDesc` で `SRGB` を指定すると、GPU はテクスチャの内容がガンマ色空間であるとみなし、シェーダでピクセルの値を読み取る際、ガンマ色空間をリニア色空間に補正（sRGB カーブを除去）した値を返します。

これで 3D レンダリングの計算処理をリニア色空間で行えるようになりました。レンダーテクスチャに 3D のシーンを描き終えたら、レンダーテクスチャをメインのシーンに転送します。このとき `Shader::LinearToScreen(renderTexture)` を使うと、転送と同時にリニア色空間からガンマ色空間への補正 (sRGB カーブの付加) が行われます。この転送後に、2D 描画を行うことで、3D シーンの上に UI などの 2D グラフィックスを表示できます。

- 色空間について、より詳しく知りたい場合の参考記事
  - https://tech.cygames.co.jp/archives/2339/
  - https://docs.unity3d.com/ja/2018.4/Manual/LinearRendering-LinearOrGammaWorkflow.html


## 36.1 3D 描画の基本
Siv3D における基本的な 3D 描画のコードは次の通りです。

3D シーンを描く `MSRenderTexture` を `TextureFormat::R8G8B8A8_Unorm_SRGB` フォーマットで用意します。合わせて、3D オブジェクトの前後関係による隠面消去をおこなうために `HasDepth::Yes` を指定し、深度バッファを持たせます。

このレンダーテクスチャをクリアするときに使う色 `backgroundColor` は、最終的にモニタに表示されてほしい色から sRGB カーブを除去した色を用意します。

3D 描画で使うテクスチャ `uvChecker` は、内容がガンマ色空間なので、`TextureDesc::MippedSRGB` を指定しておきます。

`DebugCamera3D` は 3D 空間におけるカメラの位置と視線方向を決めるために使います。詳しくはのちの節で説明します。メインループ中で `.update()` を呼ぶと、キーボード入力を使ってカメラの位置や向きを変更できます。

メインループでは、`Graphics3D::SetCameraTransform()` でカメラの設定を終えたら、`ScopedRenderTarget3D` に 3D 描画用のレンダーテクスチャを設定し、そのあと 3D 描画の処理を記述します。次のサンプルでは床、ボックス、球、円柱の 4 つの形状を描いています。3D 形状のクラスについてはのちの節で説明します。3D 描画が終わったら、`ScopedRenderTarget3D` のスコープを終わらせ、`renderTexture` をレンダーターゲットから解除させます。

`renderTexture` はマルチサンプル・レンダーテクスチャであるため、描画結果を利用する前にリゾルブが必要です（チュートリアル 34.2 参照）。`Graphics3D::Flush()` によってその時点までの 3D 描画処理をすべて実行（フラッシュ）して `MSRenderTexture` の内容を確定し、`.resolve()` `によって、MSRenderTexture` 内のマルチサンプル・テクスチャを、描画で利用可能なテクスチャに変換（リゾルブ）します。

最後に `Shader::LinearToScreen(renderTexture)` によって、3D 描画の結果が格納されたレンダーテクスチャの内容を、リニア色空間からガンマ色空間への補正 (sRGB カーブの付加) を行いつつ、シーンに転送します。

![](/images/doc_v6/tutorial/36/1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウインドウとシーンを 1280x720 にリサイズ
	Window::Resize(1280, 720);

	// 背景色 (リニアレンダリング用なので removeSRGBCurve() で sRGB カーブを除去）
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

	// UV チェック用テクスチャ (ミップマップ使用。リニアレンダリング時に正しく扱われるよう、sRGB テクスチャであると明示）
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };

	// 3D シーンを描く、マルチサンプリング対応レンダーテクスチャ
	// リニア色空間のレンダリング用に TextureFormat::R8G8B8A8_Unorm_SRGB
	// 奥行きの比較のための深度バッファも使うので HasDepth::Yes
	// マルチサンプル・レンダーテクスチャなので、描画内容を使う前に resolve() が必要
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	// 3D シーンのデバッグ用カメラ
	// 縦方向の視野角 30°, カメラの位置 (10, 16, -32)
	// 前後移動: [W][S], 左右移動: [A][D], 上下移動: [E][X], 注視点移動: アローキー, 加速: [Shift][Ctrl]
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	while (System::Update())
	{
		// デバッグカメラの更新 (カメラの移動スピード: 2.0)
		camera.update(2.0);

		// 3D シーンにカメラを設定
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			// renderTexture を背景色で塗りつぶし、
			// renderTexture を 3D 描画のレンダーターゲットに
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			// 床を描画
			Plane{ 64 }.draw(uvChecker);

			// ボックスを描画
			Box{ -8,2,0,4 }.draw(ColorF{ 0.8, 0.6, 0.4 }.removeSRGBCurve());

			// 球を描画
			Sphere{ 0,2,0,2 }.draw(ColorF{ 0.4, 0.8, 0.6 }.removeSRGBCurve());

			// 円柱を描画
			Cylinder{ 8, 2, 0, 2, 4 }.draw(ColorF{ 0.6, 0.4, 0.8 }.removeSRGBCurve());
		}

		// 3D シーンを 2D シーンに描画
		{
			// renderTexture を resolve する前に 3D 描画を実行する
			Graphics3D::Flush();

			// マルチサンプル・テクスチャのリゾルブ
			renderTexture.resolve();

			// リニアレンダリングされた renderTexture をシーンに転送
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 36.2 3D 空間の座標系
Siv3D では上方向が +Y, 右方向が +X, 奥行き方向が +Z になる次のような 3D 空間の座標系を採用しています。

![](/images/doc_v6/tutorial/36/2.png)

3D 座標の表現には `Vec3` 型を使います。`Vec3` は `double` 型の要素 `x`, `y`, `z` をメンバ変数として持ちます。


## 36.3 床を描く
3D シーンに図形を描く方法を学びましょう。Siv3D では、3D 形状オブジェクトを作成し、その `.draw()` メンバ関数を呼んで描画を行います。床を描くときは `Plane` を作成し、その `.draw()` を呼びます。

`.draw()` の引数には、色かテクスチャを指定できます。指定しなかった場合白色で描かれます。`Linear::Palette::` 名前空間のパレットは、通常の `Plaette::` をリニア色空間に補正した色定数なので、`.removeSRGBCurve()` を省略できます。半透明を扱う方法はのちの節で説明します。

画像ファイル `"example/texture/uv.png"` は各辺小さいマス目が 64 個あるため、一辺の大きさが 64 の床に貼ることで、1 マスが 3D 空間での座標 1 に相当し、3D 空間での物の配置を確認するデバッグ用途に便利に使えます。

![](/images/doc_v6/tutorial/36/3.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	while (System::Update())
	{
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			// 中心 (0,0,0) 一辺の幅が 64 の平面にテクスチャ uvChecker を貼って描画
			Plane{ 64 }.draw(uvChecker);

			// 中心　(-8,2,0), 一辺の幅が 8 の床を黄色 (sRGB カーブ除去済み）で描画
			Plane{ Vec3{ -8, 2, 0 }, 8 }.draw(Linear::Palette::Yellow);

			Plane{ Vec3{ -8, 4, 0 }, 6 }.draw(Linear::Palette::Orange);

			Plane{ Vec3{ -8, 6, 0 }, 4 }.draw(Linear::Palette::Red);

			Plane{ Vec3{ 8, 2, 8 }, 16, 8 }.draw(ColorF{ 0.2, 0.3, 0.4 }.removeSRGBCurve());

			Plane{ Vec3{ 8, 2, -8 }, 16 }.draw(uvChecker);
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 36.4 Z-fighting
異なる物体を、同じ面領域を共有または非常に接近するように配置すると、Z-fighting によりノイズ模様が生じることがあります。領域面が重なるように配置するのを避けるか、どうしても必要な場合は、あとから描く面をわずかに浮かして 3D 物体を配置します。

![](/images/doc_v6/tutorial/36/4.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	while (System::Update())
	{
		const double t = Scene::Time();

		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			Plane{ 64 }.draw(uvChecker);

			Plane{ Vec3{ -16, 0, 0}, 12 }.draw(Linear::Palette::Red);

			// ごくわずかに浮かす
			Plane{ Vec3{ 0, 0.0001, 0}, 12 }.draw(Linear::Palette::Red);

			// 浮かす
			Plane{ Vec3{ 16, 0.01, 0}, 12 }.draw(Linear::Palette::Red);
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```

（補足）: 次のように、ラスタライザーステートで深度バイアスを付加したり、カメラの `nearClip` を大きくしたり（デフォルトは `0.2`) する緩和法もあります。
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const Texture woodTexture{ U"example/texture/wood.jpg", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	constexpr double nearClip = 2.0;
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 }, Vec3{0,0,0}, Vec3{0,1,0}, nearClip };

	while (System::Update())
	{
		const double t = Scene::Time();

		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			Plane{ 64 }.draw(uvChecker);

			{
				RasterizerState r = RasterizerState::Default3D;
				r.depthBias = 100;
				const ScopedRenderStates3D rasterizer{ r };

				Plane{ Vec3{ -16, 0, 0}, 12 }.draw(Linear::Palette::Red);
			}

			Plane{ Vec3{ 0, 0.0001, 0}, 12 }.draw(Linear::Palette::Red);

			Plane{ Vec3{ 16, 0.01, 0}, 12 }.draw(Linear::Palette::Red);
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 36.5 球を描く
球を描くときは `Sphere` を作成し、その `.draw()` を呼びます。

![](/images/doc_v6/tutorial/36/5.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const Texture earthTexture{ U"example/texture/earth.jpg", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	while (System::Update())
	{
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			Plane{ 64 }.draw(uvChecker);

			// 中心 (0,2,0) 半径が 2 の球にテクスチャ earthTexture を貼って描画
			Sphere{ 0, 2, 0, 2 }.draw(earthTexture);

			for (auto i : Range(-4, 4))
			{
				Sphere{ (i * 4), 1, 8, 1 }.draw(HSV{ i * 20 }.removeSRGBCurve());
			}

			for (auto i : Range(-4, 4))
			{
				Sphere{ (i * 4), 1, -8, 0.5 }.draw(ColorF{ 0.5 + i * 0.125 }.removeSRGBCurve());
			}
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 36.6 各辺が軸に沿った直方体を描く
直方体を描くときは `Box` を作成し、その `.draw()` を呼びます。`Box` は各辺が X, Y, Z 軸に沿った向きに作成されます。回転した直方体を作成したい場合は、のちの節で登場する `OrientedBox` を使います。

`Box::FromPoints(p0, p1)` を使うと、2 点を対角線とする `Box` を作成できます。こちらのほうが、中心・大きさ指定よりも直感的な場合もあるでしょう。

![](/images/doc_v6/tutorial/36/6.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const Texture woodTexture{ U"example/texture/wood.jpg", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	while (System::Update())
	{
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			Plane{ 64 }.draw(uvChecker);

			// 中心 (0,2,0) 各辺の長さが 4 のボックスにテクスチャ woodTexture を貼って描画
			Box{ Vec3{0, 2, 0}, 4 }.draw(woodTexture);

			for (auto i : Range(-4, 4))
			{
				Box{ (i * 4), 2, 8, 1, 4, 1 }.draw(HSV{ i * 20 }.removeSRGBCurve());
			}

			// 2 点を指定して直方体を作成
			Box::FromPoints(Vec3{ -8, 2, -8 }, Vec3{ 0, 0, -12 }).draw();
			Box::FromPoints(Vec3{ 0, 2, -8 }, Vec3{ 4, 0, -16 }).draw();
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 36.7 円柱を描く
円柱を描くときは `Cylinder` を作成し、その `.draw()` を呼びます。

![](/images/doc_v6/tutorial/36/7.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const Texture woodTexture{ U"example/texture/wood.jpg", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	while (System::Update())
	{
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			Plane{ 64 }.draw(uvChecker);

			// 中心 (0,2,0) 半径 2, 高さが 4 の円柱にテクスチャ woodTexture を貼って描画
			Cylinder{ Vec3{0, 2, 0}, 2, 4 }.draw(woodTexture);

			for (auto i : Range(-4, 4))
			{
				// 2 点を指定して円柱を作成
				Cylinder{ Vec3{ (i * 4), (8 + i), 8}, Vec3{(i * 4), 0, 8}, 1 }
					.draw(HSV{ i * 20 }.removeSRGBCurve());
			}

			// 2 点を指定して円柱を作成
			Cylinder{ Vec3{-8, 0.5, -8}, Vec3{8, 4, -4}, 0.5 }.draw();
			Cylinder{ Vec3{ 8, 0.25, -8 }, Vec3{8, 0, -8}, 4 }.draw(Linear::Palette::Gray);
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 36.8 デバッグ用 3D カメラ
`DebugCamera3D` は 3D 空間におけるカメラの移動を補助してくれるクラスです。カメラの動きをプログラムで制御する場合はのちの節で登場する `BasicCamera3D` を使いますが、開発中は 3D 空間を自在に移動できると便利です。

`DebugCamera3D` のコンストラクタには、3D シーンを描くレンダーテクスチャのサイズ、カメラの縦方向の視野角 `verticalFog`, カメラ（目）の位置 `eyePosition` の初期設定、（オプションで）注目点 `focusPosition` の初期設定などを指定できます。

このサンプルでは扱いませんが、カメラの上方向を示す `upVector` や、この距離よりカメラに近い物体を表示しない `nearClip` などのパラメータもあります。

![](/images/doc_v6/tutorial/36/8a.png)

`DebugCamera3D` の `.update(speed)` は、W, A, S, D, E, X, 矢印キー、シフト、コントロールキーの入力とカメラの移動の速さ `speed` に基づいて、カメラの位置と注目点を更新します。次のようなプログラムで、カメラの位置、注目点を表示してみます。

![](/images/doc_v6/tutorial/36/8b.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	// 3D シーンのデバッグ用カメラ
	// 縦方向の視野角 30°, カメラの位置 (10, 16, -32)
	// 前後移動: [W][S], 左右移動: [A][D], 上下移動: [E][X], 注視点移動: アローキー, 加速: [Shift][Ctrl]
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	while (System::Update())
	{
		ClearPrint();

		// デバッグカメラの更新 (カメラの移動スピード: 2.0)
		camera.update(2.0);

		// カメラの状態を表示
		Print << U"eyePositon: {:.1f}"_fmt(camera.getEyePosition());
		Print << U"focusPosition: {:.1f}"_fmt(camera.getFocusPosition());
		Print << U"verticalFOV: {:.1f}°"_fmt(Math::ToDegrees(camera.getVerticlaFOV()));

		// 3D シーンにカメラを設定
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			Plane{ 64 }.draw(uvChecker);
			Box{ -8,2,0,4 }.draw(ColorF{ 0.8, 0.6, 0.4 }.removeSRGBCurve());
			Sphere{ 0,2,0,2 }.draw(ColorF{ 0.4, 0.8, 0.6 }.removeSRGBCurve());
			Cylinder{ 8, 2, 0, 2, 4 }.draw(ColorF{ 0.6, 0.4, 0.8 }.removeSRGBCurve());
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 36.9 回転付きの直方体を描く
3D 空間における回転は `Quaternion` 型を使って表現します。`Quaternion::RotateX(angle)` は X 軸に沿った angle の回転を表現する `Quaternion` を作成します。`Quaternion` は `*` 演算子によって合成することができます。

回転情報付きの直方体は `OrientedBox` を使って表現します。これは `Box` に `Quaternion` が追加されたものです。

![](/images/doc_v6/tutorial/36/9.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const Texture woodTexture{ U"example/texture/wood.jpg", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	while (System::Update())
	{
		const double t = Scene::Time();

		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			Plane{ 64 }.draw(uvChecker);

			// 中心 (0,2,0) 各辺の長さが 4, Y 軸に沿って回転した
			// ボックスにテクスチャ woodTexture を貼って描画
			OrientedBox{ Vec3{0, 2, 0}, 4, Quaternion::RotateY(t * 30_deg) }.draw(woodTexture);

			for (auto i : Range(-4, 4))
			{
				// 回転の乗算で複数の回転を合成できる
				const Quaternion orientation = (Quaternion::RotateZ(i * 20_deg) * Quaternion::RotateX(t * 30_deg));

				OrientedBox{ Vec3{ (i * 4), 2, 8}, 1, 4, 1, orientation }
					.draw(HSV{ i * 20 }.removeSRGBCurve());
			}

			OrientedBox{ Vec3{ 0, 4, -8 }, 1, 8, 1, Quaternion::RotateZ(t * 60_deg) }.draw();
			OrientedBox{ Vec3{ 0, 4, -8 }, 8, 1, 1, Quaternion::RotateZ(t * 60_deg) }.draw();
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 36.10 3D の線分を描く
3D の線分を描く場合は、`Line3D` を作成して `.draw()` します。太さは指定できず、つねに 1 です。

![](/images/doc_v6/tutorial/36/10.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	while (System::Update())
	{
		const double t = Scene::Time();

		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			Plane{ 64 }.draw(uvChecker);

			Line3D{ Vec3{0, 0, 0}, Vec3{0, 8, 0} }.draw(ColorF{ 0.25 }.removeSRGBCurve());

			for (auto z : Range(-4, 4))
			{
				Line3D{ Vec3{-8, 4, (z * 4)}, Vec3{8, 4, (z * 4)} }.draw(Linear::Palette::Red);
			}

			Line3D{ Vec3{8, 4, 16}, Vec3{-8, 4, -16} }.draw();
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 36.11 円板を描く
円板を描くには `Disc` を作成して `.draw()` します。

![](/images/doc_v6/tutorial/36/11.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const Texture woodTexture{ U"example/texture/wood.jpg", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	while (System::Update())
	{
		const double t = Scene::Time();

		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			Plane{ 64 }.draw(uvChecker);

			// 中心 (0,0.01,0), 半径 4 の円板を描く
			Disc{ Vec3{0, 0.01, 0}, 4 }.draw(woodTexture);

			for (auto i : Range(-4, 4))
			{
				Disc{ Vec3{ (i * 4), (4 + i + 0.01), 8}, 2 }.draw(HSV{ i * 20 }.removeSRGBCurve());
			}
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 36.12 円錐を描く
円錐を描くには `Cone` を作成して `.draw()` します。

![](/images/doc_v6/tutorial/36/12.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const Texture woodTexture{ U"example/texture/wood.jpg", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	while (System::Update())
	{
		const double t = Scene::Time();

		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			Plane{ 64 }.draw(uvChecker);

			// 底面の中心 (0,0.0,0), 半径 4, 高さ 4 の円錐を描く
			Cone{ Vec3{0, 0.0, 0}, 4, 4 }.draw(woodTexture);

			for (auto i : Range(-4, 4))
			{
				Cone{ Vec3{ (i * 4), 0.0, 8}, 2, (6.0 + i) }.draw(HSV{ i * 20 }.removeSRGBCurve());
			}

			// from と to を結ぶ 3D の矢印を描く
			const Vec3 from{ -8, 2, -6 }, to{ 4, 6, -4 };
			const Vec3 v = (to - from).normalize();
			const Vec3 headPos = (from + v * (to.distanceFrom(from) - 2.0));
			Cylinder{ from, headPos, 0.2 }.draw(Linear::Palette::Lightgreen);
			Cone{ headPos, to, 0.5 }.draw(Linear::Palette::Lightgreen);
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 36.13 複雑な 3D 形状を描く
より複雑な形状を描くには、`Mesh` を作成して `.draw()` します。`Mesh` は 2D 描画における `Polygon` のように、様々な方法で作成できます。

次のような `MeshData::` の関数を使うと、よく使われる形状の `Mesh` を作成できます（`Polygon` と `Shape2D::` の関係に似ています）。一部の形状は UV 座標がすべて (0, 0) なので、テクスチャをマッピングしても一色になります。そのような形状は、のちの章で説明する Tri-Planar シェーダを使うと、自然にテクスチャをマッピングできます。

| 関数 | 形状 | UV 座標 |
|--|--|:--:|
|Billboard| ビルボード（のちの節）用の有限平面 |✔|
|OneSidedPlane| 片面の有限平面 |✔|
|TwoSidedPlane| 両面の有限平面 |✔|
|Box| 直方体 |✔|
|Sphere| 球 |✔|
|SubdividedSphere| 頂点数に対する品質が良い球 |  |
|Disc| 円板 |✔|
|Cylinder| 円柱 |✔|
|Cone| 円錐 |✔|
|Pyramid| 四角錐 |✔|
|Torus| トーラス |✔|
|Hemisphere| 半球 |✔|
|Tetrahedron| 正四面体 |  |
|Octahedron| 正八面体 |  |
|Dodecahedron| 正十二面体 |  |
|Icosahedron| 正二十面体 |  |
|Grid| グリッド状の有限平面 |✔|

このサンプルでは扱いませんが、自身で用意した頂点およびインデックス配列を `MeshData` に格納し、そこから `Mesh` を作成することができます。

![](/images/doc_v6/tutorial/36/13a.png)

![](/images/doc_v6/tutorial/36/13b.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	const Mesh oneSidedPlane{ MeshData::OneSidedPlane(Vec2{ 6,2 }) };
	const Mesh twoSidedPlane{ MeshData::TwoSidedPlane(Vec2{ 4,3 }) };
	const Mesh box{ MeshData::Box(Vec3{ 6, 2, 2 }) };
	const Mesh sphere8{ MeshData::Sphere(3, 8u) };
	const Mesh sphere24{ MeshData::Sphere(3, 24u) };

	const Mesh subdivSphere2{ MeshData::SubdividedSphere(3, 2u) };
	const Mesh subdivSphere3{ MeshData::SubdividedSphere(3, 3u) };
	const Mesh subdivSphere4{ MeshData::SubdividedSphere(3, 4u) };
	const Mesh disc8{ MeshData::Disc(3, 8u) };
	const Mesh disc16{ MeshData::Disc(3, 16u) };

	const Mesh cylinder6{ MeshData::Cylinder(Vec3{0,0,0}, 3, 4, 6u) };
	const Mesh cylinder24{ MeshData::Cylinder(Vec3{0,0,0}, 3, 4, 24u) };
	const Mesh cone6{ MeshData::Cone(Vec3{0,0,0}, 3, 4, 6u) };
	const Mesh cone24{ MeshData::Cone(Vec3{0,0,0}, 3, 4, 24u) };
	const Mesh pyramid{ MeshData::Pyramid(3.0, 3.0) };

	const Mesh torus{ MeshData::Torus(3.0, 0.6) };
	const Mesh hemisphere{ MeshData::Hemisphere(3.0) };
	const Mesh tetrahedron{ MeshData::Tetrahedron(3.0) };
	const Mesh octahedron{ MeshData::Octahedron(3.0) };
	const Mesh dodecahedron{ MeshData::Dodecahedron(3.0) };

	const Mesh icosahedron{ MeshData::Icosahedron(3.0) };
	const Mesh grid{ MeshData::Grid(Vec2{4.0,2.0}, 10, 5, Vec2{2, 1}) };

	bool wireframe = false;

	while (System::Update())
	{
		const double t = Scene::Time();

		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			const ScopedRenderStates3D rastetrizer{ (wireframe ? RasterizerState::WireframeCullNone : RasterizerState::Default3D) };

			oneSidedPlane.draw(-16, 0, 16, uvChecker);
			twoSidedPlane.draw(-8, 0, 16, uvChecker);
			box.draw(0, 0, 16, uvChecker);
			sphere8.draw(8, 0, 16, uvChecker);
			sphere24.draw(16, 0, 16, uvChecker);

			subdivSphere2.draw(-16, 0, 8, uvChecker);
			subdivSphere3.draw(-8, 0, 8, uvChecker);
			subdivSphere4.draw(0, 0, 8, uvChecker);
			disc8.draw(8, 0, 8, uvChecker);
			disc16.draw(16, 0, 8, uvChecker);

			cylinder6.draw(-16, 0, 0, uvChecker);
			cylinder24.draw(-8, 0, 0, uvChecker);
			cone6.draw(0, 0, 0, uvChecker);
			cone24.draw(8, 0, 0, uvChecker);
			pyramid.draw(16, 0, 0, uvChecker);

			torus.draw(-16, 0, -8, uvChecker);
			hemisphere.draw(-8, 0, -8, uvChecker);
			tetrahedron.draw(0, 0, -8, uvChecker);
			octahedron.draw(8, 0, -8, uvChecker);
			dodecahedron.draw(16, 0, -8, uvChecker);

			icosahedron.draw(-16, 0, -16, uvChecker);
			grid.draw(-8, 0, -16, uvChecker);
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}

		SimpleGUI::CheckBox(wireframe, U"wireframe", Vec2{ 20,20 });
	}
}
```

## 36.14 メッシュを座標して描画する
`Mesh` の `.draw()` には回転 `Quaternion` や、3D の座標変換行列 `Mat4x4` 型を渡して、座標変換を適用して描画することができます。

```cpp

```


## 36.15 一括して座標変換行列を適用する
`Transformer3D` は、描画座標に対して、回転・拡大縮小、座標移動などの座標変換を適用できる機能です。`Transformer2D` の 3D 版です。

座標変換行列は `Mat4x4` 型によって定義し、`Transformer3D` オブジェクトのコンストラクタに値を設定します。オブジェクトのスコープが有効な間、その行列による座標変換が 3D 描画に適用されます。

`Transformer3D` の座標変換行列は `Transformer2D` のように重ねがけできます。個別の `.draw()` でも回転や座標変換行列を指定している場合、`Transformer3D` による座標変換のあとに、それらが適用されます。

```cpp

```


## 36.16 カメラをプログラムで制御する
`BasicCamera3D` は、プログラムだけで制御する 3D カメラです。

```cpp

```


## 36.17 環境光を変更する
**環境光**は、どの物体のどの面にも等しく照らされる光源です。現実において、太陽や照明の方向を直接向いていない面でも、床や壁からの反射によって真っ暗にはならず、それなりに明るく見える現象を大雑把に再現する光源です。

`Graphics3D::SetGlobalAmbientColor(color)` で設定します。デフォルトでは `ColorF{ 0.5 }` です。

```cpp

```


## 36.18 太陽の明るさを変更する

```cpp

```


## 36.19 太陽の方向を変更する

```cpp

```


## 36.20 透過を扱う

```cpp

```


## 36.21 半透明を扱う

```cpp

```


## 36.22 ワイヤフレームで描画する

```cpp

```


## 36.23 テクスチャを繰り返す

```cpp

```


## 36.24 円柱座標系

```cpp

```


## 36.25 球面座標系

```cpp

```


## 36.26 3D モデルを描く

```cpp

```


## 36.27 3D モデルを動かす

```cpp

```


## 36.28 ビルボードを描く

```cpp

```


## 36.29 動画を描く

```cpp

```
