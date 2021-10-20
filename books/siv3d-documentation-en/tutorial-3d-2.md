---
title: "Tutorial 37 | Drawing 3D Geometries (Advanced)"
free: true
---

# 37. Drawing 3D Geometries (Advanced)
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
`MeshData::Billboard()` で作成した有限平面のメッシュを、カメラの `.billboard()` で得られる座標変換を使って描画することで、常にカメラのほうを向くビルボードを描画できます。

![](/images/doc_v6/tutorial/37/5.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	// ビルボード表示する板
	const Mesh billboard{ MeshData::Billboard() };
	const Mesh wideBillboard{ MeshData::Billboard({2, 1}) };

	while (System::Update())
	{
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);
		const Mat4x4 billboardMat = camera.getInvView();

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			Plane{ 64 }.draw(uvChecker);
			Box{ -8,2,0,4 }.draw(ColorF{ 0.8, 0.6, 0.4 }.removeSRGBCurve());
			Sphere{ 0,2,0,2 }.draw(ColorF{ 0.4, 0.8, 0.6 }.removeSRGBCurve());
			Cylinder{ 8, 2, 0, 2, 4 }.draw(ColorF{ 0.6, 0.4, 0.8 }.removeSRGBCurve());

			billboard.draw(camera.billboard(Vec3{ -8, 4, -4 }, 1), uvChecker);
			billboard.draw(camera.billboard(Vec3{ 0, 4, -4 }, 2), uvChecker);
			billboard.draw(camera.billboard(Vec3{ 8, 4, -4 }, 4), uvChecker);

			wideBillboard.draw(camera.billboard(Vec3{ -8, 4, 4 }, { 2, 1 }), uvChecker);
			wideBillboard.draw(camera.billboard(Vec3{ 0, 4, 4 }, { 4, 2 }), uvChecker);
			wideBillboard.draw(camera.billboard(Vec3{ 8, 4, 4 }, { 8, 4 }), uvChecker);
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


## 37.6 動画を描く
`VideoTexture` の作成時の `TextureDesc` に `SRGB` を指定することで、3D 描画でも動画をテクスチャとして扱うことができます。

![](/images/doc_v6/tutorial/37/6.jpg)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	const Mesh mesh{ MeshData::TwoSidedPlane(Vec2{16, 9}) };

	// ループする場合は Loop::Yes, ループしない場合は Loop::No, (VideoTexture に Mipped は効かない)
	const VideoTexture videoTexture{ U"example/video/river.mp4", Loop::Yes, TextureDesc::MippedSRGB };

	while (System::Update())
	{
		// 動画の時間を進める (デフォルトでは Scece::DeltaTime() 秒)
		videoTexture.advance();

		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			Plane{ 64 }.draw(uvChecker);
			mesh.draw(Vec3{ 0, 4.5, 8 }, Quaternion::RotateX(-90_deg), videoTexture);
			Box{ -8,2,0,4 }.draw(videoTexture);
			Sphere{ 0,2,0,2 }.draw(videoTexture);
			Cylinder{ 8, 2, 0, 2, 4 }.draw(videoTexture);
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


## 37.7 3D カスタムシェーダ
3D 描画でも `ScopedCustomShader3D` を使うことで、2D 描画のようにカスタムシェーダを使用できます。

カスタムのシェーダプログラムを書く前に、デフォルト使われている 3D 描画のシェーダプログラムを見てみましょう。これらを書き換えることで、容易にシェーダをカスタマイズできます。

デフォルトの 3D 描画シェーダは、次のような定義済みの定数バッファを持っています。

- 頂点シェーダ
  - (予約済み) (slot0)
  - VSPerView (slot 1)
  - VSPerObject (slot 2)
  - (予約済み) (slot 3)
- ピクセルシェーダ
  - PSPerFrame (slot 0)
  - PSPerView (slot 1)
  - (予約済み) (slot 2)
  - PSPerMaterial (slot 3)

頂点シェーダ、ピクセルシェーダともに slot 0～3 は予約済みで、頂点シェーダには 2 つの定義済み定数バッファ、ピクセルシェーダには 3 つの定義済み定数バッファがあります。カスタムシェーダで独自の定数バッファを使いたい場合、予約済みのスロットインデックスは使用できません。

### HLSL
HLSL は 1 つのファイルに複数のシェーダ関数をまとめて記述できます。`default3d_forward.hlsl` の `VS()` が、デフォルトの 3D 描画用頂点シェーダ関数です。入力 s3d::VSInput を受け取り、定数バッファ `VS～` の座標変換情報等を適用した結果を s3d::PSInput 型で返します。

`PS()` が、デフォルトの 3D 描画用ピクセルシェーダ関数です。入力 `s3d::PSInput` を受け取り、出力するピクセルの RGBA カラー (`float4` 型) を求めます。

定数バッファ `PSPerMaterial` の `g_hasTexture` が `1u` の場合はテクスチャを持つ物体です。テクスチャ `g_texture0` と、サンプラー `g_sampler0` を使ってテクスチャから色を読み込みます。法線やマテリアル、光源の情報等に基づいて Phong shading モデルの計算により最終的な色を決定しています。

```hlsl:example/shader/hlsl/default3d_forward.hlsl
//
//	Textures
//
Texture2D		g_texture0 : register(t0);
SamplerState	g_sampler0 : register(s0);

namespace s3d
{
	//
	//	VS Input
	//
	struct VSInput
	{
		float4 position : POSITION;
		float3 normal : NORMAL;
		float2 uv : TEXCOORD0;
	};

	//
	//	VS Output / PS Input
	//
	struct PSInput
	{
		float4 position : SV_POSITION;
		float3 worldPosition : TEXCOORD0;
		float2 uv : TEXCOORD1;
		float3 normal : TEXCOORD2;
	};
}

//
//	Constant Buffer
//
cbuffer VSPerView : register(b1)
{
	row_major float4x4 g_worldToProjected;
}

cbuffer VSPerObject : register(b2)
{
	row_major float4x4 g_localToWorld;
}

cbuffer PSPerFrame : register(b0)
{
	float3 g_gloablAmbientColor;
	float3 g_sunColor;
	float3 g_sunDirection;
}

cbuffer PSPerView : register(b1)
{
	float3 g_eyePosition;
}

cbuffer PSPerMaterial : register(b3)
{
	float3 g_amibientColor;
	uint   g_hasTexture;
	float4 g_diffuseColor;
	float3 g_specularColor;
	float  g_shininess;
	float3 g_emissionColor;
}

//
//	Functions
//
s3d::PSInput VS(s3d::VSInput input)
{
	s3d::PSInput result;

	const float4 worldPosition = mul(input.position, g_localToWorld);

	result.position			= mul(worldPosition, g_worldToProjected);
	result.worldPosition	= worldPosition.xyz;
	result.uv				= input.uv;
	result.normal			= mul(input.normal, (float3x3)g_localToWorld);
	return result;
}

float4 GetDiffuseColor(float2 uv)
{
	float4 diffuseColor = g_diffuseColor;

	if (g_hasTexture)
	{
		diffuseColor *= g_texture0.Sample(g_sampler0, uv);
	}

	return diffuseColor;
}

float3 CalculateDiffuseReflection(float3 n, float3 l, float3 lightColor, float3 diffuseColor, float3 ambientColor)
{
	const float3 directColor = lightColor * saturate(dot(n, l));
	return ((ambientColor + directColor) * diffuseColor);
}

float3 CalculateSpecularReflection(float3 n, float3 h, float shininess, float nl, float3 lightColor, float3 specularColor)
{
	const float highlight = pow(saturate(dot(n, h)), shininess) * float(0.0 < nl);
	return (lightColor * specularColor * highlight);
}

float4 PS(s3d::PSInput input) : SV_TARGET
{
	const float3 lightColor		= g_sunColor;
	const float3 lightDirection	= g_sunDirection;

	const float3 n = normalize(input.normal);
	const float3 l = lightDirection;
	const float4 diffuseColor = GetDiffuseColor(input.uv);
	const float3 ambientColor = (g_amibientColor * g_gloablAmbientColor);

	// Diffuse
	const float3 diffuseReflection = CalculateDiffuseReflection(n, l, lightColor, diffuseColor.rgb, ambientColor);

	// Specular
	const float3 v = normalize(g_eyePosition - input.worldPosition);
	const float3 h = normalize(v + lightDirection);
	const float3 specularReflection = CalculateSpecularReflection(n, h, g_shininess, dot(n, l), lightColor, g_specularColor);

	return float4(diffuseReflection + specularReflection + g_emissionColor, diffuseColor.a);
}
```


### GLSL
GLSL では、1 つのファイルに 1 つのシェーダ関数を実装します。`default3d_forward.vert` の `main()` が、デフォルトの 3D 描画用シェーダ関数です。VSInput` の形式で入力座標を受け取り、定数バッファ `VS～` の座標変換情報を適用した結果を `VSOutput` の形式で出力します。

`default3d_forward.frag` の main() が、デフォルトの 3D 描画用ピクセルシェーダ関数です。`PSInput` 形式で入力を受け取り、出力するピクセルの RGBA カラー (`vec4` 型) を `FragColor` に書き込みます。

定数バッファ `PSPerMaterial` の `g_hasTexture` が `1u` の場合はテクスチャを持つ物体です。テクスチャサンプラー `Texture0` から色を読み込みます。法線やマテリアル、光源の情報等に基づいて Phong shading モデルの計算により最終的な色を決定しています。

頂点シェーダ

```glsl:example/shader/glsl/default3d_forward.vert
# version 410

//
//	VSInput
//
layout(location = 0) in vec4 VertexPosition;
layout(location = 1) in vec3 VertexNormal;
layout(location = 2) in vec2 VertexUV;

//
//	VSOutput
//
layout(location = 0) out vec3 WorldPosition;
layout(location = 1) out vec2 UV;
layout(location = 2) out vec3 Normal;
out gl_PerVertex
{
	vec4 gl_Position;
};

//
//	Constant Buffer
//
layout(std140) uniform VSPerView // slot 1
{
	mat4x4 g_worldToProjected;
};

layout(std140) uniform VSPerObject // slot 2
{
	mat4x4 g_localToWorld;
};

//
//	Functions
//
void main()
{
	vec4 worldPosition = VertexPosition * g_localToWorld;

	gl_Position		= worldPosition * g_worldToProjected;
	WorldPosition	= worldPosition.xyz;
	UV				= VertexUV;
	Normal			= VertexNormal * mat3x3(g_localToWorld);
}
```

ピクセルシェーダ

```glsl:example/shader/glsl/default3d_forward.frag
# version 410

//
//	Textures
//
uniform sampler2D Texture0;

//
//	PSInput
//
layout(location = 0) in vec3 WorldPosition;
layout(location = 1) in vec2 UV;
layout(location = 2) in vec3 Normal;

//
//	PSOutput
//
layout(location = 0) out vec4 FragColor;

//
//	Constant Buffer
//
layout(std140) uniform PSPerFrame // slot 0
{
	vec3 g_gloablAmbientColor;
	vec3 g_sunColor;
	vec3 g_sunDirection;
};

layout(std140) uniform PSPerView // slot 1
{
	vec3 g_eyePosition;
};

layout(std140) uniform PSPerMaterial // slot 3
{
	vec3  g_amibientColor;
	uint  g_hasTexture;
	vec4  g_diffuseColor;
	vec3  g_specularColor;
	float g_shininess;
	vec3  g_emissionColor;
};

//
//	Functions
//
vec4 GetDiffuseColor(vec2 uv)
{
	vec4 diffuseColor = g_diffuseColor;

	if (g_hasTexture == 1)
	{
		diffuseColor *= texture(Texture0, uv);
	}

	return diffuseColor;
}

vec3 CalculateDiffuseReflection(vec3 n, vec3 l, vec3 lightColor, vec3 diffuseColor, vec3 ambientColor)
{
	vec3 directColor = lightColor * max(dot(n, l), 0.0f);
	return ((ambientColor + directColor) * diffuseColor);
}

vec3 CalculateSpecularReflection(vec3 n, vec3 h, float shininess, float nl, vec3 lightColor, vec3 specularColor)
{
	float highlight = pow(max(dot(n, h), 0.0f), shininess) * float(0.0f < nl);
	return (lightColor * specularColor * highlight);
}

void main()
{
	vec3 lightColor		= g_sunColor;
	vec3 lightDirection	= g_sunDirection;

	vec3 n = normalize(Normal);
	vec3 l = lightDirection;
	vec4 diffuseColor = GetDiffuseColor(UV);
	vec3 ambientColor = (g_amibientColor * g_gloablAmbientColor);

	// Diffuse
	vec3 diffuseReflection = CalculateDiffuseReflection(n, l, lightColor, diffuseColor.rgb, ambientColor);

	// Specular
	vec3 v = normalize(g_eyePosition - WorldPosition);
	vec3 h = normalize(v + lightDirection);
	vec3 specularReflection = CalculateSpecularReflection(n, h, g_shininess, dot(n, l), lightColor, g_specularColor);

	FragColor = vec4(diffuseReflection + specularReflection + g_emissionColor, diffuseColor.a);
}
```

---

次のサンプルは Siv3D のデフォルトの 3D 形状描画で使われているのと同じカスタムシェーダプログラムを読み込んで使用します。レンダリング結果は通常の描画と同じになります。

![](/images/doc_v6/tutorial/37/7.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	// 頂点シェーダ
	const VertexShader vs3D = HLSL{ U"example/shader/hlsl/default3d_forward.hlsl", U"VS" }
		| GLSL{ U"example/shader/glsl/default3d_forward.vert", {{ U"VSPerView", 1 }, { U"VSPerObject", 2 }} };

	// ピクセルシェーダ
	const PixelShader ps3D = HLSL{ U"example/shader/hlsl/default3d_forward.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/default3d_forward.frag", {{ U"PSPerFrame", 0 }, { U"PSPerView", 1 }, { U"PSPerMaterial", 3 }} };

	if ((not vs3D) || (not ps3D))
	{
		return;
	}

	while (System::Update())
	{
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			// カスタムシェーダ使用
			const ScopedCustomShader3D shader{ vs3D, ps3D };

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


## 37.8 フォグ
ピクセルシェーダにおいて、描画するピクセルが視点からどれだけ離れているかに応じて、色をフォグカラーに近づけることで、霧のような表現を実現できます。

![](/images/doc_v6/tutorial/37/8.png)
```cpp
# include <Siv3D.hpp>

struct PSFog
{
	Float3 fogColor;
	float fogCoefficient;
};

void Main()
{
	Window::Resize(1280, 720);

	const PixelShader ps = HLSL{ U"example/shader/hlsl/forward_fog.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/forward_fog.frag", {{ U"PSPerFrame", 0 }, { U"PSPerView", 1 }, { U"PSPerMaterial", 3 }, { U"PSFog", 4 }} };

	if (not ps)
	{
		return;
	}

	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	double fogParam = 0.6;
	ConstantBuffer<PSFog> cb{ { backgroundColor.rgb(), 0.0f } };

	while (System::Update())
	{
		camera.update(2.0);

		const double fogCoefficient = Math::Eerp(0.001, 0.25, fogParam);
		cb->fogCoefficient = static_cast<float>(fogCoefficient);

		// 3D
		{
			Graphics3D::SetCameraTransform(camera);
			Graphics3D::SetPSConstantBuffer(4, cb);

			const ScopedCustomShader3D shader{ ps };
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			Plane{ 64 }.draw(uvChecker);

			for (auto x : Range(-4, 4))
			{
				for (auto z : Range(-4, 4))
				{
					Box{ {x * 8, 4, z * 8} , {2, 8, 2} }.draw();
				}
			}
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}

		SimpleGUI::Slider(U"fogCoefficient: {:.3f}"_fmt(fogCoefficient), fogParam, Vec2{ 20, 20 }, 200, 160);
	}
}
```


## 37.9 Tri-Planar
適切な UV 座標が設定されていない複雑な形状の表面に対し、頂点の位置と法線に基づいて最大 3 種類のテクスチャをブレンドするのが Tri-Planer という手法です。

一部の `MeshData::～` で作成した形状は UV 座標を持たないという制約がありましたが、次のサンプルでは Tri-Planer のピクセルシェーダを使うことで、テクスチャを良い感じにマッピングします。Tri-Planar は、この章に登場する地形シェーダでも使われています。すでに適切な UV 座標が設定されている形状に対しては、特別な理由が無い限り Tri-Planer は不要でしょう。

![](/images/doc_v6/tutorial/37/9.jpg)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	const PixelShader ps = HLSL{ U"example/shader/hlsl/forward_triplanar.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/forward_triplanar.frag", {{ U"PSPerFrame", 0 }, { U"PSPerView", 1 }, { U"PSPerMaterial", 3 }} };

	if (not ps)
	{
		return;
	}

	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const Texture woodTexture{ U"example/texture/wood.jpg", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	const Mesh mesh0{ MeshData::Octahedron(2) };
	const Mesh mesh1{ MeshData::Dodecahedron(2) };
	const Mesh mesh2{ MeshData::Icosahedron(2) };

	bool triplanar = true;

	while (System::Update())
	{
		camera.update(2.0);

		// 3D
		{
			Graphics3D::SetCameraTransform(camera);

			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			Plane{ 64 }.draw(uvChecker);

			if (triplanar)
			{
				const ScopedCustomShader3D shader{ ps };
				Box{ -8,2,0,4 }.draw(woodTexture);
				Sphere{ 0,2,0,2 }.draw(woodTexture);
				Cylinder{ 8, 2, 0, 2, 4 }.draw(woodTexture);
				mesh0.draw({ -8, 2, 8 }, woodTexture);
				mesh1.draw({ 0, 2, 8 }, woodTexture);
				mesh2.draw({ 8, 2, 8 }, woodTexture);
			}
			else
			{
				Box{ -8,2,0,4 }.draw(woodTexture);
				Sphere{ 0,2,0,2 }.draw(woodTexture);
				Cylinder{ 8, 2, 0, 2, 4 }.draw(woodTexture);
				mesh0.draw({ -8, 2, 8 }, woodTexture);
				mesh1.draw({ 0, 2, 8 }, woodTexture);
				mesh2.draw({ 8, 2, 8 }, woodTexture);
			}
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}

		SimpleGUI::CheckBox(triplanar, U"Tri-Planer", Vec2{ 20,20 });
	}
}
```


## 37.10 地形
高さマップをレンダーテクスチャで作成し、頂点シェーダにおいて、グリッド状の各頂点の Y 座標を高さマップに基づいて変位させることで、地形を効率的に描画できます。

次のサンプルでは、`heightmap` に対する加算ブレンドを使った描き込みで高度情報を編集し、そこから `psNormal` を使って法線情報を作成し `normalmap` に描き込みます。地形を描画する際、これらの 2 つのテクスチャを書くシェーダが参照し、グリッド状のモデルを地形の形に変化させます。Tri-Planer によって、草と岩肌のテクスチャをマッピングしています。

法線を格納するとき、法線が上向きの単位ベクトルであることを利用して、x, y, z の 3 成分ではなく x, z の 2 成分だけを格納し、ピクセルシェーダで `y = sqrt(1 - x*x - z*z)` から y を復元しています。これはテクスチャのサイズを圧縮することで GPU のメモリ転送量を節約し、実行時性能を向上させるテクニックです。

サンプルでは左上の高さマップを左クリック / 右クリックすることで地形の高さを編集できます。

![](/images/doc_v6/tutorial/37/10.jpg)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	const VertexShader vsTerrain = HLSL{ U"example/shader/hlsl/terrain_forward.hlsl", U"VS" }
		| GLSL{ U"example/shader/glsl/terrain_forward.vert", {{ U"VSPerView", 1 }, { U"VSPerObject", 2 }} };

	const PixelShader psTerrain = HLSL{ U"example/shader/hlsl/terrain_forward.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/terrain_forward.frag", {{ U"PSPerFrame", 0 }, { U"PSPerView", 1 }, { U"PSPerMaterial", 3 }} };

	const PixelShader psNormal = HLSL{ U"example/shader/hlsl/terrain_normal.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/terrain_normal.frag", {{U"PSConstants2D", 0}} };

	if ((not vsTerrain) || (not psTerrain) || (not psNormal))
	{
		return;
	}

	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const Texture terrainTexture{ U"example/texture/grass.jpg", TextureDesc::MippedSRGB };
	const Texture rockTexture{ U"example/texture/rock.jpg", TextureDesc::MippedSRGB };
	const Texture brushTexture{ U"example/particle.png" };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	const Mesh gridMesh{ MeshData::Grid({128, 128}, 128, 128) };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };
	RenderTexture heightmap{ Size{ 256, 256 }, ColorF{0.0}, TextureFormat::R32_Float };
	RenderTexture normalmap{ Size{ 256, 256 }, ColorF{0.0}, TextureFormat::R16G16_Float };

	while (System::Update())
	{
		camera.update(2.0);

		// 3D
		{
			Graphics3D::SetCameraTransform(camera);

			const ScopedCustomShader3D shader{ vsTerrain, psTerrain };
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			const ScopedRenderStates3D ss{ { ShaderStage::Vertex, 0, SamplerState::ClampLinear} };
			Graphics3D::SetVSTexture(0, heightmap);
			Graphics3D::SetPSTexture(1, normalmap);
			Graphics3D::SetPSTexture(2, rockTexture);

			gridMesh.draw(terrainTexture);
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}

		if (const bool gen = SimpleGUI::Button(U"Random", Vec2{270, 10});
			(gen || (MouseL | MouseR).pressed())) // 地形を編集
		{
			// heightmap を編集
			if (gen)
			{
				const PerlinNoiseF perlin{ RandomUint64() };
				Grid<float> grid(256, 256);
				for (auto p : step(grid.size()))
				{
					grid[p] = perlin.octave2D0_1(p / 256.0f, 5) * 16.0f;
				}
				const RenderTexture noise{ grid };

				const ScopedRenderTarget2D target{ heightmap };
				noise.draw();
			}
			else
			{
				const ScopedRenderTarget2D target{ heightmap };
				const ScopedRenderStates2D blend{ BlendState::Additive };
				brushTexture.scaled(1.0 + MouseL.pressed()).drawAt(Cursor::PosF(), ColorF{ Scene::DeltaTime() * 15.0 });
			}

			// normal map を更新
			{
				const ScopedRenderTarget2D target{ normalmap };
				const ScopedCustomShader2D shader{ psNormal };
				const ScopedRenderStates2D blend{ BlendState::Opaque, SamplerState::ClampLinear };
				heightmap.draw();
			}
		}

		if (SimpleGUI::Button(U"Clear", Vec2{ 270, 50 }))
		{
			heightmap.clear(ColorF{ 0.0 });
			normalmap.clear(ColorF{ 0.0, 0.0 });
		}

		heightmap.draw(ColorF{ 0.1 });
		normalmap.draw(0, 260);
	}
}
```


## 37.11 ライトブルーム
3D の描画結果から `psBright` ピクセルシェーダで高輝度部分を抽出し、それに対してガウスぼかしをかけた結果を、最初の結果に加算ブレンドすることで、ライトブルーム効果を実現できます。ガウスぼかしの処理は重いため、小さい解像度のレンダーテクスチャに対して行っていることがポイントです。

![](/images/doc_v6/tutorial/37/11.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetResizeMode(ResizeMode::Keep);

	const PixelShader psBright = HLSL{ U"example/shader/hlsl/extract_bright_linear.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/extract_bright_linear.frag", {{U"PSConstants2D", 0}} };

	if (not psBright)
	{
		return;
	}

	const ColorF backgroundColor = ColorF{ 0.02 }.removeSRGBCurve();
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R16G16B16A16_Float, HasDepth::Yes };
	const RenderTexture gaussianA4{ renderTexture.size() / 4 }, gaussianB4{ renderTexture.size() / 4 };
	const RenderTexture gaussianA8{ renderTexture.size() / 8 }, gaussianB8{ renderTexture.size() / 8 };
	const RenderTexture gaussianA16{ renderTexture.size() / 16 }, gaussianB16{ renderTexture.size() / 16 };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

	bool glowEffect = true;

	while (System::Update())
	{
		camera.update(2.0);

		// 3D
		{
			Graphics3D::SetCameraTransform(camera);

			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			PhongMaterial phong;
			phong.amibientColor = ColorF{ 1.0 };
			phong.diffuseColor = ColorF{ 0.0 };

			for (auto i : Range(0, 10))
			{
				phong.emissionColor = ColorF{ 1.0, 0.4, 0.4 }.removeSRGBCurve() * (i / 5.0);
				Box{ {-20 + i * 4, 2, 8}, 2 }
					.draw(phong);

				phong.emissionColor = ColorF{ 0.4, 1.0, 0.4 }.removeSRGBCurve() * (i / 5.0);
				Sphere{ {-20 + i * 4, 2, 0}, 1 }
					.draw(phong);

				phong.emissionColor = ColorF{ 0.4, 0.4, 1.0 }.removeSRGBCurve() * (i / 5.0);
				Cylinder{ {-20 + i * 4, 2, -8}, 1, 2 }
					.draw(phong);
			}
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}

		if (glowEffect)
		{
			// 高輝度部分を抽出
			{
				const ScopedCustomShader2D shader{ psBright };
				const ScopedRenderTarget2D target{ gaussianA4.clear(ColorF{0.0}) };
				renderTexture.scaled(0.25).draw();
			}

			// 高輝度部分のぼかしテクスチャの生成
			{
				Shader::GaussianBlur(gaussianA4, gaussianB4, gaussianA4);
				Shader::Downsample(gaussianA4, gaussianA8);
				Shader::GaussianBlur(gaussianA8, gaussianB8, gaussianA8);
				Shader::GaussianBlur(gaussianA8, gaussianB8, gaussianA8);
				Shader::Downsample(gaussianA8, gaussianA16);
				Shader::GaussianBlur(gaussianA16, gaussianB16, gaussianA16);
				Shader::GaussianBlur(gaussianA16, gaussianB16, gaussianA16);
				Shader::GaussianBlur(gaussianA16, gaussianB16, gaussianA16);
			}

			// Glow エフェクト
			{
				const ScopedRenderStates2D blend{ BlendState::AdditiveRGB };

				{
					const ScopedRenderTarget2D target{ gaussianA8 };
					gaussianA16.scaled(2.0).draw(ColorF{ 3.0 });
				}

				{
					const ScopedRenderTarget2D target{ gaussianA4 };
					gaussianA8.scaled(2.0).draw(ColorF{ 1.0 });
				}

				gaussianA4.resized(Scene::Size()).draw(ColorF{ 1.0 });
			}
		}

		SimpleGUI::CheckBox(glowEffect, U"Glow", Vec2{ 20,20 });
	}
}
```


## 37.12 天空レンダリングエンジン
`Sky` は、Siv3D で説得力のある空を描画するための機能です。様々なパラメータを設定して、空、雲、星を描画できます。

![](/images/doc_v6/tutorial/37/12.png)
```cpp
# include <Siv3D.hpp>

void DrawMillModel(const Model& model, const Mat4x4& mat)
{
	const auto& materials = model.materials();

	for (const auto& object : model.objects())
	{
		Mat4x4 m = Mat4x4::Identity();

		if (object.name == U"Mill_Blades_Cube.007")
		{
			m *= Mat4x4::Rotate(Vec3{ 0,0,-1 }, (Scene::Time() * -120_deg), Vec3{ 0, 9.37401, 0 });
		}

		const Transformer3D t{ (m * mat) };

		object.draw(materials);
	}
}

void Main()
{
	Window::Resize(1280, 720);
	const Mesh groundPlane{ MeshData::OneSidedPlane(2000, { 400, 400 }) };
	const Texture groundTexture{ U"example/texture/ground.jpg", TextureDesc::MippedSRGB };
	const Model blacksmithModel{ U"example/obj/blacksmith.obj" };
	const Model millModel{ U"example/obj/mill.obj" };
	const Model treeModel{ U"example/obj/tree.obj" };
	const Model pineModel{ U"example/obj/pine.obj" };
	Model::RegisterDiffuseTextures(treeModel, TextureDesc::MippedSRGB);
	Model::RegisterDiffuseTextures(pineModel, TextureDesc::MippedSRGB);
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ Graphics3D::GetRenderTargetSize(), 40_deg, Vec3{ 0, 3, -16 } };

	Sky sky;
	double skyTime = 0.5;
	bool showUI = true;

	while (System::Update())
	{
		// [3D シーンの描画]
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(ColorF{ 0.0 }) };
			camera.update(4.0);
			Graphics3D::SetCameraTransform(camera);

			// [モデルの描画]
			{
				groundPlane.draw(groundTexture);
				Sphere{ { 0, 1, 0 }, 1 }.draw(ColorF{ 0.75 }.removeSRGBCurve());
				blacksmithModel.draw(Vec3{ 8, 0, 4 });
				DrawMillModel(millModel, Mat4x4::Translate(-8, 0, 4));
				{
					const ScopedRenderStates3D renderState{ BlendState::OpaqueAlphaToCoverage, RasterizerState::SolidCullNone };
					treeModel.draw(Vec3{ 16, 0, 4 });
					pineModel.draw(Vec3{ 16, 0, 0 });
				}
			}

			// [天空レンダリング]
			{
				const double time0_2 = Math::Fraction(skyTime * 0.5) * 2.0;
				const double halfDay0_1 = Math::Fraction(skyTime);
				const double distanceFromNoon0_1 = Math::Saturate(1.0 - (Abs(0.5 - halfDay0_1) * 2.0));
				const bool night = (1.0 < time0_2);
				const double tf = EaseOutCubic(distanceFromNoon0_1);
				const double tc = EaseInOutCubic(distanceFromNoon0_1);
				const double starCenteredTime = Math::Fmod(time0_2 + 1.5, 2.0);

				// set sun
				{
					const Quaternion q = (Quaternion::RotateY(halfDay0_1 * 180_deg) * Quaternion::RotateX(50_deg));
					const Vec3 sunDirection = q * Vec3::Right();
					const ColorF sunColor{ 0.1 + Math::Pow(tf, 1.0 / 2.0) * (night ? 0.1 : 0.9) };

					Graphics3D::SetSunDirection(sunDirection);
					Graphics3D::SetSunColor(sunColor);
					Graphics3D::SetGlobalAmbientColor(ColorF{ sky.zenithColor });
				}

				// set sky color
				{
					if (night)
					{
						sky.zenithColor = ColorF{ 0.3, 0.05, 0.1 }.lerp(ColorF{ 0.1, 0.1, 0.15 }, tf);
						sky.horizonColor = ColorF{ 0.1, 0.1, 0.15 }.lerp(ColorF{ 0.1, 0.1, 0.2 }, tf);
					}
					else
					{
						sky.zenithColor = ColorF{ 0.4, 0.05, 0.1 }.lerp(ColorF{ 0.15, 0.24, 0.56 }, tf);
						sky.horizonColor = ColorF{ 0.2, 0.05, 0.15 }.lerp(ColorF{ 0.3, 0.4, 0.5 }, tf);
					}
				}

				// set parameters
				{
					sky.starBrightness = Math::Saturate(1.0 - Pow(Abs(1.0 - starCenteredTime) * 1.8, 4));
					sky.fogHeightSky = (1.0 - tf);
					sky.cloudColor = ColorF{ 0.02 + (night ? 0.0 : (0.98 * tc)) };
					sky.sunEnabled = (not night);
					sky.cloudTime = skyTime * sky.cloudScale * 40.0;
					sky.starTime = skyTime;
				}

				sky.draw();
			}
		}

		// [RenderTexture を 2D シーンに描画]
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}

		// 天空レンダリングのパラメータ設定
		if (showUI)
		{
			Rect{ 20, 20, 480, 76 }.draw();
			SimpleGUI::GetFont()(U"zenith:").draw(28, 24, ColorF{ 0.11 });
			Rect{ 100, 26, 28 }.draw(sky.zenithColor.gamma(2.2)).drawFrame(1, 0, ColorF{ 0.5 });
			SimpleGUI::GetFont()(U"horizon:").draw(148, 24, ColorF{ 0.11 });
			Rect{ 230, 26, 28 }.draw(sky.horizonColor.gamma(2.2)).drawFrame(1, 0, ColorF{ 0.5 });
			SimpleGUI::GetFont()(U"cloud:").draw(276, 24, ColorF{ 0.11 });
			Rect{ 340, 26, 28 }.draw(sky.cloudColor.gamma(2.2)).drawFrame(1, 0, ColorF{ 0.5 });
			SimpleGUI::GetFont()(U"sun:").draw(386, 24, ColorF{ 0.11 });
			Rect{ 430, 26, 28 }.draw(Graphics3D::GetSunColor().gamma(2.2)).drawFrame(1, 0, ColorF{ 0.5 });
			SimpleGUI::GetFont()(U"sunDir: {:.2f}   cloudTime: {:.1f}"_fmt(Graphics3D::GetSunDirection(), sky.cloudTime)).draw(28, 60, ColorF{ 0.11 });

			SimpleGUI::Slider(U"cloudiness: {:.3f}"_fmt(sky.cloudiness), sky.cloudiness, Vec2{ 20, 100 }, 180, 300);
			SimpleGUI::Slider(U"cloudScale: {:.2f}"_fmt(sky.cloudScale), sky.cloudScale, 0.0, 2.0, Vec2{ 20, 140 }, 180, 300);
			SimpleGUI::Slider(U"cloudHeight: {:.0f}"_fmt(sky.cloudPlaneHeight), sky.cloudPlaneHeight, 20.0, 6000.0, Vec2{ 20, 180 }, 180, 300);
			SimpleGUI::Slider(U"orientation: {:.0f}"_fmt(Math::ToDegrees(sky.cloudOrientation)), sky.cloudOrientation, 0.0, Math::TwoPi, Vec2{ 20, 220 }, 180, 300);
			SimpleGUI::Slider(U"fogHeightSky: {:.2f}"_fmt(sky.fogHeightSky), sky.fogHeightSky, Vec2{ 20, 260 }, 180, 300, false);
			SimpleGUI::Slider(U"star: {:.2f}"_fmt(sky.starBrightness), sky.starBrightness, Vec2{ 20, 300 }, 180, 300, false);
			SimpleGUI::Slider(U"starF: {:.2f}"_fmt(sky.starBrightnessFactor), sky.starBrightnessFactor, Vec2{ 20, 340 }, 180, 300);
			SimpleGUI::Slider(U"starSat: {:.2f}"_fmt(sky.starSaturation), sky.starSaturation, 0.0, 1.0, Vec2{ 20, 380 }, 180, 300);
			SimpleGUI::CheckBox(sky.sunEnabled, U"sun", Vec2{ 20, 420 }, 120, false);
			SimpleGUI::CheckBox(sky.cloudsEnabled, U"clouds", Vec2{ 150, 420 }, 120);
			SimpleGUI::CheckBox(sky.cloudsLightingEnabled, U"cloudsLighting", Vec2{ 280, 420 }, 220);
		}

		SimpleGUI::CheckBox(showUI, U"UI", Vec2{ 20, Scene::Height() - 100 });
		SimpleGUI::Slider(U"time: {:.2f}"_fmt(skyTime), skyTime, -2.0, 4.0, Vec2{ 20, Scene::Height() - 60 }, 120, Scene::Width() - 160);
	}
}
```
