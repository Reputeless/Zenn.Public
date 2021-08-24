---
title: "サンプル集"
free: true
---

# 1. マンデルブロ集合
```cpp
# include <Siv3D.hpp>

int32 Mandelbrot(double x, double y)
{
	double a = 0.0, b = 0.0;

	for (int32 n = 0; n < 360; ++n)
	{
		const double t = a * a - b * b + x;
		const double u = 2.0 * a * b + y;

		if (t * t + u * u > 4.0)
		{
			return n;
		}

		a = t;
		b = u;
	}

	return 0;
}

void Main()
{
	constexpr Size resolutuion{ 640, 480 };
	Window::Resize(resolutuion);

	Vec2 center{ 0, 0 };
	double scale = -4.0;

	// 結果を格納する画像
	Image image{ resolutuion, Palette::Black };

	// 描画用の動的テクスチャ
	DynamicTexture texture{ image };

	while (System::Update())
	{
		const double wheel = Mouse::Wheel();
		const bool clicked = MouseL.down();

		// 最初のフレームか、操作されたときだけ更新
		if (wheel || clicked || (Scene::FrameCount() == 1))
		{
			scale -= wheel;

			const double s = Pow(1.25, scale);
			const double d = (1.0 / s) / resolutuion.x;

			if (clicked)
			{
				center += (Cursor::PosF() - resolutuion / 2) * d;
			}

			const double xb = center.x - d * (resolutuion.x * 0.5);
			const double yb = center.y - d * (resolutuion.y * 0.5);

			for (auto y : step(resolutuion.y))
			{
				const double yPos = yb + (d * y);

				for (auto x : step(resolutuion.x))
				{
					const double xPos = xb + (d * x);

					if (const int32 m = Mandelbrot(xPos, yPos))
					{
						image[y][x] = HSV{ 240 - m, 0.8, 1.0 };
					}
					else
					{
						image[y][x] = Color{ 0 };
					}
				}
			}

			// 動的テクスチャの中身を image で更新
			texture.fill(image);
		}

		// テクスチャを描画
		texture.draw();
	}
}
```