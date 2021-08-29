---
title: "チュートリアル 04 | 図形を描く（発展）"
free: true
---

# 4. 図形を描く（発展）
この章では、ここまでの内容の振り返りとして、発展的な 2D グラフィックスを描きます。  
新しい Siv3D の機能や文法は登場しません。

## 4.1 縞模様
![](/images/doc_v6/tutorial/4/1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (auto i : step(10))
		{
			Rect{ 0, (i * 60), 800, 30 }.draw(ColorF{ 0.3, 0.9, 0.6 });
		}
	}
}
```

## 4.2 市松模様
![](/images/doc_v6/tutorial/4/2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (auto x : step(8))
		{
			for (auto y : step(6))
			{
				if ((x + y) % 2 == 0)
				{
					Rect{ (x * 100), (y * 100), 100 }
						.draw(ColorF{ 0.3, 0.9, 0.6 });
				}
			}
		}
	}
}
```


## 4.3 水玉模様
![](/images/doc_v6/tutorial/4/3.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (auto x : step(9))
		{
			for (auto y : step(7))
			{
				Circle{ x * 100, y * 100, 20 }
					.draw(ColorF{ 0.3, 0.6, 0.9 });
			}
		}
	}
}
```


## 4.4 虹色の円

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const double t = Scene::Time();

		for (auto i : step(12))
		{
			const double angle = (i * 30_deg) + (t * 30_deg);

			const Vec2 pos = OffsetCircular{ Scene::Center(), 160, angle };

			Circle{ pos, 20 }.draw(HSV{ (i * 30) });
		}
	}
}
```


## 4.5 ローディング中

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double t = Scene::Time();

		Circle{ Scene::Center(), 100 }
			.drawArc((t * 90_deg), 320_deg, 10, 10);
	}
}
```


## 4.6 複数の円弧
https://youtu.be/6nCzyTGCYjw
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Vec2 center = Scene::Center();

	while (System::Update())
	{
		const double t = Scene::Time();

		Circle{ center, 120 }
			.drawArc((t * 140_deg), 240_deg, 60, 0, ColorF{ 0.4 });

		Circle{ center, 180 }
			.drawArc((t * 90_deg), 160_deg, 60, 0, ColorF{ 0.6 });

		Circle{ center, 240 }
			.drawArc((t * 50_deg), 120_deg, 60, 0, ColorF{ 0.8 });
	}
}
```


## 4.7 太陽
https://youtu.be/qEvQq_8WOHM
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Vec2 center = Scene::Center();

	const ColorF sunColor{ 0.9, 0.6, 0.3 };

	while (System::Update())
	{
		const double t = Scene::Time();

		const double s = (Periodic::Triangle0_1(0.5s) * 20);

		Circle{ center, 120 }.draw(sunColor);

		for (auto i : step(8))
		{
			const double angle = (i * 45_deg);

			const Vec2 pos = OffsetCircular{ center, 160, angle };

			Triangle{ pos, (40 + s), angle }.draw(sunColor);
		}
	}
}
```


## 4.8 注目されるマウスカーソル
https://youtu.be/OUpz2bJC9jM
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const Vec2 center = Cursor::Pos();

		for (auto i : step(8))
		{
			const double angle = (i * 45_deg);

			const Vec2 from = OffsetCircular{ center, 80, angle };

			const Vec2 to = OffsetCircular{ center, 160, angle };

			Line{ from, to }
				.draw(10, ColorF{ 1.0 }, ColorF{ 0.25 });
		}
	}
}
```


## 4.9 幕が閉じる

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const double t = Scene::Time();

		for (auto i : step(6))
		{
			const double width = Max(t - i, 0.0) * 100.0;

			RectF{ 0, (i * 100), width, 100 }.draw(ColorF{ 0.8 - i * 0.1 });
		}
	}
}
```


## 4.10 半透明
![](/images/doc_v6/tutorial/4/10.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (auto i : step(6))
		{
			Rect{ Arg::center = Scene::Center(), 60, 400 }
				.rotated(i * 30_deg)
				.draw(HSV{ (i * 60), 0.3 });
		}
	}
}
```
