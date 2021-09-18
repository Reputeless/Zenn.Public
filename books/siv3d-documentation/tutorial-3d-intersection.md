---
title: "チュートリアル 38 | 3D の交差判定"
free: true
---

# 38. 3D の交差判定
この章では、3D 空間における交差判定の方法を学びます。

## 38.1 3D 形状の交差
2 つの 3D 形状 `a` と `b` が交差しているかは、`a.intersects(b)` で調べられます。

![](/images/doc_v6/tutorial/38/1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.25 });
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	Array<Box> boxes;
	for (auto z : Range(-3, 3))
	{
		for (auto x : Range(-6, 6))
		{
			boxes << Box{ (x * 3), 2, (z * 3), 1 };
		}
	}

	while (System::Update())
	{
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D
		{
			const Ray ray = camera.screenToRay(Cursor::PosF());
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			Plane{ 64 }.draw(uvChecker);

			const Sphere sphere{
				(Math::Sin(Scene::Time() * 15_deg) * 16), 2, (Math::Sin(Scene::Time() * 10_deg) * 8), 4 };

			for (const auto& box : boxes)
			{
				const bool intersects = sphere.intersects(box);
				box.draw(intersects ? Linear::Palette::Orange : Linear::Palette::White);
			}

			{
				const ScopedRenderStates3D rasterizer{ RasterizerState::WireframeCullNone };
				sphere.draw();
			}
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 38.2 3D 形状を内側に含む
ある 3D 形状 `a` が別の 3D 形状 `b` を完全に内側に含んでいるかは、`a.contains(b)` で調べられます。

![](/images/doc_v6/tutorial/38/2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.25 });
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	Array<Box> boxes;
	for (auto z : Range(-3, 3))
	{
		for (auto x : Range(-6, 6))
		{
			boxes << Box{ (x * 3), 2, (z * 3), 1 };
		}
	}

	while (System::Update())
	{
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D
		{
			const Ray ray = camera.screenToRay(Cursor::PosF());
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			Plane{ 64 }.draw(uvChecker);

			const Sphere sphere{
				(Math::Sin(Scene::Time() * 15_deg) * 16), 2, (Math::Sin(Scene::Time() * 10_deg) * 8), 4 };

			for (const auto& box : boxes)
			{
				const bool contains = sphere.contains(box);
				box.draw(contains ? Linear::Palette::Orange : Linear::Palette::White);
			}

			{
				const ScopedRenderStates3D rasterizer{ RasterizerState::WireframeCullNone };
				sphere.draw();
			}
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 38.3 レイ
`Ray` は光線（視線）を表現するクラスで、始点 `origin` と単位方向ベクトル `direction` をメンバ変数に持ちます。レイと各種 3D 形状の交差判定を行うメンバ関数は次のとおりです。

|メンバ関数| 戻り値の型 |説明 |
|--|--|--|
|`.intersects(v)`| `Optional<float>` | 形状 `v` と交差する場合は交差地点までの距離、交差しない場合は `none` |
|`.intersectsAt(v)`| `Optional<Float3>` | 形状 `v` と交差する場合は、最も近い交差地点の座標、交差しない場合は `none` |

3D カメラの `.screenToRay(cursorPos)` メンバ関数を使うと、マウスカーソルのレイを作成できます。マウスカーソルのレイの `origin` はカメラの `eyePosition` と一致します。そこからマウスカーソルの方向に向く `direction` を計算し、`Ray` を作成します。

次のサンプルでは、3D 空間上の各種形状とマウスカーソルのレイの交差判定を可視化します。

![](/images/doc_v6/tutorial/38/3.png)
```cpp
# include <Siv3D.hpp>

template <class Shape>
void Draw(const Shape& shape, const Ray& ray)
{
	if (auto pos = ray.intersectsAt(shape))
	{
		shape.draw(Linear::Palette::Orange);
		Sphere{ *pos, 0.15 }.draw(Linear::Palette::Red);
	}
	else
	{
		shape.draw(HSV{ (shape.center.x * 60), 0.1, 0.95 }.removeSRGBCurve());
	}
}

void Main()
{
	Window::Resize(1280, 720);
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.25 });
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	const Sphere sphere = Sphere{ { 2, 2, 8 }, 2 };
	const Box box1 = Box::FromPoints({ -13, 0, 14 }, { -2, 4, 13 });
	const Box box2 = Box::FromPoints({ -14, 0, 14 }, { -13, 2, 2 });
	const Cylinder cy1{ {-8,2,4},{-4, 5,6}, 1 };
	const Cylinder cy2{ {-10,2,1},{-6, 3,-2}, 0.5 };

	while (System::Update())
	{
		ClearPrint();
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D
		{
			const Ray ray = camera.screenToRay(Cursor::PosF());
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			const OrientedBox ob1 = OrientedBox{ Arg::bottomCenter(-4, 0, 3), { 4, 2, 0.25 },  Quaternion::RotateY(Scene::Time() * -20_deg) };
			const OrientedBox ob2 = OrientedBox{ Arg::bottomCenter(0, 0, -1), { 4, 2, 0.25 },  Quaternion::RotateY(Scene::Time() * 20_deg) };
			const Cone cone{ { 4, 4, 0},{ 4, 7, 0}, 1.0, Quaternion::RotateZ(Scene::Time() * 10_deg) };

			Plane{ 64 }.draw(uvChecker);
			Draw(sphere, ray);
			Draw(box1, ray);
			Draw(box2, ray);
			Draw(cy1, ray);
			Draw(cy2, ray);
			Draw(ob1, ray);
			Draw(ob2, ray);
			Draw(cone, ray);

			Print << U"origin" << ray.origin.xyz();
			Print << U"direction" << ray.direction.xyz();
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 38.4 最も手前で交差する物体の取得
レイは無限遠に伸びるため、複数の形状と交差する場合があります。`Ray` の `.intersects()` で得られる距離情報を調べ、最も近いものが最初に交差する形状です。

![](/images/doc_v6/tutorial/38/4.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.25 });
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	Array<Box> boxes;
	for (auto z : Range(-3, 3))
	{
		for (auto x : Range(-6, 6))
		{
			boxes << Box{ (x * 4), 2, (z * 4), 3, 4, 1 };
		}
	}

	while (System::Update())
	{
		ClearPrint();
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D
		{
			const Ray ray = camera.screenToRay(Cursor::PosF());
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			Plane{ 64 }.draw(uvChecker);

			// 交差し、もっとも手前にあるボックスのインデックス
			Optional<size_t> boxIndex;
			double minDistance = Math::Inf;

			for (auto [i, box] : Indexed(boxes))
			{
				if (const auto distance = ray.intersects(box))
				{
					if (*distance < minDistance)
					{
						minDistance = *distance;
						boxIndex = i;
					}
				}
			}

			for (auto [i, box] : Indexed(boxes))
			{
				if (i == boxIndex)
				{
					box.draw(Linear::Palette::Orange);
				}
				else
				{
					box.draw();
				}
			}

			if (boxIndex)
			{
				Cursor::RequestStyle(CursorStyle::Hand);

				// 距離から交差地点を計算
				const Vec3 pos = ray.point_at(minDistance);
				Sphere{ pos, 0.2 }.draw(Linear::Palette::Red);

				Print << *boxIndex;
				Print << pos;
			}
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 38.5 ViewFrustum
カメラに映っている空間は、四角錐台の形状です。これは `ViewFrustum` クラスで表現できます。この形状と交差していれば、カメラに可視であるという判定ができ、不可視の物体は描画しないことで実行時性能を向上させることができます。

Siv3D のカメラの `ViewFrustum` の奥行きは無限遠にあるため、適当な奥行きのリミットを指定する必要があります。

次のサンプルは 2 つのカメラ `camera1`, `camera2` が登場し、`camera2` の `ViewFrustum` を赤い線分で可視化しています。そして、その `ViewFrustum` 内に含まれる球の色を変えて描画しています。

このサンプルでの `camera2D` は、`upDirection` パラメータをカスタマイズすることで、カメラが左右にロールする姿勢制御も行っています。

![](/images/doc_v6/tutorial/38/5.png)
```cpp
# include <Siv3D.hpp>

void DrawScene(const ViewFrustum& frustum, const Texture& groundTexture)
{
	Plane{ 60 }.draw(groundTexture);
	const ColorF orange = ColorF{ Palette::Orange }.removeSRGBCurve();

	for (auto z : Range(-2, 2))
	{
		for (auto x : Range(-4, 4))
		{
			for (auto y : Range(1, 4))
			{
				const Sphere s{ Vec3{(x * 2), (y * 2), (z * 2) }, 0.4 };
				s.draw(frustum.intersects(s) ? orange : ColorF{ 1.0 });
			}
		}
	}
}

void Main()
{
	Window::Resize(1280, 720);
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.5 });

	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture groundTexture{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	const MSRenderTexture renderTexture2{ 360, 240, TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera1{ renderTexture.size(), 25_deg, Vec3{ 10, 16, -32 } };
	BasicCamera3D camera2{ renderTexture2.size(), 25_deg };

	while (System::Update())
	{
		camera1.update(4.0);
		const Vec3 camera2Pos = Cylindrical{ 18, (Scene::Time() * 10_deg), (2 + Periodic::Sine0_1(4s) * 10) };
		camera2.setView(camera2Pos, Vec3::Zero());
		const Vec3 upDirection = Vec3{ Math::Sin(Scene::Time() * 0.2), 1, 0 }.normalized() * camera2.getLookAtOrientation();
		camera2.setUpDirection(upDirection);

		// camera2 が作る、手前 1.0, 奥行き 30.0 の ViewFrustum
		const ViewFrustum frustum{ camera2, 1.0, 30.0 };

		// 俯瞰カメラ (camera1)
		{
			Graphics3D::SetCameraTransform(camera1);
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			DrawScene(frustum, groundTexture);
			frustum.drawFrame(Palette::Red);
			Line3D{ camera2Pos, camera2Pos + upDirection * 4 }.draw(Palette::Red);
			OrientedBox{ frustum.getOrigin(), {1.2, 0.8, 2}, frustum.getOrientation() }.draw(ColorF{ 0.2 }.removeSRGBCurve());
		}

		// (camera2)
		{
			Graphics3D::SetCameraTransform(camera2);
			const ScopedRenderTarget3D target{ renderTexture2.clear(backgroundColor) };
			DrawScene(frustum, groundTexture);
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			renderTexture2.resolve();
			Shader::LinearToScreen(renderTexture);
			RectF{ 360,240 }.drawShadow({ 0,0 }, 8, 3);
			Shader::LinearToScreen(renderTexture2, Vec2{ 0,0 });
		}
	}
}
```


## 38.6 Mesh の境界ボリューム
`Mesh` は境界ボリューム情報を内部に保存していて、`.boundingSphere()` で境界球を、`.boundingBox()` で境界ボックスを返します。

![](/images/doc_v6/tutorial/38/6.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.25 });
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	const Mesh mesh{ MeshData::Dodecahedron(Vec3{0,4,0}, 4) };

	while (System::Update())
	{
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D
		{
			const Ray ray = camera.screenToRay(Cursor::PosF());
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			Plane{ 64 }.draw(uvChecker);

			mesh.draw();

			// 境界ボックスを取得
			mesh.boundingBox().drawFrame(Linear::Palette::Red);
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 38.7 モデルの境界ボリューム
`Model` は境界ボリューム情報を内部に保存していて、`.boundingSphere()` で境界球を、`.boundingBox()` で境界ボックスを返します。また、`ModelObject` はメンバ変数に `boundingSphere` と `boundingBox` を持ちます。

![](/images/doc_v6/tutorial/38/7.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.25 });
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	// モデルデータをロード
	const Model millModel{ U"example/obj/mill.obj" };

	while (System::Update())
	{
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D
		{
			const Ray ray = camera.screenToRay(Cursor::PosF());
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			Plane{ 64 }.draw(uvChecker);

			millModel.draw();

			// Model の境界ボックス
			millModel.boundingBox().drawFrame(Linear::Palette::Red);

			for (const auto& object : millModel.objects())
			{
				// ModelObject の境界ボックス
				object.boundingBox.drawFrame();
			}
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```

このチュートリアルでは扱いませんが、境界ボリュームの作成、座標変換、合成などは `Geometry3D::` 名前空間にある関数が便利です。


## 38.8 （サンプル）3D 空間のペイント
3D の床に線を引くサンプルです。太陽光の影響を受けないよう、環境光を `ColorF{1.0}`, 太陽光を `ColorF{0.0}` に設定しています。

![](/images/doc_v6/tutorial/38/8.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.25 });
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	const Plane floorPlane{ 20 };
	Image image{ 1000, 1000, Palette::White };
	DynamicTexture texture{ image, TextureDesc::MippedSRGB };
	Optional<Vec2> previousPenPos;

	// 太陽光の影響を与えないようにする
	Graphics3D::SetGlobalAmbientColor(ColorF{ 1.0 });
	Graphics3D::SetSunColor(ColorF{ 0.0 });

	while (System::Update())
	{
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D
		{
			const Ray ray = camera.screenToRay(Cursor::PosF());
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			if (const auto pos = ray.intersectsAt(floorPlane))
			{
				Sphere{ *pos, 0.2 }.draw(Linear::Palette::Orange);
				const Vec2 penPos = pos->xz();

				if (MouseL.pressed())
				{
					const Vec2 from = (previousPenPos ? *previousPenPos : penPos);
					const Vec2 to = penPos;
					previousPenPos = penPos;
					Line{ (from * Vec2{ 50, -50 }), (to * Vec2{ 50, -50 }) }
						.movedBy(500, 500)
						.overwrite(image, 5, ColorF{ Palette::Orange }.removeSRGBCurve());
					texture.fill(image);
				}
				else
				{
					previousPenPos.reset();
				}
			}
			else
			{
				previousPenPos.reset();
			}

			floorPlane.draw(texture);
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```
