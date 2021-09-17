---
title: "チュートリアル 37 | 3D 形状を描く（発展）"
free: true
---

## 37.1 円柱座標系
`Cylindrical{ r, phi, y }` を使うと、円柱座標系から `Vec3` を得ることができます。

![](/images/doc_v6/tutorial/36/23.png)
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

			Plane{ 64 }.draw(uvChecker);

			for (auto i : step(60))
			{
				const Vec3 pos = Cylindrical{ 4.0, (i * 20_deg), (i * 0.2) };
				Box{ pos, 0.5 }.draw(HSV{ i * 10 });
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


## 37.2 球面座標系
`Spherical{ r, theta, phi }` を使うと、球面座標系から `Vec3` を得ることができます。

![](/images/doc_v6/tutorial/36/24.png)
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

			Plane{ 64 }.draw(uvChecker);

			for (auto i : step(60))
			{
				const Vec3 pos = Spherical{ 8.0, (i * 1.5_deg), (i * 20_deg) };
				Box{ pos, 0.5 }.draw(HSV{ i * 10 });
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


## 37.3 ビルボードを描く
常にカメラのほうを向くビルボードを描画するには、

```cpp

```


## 37.4 動画を描く
`VideoTexture` の作成時に `SRGB` を指定することで、3D 空間でも動画をテクスチャとして扱うことができます。

```cpp

```


