---
title: "API リファレンス | S"
free: true
---

:::message
将来追加予定の API リファレンスの仮ページです。
:::

# 17. `<ScreenCapture.hpp>`

## 17.1 スクリーンショット保存のショートカットキーをカスタマイズする
デフォルトは `{ KeyPrintScreen, KeyF12 }` です。

```cpp
#include <Siv3D.hpp>

void Main()
{
	// PrintScreen または (Ctrl + P) キーでスクリーンショットを保存
	ScreenCapture::SetShortcutKeys({ KeyPrintScreen, (KeyControl + KeyP) });

	const Font font{ 40 };

	while (System::Update())
	{
		font(Scene::FrameCount()).drawAt(Scene::Center());
	}
}
```


# 29. `<Shape2D.hpp>`

## 29.1 円形のような正方形（Squircle) を描画する

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


# 34. `<SimpleAnimation.hpp>`

## 34.1 キーフレームアニメーション

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.3, 0.5, 0.4 });
	const Texture texture1{ U"🐥"_emoji };
	const Texture texture2{ U"🐢"_emoji };

	SimpleAnimation a1;
	a1.setLoop(12s)
		.set(U"r", { 0.5s, 0 }, { 1.5s, 1 }, EaseOutBounce)
		.set(U"g", { 1s, 0 }, { 2s, 1 }, EaseOutBounce)
		.set(U"b", { 1.5s, 0 }, { 2.5s, 1 }, EaseOutBounce)
		.set(U"angle", { 3s, 0_deg }, { 8.5s, 720_deg }, EaseOutBounce)
		.set(U"size", { 0s, 0 }, { 0.5s, 320 }, EaseOutExpo)
		.set(U"size", { 9s, 320 }, { 9.5s, 0 }, EaseOutExpo)
		.start();

	SimpleAnimation a2;
	a2.setLoop(6s)
		.set(U"x", { 1s, 150 }, { 3s, 650 }, EaseInOutExpo)
		.set(U"y", { 0s, 350 }, { 1s, 150 }, EaseOutBack)
		.set(U"y", { 3s, 150 }, { 4s, 350 }, EaseInQuad)
		.set(U"t", { 0s, 0 }, { 4s, 12_pi }, EaseInOutQuad)
		.set(U"a", { 5s, 1 }, { 6s, 0 }, EaseOutCubic)
		.start();

	SimpleAnimation a3;
	a3.setLoop(6s)
		.set(U"x", { 0s, 100 }, { 3s, 700 }, EaseInOutQuad)
		.set(U"x", { 3s, 700 }, { 6s, 100 }, EaseInOutQuad)
		.set(U"mirrored", { 0s, 1 }, { 3s, 1 })
		.set(U"mirrored", { 3s, 0 }, { 6s, 0 })
		.start();

	while (System::Update())
	{
		Triangle{ Scene::Center(), a1[U"size"], a1[U"angle"] }
		.draw(ColorF{ a1[U"r"], 0, 0 }, ColorF{ 0, a1[U"g"], 0 }, ColorF{ 0, 0, a1[U"b"] });

		texture1
			.drawAt(a2[U"x"], a2[U"y"] + Math::Sin(a2[U"t"]) * 20.0, ColorF{ 1, a2[U"a"] });

		texture2
			.mirrored(a3[U"mirrored"])
			.drawAt(a3[U"x"], 500);
	}
}
```


# 52. `<SVG.hpp>`

## 52.1 SVG ファイルから Texture を作成

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

			PutText(U"{}"_fmt(texture.size()), Vec2{ (i * 200 + 60), 40 });
		}
	}
}
```
