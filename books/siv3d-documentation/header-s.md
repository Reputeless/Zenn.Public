---
title: "S から始まるヘッダ"
free: true
---

# 1. `<Shape2D.hpp>`

## 1.1 円形のような正方形（Squircle) を描画する

```cpp
# include <Siv3D.hpp>

void Main()
{
	constexpr int32 iconSize = 72;

	const Array<Texture> icons =
	{
		Texture{ 0xF018C_icon, iconSize },
		Texture{ 0xF0851_icon, iconSize },
		Texture{ 0xF00A5_icon, iconSize },
		Texture{ 0xF00E5_icon, iconSize },
		Texture{ 0xF0A9A_icon, iconSize },
		Texture{ 0xF0638_icon, iconSize },
		Texture{ 0xF0352_icon, iconSize },
		Texture{ 0xF1484_icon, iconSize },
		Texture{ 0xF01E7_icon, iconSize },
		Texture{ 0xF0AC2_icon, iconSize },
		Texture{ 0xF02CE_icon, iconSize },
		Texture{ 0xF027C_icon, iconSize },
	};

	while (System::Update())
	{
		Scene::Rect().draw(Arg::top(0.3, 0.4, 0.4), Arg::bottom(0.1));

		int32 n = 0;

		for (auto p : step({ 4, 3 }))
		{
			const Vec2 pos = Vec2{ 100, 100 }.moveBy(p * 190);

			const ColorF color = Colormap01F(n / 11.0, ColormapType::Turbo) + HSV(0, 0.1, -0.1);
			
			if (MouseL.pressed()) // 左クリックで RoundRect と比較
			{
				RoundRect{ Arg::center(pos), 108, 108, 27 }.draw(color);
			}
			else
			{
				Shape2D::Squircle(54, pos, 64).draw(color);
			}

			icons[n].drawAt(pos);

			++n;
		}
	}
}
```


# 2. `<SVG.hpp>`

## 2.1 SVG ファイルから `Texture` を作成

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	const SVG svg{ U"example/svg/cat.svg" };

	const Array<Texture> textures =
	{
		Texture{ svg.render() }, // オリジナルのサイズでレンダリング
		Texture{ svg.render(svg.size() * 2) },
		Texture{ svg.render(svg.size() * 3) },
		Texture{ svg.render(svg.size() * 4) },
	};

	while (System::Update())
	{
		for (auto[i, texture] : Indexed(textures))
		{
			texture.draw(i * 200.0, 20);

			PutText(U"{}"_fmt(texture.size()), Vec2{ i * 200 + 60, 40 });
		}
	}
}
```
