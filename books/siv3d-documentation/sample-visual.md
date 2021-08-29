---
title: "サンプル集 | ビジュアル表現"
free: true
---

## 図形や絵文字に影を付ける

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
