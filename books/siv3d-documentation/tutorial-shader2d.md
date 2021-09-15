---
title: "チュートリアル 35 | 2D カスタムシェーダ"
free: true
---

# 35. 2D カスタムシェーダ
2D グラフィックスがレンダーターゲットに描かれるとき、どのような頂点座標変換を行い、どのような色を出力するかは、「**頂点シェーダ**」と「**ピクセルシェ―ダ**」と呼ばれる、GPU 上で実行される 2 つのプログラムを通して計算・決定されます。カスタムシェーダ機能を使うと、そのプログラムを変更し、GPU の計算性能をフル活用した高度な描画プログラムを実装することができます。

OpenSiv3D v0.6.0 が対応するシェーダプログラミング言語は次の 3 つです。ターゲットとするプラットフォームに応じた言語を使用します。
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

これらの定義済み定数バッファの内容やスロットインデックスは Siv3D エンジンが予約しているため、カスタムシェーダを作成する場合でも変更はできません。


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
GLSL では、1 つのファイルに 1 つのシェーダ関数を実装します。`default2d.vert` の `VS()` が、デフォルトの頂点シェーダ関数です。VSInput の形式で入力座標を受け取り、定数バッファ `VSConstants2D` の座標変換情報と乗算カラーを適用した結果を `VSOutput` の形式で出力します。

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

Direct3D と OpenGL 環境の両方をサポートするプログラムで、デフォルトのシェーダ 3 つを読み込むプログラムは次のようになります。

`HLSL{}` は、ファイルパスとエントリーポイントとなる関数の名前、`GLSL{}` は、ファイル名と定数バッファの名前とスロットインデックスの組の配列を記述します。

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

```cpp

```


## 35.4 (Windows) コンパイル済みシェーダを作成・使用する

```cpp

```


## 35.5 R 成分と B 成分の入れ替え

```cpp

```


## 35.6 RGB シフト

```cpp

```


## 35.7 グレイスケール化

```cpp

```


## 35.8 ポスタライズ

```cpp

```


## 35.9 Poisson-Disk Sampling

```cpp

```


## 35.10 渦巻き効果

```cpp

```


## 35.11 マルチテクスチャ

```cpp

```


## 35.12 テクスチャによるマスク

```cpp

```


## 35.13 GPU 上でのライフゲームシミュレーション

```cpp

```


## 35.14 GPU 上でのライフゲームシミュレーション (Camera2D 使用)

```cpp

```


## 35.15 ゲーム画面に後処理としてシェーダを適用する

```cpp

```

