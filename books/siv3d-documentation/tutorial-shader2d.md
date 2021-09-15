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

### 図形やテクスチャを描くときに使われるデフォルトのシェーダ

#### HLSL

```hlsl:example/shader/hlsl/default2d.hlsl
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
		float2 position	: POSITION;
		float2 uv		: TEXCOORD0;
		float4 color	: COLOR0;
	};

	//
	//	VS Output / PS Input
	//
	struct PSInput
	{
		float4 position	: SV_POSITION;
		float4 color	: COLOR0;
		float2 uv		: TEXCOORD0;
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
	result.position	= s3d::Transform2D(input.position, g_transform);
	result.color	= input.color * g_colorMul;
	result.uv		= input.uv;
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

#### GLSL

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


## 35.2 カスタムシェーダを使用する

```cpp

```


## 35.3 (Windows) コンパイル済みシェーダを作成・使用する

```cpp

```


## 35.4 R 成分と B 成分の入れ替え

```cpp

```


## 35.5 RGB シフト

```cpp

```


## 35.6 グレイスケール化

```cpp

```


## 35.7 ポスタライズ

```cpp

```


## 35.8 Poisson-Disk Sampling

```cpp

```


## 35.9 渦巻き効果

```cpp

```


## 35.10 マルチテクスチャ

```cpp

```


## 35.11 テクスチャによるマスク

```cpp

```


## 35.12 GPU 上でのライフゲームシミュレーション

```cpp

```


## 35.13 GPU 上でのライフゲームシミュレーション (Camera2D 使用)

```cpp

```


## 35.14 ゲーム画面に後処理としてシェーダを適用する

```cpp

```

