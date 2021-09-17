---
title: "チュートリアル 37 | 3D 形状を描く（発展）"
free: true
---

# 37. 3D 形状を描く（発展）
この章では、3D 空間にモデルデータやを描画する方法や、3D 描画でカスタムシェーダを使う方法などを学びます。

## 37.1 3D モデルを描く
`Model` クラスを使うと、**Wavefront .obj 形式**の 3D モデルファイルからモデルデータを読み込み、描画することができます。.obj ファイルにマテリアルファイル .mtl ファイルが付属する場合は自動的に読み込まれます。

マテリアルで使用するテクスチャがある場合、`Model::RegisterDiffuseTextures()` を使うと、その画像ファイルをアセット管理に登録しますい。`Model` の `.draw()` で必要になった際、アセット管理を使って自動的にテクスチャがロード・使用されます。

透過を含むモデルは、適切なレンダーステートの設定が必要になります (36.20 参照)。

![](/images/doc_v6/tutorial/36/25.jpg)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

	const Mesh groundPlane{ MeshData::OneSidedPlane(2000, { 400, 400 }) };
	const Texture groundTexture{ U"example/texture/ground.jpg", TextureDesc::MippedSRGB };

	// モデルデータをロード
	const Model blacksmithModel{ U"example/obj/blacksmith.obj" };
	const Model millModel{ U"example/obj/mill.obj" };
	const Model treeModel{ U"example/obj/tree.obj" };
	const Model pineModel{ U"example/obj/pine.obj" };
	const Model siv3dkunModel{ U"example/obj/siv3d-kun.obj" };

	// モデルに付随するテクスチャをアセット管理に登録
	Model::RegisterDiffuseTextures(treeModel, TextureDesc::MippedSRGB);
	Model::RegisterDiffuseTextures(pineModel, TextureDesc::MippedSRGB);
	Model::RegisterDiffuseTextures(siv3dkunModel, TextureDesc::MippedSRGB);

	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ Graphics3D::GetRenderTargetSize(), 40_deg, Vec3{ 0, 3, -16 } };

	while (System::Update())
	{
		camera.update(4.0);
		Graphics3D::SetCameraTransform(camera);

		// [3D シーンの描画]
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			// [モデルの描画]
			{
				// 地面の描画
				groundPlane.draw(groundTexture);

				// 球の描画
				Sphere{ { 0, 1, 0 }, 1 }.draw(ColorF{ 0.75 }.removeSRGBCurve());

				// 鍛冶屋の描画
				blacksmithModel.draw(Vec3{ 8, 0, 4 });

				// 風車の描画
				millModel.draw(Vec3{ -8, 0, 4 });

				// 木の描画
				{
					const ScopedRenderStates3D renderStates{ BlendState::OpaqueAlphaToCoverage, RasterizerState::SolidCullNone };
					treeModel.draw(Vec3{ 16, 0, 4 });
					pineModel.draw(Vec3{ 16, 0, 0 });
				}

				// Siv3D くんの描画
				siv3dkunModel.draw(Vec3{ 2, 0, -2 }, Quaternion::RotateY(180_deg));
			}
		}

		// [RenderTexture を 2D シーンに描画]
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 37.2 3D モデルを動かす
`Model` は `Array<ModelObject>` から構成されており、特定の `ModelObject` について座標変換行列を適用することで、モデルの一部分だけを動かすことができます。

次のサンプルでは、風車のモデルデータに含まれる `"Mill_Blades_Cube.007"` という名前の `ModelObject` を描画する際に、回転の行列を付加しています。

![](/images/doc_v6/tutorial/36/26.jpg)
```cpp
# include <Siv3D.hpp>

// 風車の回転する羽根を描く関数
void DrawMillModel(const Model& model, const Mat4x4& mat)
{
	const auto& materials = model.materials();

	for (const auto& object : model.objects())
	{
		Mat4x4 m = Mat4x4::Identity();

		// 風車の羽根の回転
		if (object.name == U"Mill_Blades_Cube.007")
		{
			m *= Mat4x4::Rotate(Vec3{ 0,0,-1 }, (Scene::Time() * -120_deg), Vec3{ 0, 9.37401, 0 });
		}

		const Transformer3D transform{ (m * mat) };

		object.draw(materials);
	}
}

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

	const Mesh groundPlane{ MeshData::OneSidedPlane(2000, { 400, 400 }) };
	const Texture groundTexture{ U"example/texture/ground.jpg", TextureDesc::MippedSRGB };

	// モデルデータをロード
	const Model blacksmithModel{ U"example/obj/blacksmith.obj" };
	const Model millModel{ U"example/obj/mill.obj" };
	const Model treeModel{ U"example/obj/tree.obj" };
	const Model pineModel{ U"example/obj/pine.obj" };
	const Model siv3dkunModel{ U"example/obj/siv3d-kun.obj" };

	// モデルに付随するテクスチャをアセット管理に登録
	Model::RegisterDiffuseTextures(treeModel, TextureDesc::MippedSRGB);
	Model::RegisterDiffuseTextures(pineModel, TextureDesc::MippedSRGB);
	Model::RegisterDiffuseTextures(siv3dkunModel, TextureDesc::MippedSRGB);

	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ Graphics3D::GetRenderTargetSize(), 40_deg, Vec3{ 0, 3, -16 } };

	while (System::Update())
	{
		camera.update(4.0);
		Graphics3D::SetCameraTransform(camera);

		// [3D シーンの描画]
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			// [モデルの描画]
			{
				// 地面の描画
				groundPlane.draw(groundTexture);

				// 球の描画
				Sphere{ { 0, 1, 0 }, 1 }.draw(ColorF{ 0.75 }.removeSRGBCurve());

				// 鍛冶屋の描画
				blacksmithModel.draw(Vec3{ 8, 0, 4 });

				// 風車の描画
				DrawMillModel(millModel, Mat4x4::Translate(-8, 0, 4));

				// 木の描画
				{
					const ScopedRenderStates3D renderStates{ BlendState::OpaqueAlphaToCoverage, RasterizerState::SolidCullNone };
					treeModel.draw(Vec3{ 16, 0, 4 });
					pineModel.draw(Vec3{ 16, 0, 0 });
				}

				// Siv3D くんの描画
				siv3dkunModel.draw(Vec3{ 2, 0, -2 }, Quaternion::RotateY(180_deg));
			}
		}

		// [RenderTexture を 2D シーンに描画]
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 37.3 円柱座標系
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


## 37.4 球面座標系
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


## 37.5 ビルボードを描く
常にカメラのほうを向くビルボードを描画するには、

```cpp

```


## 37.6 動画を描く
`VideoTexture` の作成時に `SRGB` を指定することで、3D 空間でも動画をテクスチャとして扱うことができます。

```cpp

```


