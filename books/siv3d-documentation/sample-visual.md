---
title: "サンプル集 | ビジュアル表現"
free: true
---

## 図形や絵文字に影を付ける
![](/images/doc_v6/sample/visual/2d-shadow.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });
	const Texture emoji{ U"🐈"_emoji };

	const RenderTexture shadowTexture{ Scene::Size(), ColorF{ 1.0, 0.0 } };
	const RenderTexture internalTexture{ shadowTexture.size() };

	while (System::Update())
	{
		const double angle = (Scene::Time() * 10_deg);

		// 影を作る図形を shadowTexture に描く
		{
			const ScopedRenderTarget2D target{ shadowTexture.clear(ColorF{ 1.0, 0.0 }) };
			const ScopedRenderStates2D blend{ BlendState::MaxAlpha };
			const Transformer2D transform{ Mat3x2::Translate(3, 3) };

			Circle{ 200, 200, 100 }.draw();
			Shape2D::Star(120, Vec2{ 400, 400 }, angle).draw();
			Shape2D::RectBalloon(Rect{ 500, 103, 200, 100 }, Vec2{ 480, 240 }).drawFrame(10);
			emoji.rotated(angle).drawAt(600, 500);
		}

		// shadowTexture を 2 回ガウスぼかし
		{
			Shader::GaussianBlur(shadowTexture, internalTexture, shadowTexture);
			Shader::GaussianBlur(shadowTexture, internalTexture, shadowTexture);
		}

		shadowTexture.draw(ColorF{ 0.0, 0.5 });

		Circle{ 200, 200, 100 }.draw();
		Shape2D::Star(120, Vec2{ 400, 400 }, angle).draw(Palette::Yellow);
		Shape2D::RectBalloon(Rect{ 500, 100, 200, 100 }, Vec2{ 480, 240 })
			.drawFrame(10, Palette::Seagreen);
		emoji.rotated(angle).drawAt(600, 500);
	}
}
```


## 紙から切り抜いたような描画
![](/images/doc_v6/sample/visual/paper.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 1.0, 0.9, 0.7 });

	constexpr Vec2 pos{ 220, 60 };

	const Image image{ U"example/siv3d-kun.png" };

	const Texture texture(image);

	// 画像の輪郭から Polygon を作成
	const Polygon polygon = image.alphaToPolygon(160, AllowHoles::No);

	// 凸包を計算
	const Polygon convexHull = polygon.computeConvexHull();

	// Polygon を太らせる
	const Polygon largeConvex = convexHull.calculateBuffer(20);

	// 影用の画像
	Image shadowImage{ Scene::Size(), Color{ 255, 0 } };

	// 影のもとになる図形を書き込む
	convexHull.calculateBuffer(10).movedBy(pos + Vec2{ 10, 10 }).overwrite(shadowImage, Color{ 255 });

	// それをぼかしたものを影用テクスチャにする
	const Texture shadow{ shadowImage.gaussianBlurred(40, 40) };

	while (System::Update())
	{
		shadow.draw(ColorF{ 0.0, 0.5 });

		largeConvex.draw(pos, ColorF{ 0.96, 0.98, 1.0 });

		texture.draw(pos);
	}
}
```


## 付箋
![](/images/doc_v6/sample/visual/sticky.png)
```cpp
# include <Siv3D.hpp>

void DrawStickyNote(const RectF& rect, const ColorF& noteColor)
{
	// 少しだけ回転させて影を描く
	{
		const Transformer2D t{ Mat3x2::Rotate(2_deg, rect.pos) };

		rect.stretched(-2, 1, 1, -4).drawShadow(Vec2{ 0, 0 }, 12, 0, ColorF{ 0.0, 0.4 });
	}

	rect.draw(noteColor);
}

void Main()
{
	Scene::SetBackground(ColorF{ 1.0, 0.98, 0.96 });

	const Font font{ 36, Typeface::Bold };

	while (System::Update())
	{
		for (auto i : step(10))
		{
			const RectF rect{ (60 + i / 5 * 280), (20 + i % 5 * 90), 230, 70 };

			DrawStickyNote(rect, HSV{ (i * 36), 0.46, 1.0 });

			font(U"Text").draw(rect.pos.movedBy(20, 10), ColorF{ 0.1, 0.95 });
		}
	}
}
```


## テクスチャの反射
![](/images/doc_v6/sample/visual/reflection.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<Texture> textures =
	{
		Texture{ U"💹"_emoji },
		Texture{ U"📅"_emoji },
		Texture{ U"🏡"_emoji },
	};

	constexpr Size imageSize = Emoji::ImageSize;

	while (System::Update())
	{
		Rect{ 0, 300, 800, 300 }.draw(ColorF{ 0.2, 0.3, 0.4 });

		for (auto [i, texture] : Indexed(textures))
		{
			const Vec2 pos{ (140 + i * 200), 220 };

			texture.draw(pos);

			// 反射するテクスチャ
			texture(0, (imageSize.y / 2), imageSize.x, (imageSize.y / 2))
				.flipped()
				.draw(pos.x, (pos.y + imageSize.y),
					Arg::top = AlphaF(0.8), Arg::bottom = AlphaF(0.0));
		}
	}
}
```


## テキストの登場
![](/images/doc_v6/sample/visual/text-effect.png)
```cpp
# include <Siv3D.hpp>

// Glyph とエフェクトの関数を組み合わせてテキストを描画
void DrawText(const Font& font, const String& text, const Vec2& pos, const ColorF& color, double t,
	void f(const Vec2&, const Glyph&, const ColorF&, double), double characterdPerSec)
{
	Vec2 penPos = pos;

	for (auto[i, glyph] : Indexed(font.getGlyphs(text)))
	{
		if (glyph.codePoint == U'\n')
		{
			penPos.x = pos.x;
			penPos.y += font.height();
			continue;
		}

		const double targetTime = (i * characterdPerSec);

		if (t < targetTime)
		{
			break;
		}

		f(penPos, glyph, color, (t - targetTime));

		penPos.x += glyph.xAdvance;
	}
}

// 文字が上からゆっくり降ってくる表現
void TextEffect1(const Vec2& penPos, const Glyph& glyph, const ColorF& color, double t)
{
	const double y = EaseInQuad(Saturate(1 - t / 0.3)) * -20.0;
	const double a = Min(t / 0.3, 1.0);
	glyph.texture.draw(penPos + glyph.getOffset() + Vec2{ 0, y }, ColorF{ color, a });
}

// 文字が勢いよく現れる表現
void TextEffect2(const Vec2& penPos, const Glyph& glyph, const ColorF& color, double t)
{
	const double s = Min(t / 0.1, 1.0);
	const double a = Min(t / 0.2, 1.0);
	glyph.texture.scaled(3.0 - s * 2).draw(penPos + glyph.getOffset(), ColorF{ color, a });
}

// 落ちてきた文字がしばらく揺れる表現
void TextEffect3(const Vec2& penPos, const Glyph& glyph, const ColorF& color, double t)
{
	const double angle = Sin(t * 1440_deg) * 25_deg * Saturate(1.0 - t / 0.6);
	const double y = Saturate(1 - t / 0.05) * -20.0;
	glyph.texture.rotated(angle).draw(penPos + glyph.getOffset() + Vec2{ 0, y }, color);
}

void Main()
{
	const Font font{ 32, Typeface::Bold };
	const String text = U"Lorem ipsum dolor sit amet, consectetur\n"
		U"adipiscing elit, sed do eiusmod tempor\n"
		U"incididunt ut labore et dolore magna aliqua.";

	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 620, 520 }))
		{
			stopwatch.restart();
		}

		const double t = stopwatch.sF();
		DrawText(font, text, Vec2{ 40, 40 }, Palette::Skyblue, t, TextEffect1, 0.1);
		DrawText(font, text, Vec2{ 40, 200 }, Palette::Orange, t, TextEffect2, 0.1);
		DrawText(font, text, Vec2{ 40, 360 }, Palette::Seagreen, t, TextEffect3, 0.1);
	}
}
```


## 2D ライトブルーム
![](/images/doc_v6/sample/visual/2d-bloom.png)
```cpp
# include <Siv3D.hpp>

void DrawScene()
{
	Circle{ 680, 40, 20 }.draw();
	Rect{ Arg::center(680, 110), 30 }.draw();
	Triangle{ 680, 180, 40 }.draw();

	Circle{ 740, 40, 20 }.draw(HSV{ 0 });
	Rect{ Arg::center(740, 110), 30 }.draw(HSV{ 120 });
	Triangle{ 740, 180, 40 }.draw(HSV{ 240 });

	Circle{ 50, 200, 300 }.drawFrame(4);
	Circle{ 550, 450, 200 }.drawFrame(4);

	for (auto i : step(12))
	{
		const double angle = (i * 30_deg + Scene::Time() * 5_deg);
		const Vec2 pos = OffsetCircular{ Scene::Center(), 200, angle };
		Circle{ pos, 8 }.draw(HSV{ i * 30 });
	}
}

void Main()
{
	constexpr Size sceneSize{ 800, 600 };
	RenderTexture gaussianA1{ sceneSize }, gaussianB1{ sceneSize };
	RenderTexture gaussianA4{ sceneSize / 4 }, gaussianB4{ sceneSize / 4 };
	RenderTexture gaussianA8{ sceneSize / 8 }, gaussianB8{ sceneSize / 8 };

	double a1 = 0.0, a4 = 0.0, a8 = 0.0;

	while (System::Update())
	{
		// 通常のシーン描画
		DrawScene();

		{
			// ガウスぼかし用テクスチャにもう一度シーンを描く
			{
				const ScopedRenderTarget2D target{ gaussianA1.clear(ColorF{ 0.0 }) };
				const ScopedRenderStates2D blend{ BlendState::Additive };
				DrawScene();
			}

			// オリジナルサイズのガウスぼかし (A1)
			// A1 を 1/4 サイズにしてガウスぼかし (A4)
			// A4 を 1/2 サイズにしてガウスぼかし (A8)
			Shader::GaussianBlur(gaussianA1, gaussianB1, gaussianA1);
			Shader::Downsample(gaussianA1, gaussianA4);
			Shader::GaussianBlur(gaussianA4, gaussianB4, gaussianA4);
			Shader::Downsample(gaussianA4, gaussianA8);
			Shader::GaussianBlur(gaussianA8, gaussianB8, gaussianA8);
		}

		{
			const ScopedRenderStates2D blend{ BlendState::Additive };

			if (a1)
			{
				gaussianA1.resized(sceneSize).draw(ColorF{ a1 });
			}

			if (a4)
			{
				gaussianA4.resized(sceneSize).draw(ColorF{ a4 });
			}

			if (a8)
			{
				gaussianA8.resized(sceneSize).draw(ColorF{ a8 });
			}
		}

		SimpleGUI::Slider(U"a1: {:.1f}"_fmt(a1), a1, 0.0, 4.0, Vec2{ 20, 20 });
		SimpleGUI::Slider(U"a4: {:.1f}"_fmt(a4), a4, 0.0, 4.0, Vec2{ 20, 60 });
		SimpleGUI::Slider(U"a8: {:.1f}"_fmt(a8), a8, 0.0, 4.0, Vec2{ 20, 100 });

		if (SimpleGUI::Button(U"0, 0, 0", Vec2{ 20, 140 }))
		{
			a1 = a4 = a8 = 0.0;
		}

		if (SimpleGUI::Button(U"0, 0, 1", Vec2{ 20, 180 }))
		{
			a1= a4 = 0.0;
			a8 = 1.0;
		}

		if (SimpleGUI::Button(U"0, 1, 1", Vec2{ 20, 220 }))
		{
			a1 = 0.0;
			a8 = a4 = 1.0;
		}

		if (SimpleGUI::Button(U"1, 1, 1", Vec2{ 20, 260 }))
		{
			a1 = a4 = a8 = 1.0;
		}
	}
}
```


## パターンブラシ
![](/images/doc_v6/sample/visual/pattern-brush.png)
```hlsl:pattern_brush.hlsl
//
//	Textures
//
Texture2D g_texture0 : register(t0);
Texture2D g_texture1 : register(t1);
SamplerState g_sampler0 : register(s0);
SamplerState g_sampler1 : register(s1);

namespace s3d
{
	//
	//	VS Output / PS Input
	//
	struct PSInput
	{
		float4 position	: SV_POSITION;
		float4 color	: COLOR0;
		float2 uv		: TEXCOORD0;
	};
}

//
//	Constant Buffer
//
cbuffer PSConstants2D : register(b0)
{
	float4 g_colorAdd;
	float4 g_sdfParam;
	float4 g_sdfOutlineColor;
	float4 g_sdfShadowColor;
	float4 g_internal;
}

// 定数バッファ (PS_1)
cbuffer PatternBrush : register(b1)
{
	float2 g_uvScale;
}

//
//	Functions
//
float4 PS(s3d::PSInput input) : SV_TARGET
{
	const float alpha = g_texture0.Sample(g_sampler0, input.uv).r;

	float4 texColor = g_texture1.Sample(g_sampler1, input.uv * g_uvScale);

	texColor.a = alpha;

	return ((texColor * input.color) + g_colorAdd);
}
```

```glsl:pattern_brush.frag
# version 410

//
//	Textures
//
uniform sampler2D Texture0;
uniform sampler2D Texture1;

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

// PS_1
layout(std140) uniform PatternBrush
{
	vec2 g_uvScale;
};

//
//	Functions
//
void main()
{
	float alpha = texture(Texture0, UV).r;

	vec4 texColor = texture(Texture1, UV * g_uvScale);

	texColor.a = alpha;

	FragColor = (texColor * Color) + g_colorAdd;
}
```

```cpp
# include <Siv3D.hpp>

// パターン画像を作る
Image CreatePattern()
{
	Image image{ 16, 16, Palette::White };
	Circle{ 0, 4, 2 }.paint(image, Palette::Black);
	Circle{ 8, 4, 2 }.paint(image, Palette::Black);
	Circle{ 16, 4, 2 }.paint(image, Palette::Black);
	Circle{ 4, 12, 2 }.paint(image, Palette::Black);
	Circle{ 12, 12, 2 }.paint(image, Palette::Black);
	return image;
}

// 定数バッファ (PS_1)
struct PatternBrush
{
	// パターンの UV のスケール
	Float2 uvScale;
};

void Main()
{
	constexpr Size sceneSize{ 600, 600 };

	// パターン画像のテクスチャ
	const Texture patternTexture{ CreatePattern(), TextureDesc::Mipped };

	// パターンブラシ用のピクセルシェーダ
	const PixelShader ps = HLSL{ U"pattern_brush.hlsl", U"PS" }
		| GLSL{ U"pattern_brush.frag", {{ U"PSConstants2D", 0 }, { U"PatternBrush", 1 }} };

	if (not ps)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	// 定数バッファ
	ConstantBuffer<PatternBrush> cb;
	cb->uvScale = (Float2{ sceneSize } / patternTexture.size());

	// ペンで書き込むレンダーテクスチャ
	MSRenderTexture renderTexture{ sceneSize, Palette::Black };

	// ペンの太さ
	constexpr double thickness = 20;

	while (System::Update())
	{
		if (MouseL.pressed())
		{
			{
				const ScopedRenderTarget2D target{ renderTexture };

				if (MouseL.down())
				{
					Circle{ Cursor::PosF(), thickness * 0.5 }.draw();
				}
				else if (MouseL.pressed() && (not Cursor::Delta().isZero()))
				{
					Line{ Cursor::PreviousPosF(), Cursor::PosF() }
						.draw(LineStyle::RoundCap, thickness);
				}
			}

			Graphics2D::Flush();
			renderTexture.resolve();
		}

		Rect{ sceneSize }.draw();
		{
			// パターン画像を PS テクスチャスロット 1 にセット 
			Graphics2D::SetPSTexture(1, patternTexture);
			Graphics2D::SetPSConstantBuffer(1, cb);

			// パターンをくり返しマッピングできるようにする
			{
				const ScopedRenderStates2D sampler{ {ShaderStage::Pixel, 1, SamplerState::RepeatLinear} };
				const ScopedCustomShader2D shader{ ps };
				renderTexture.draw();
			}
		}

		// パターン画像を右上に表示
		patternTexture.draw(620, 20);
	}
}
```


## 文章中に画像を入れる
`Font::addFallback()` を使ってカラー絵文字フォントをテキストフォントに追加する方法では、絵文字のサイズなどの細かい制御ができません。次のような方法を使うと、絵文字をテキスト中で柔軟に扱えます。
![](/images/doc_v6/sample/visual/inline-emoji.png)
```cpp
# include <Siv3D.hpp>

struct Building
{
	Texture icon;
	String name, desc;
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.3, 0.6, 0.4 });
	const Font font1{ 30, Typeface::Heavy }, font2{ 15, Typeface::Medium };
	const Array<Texture> emojis = {
		Texture{ U"⚙️"_emoji }, Texture{ U"⚡"_emoji }, Texture{ U"♥"_emoji } };
	const Array<Building> buildings =
	{
		{ Texture{ U"🏭"_emoji }, U"工場", U"毎ターン 6$0 を生産する\n電力 3$1 が必要" },
		{ Texture{ U"🏟"_emoji }, U"スタジアム", U"毎ターン 4$2 を供給する\n電力 2$1 が必要" },
		{ Texture{ U"🏖"_emoji }, U"ビーチ", U"毎ターン 2$2 を供給する\n砂浜にしか建設できない" }
	};

	const RoundRect r0{ 0, 0, 360, 100, 6 };
	const RoundRect r1{ 5, 5, 90, 90, 5 };

	while (System::Update())
	{
		for (auto [i, building] : Indexed(buildings))
		{
			const Transformer2D t{ Mat3x2::Translate(40, (40 + i * 110)) };

			r0.drawShadow(Vec2{ 4, 4 }, 8, 1)
				.draw(ColorF{ 0.2, 0.25, 0.3 })
				.drawFrame(1, 1, ColorF{ 0.4, 0.5, 0.6 });
			r1.draw(ColorF{ 0.85, 0.9, 0.95 });
			building.icon.resized(80).drawAt(r1.center());
			font1(building.name).draw(r1.rect.pos.movedBy(100, 0));

			const Vec2 penPos0 = r1.rect.pos.movedBy(100, 42);
			Vec2 penPos = penPos0;
			bool onTag = false;

			for (const auto& glyph : font2.getGlyphs(building.desc))
			{
				if (onTag)
				{
					emojis[(glyph.codePoint - U'0')].resized(25).draw(penPos);
					penPos.x += 25;
					onTag = false;
					continue;
				}

				if (glyph.codePoint == U'$')
				{
					onTag = true;
					continue;
				}

				onTag = false;

				if (glyph.codePoint == U'\n')
				{
					penPos.x = penPos0.x;
					penPos.y += font2.height();
				}
				else
				{
					glyph.texture.draw(penPos + glyph.getOffset());
					penPos.x += glyph.xAdvance;
				}
			}
		}
	}
}
```


## ボロノイ図
![](/images/doc_v6/sample/visual/voronoi.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	constexpr Size size{ 800, 600 };
	constexpr Rect rect{ size };
	Subdivision2D subdiv{ rect };

	for (const PoissonDisk2D pd{ size, 40 };
		const auto & point : pd.getPoints())
	{
		if (rect.contains(point))
		{
			subdiv.addPoint(point);
		}
	}

	const Array<Polygon> facetPolygons = subdiv
		.calculateVoronoiFacets()
		.map([rect](const VoronoiFacet& f)
	{
		return Geometry2D::And(Polygon{ f.points }, rect).front();
	});

	while (System::Update())
	{
		for (auto [i, facetPolygon] : Indexed(facetPolygons))
		{
			facetPolygon
				.draw(HSV{ i * 25.0, 0.65, 0.8 })
				.drawFrame(2, ColorF{ 0.25 });
		}
	}
}
```


## OutlineGlyph の応用
![](/images/doc_v6/sample/visual/outlineglyph.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.99, 0.96, 0.93 });

	const Font font{ 130, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
	const Array<OutlineGlyph> glyphs = font.renderOutlines(U"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz1234567890!?");

	while (System::Update())
	{
		const double t = Periodic::Sawtooth0_1(2.6s);
		const double len = Periodic::Sine0_1(16s) * 0.5;
		constexpr Vec2 basePos{ 70, 0 };
		Vec2 penPos{ basePos };

		for (const auto& glyph : glyphs)
		{
			const Transformer2D transform{ Mat3x2::Translate(penPos + glyph.getOffset()) };

			for (const auto& ring : glyph.rings)
			{
				const double length = ring.calculateLength(CloseRing::Yes);
				LineString z1 = ring.extractLineString(t * length, length * len, CloseRing::Yes);
				const LineString z2 = ring.extractLineString((t + 0.5) * length, length * len, CloseRing::Yes);
				z1.append(z2.reversed()).drawClosed(3, ColorF{ 0.25 });
			}

			if (penPos.x += glyph.xAdvance;
				1120 < penPos.x)
			{
				penPos.x = basePos.x;
				penPos.y += font.fontSize();
			}
		}
	}
}
```


## ランダムなテキストから目的のテキストに変化する
![](/images/doc_v6/sample/visual/randomtext.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 75, Typeface::Light };
	const Array<char32> chars = Range(U'A', U'Z').asArray().append(Range(U'a', U'z'));
	const String targetText = U"C++/OpenSiv3D";

	Array<int32> delays = targetText.map([](auto) { return Random(20, 60); });
	String randomText = targetText;

	constexpr double displayTime = 0.05;
	double accumulator = 0.0;

	while (System::Update())
	{
		accumulator += Scene::DeltaTime();

		if (MouseL.down())
		{
			delays = targetText.map([](auto) { return Random(20, 60); });
		}

		if (displayTime <= accumulator)
		{
			accumulator -= displayTime;

			for (auto i : step(targetText.size()))
			{
				if (delays[i])
				{
					randomText[i] = chars.choice();
					--delays[i];
				}
				else
				{
					randomText[i] = targetText[i];
				}
			}
		}

		Vec2 penPos{ 50, 240 };

		for (auto [i, glyph] : Indexed(font.getGlyphs(targetText)))
		{
			const auto glyph2 = font.getGlyph(randomText[i]);

			glyph2.texture.draw(penPos + glyph2.getOffset());

			penPos.x += (glyph.xAdvance * 1.2);
		}
	}
}
```


## 2D パーティクルシステム

### パーティクルエディタ

```cpp
# include <Siv3D.hpp>

// Siv3D-kun の形の Polygon を作成
Polygon CreatePolygon()
{
	return Image{ U"example/siv3d-kun.png" }
		.alphaToPolygonCentered().simplified(1).scaled(1.3);
}

void Main()
{
	Window::Resize(1280, 720);
	const Texture textureSiv3D{ U"example/siv3d-kun.png" };
	const std::array<BlendState, 4> blends = {
		BlendState::Default2D, BlendState::Additive, BlendState::Opaque, BlendState::Subtractive
	};

	// パーティクル用のテクスチャ
	const Texture texture{ U"example/particle.png", TextureDesc::Mipped };

	// デバッグモードのパーティクル描画
	bool debugMode = false;

	// 背景を白に
	bool whiteBackground = false;

	// Siv3D-kun を表示
	bool drawTexture = false;

	Vec2 position{ 300, 340 };
	Vec2 force{ 0.0, 0.0 };
	HSV startColor = ColorF{ 1.0 };
	size_t blendIndex = 1;
	size_t emitterIndex = 1;

	CircleEmitter2D circleEmitter;
	ArcEmitter2D arcEmitter;
	RectEmitter2D rectEmitter;
	PolygonEmitter2D polygonEmitter{ CreatePolygon() };
	ParticleSystem2DParameters parameters;

	ParticleSystem2D particleSystem{ position, force };
	particleSystem.setEmitter(arcEmitter);
	particleSystem.setTexture(texture);
	particleSystem.setParameters(parameters);
	particleSystem.prewarm();

	while (System::Update())
	{
		// パーティクルの個数を表示
		ClearPrint();
		Print << U"{} particles"_fmt(particleSystem.num_particles());

		// パーティクル更新
		particleSystem.update();

		if (debugMode)
		{
			// デバッグモードでパーティクル描画
			particleSystem.drawDebug();
		}
		else
		{
			// パーティクル描画
			particleSystem.draw();
		}

		if (emitterIndex == 3 && drawTexture)
		{
			// Siv3D-kun を表示
			textureSiv3D.scaled(1.3).drawAt(position);
		}

		if (MouseR.pressed())
		{
			// パーティクルの発生位置を移動
			particleSystem.setPosition(position = Cursor::Pos());
		}

		SimpleGUI::CheckBox(debugMode, U"Debug", Vec2{ 80, 660 }, 140);
		SimpleGUI::CheckBox(whiteBackground, U"White", Vec2{ 240, 660 }, 140);
		SimpleGUI::CheckBox(drawTexture, U"Texture", Vec2{ 400, 660 }, 140, (emitterIndex == 3));
		Scene::SetBackground(whiteBackground ? Color{ 250, 252, 255 } : Palette::DefaultBackground);

		const int32 x0 = 560, x1 = 900;
		{
			SimpleGUI::Slider(U"Rate", parameters.rate, 1.0, 500.0, Vec2{ x0, 20 }, 120, 200);
			SimpleGUI::Slider(U"Max", parameters.maxParticles, 50.0, 2500.0, Vec2{ x0, 60 }, 120, 200);
			SimpleGUI::Slider(U"LifeTime", parameters.startLifeTime, 0.0, 5.0, Vec2{ x0, 100 }, 120, 200);
			SimpleGUI::Slider(U"Speed", parameters.startSpeed, 0.0, 320.0, Vec2{ x0, 140 }, 120, 200);
			SimpleGUI::Slider(U"Color H", startColor.h, 0.0, 360.0, Vec2{ x0, 180 }, 120, 200);
			SimpleGUI::Slider(U"Color S", startColor.s, 0.0, 1.0, Vec2{ x0, 220 }, 120, 200);
			SimpleGUI::Slider(U"Color V", startColor.v, 0.0, 1.0, Vec2{ x0, 260 }, 120, 200);
			SimpleGUI::Slider(U"Color A", startColor.a, 0.0, 1.0, Vec2{ x0, 300 }, 120, 200);
			SimpleGUI::Slider(U"Size", parameters.startSize, 0.0, 150.0, Vec2{ x0, 340 }, 120, 200);
			SimpleGUI::Slider(U"Rotation", parameters.startRotationDeg, -180, 180, Vec2{ x0, 380 }, 120, 200);
			SimpleGUI::Slider(U"AngularVel", parameters.startAngularVelocityDeg, -720, 720, Vec2{ x0, 420 }, 120, 200);
			SimpleGUI::Slider(U"Force X", force.x, -320.0, 320.0, Vec2{ x0, 460 }, 120, 200);
			SimpleGUI::Slider(U"Force Y", force.y, -320, 320.0, Vec2{ x0, 500 }, 120, 200);
			SimpleGUI::RadioButtons(blendIndex, { U"Default", U"Additive", U"Opaque", U"Subtractive" }, Vec2{ x0, 540 }, 320);

			parameters.blendState = blends[blendIndex];
			parameters.startColor = startColor;
			particleSystem.setParameters(parameters);
			particleSystem.setForce(force);
		}

		{
			bool changed = false;
			changed |= SimpleGUI::RadioButtons(emitterIndex, { U"Circle", U"Arc", U"Rect", U"Polygon" }, Vec2{ x1, 20 }, 360);

			if (emitterIndex == 0)
			{
				changed |= SimpleGUI::Slider(U"Source Radius", circleEmitter.sourceRadius, 0.0, 40.0, Vec2{ x1, 180 }, 160, 200);
				changed |= SimpleGUI::Slider(U"R", circleEmitter.r, 0.0, 320.0, Vec2{ x1, 220 }, 160, 200);
				changed |= SimpleGUI::CheckBox(circleEmitter.randomDirection, U"Random Direction", Vec2{ x1, 260 }, 360);
				changed |= SimpleGUI::CheckBox(circleEmitter.fromShell, U"From Shell", Vec2{ x1, 300 }, 300);

				if (changed) // setEmitter は重い処理なので、変更があった時だけ
				{
					particleSystem.setEmitter(circleEmitter);
				}
			}
			else if (emitterIndex == 1)
			{
				changed |= SimpleGUI::Slider(U"Source Radius", arcEmitter.sourceRadius, 0.0, 40.0, Vec2{ x1, 180 }, 160, 200);
				changed |= SimpleGUI::Slider(U"R", arcEmitter.r, 0.0, 320.0, Vec2{ x1, 220 }, 160, 200);
				changed |= SimpleGUI::Slider(U"Direction", arcEmitter.direction, -180, 180, Vec2{ x1, 260 }, 160, 200);
				changed |= SimpleGUI::Slider(U"Angle", arcEmitter.angle, 0.0, 360, Vec2{ x1, 300 }, 160, 200);
				changed |= SimpleGUI::CheckBox(arcEmitter.randomDirection, U"Random Direction", Vec2{ x1, 340 }, 360);
				changed |= SimpleGUI::CheckBox(arcEmitter.fromShell, U"From Shell", Vec2{ x1, 380 }, 360);

				if (changed) // setEmitter は重い処理なので、変更があった時だけ
				{
					particleSystem.setEmitter(arcEmitter);
				}
			}
			else if (emitterIndex == 2)
			{
				changed |= SimpleGUI::Slider(U"Source Radius", rectEmitter.sourceRadius, 0.0, 40.0, Vec2{ x1, 180 }, 160, 200);
				changed |= SimpleGUI::Slider(U"Width", rectEmitter.width, 0, 720, Vec2{ x1, 220 }, 160, 200);
				changed |= SimpleGUI::Slider(U"Height", rectEmitter.height, 0, 720, Vec2{ x1, 260 }, 160, 200);
				changed |= SimpleGUI::CheckBox(rectEmitter.randomDirection, U"Random Direction", Vec2{ x1, 300 }, 360);
				changed |= SimpleGUI::CheckBox(rectEmitter.fromShell, U"From Shell", Vec2{ x1, 340 }, 360);

				if (changed) // setEmitter は重い処理なので、変更があった時だけ
				{
					particleSystem.setEmitter(rectEmitter);
				}
			}
			else if (emitterIndex == 3)
			{
				if (changed) // setEmitter は重い処理なので、変更があった時だけ
				{
					particleSystem.setEmitter(polygonEmitter);
				}
			}
		}
	}
}
```


### ゲームへの組み込み

```cpp
# include <Siv3D.hpp>

// ボール用のパーティクルクラス
class BallParticleSystem
{
public:

	BallParticleSystem(const Vec2& pos, const Vec2& force)
		: m_system{ pos, force }
		, m_texture{ U"example/particle.png", TextureDesc::Mipped }
	{
		m_system.setTexture(m_texture);

		// パラメータ
		ParticleSystem2DParameters parameters;
		parameters.rate = 300.0;
		parameters.startSpeed = 50.0;
		parameters.startColor = HSV{ 10.0, 0.8, 0.25 };
		parameters.startSize = 60.0;
		parameters.blendState = BlendState::Additive;
		m_system.setParameters(parameters);

		// エミッター
		CircleEmitter2D circleEmitter;
		circleEmitter.r = 12.0;
		m_system.setEmitter(circleEmitter);

		m_system.prewarm();
	}

	void setPos(const Vec2& pos)
	{
		m_system.setPosition(pos);
	}

	void update()
	{
		m_system.update();
	}

	void draw() const
	{
		m_system.draw();
	}

private:

	// 2D パーティクルシステム
	ParticleSystem2D m_system;

	// パーティクル用のテクスチャ
	Texture m_texture;
};

void Main()
{
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

	// ボール用のパーティクルクラス
	BallParticleSystem particleSystem{ ball.center, Vec2{ 0.0, -120 } };

	while (System::Update())
	{
		// パドル
		const Rect paddle{ Arg::center(Cursor::Pos().x, 500), 60, 10 };

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
		}

		// パーティクル更新
		particleSystem.setPos(ball.center);
		particleSystem.update();

		// すべてのブロックを描画する
		for (const auto& brick : bricks)
		{
			brick.stretched(-1).draw(HSV{ brick.y - 40 });
		}

		// ボールを描く
		ball.draw();

		// パドルを描く
		paddle.draw();

		// パーティクル描画
		particleSystem.draw();
	}
}
```

## 図形の輪郭の一部を LineString として取得
`Triangle`, `Rect`, `Quad`, `Polygon` の輪郭や輪郭の一部を取得するには `.outline()` メンバ関数を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.15 });

	const Polygon polygon0 = Shape2D::Plus(180, 100, Scene::Center().movedBy(-350, -120));
	const Polygon polygon1 = Shape2D::Heart(180, Scene::Center().movedBy(0, 120));
	const Polygon polygon2 = Shape2D::NStar(8, 180, 140, Scene::Center().movedBy(350, -120));

	while (System::Update())
	{
		const double t = (Scene::Time() * 720);

		polygon0.draw(ColorF{ 0.4 });
		polygon0.outline(t, 200).draw(LineStyle::RoundCap, 8, ColorF{ 0, 1, 0.5 });

		polygon1.draw(ColorF{ 0.4 });
		polygon1.outline(t, 200).draw(LineStyle::RoundCap, 8, ColorF{ 0, 1, 0.5 });
		
		polygon2.draw(ColorF{ 0.4 });
		polygon2.outline(t, 200).draw(LineStyle::RoundCap, 8, ColorF{ 0, 1, 0.5 });
	}
}
```


## GPU での頂点生成
CPU から 1 つの float (時間) だけを渡して、GPU の頂点シェーダでポリゴンのすべての座標を計算して描画するサンプルです。複雑な形状の図形の頂点の計算を、CPU から高速な GPU に移せます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });
	
	const VertexShader vs
		= HLSL{ U"example/shader/hlsl/soft_shape.hlsl" }
		| GLSL{ U"example/shader/glsl/soft_shape.vert", { { U"VSConstants2D", 0 }, { U"SoftShape", 1 } }};

	if (!vs)
	{
		throw Error{ U"Failed to load a shader file" };
	}

	ConstantBuffer<float> cb;

	while (System::Update())
	{
		cb = static_cast<float>(Scene::Time());
		Graphics2D::SetVSConstantBuffer(1, cb);
		
		{
			const ScopedCustomShader2D shader{ vs };

			// 頂点情報が未完成の三角形を 360 個描画
			// 頂点情報は頂点シェーダで生成
			Graphics2D::DrawTriangles(360);
		}
	}
}
```

