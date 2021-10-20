---
title: "API Reference | P"
free: true
---

:::message
将来追加予定の API リファレンスの仮ページです。
:::

# 11. `<PerlinNoise.hpp>`

## 11.1 PerlinNoise を生成

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	Image image1{ 512, 512, Palette::White };
	Image image2{ 512, 512, Palette::White };
	DynamicTexture texture1{ image1 };
	DynamicTexture texture2{ image2 };

	PerlinNoise noise;
	size_t oct = 5;
	double persistence = 0.5;
	const Array<String> options = Range(1, 6).map(Format);

	while (System::Update())
	{
		SimpleGUI::RadioButtons(oct, options, Vec2{ 1040, 40 });

		SimpleGUI::Slider(U"{:.2f}"_fmt(persistence), persistence, Vec2{ 1040, 280 });

		if (SimpleGUI::Button(U"Generate", Vec2{ 1040, 320 }))
		{
			noise.reseed(RandomUint64());
			const int32 octaves = static_cast<int32>(oct + 1);

			for (auto p : step(image1.size()))
			{
				image1[p] = ColorF{ noise.normalizedOctave2D0_1(p / 128.0, octaves, persistence) };
			}
			for (auto p : step(image2.size()))
			{
				image2[p] = ColorF{ noise.octave2D0_1(p / 128.0, octaves, persistence) };
			}
			texture1.fill(image1);
			texture2.fill(image2);
		}

		texture1.draw();
		texture2.draw(512, 0);
	}
}
```


# 24. `<PoissonDisk2D.hpp>`

## 24.1 ほどよい距離で重ならない点群を生成する

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.2, 0.3, 0.4 });

	constexpr Rect rect{ 100, 100, 600, 400 };

	double r = 15.0;

	// 点群を生成
	PoissonDisk2D pd{ rect.size, r };

	while (System::Update())
	{
		rect.drawFrame(1, 1, ColorF{ 0.2 });

		for (const auto& point : pd.getPoints())
		{
			Circle{ point, (r / 4) }.movedBy(rect.pos).draw();
		}

		if (SimpleGUI::Slider(r, 5.0, 40.0, Vec2{ 10, 10 }))
		{
			pd = PoissonDisk2D{ rect.size, r };
		}
	}
}
```


