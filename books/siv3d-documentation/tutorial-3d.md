---
title: "チュートリアル 36 | 3D 形状を描く"
free: true
---

# 36. 3D 形状を描く
この章では、3D 空間に形状やモデルを描画する方法を学びます。

![](/images/doc_v6/tutorial/36/0.png)

これまでの 2D 描画は**ガンマ色空間** (sRGB 色空間) で行われてきました。画像ファイル中のデータは基本的にガンマ色空間で記録されており、ガンマ色空間で色を出力すれば、モニタ上でその色を正確に再現できます。しかし、ガンマ色空間における明るさは非線形で、色に 0.5 を乗算しても、正確に半分の明るさになるわけではないという欠点があります。このような非線形な性質は、陰影を扱う 3D 描画で正確なレンダリングを行う上で障壁となります。そのため、3D 描画は**リニア色空間**で行います。

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

![](/images/doc_v6/tutorial/36/2.png)
```cpp

```


## 36.3 

```cpp

```


## 36.4 

```cpp

```


## 36.5 

```cpp

```


## 36.6

```cpp

```

