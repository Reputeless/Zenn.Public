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


## 36.4 球を描く
球を描くときは `Sphere` を作成し、その `.draw()` を呼びます。

![](/images/doc_v6/tutorial/36/4.png)
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


## 36.5 各辺が軸に沿った直方体を描く
直方体を描くときは `Box` を作成し、その `.draw()` を呼びます。`Box` は各辺が X, Y, Z 軸に沿った向きに作成されます。回転した直方体を作成したい場合は、のちの節で登場する `OrientedBox` を使います。

`Box::FromPoints(p0, p1)` を使うと、2 点を対角線とする `Box` を作成できます。こちらのほうが、中心・大きさ指定よりも直感的な場合もあるでしょう。

![](/images/doc_v6/tutorial/36/5.png)
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


## 36.6 円柱を描く
円柱を描くときは `Cylinder` を作成し、その `.draw()` を呼びます。

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


## 36.7 デバッグ用 3D カメラ
`DebugCamera3D` は 3D 空間におけるカメラの移動を補助してくれるクラスです。カメラの動きをプログラムで制御する場合はのちの節で登場する `BasicCamera3D` を使いますが、テスト中は 3D 空間を自在に移動できると便利です。

`DebugCamera3D` のコンストラクタには、3D シーンを描くレンダーテクスチャのサイズ、カメラの縦方向の視野角 `verticalFog`, カメラ（目）の初期位置 `eyePosition`, （オプションで）注目点 `focusPosition` などを指定できます。

![](/images/doc_v6/tutorial/36/7a.png)

`DebugCamera3D` の `.update(speed)` は、W, A, S, D, E, X, 矢印キー、シフト、コントロールキーの入力に基づいて、カメラの位置と注目点を更新します。次のようなプログラムで、カメラの位置、注目点を表示してみます。

![](/images/doc_v6/tutorial/36/7b.png)

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


## 36.8 各辺が軸に沿っていない直方体を描く

```cpp

```


## 36.9 3D の線分を描く

```cpp

```


## 36.10 円板を描く

```cpp

```


## 36.11 円錐を描く

```cpp

```


## 36.12 複雑な 3D 形状を描く

```cpp

```


## 36.13 座標変換行列を適用する

```cpp

```


## 36.14 カメラをプログラムで制御する

```cpp

```


## 36.15 環境光を変更する

```cpp

```


## 36.16 太陽の明るさを変更する

```cpp

```


## 36.17 太陽の方向を変更する

```cpp

```


## 36.18 透過を扱う

```cpp

```


## 36.19 半透明を扱う

```cpp

```


## 36.20 テクスチャを繰り返す

```cpp

```


## 36.21 円柱座標系

```cpp

```


## 36.22 球面座標系

```cpp

```


## 36.23 3D モデルを描く

```cpp

```


## 36.24 3D モデルを動かす

```cpp

```


## 36.25 ビルボードを描く

```cpp

```


## 36.26 動画を描く

```cpp

```
