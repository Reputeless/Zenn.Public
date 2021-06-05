---
title: "I から始まるヘッダ"
free: true
---

# 1. `<Image.hpp>`

## 1.1 画像に別の画像を書き込む

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Image image{ 1000, 720, Color{ 0, 0 } };
	Rect{ 300, 0,200, 720 }.overwrite(image, ColorF{ 1, 0.5 });
	Rect{ 500, 0,500, 720 }.overwrite(image, Palette::White);
	DynamicTexture texture{ image };
	const Image emoji{ U"🐈"_emoji };
	const Texture stamp{ emoji };
	size_t mode = 0;

	while (System::Update())
	{
		SimpleGUI::RadioButtons(mode, { U"paint", U"stamp", U"overwrite" }, Vec2{ 1040, 40 });

		if (Rect{ image.size() }.mouseOver() && MouseL.down())
		{
			if (mode == 0)
			{
				emoji.paintAt(image, Cursor::Pos());
			}
			else if (mode == 1)
			{
				emoji.stampAt(image, Cursor::Pos());
			}
			else if (mode == 2)
			{
				emoji.overwriteAt(image, Cursor::Pos());
			}

			texture.fill(image);
		}

		for (auto p : step({ 1000/40, 720/40+2 }))
		{
			if (IsEven(p.x + p.y))
			{
				RectF{ p * 40, 40 }
					.movedBy(0, -80 + Periodic::Sawtooth0_1(8s)*80)
					.draw(ColorF{ 0.3, 0.4, 0.3 });
			}
		}

		texture.draw();
		if (Rect{ image.size() }.mouseOver())
		{
			stamp.drawAt(Cursor::Pos());
		}
	}
}
```

