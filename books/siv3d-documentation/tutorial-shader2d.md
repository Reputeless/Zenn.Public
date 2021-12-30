---
title: "チュートリアル 35 | 2D カスタムシェーダ"
free: true
---

# 35. 2D カスタムシェーダ
2D グラフィックスがレンダーターゲットに描かれるとき、どのような頂点座標変換を行い、どのような色を出力するかは、「**頂点シェーダ**」と「**ピクセルシェ―ダ**」と呼ばれる、GPU 上で実行される 2 つのプログラムを通して計算・決定されます。カスタムシェーダ機能を使うと、そのプログラムを変更し、GPU の計算性能をフル活用した高度な描画プログラムを実装することができます。

OpenSiv3D v0.6.3 が対応するシェーダプログラミング言語は次の 3 つです。ターゲットとするプラットフォームに応じた言語を使用します。
- HLSL シェーダーモデル 4.0
- GLSL 4.1
- GLSL ES 3.0

| ターゲット | HLSL | GLSL | GLSL ES |
|:--:|:--:|:--:|:--:|
| Windows | ✔ | ✔<br>(エンジンオプションを変更) | |
| macOS | | ✔ | |
| Linux | | ✔ | |
| Web | | | ✔ | 

Windows (Direct3D) では HLSL, macOS/Linux (OpenGL) では GLSL, Web (WebGL) では GLSL ES で記述します。なお、Windows は `SIV3D_SET(EngineOption::Renderer::OpenGL);` を `#include <Siv3D.hpp>` の直後に記述することで OpenGL モードになり、その場合は GLSL を使います。

```cpp
# include <Siv3D.hpp>
SIV3D_SET(EngineOption::Renderer::OpenGL); // Windows で Direct3D の代わりに OpenGL を使用

void Main()
{
    // OpenGL による描画処理が行われる
}
```


## 35.1 デフォルトのシェーダ
カスタムのシェーダプログラムを書く前に、デフォルト使われている図形およびテクスチャ描画のシェーダプログラムを見てみましょう。これらを書き換えることで、容易にシェーダをカスタマイズできます。

デフォルトの 2D シェーダは、いずれも次のような定義済みの定数バッファを持っています。
- 頂点シェーダ
  - `VSConstants2D` (slot 0)
- ピクセルシェーダ
  - `PSConstants2D` (slot 0)

これらの定義済み定数バッファの内容やスロットインデックスは Siv3D エンジンが予約しています。カスタムシェーダで独自の定数バッファを使いたい場合、上記の 0 番のスロットインデックスは使用できません。


### HLSL
HLSL は 1 つのファイルに複数のシェーダ関数をまとめて記述できます。`default2d.hlsl` の `VS()` が、デフォルトの頂点シェーダ関数です。入力 `s3d::VSInput` を受け取り、定数バッファ `VSConstants2D` の座標変換情報と乗算カラーを適用した結果を `s3d::PSInput` 型で返します。

`PS_Shape()` が、デフォルトの図形描画用ピクセルシェーダ関数です。入力 `s3d::PSInput` を受け取り、出力するピクセルの RGBA カラー (`float4` 型) を求めます。

`PS_Texture()` が、デフォルトのテクスチャ描画用ピクセルシェーダ関数です。テクスチャ `g_texture0` と、サンプラー `g_sampler0` を使ってテクスチャから色を読み込みます。UV 座標として、頂点シェーダから渡される `input.uv` を使っています。

```hlsl:example/shader/hlsl/default2d.hlsl
//
//	Textures
//
Texture2D g_texture0 : register(t0);
SamplerState g_sampler0 : register(s0);

namespace s3d
{
	//
	//	VS Input
	//
	struct VSInput
	{
		float2 position : POSITION;
		float2 uv : TEXCOORD0;
		float4 color : COLOR0;
	};

	//
	//	VS Output / PS Input
	//
	struct PSInput
	{
		float4 position : SV_POSITION;
		float4 color : COLOR0;
		float2 uv : TEXCOORD0;
	};

	//
	//	Siv3D Functions
	//
	float4 Transform2D(float2 pos, float2x4 t)
	{
		return float4((t._13_14 + (pos.x * t._11_12) + (pos.y * t._21_22)), t._23_24);
	}
}

//
//	Constant Buffer
//
cbuffer VSConstants2D : register(b0)
{
	row_major float2x4 g_transform;
	float4 g_colorMul;
}

cbuffer PSConstants2D : register(b0)
{
	float4 g_colorAdd;
	float4 g_sdfParam;
	float4 g_sdfOutlineColor;
	float4 g_sdfShadowColor;
	float4 g_internal;
}

//
//	Functions
//
s3d::PSInput VS(s3d::VSInput input)
{
	s3d::PSInput result;
	result.position = s3d::Transform2D(input.position, g_transform);
	result.color = input.color * g_colorMul;
	result.uv = input.uv;
	return result;
}

float4 PS_Shape(s3d::PSInput input) : SV_TARGET
{
	return (input.color + g_colorAdd);
}

float4 PS_Texture(s3d::PSInput input) : SV_TARGET
{
	const float4 texColor = g_texture0.Sample(g_sampler0, input.uv);

	return ((texColor * input.color) + g_colorAdd);
}
```

### GLSL
GLSL では、1 つのファイルに 1 つのシェーダ関数を実装します。`default2d.vert` の `main()` が、デフォルトの頂点シェーダ関数です。VSInput の形式で入力座標を受け取り、定数バッファ `VSConstants2D` の座標変換情報と乗算カラーを適用した結果を `VSOutput` の形式で出力します。

`default2d_shape.frag` の `main()` が、デフォルトの図形描画用ピクセルシェーダ関数です。PSInput 形式で入力を受け取り、出力するピクセルの RGBA カラー (`vec4` 型) を `FragColor` に書き込みます。

`default2d_texture.frag` の `main()` が、デフォルトのテクスチャ描画用ピクセルシェーダ関数です。テクスチャサンプラー `Texture0` から色を読み込みます。UV 座標として、頂点シェーダから渡される `UV` を使っています。

頂点シェーダ
```glsl:example/shader/glsl/default2d.vert
# version 410

//
//	VSInput
//
layout(location = 0) in vec2 VertexPosition;
layout(location = 1) in vec2 VertexUV;
layout(location = 2) in vec4 VertexColor;

//
//	VSOutput
//
layout(location = 0) out vec4 Color;
layout(location = 1) out vec2 UV;
out gl_PerVertex
{
	vec4 gl_Position;
};

//
//	Siv3D Functions
//
vec4 s3d_Transform2D(const vec2 pos, const vec4 t[2])
{
	return vec4(t[0].zw + (pos.x * t[0].xy) + (pos.y * t[1].xy), t[1].zw);
}

//
//	Constant Buffer
//
layout(std140) uniform VSConstants2D
{
	vec4 g_transform[2];
	vec4 g_colorMul;
};

//
//	Functions
//
void main()
{
	gl_Position = s3d_Transform2D(VertexPosition, g_transform);

	Color = (VertexColor * g_colorMul);
	
	UV = VertexUV;
}
```

図形用のピクセルシェーダ

```glsl:example/shader/glsl/default2d_shape.frag
# version 410

//
//	PSInput
//
layout(location = 0) in vec4 Color;

//
//	PSOutput
//
layout(location = 0) out vec4 FragColor;

//
//	Constant Buffer
//
layout(std140) uniform PSConstants2D
{
	vec4 g_colorAdd;
	vec4 g_sdfParam;
	vec4 g_sdfOutlineColor;
	vec4 g_sdfShadowColor;
	vec4 g_internal;
};

//
//	Functions
//
void main()
{
	FragColor = (Color + g_colorAdd);
}
```

テクスチャ用のピクセルシェーダ

```glsl:example/shader/glsl/default2d_texture.frag
# version 410

//
//	Textures
//
uniform sampler2D Texture0;

//
//	PSInput
//
layout(location = 0) in vec4 Color;
layout(location = 1) in vec2 UV;

//
//	PSOutput
//
layout(location = 0) out vec4 FragColor;

//
//	Constant Buffer
//
layout(std140) uniform PSConstants2D
{
	vec4 g_colorAdd;
	vec4 g_sdfParam;
	vec4 g_sdfOutlineColor;
	vec4 g_sdfShadowColor;
	vec4 g_internal;
};

//
//	Functions
//
void main()
{
	vec4 texColor = texture(Texture0, UV);

	FragColor = ((texColor * Color) + g_colorAdd);
}
```


## 35.2 シェーダクラスとプラットフォームに応じたシェーダファイルの読み込み
頂点シェーダとピクセルシェーダはそれぞれ `VertexShader` と `PixelShader` クラスで扱います。しかし、HLSL ファイルの読み込み方と、GLSL ファイルの読み込み方は違うため、次のような文法を使ってシェーダを読み込みます。

```cpp
// Direct3D 環境では HLSL を、
// OpenGL 環境では GLSL を、
// Web 環境では GLSL ES を読み込んでピクセルシェーダを作成
PixelShader ps = HLSL{ ... } | GLSL{ ... } | ESSL{ ... };
```

上記のようにすると、一連のファイルのうち、実行環境に適したものを選択してシェーダを読み込むため、クロスプラットフォームアプリケーションのプログラムを共通のコードで記述できます。

実行するプラットフォームが固定の場合は、次のように記述しても問題ありません。

```cpp
// （Windows でしか実行しないプログラムでの書き方）
// HLSL を読み込んでピクセルシェーダを作成
PixelShader ps = HLSL{ ... };
```

`HLSL{}` は、ファイルパスとエントリーポイントとなる関数の名前、`GLSL{}` は、ファイル名と定数バッファ（定義済み定数バッファを含む）の名前とスロットインデックスの組の配列を記述します。

Direct3D と OpenGL 環境の両方をサポートするアプリケーションで、デフォルトのシェーダ 3 つを読み込むプログラムは次のようになります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const VertexShader vs2D = HLSL{ U"example/shader/hlsl/default2d.hlsl", U"VS" }
		| GLSL{ U"example/shader/glsl/default2d.vert", {{U"VSConstants2D", 0}} };

	const PixelShader ps2DShape = HLSL{ U"example/shader/hlsl/default2d.hlsl", U"PS_Shape" }
		| GLSL{ U"example/shader/glsl/default2d_shape.frag", {{U"PSConstants2D", 0}} };

	const PixelShader ps2DTexture = HLSL{ U"example/shader/hlsl/default2d.hlsl", U"PS_Texture" }
		| GLSL{ U"example/shader/glsl/default2d_texture.frag", {{U"PSConstants2D", 0}} };

	if ((not vs2D) || (not ps2DShape) || (not ps2DTexture))
	{
		throw Error{ U"Failed to load shader files" };
	}

	while (System::Update())
	{

	}
}
```


## 35.3 カスタムシェーダを適用する
`ScopedCustomShader2D` オブジェクトのコンストラクタに、ロードしたピクセルシェーダを渡すと、そのオブジェクトのスコープが有効な間、2D グラフィックスがそのカスタムピクセルシェーダを使用して描画されます。

次のサンプルプログラムでは、カスタムシェーダを使用していますが、シェーダプログラムの内容はデフォルトのシェーダと同じであるため、通常の描画と同じ描画結果になります。

![](/images/doc_v6/tutorial/35/3.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const VertexShader vs2D = HLSL{ U"example/shader/hlsl/default2d.hlsl", U"VS" }
		| GLSL{ U"example/shader/glsl/default2d.vert", {{U"VSConstants2D", 0}} };

	const PixelShader ps2DShape = HLSL{ U"example/shader/hlsl/default2d.hlsl", U"PS_Shape" }
		| GLSL{ U"example/shader/glsl/default2d_shape.frag", {{U"PSConstants2D", 0}} };

	const PixelShader ps2DTexture = HLSL{ U"example/shader/hlsl/default2d.hlsl", U"PS_Texture" }
		| GLSL{ U"example/shader/glsl/default2d_texture.frag", {{U"PSConstants2D", 0}} };

	if ((not vs2D) || (not ps2DShape) || (not ps2DTexture))
	{
		return;
	}

	const Texture texture{ U"example/windmill.png" };

	while (System::Update())
	{
		{
			const ScopedCustomShader2D shader{ vs2D, ps2DShape };
			Circle{ 600, 400, 100 }.draw(Palette::Orange);
		}

		{
			const ScopedCustomShader2D shader{ vs2D, ps2DTexture };
			texture.draw();
		}
	}
}
```


## 35.4 R 成分と B 成分の入れ替え

次のプログラムでは、テクスチャの R 成分と B 成分を入れ替えて描画するカスタムピクセルシェーダを使用します。

![](/images/doc_v6/tutorial/35/4.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture windmill{ U"example/windmill.png" };
	const PixelShader ps = HLSL{ U"example/shader/hlsl/rgb_to_bgr.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/rgb_to_bgr.frag", {{U"PSConstants2D", 0}} };

	if (not ps)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	while (System::Update())
	{
		{
			// R と B を入れ替えるピクセルシェーダを開始
			const ScopedCustomShader2D shader{ ps };
			windmill.draw(10, 10);
		}
	}
}
```

## 35.5 (Windows) コンパイル済みシェーダを作成・使用する
Windows 版では、`Platform::Windows::Shader::CompileHLSLToFile()` 関数を使うと、HLSL ファイルをあらかじめコンパイルすることができます。コンパイル済みのシェーダファイルは PixelShader でそのまま読み込むことができ、実行時の処理を削減することができます。Windows 向けに、カスタムシェーダを使用するアプリケーションをリリースするときは、すべてのシェーダをコンパイル済みにしておくことを推奨します。

![](/images/doc_v6/tutorial/35/5.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// コンパイル済み HLSL シェーダを作って保存する（1 度だけ作成すれば OK）
	if (not Platform::Windows::CompileHLSLToFile(U"example/shader/hlsl/rgb_to_bgr.hlsl", U"rgb_to_bgr.ps", ShaderStage::Pixel, U"PS"))
	{
		throw Error{ U"Failed to save a shader file" };
	}

	const Texture windmill{ U"example/windmill.png" };

	// コンパイル済みの HLSL シェーダをロード
	const PixelShader ps = HLSL{ U"rgb_to_bgr.ps" };

	if (not ps)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	while (System::Update())
	{
		{
			// R と B を入れ替えるピクセルシェーダを開始
			const ScopedCustomShader2D shader{ ps };
			windmill.draw(10, 10);
		}
	}
}
```


## 35.6 RGB シフト
RGB 成分ごとに UV 座標をずらすカスタムピクセルシェーダのサンプルです。

![](/images/doc_v6/tutorial/35/6.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture windmill{ U"example/windmill.png" };
	const PixelShader ps = HLSL{ U"example/shader/hlsl/rgb_shift.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/rgb_shift.frag", {{U"PSConstants2D", 0}} };

	if (not ps)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	while (System::Update())
	{
		{
			// RGB シフト用のシェーダを開始
			const ScopedCustomShader2D shader{ ps };
			windmill.draw(10, 10);
		}
	}
}
```


## 35.7 グレイスケール化
グレイスケール化するカスタムピクセルシェーダのサンプルです。

![](/images/doc_v6/tutorial/35/7.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture windmill{ U"example/windmill.png" };
	const PixelShader ps = HLSL{ U"example/shader/hlsl/grayscale.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/grayscale.frag", {{U"PSConstants2D", 0}} };

	if (not ps)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	while (System::Update())
	{
		{
			// グレースケール化するピクセルシェーダを開始
			const ScopedCustomShader2D shader{ ps };
			windmill.draw(10, 10);
		}
	}
}
```


## 35.8 ポスタライズ
ポスタライズ効果をかけるカスタムピクセルシェーダのサンプルです。

![](/images/doc_v6/tutorial/35/8.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture windmill{ U"example/windmill.png" };
	const PixelShader ps = HLSL{ U"example/shader/hlsl/posterize.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/posterize.frag", {{U"PSConstants2D", 0}} };

	if (not ps)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	while (System::Update())
	{
		{
			// ポスタライズするピクセルシェーダを開始
			const ScopedCustomShader2D shader{ ps };
			windmill.draw(10, 10);
		}
	}
}
```


## 35.9 渦巻き効果
定数バッファを使うと、C++ プログラムからシェーダプログラムに値を渡せます。定数バッファにする構造体は 16 の倍数のサイズで用意します。1 つの定数バッファの最大サイズは 64KB です。

カスタムピクセルシェーダで新しい定数バッファを使うには、シェーダプログラムに定数バッファを追加します。`GLSL{ ... }` には、対応する追加の定数バッファの名前とインデックスを追加します。

C++ プログラムでは、`ConstantBuffer<>` クラステンプレートで定数バッファにする構造体のラッパーを作り、このクラス経由で値を操作します。描画前に、`Graphics2D::SetVSConstantBuffer()` または `Graphics2D::SetPSConstantBuffer()` によって、適切な定数バッファのスロットにデータを転送します。

次のサンプルは、定数バッファに設定された値をもとに渦巻効果でテクスチャを描画するカスタムピクセルシェーダを使う例です。

![](/images/doc_v6/tutorial/35/9.png)
```cpp
# include <Siv3D.hpp>

// 定数バッファ (PS_1)
struct Swirl
{
	// 回転
	float angle;
};

void Main()
{
	const Texture windmill{ U"example/windmill.png" };
	const PixelShader ps = HLSL{ U"example/shader/hlsl/swirl.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/swirl.frag", {{U"PSConstants2D", 0}, {U"Swirl", 1}} };

	if (not ps)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	// 定数バッファ
	ConstantBuffer<Swirl> cb;

	while (System::Update())
	{
		cb->angle = static_cast<float>(Math::Sin(Scene::Time()) * 720_deg);

		{
			// 定数バッファを、ピクセルシェーダの定数バッファスロット 1 に設定
			Graphics2D::SetPSConstantBuffer(1, cb);

			// 渦巻き効果のシェーダを開始
			const ScopedCustomShader2D shader{ ps };
			windmill.draw(10, 10);
		}
	}
}
```


## 35.10 Poisson-Disk Sampling
Poisson-Disk Sampling をおこなうカスタムピクセルシェーダのサンプルです。

![](/images/doc_v6/tutorial/35/10.png)
```cpp
# include <Siv3D.hpp>

// 定数バッファ (PS_1)
struct PoissonDisk
{
	// 1 ピクセルあたりの UV サイズ
	Float2 pixelSize;

	// サンプリング半径
	float diskRadius;
};

void Main()
{
	const Texture windmill{ U"example/windmill.png" };
	const PixelShader ps = HLSL{ U"example/shader/hlsl/poisson_disk.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/poisson_disk.frag", {{U"PSConstants2D", 0}, {U"PoissonDisk", 1}} };

	if (not ps)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	// 定数バッファ
	ConstantBuffer<PoissonDisk> cb;
	cb->pixelSize = Float2{ 1.0, 1.0 } / windmill.size();

	// サンプリング半径
	double diskRadius = 0.0;

	while (System::Update())
	{
		// サンプリング半径をスライダーで変更
		if (SimpleGUI::Slider(U"diskRadius", diskRadius, 0.0, 8.0, Vec2{ 10, 340 }, 120, 200))
		{
			cb->diskRadius = static_cast<float>(diskRadius);
		}

		{
			// 定数バッファを、ピクセルシェーダの定数バッファスロット 1 に設定
			Graphics2D::SetPSConstantBuffer(1, cb);

			// Poisson-Disk Sampling 用のシェーダを開始
			const ScopedCustomShader2D shader{ ps };
			windmill.draw(10, 10);
		}
	}
}
```


## 35.11 マルチテクスチャ
通常のテクスチャ描画時、ピクセルシェーダプログラムには、`.draw()` で使われたテクスチャがテクスチャインデックス 0 にセットされています。`Graphics2D::SetVSTexture()`, `Graphics2D::SetPSTexture()` を使うと、それ以外のスロットに追加のテクスチャをセットし、シェーダプログラムで複数のテクスチャを参照できるようになります。スロットにセットしたテクスチャを解除するには、これらの関数に `none` を渡します。

次のサンプルは、1 回の `.draw()` で 2 つのテクスチャの内容をブレンドして表示するカスタムシェーダを使う例です。

![](/images/doc_v6/tutorial/35/11.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture emoji{ U"🐈"_emoji };
	const Texture windmill{ U"example/windmill.png", TextureDesc::Mipped };
	const PixelShader ps = HLSL{ U"example/shader/hlsl/multi_texture_blend.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/multi_texture_blend.frag", {{U"PSConstants2D", 0}} };

	if (not ps)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	while (System::Update())
	{
		// windmill をピクセルシェーダのテクスチャスロット [1] にセット 
		Graphics2D::SetPSTexture(1, windmill);
		{
			// マルチテクスチャによるブレンドのシェーダを開始
			const ScopedCustomShader2D shader{ ps };
			emoji.scaled(2).drawAt(Scene::Center());
		}
	}
}
```


## 35.12 テクスチャによるマスク処理
レンダーテクスチャにリンゴの形のマスク画像を描き、そのマスク画像に基づいて風車の写真を切り抜いて描くサンプルです。

![](/images/doc_v6/tutorial/35/12.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを 960x600 にリサイズ
	Window::Resize(960, 600);

	// シーンの背景色を淡い水色に設定
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ Emoji::CreateSilhouetteImage(U"🍎"), TextureDesc::Mipped };
	const Texture windmill{ U"example/windmill.png", TextureDesc::Mipped };

	const PixelShader ps = HLSL{ U"example/shader/hlsl/multi_texture_mask.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/multi_texture_mask.frag", {{U"PSConstants2D", 0}} };

	if (not ps)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	// レンダーテクスチャを作成
	const RenderTexture renderTexture{ 480, 320 };

	while (System::Update())
	{
		// レンダーテクスチャをクリア
		renderTexture.clear(ColorF{ 0.0, 1.0 });
		{
			// レンダーターゲットを renderTexture に設定
			const ScopedRenderTarget2D target{ renderTexture };
			emoji.scaled(2).rotated(Scene::Time() * 60_deg).drawAt(renderTexture.size() / 2);
		}

		// 描画された renderTexture を表示
		renderTexture.draw(0, 140);

		// renderTexture をピクセルシェーダのテクスチャスロット [1] にセット 
		Graphics2D::SetPSTexture(1, renderTexture);
		{
			// マルチテクスチャによるマスクのシェーダを開始
			const ScopedCustomShader2D shader{ ps };
			windmill.draw(480, 140);
		}
	}
}
```


## 35.13 GPU 上でのライフゲームシミュレーション
ライフゲームをピクセルシェーダで実行するサンプルです。

![](/images/doc_v6/tutorial/35/13.png)
```cpp
# include <Siv3D.hpp>

// 定数バッファ (PS_1)
struct GameOfLife
{
	Float2 pixelSize;
};

void Main()
{
	// ウィンドウを 1280x720 にリサイズ
	Window::Resize(1280, 720);

	// セルの数 (1280x720)
	constexpr Size FieldSize{ 1280, 720 };

	const PixelShader ps = HLSL{ U"example/shader/hlsl/game_of_life.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/game_of_life.frag", {{U"PSConstants2D", 0}, {U"GameOfLife", 1}} };

	if (not ps)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	// 定数バッファ
	const ConstantBuffer<GameOfLife> cb{ { (Float2{ 1.0f, 1.0f } / FieldSize) } };

	// レンダーテクスチャ 0
	RenderTexture renderTexture0{ Image{FieldSize, Arg::generator = []() { return Color(RandomBool() * 255); }} };

	// レンダーテクスチャ 1
	RenderTexture renderTexture1{ FieldSize, ColorF{0.0} };

	while (System::Update())
	{
		{
			// テクスチャフィルタなし
			const ScopedRenderStates2D sampler{ SamplerState::ClampNearest };

			// 現在の状態を画面に描く
			renderTexture0.draw(ColorF{ 0.0, 1.0, 0.0 });

			{
				// ライフゲーム用のシェーダ
				Graphics2D::SetPSConstantBuffer(1, cb);
				const ScopedCustomShader2D shader{ ps };

				// 更新後の状態を描く renderTexture1 に描く
				const ScopedRenderTarget2D target{ renderTexture1 };
				renderTexture0.draw();
			}
		}

		// renderTexture0 と renderTexture1 を入れ替える
		std::swap(renderTexture0, renderTexture1);
	}
}
```


## 35.14 GPU 上でのライフゲームシミュレーション (Camera2D 使用)
35.13 のプログラムに、Camera2D を組み込んだサンプルです。

![](/images/doc_v6/tutorial/35/14.png)
```cpp
# include <Siv3D.hpp>

// 定数バッファ (PS_1)
struct GameOfLife
{
	Float2 pixelSize;
};

void Main()
{
	// ウィンドウを 1280x720 にリサイズ
	Window::Resize(1280, 720);

	// セルの数 (1280x720)
	constexpr Size FieldSize{ 1280, 720 };

	const PixelShader ps = HLSL{ U"example/shader/hlsl/game_of_life.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/game_of_life.frag", {{U"PSConstants2D", 0}, {U"GameOfLife", 1}} };

	if (not ps)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	// 定数バッファ
	const ConstantBuffer<GameOfLife> cb{ { (Float2{ 1.0f, 1.0f } / FieldSize) } };

	// レンダーテクスチャ 0
	RenderTexture renderTexture0{ Image{ FieldSize, Arg::generator = []() { return Color(RandomBool() * 255); }} };

	// レンダーテクスチャ 1
	RenderTexture renderTexture1{ FieldSize, ColorF{ 0. } };

	// 2D カメラ
	Camera2D camera{ Vec2{0, 0}, 4.0 };

	while (System::Update())
	{
		// 2D カメラを更新
		camera.update();
		{
			// テクスチャフィルタなし
			const ScopedRenderStates2D sampler{ SamplerState::ClampNearest };

			{
				// 2D カメラの設定から Transformer2D を作成
				const auto t = camera.createTransformer();

				// 現在の状態を画面に描く
				renderTexture0.draw(ColorF{ 0.0, 1.0, 0.0 });

				// 2D カメラの UI を描画
				camera.draw(Palette::Orange);
			}

			{
				// ライフゲーム用のシェーダ
				Graphics2D::SetPSConstantBuffer(1, cb);
				const ScopedCustomShader2D shader{ ps };

				// 更新後の状態を描く renderTexture1 に描く
				const ScopedRenderTarget2D target{ renderTexture1 };
				renderTexture0.draw();
			}
		}

		// renderTexture0 と renderTexture1 を入れ替える
		std::swap(renderTexture0, renderTexture1);
	}
}
```


## 35.15 ゲーム画面に後処理としてシェーダを適用する
レンダーテクスチャにゲームのグラフィックスを描画し、そのテクスチャをシーンに描画する際にシェーダを適用することで、ゲーム画面全体に後処理としてカスタムピクセルシェーダを適用するサンプルです。

![](/images/doc_v6/tutorial/35/15.png)
```cpp
# include <Siv3D.hpp>

// 渦巻き効果のピクセルシェーダ用の
// 定数バッファ (PS_1)
struct Swirl
{
	// 回転
	float angle;
};

void Main()
{
	// ゲームの描画用のレンダーテクスチャ
	const MSRenderTexture renderTexture{ Scene::Size() };

	// グレースケール化するピクセルシェーダ
	const PixelShader psGrayscale = HLSL{ U"example/shader/hlsl/grayscale.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/grayscale.frag", {{U"PSConstants2D", 0}} };

	if (not psGrayscale)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	// 渦巻き効果のピクセルシェーダ
	const PixelShader psSwirl = HLSL{ U"example/shader/hlsl/swirl.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/swirl.frag", {{U"PSConstants2D", 0}, {U"Swirl", 1}} };

	if (not psSwirl)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	// 渦巻き効果のピクセルシェーダ用の定数バッファ
	ConstantBuffer<Swirl> cb;

	// ガウスぼかしの中間で使うレンダーテクスチャ
	const RenderTexture gaussianA1{ renderTexture.size() }, gaussianB1{ renderTexture.size() };
	const RenderTexture gaussianA4{ renderTexture.size() / 4 }, gaussianB4{ renderTexture.size() / 4 };
	const RenderTexture gaussianA8{ renderTexture.size() / 8 }, gaussianB8{ renderTexture.size() / 8 };

	// ゲーム画面に適用するエフェクト
	size_t effectIndex = 0;

	// 背景色
	constexpr ColorF backgroundColor{ 0.3, 0.4, 0.5 };

	// ブロックのサイズ
	constexpr Size brickSize{ 40, 20 };

	// ボールの速さ
	constexpr double speed = 480.0;

	// ボールの速度
	Vec2 ballVelocity{ 0, -speed };

	// ボール
	Circle ball{ 400, 400, 8 };

	// ブロックの配列
	Array<Rect> bricks;

	// 横 (Scene::Width() / blockSize.x) 個、縦 5 個のブロックを配列に追加する
	for (auto p : step(Size{ (Scene::Width() / brickSize.x), 5 }))
	{
		bricks << Rect{ (p.x * brickSize.x), (60 + p.y * brickSize.y), brickSize };
	}

	// 自動プレイ用のパラメータ
	double paddleCenter = 400;
	double randomOffset = 0.0;

	while (System::Update())
	{
		// 自動プレイ
		paddleCenter = Math::Damp(paddleCenter, ball.x + ballVelocity.x * 1.2 + randomOffset, 0.9, Scene::DeltaTime());

		// パドル
		const RectF paddle{ Arg::center(paddleCenter, 500), 120, 10 };

		// ボールを移動
		ball.moveBy(ballVelocity * Scene::DeltaTime());

		// ブロックを順にチェック
		for (auto it = bricks.begin(); it != bricks.end(); ++it)
		{
			// ブロックとボールが交差していたら
			if (it->intersects(ball))
			{
				// ボールの向きを反転する
				(it->bottom().intersects(ball) || it->top().intersects(ball)
					? ballVelocity.y : ballVelocity.x) *= -1;

				// ブロックを配列から削除（イテレータが無効になるので注意）
				bricks.erase(it);

				// これ以上チェックしない
				break;
			}
		}

		// 天井にぶつかったらはね返る
		if (ball.y < 0 && ballVelocity.y < 0)
		{
			ballVelocity.y *= -1;
		}

		// 左右の壁にぶつかったらはね返る
		if ((ball.x < 0 && ballVelocity.x < 0)
			|| (Scene::Width() < ball.x && 0 < ballVelocity.x))
		{
			ballVelocity.x *= -1;
		}

		// パドルにあたったらはね返る
		if (0 < ballVelocity.y && paddle.intersects(ball))
		{
			// パドルの中心からの距離に応じてはね返る方向を変える
			ballVelocity = Vec2{ (ball.x - paddle.center().x) * 10, -ballVelocity.y }.setLength(speed);
			randomOffset = Random(-40, 40);
		}

		// レンダーテクスチャをクリア
		renderTexture.clear(backgroundColor);
		{
			// レンダーターゲットを renderTexture に設定
			const ScopedRenderTarget2D target{ renderTexture };

			for (auto y : Range(1, 5))
			{
				Line{ 0, y * 100, 800, y * 100 }.draw(1, Palette::Gray);
			}

			for (auto x : Range(1, 7))
			{
				Line{ x * 100, 0, x * 100, 600 }.draw(1, Palette::Gray);
			}

			// すべてのブロックを描画する
			for (const auto& brick : bricks)
			{
				brick.stretched(-1).draw(HSV{ brick.y - 40 });
			}

			// ボールを描く
			ball.draw();

			// パドルを描く
			paddle.draw();
		}

		// resolve のために描画を完了させる
		Graphics2D::Flush();

		// マルチサンプル・テクスチャを resolve
		renderTexture.resolve();

		if (effectIndex == 0) // ゲーム画面をそのまま描画
		{
			renderTexture.draw();
		}
		else if (effectIndex == 1) // ゲーム画面をグレースケール化して描画
		{
			// グレースケール化するピクセルシェーダを開始
			const ScopedCustomShader2D shader{ psGrayscale };
			renderTexture.draw();
		}
		else if (effectIndex == 2) // ゲーム画面を渦巻き効果で描画
		{
			cb->angle = static_cast<float>(Math::Sin(Scene::Time()) * 240_deg);

			// 定数バッファを設定
			Graphics2D::SetPSConstantBuffer(1, cb);

			// 渦巻き効果のシェーダを開始
			const ScopedCustomShader2D shader{ psSwirl };
			renderTexture.draw();
		}
		else if (effectIndex == 3) // ゲーム画面をガウスぼかしで描画
		{
			// [オリジナル]->[ガウスぼかし]->[1/4サイズ]->[ガウスぼかし]
			Shader::GaussianBlur(renderTexture, gaussianB1, gaussianA1);
			Shader::Downsample(gaussianA1, gaussianA4);
			Shader::GaussianBlur(gaussianA4, gaussianB4, gaussianA4);
			Shader::Downsample(gaussianA4, gaussianA8);
			Shader::GaussianBlur(gaussianA8, gaussianB8, gaussianA8);

			gaussianA8.scaled(8).draw();
		}

		// エフェクトの種類の選択
		SimpleGUI::RadioButtons(effectIndex, { U"Default", U"Grayscale", U"Swirl", U"GaussianBlur" }, Vec2{ 10, 10 });
	}
}
```

