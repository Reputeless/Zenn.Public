---
title: "API リファレンス | B"
free: true
---

:::message
将来追加予定の API リファレンスの仮ページです。
:::

# 17. `<Buffer2D.hpp>`

## 17.1 Buffer2D による Polygon へのテクスチャ貼り付け

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"🥺"_emoji };

	while (System::Update())
	{
		const double angle = (Scene::Time() * 30_deg);

		Shape2D::Pentagon(60, Vec2{ 100, 100 }, angle)
			.toBuffer2D(Arg::center(100, 100), texture.size())
			.draw(texture);

		Shape2D::Ngon(7, 60, Vec2{ 300, 100 }, angle)
			.toBuffer2D(Arg::center(300, 100), texture.size())
			.draw(texture);

		Shape2D::Plus(60, 30, Vec2{ 500, 100 }, angle)
			.toBuffer2D(Arg::center(500, 100), texture.size())
			.draw(texture);

		Shape2D::Heart(60, Vec2{ 700, 100 }, angle)
			.toBuffer2D(Arg::center(700, 100), texture.size())
			.draw(texture);


		Shape2D::Pentagon(60, Vec2{ 100, 300 }, angle)
			.toBuffer2D(Arg::center(100, 300), texture.size(), angle)
			.draw(texture);

		Shape2D::Ngon(7, 60, Vec2{ 300, 300 }, angle)
			.toBuffer2D(Arg::center(300, 300), texture.size(), angle)
			.draw(texture);

		Shape2D::Plus(60, 30, Vec2{ 500, 300 }, angle)
			.toBuffer2D(Arg::center(500, 300), texture.size(), angle)
			.draw(texture);

		Shape2D::Heart(60, Vec2{ 700, 300 }, angle)
			.toBuffer2D(Arg::center(700, 300), texture.size(), angle)
			.draw(texture);


		Shape2D::Pentagon(60, Vec2{ 100, 500 })
			.toBuffer2D(Arg::center(100, 500), texture.size(), angle)
			.draw(texture);

		Shape2D::Ngon(7, 60, Vec2{ 300, 500 })
			.toBuffer2D(Arg::center(300, 500), texture.size(), angle)
			.draw(texture);

		Shape2D::Plus(60, 30, Vec2{ 500, 500 })
			.toBuffer2D(Arg::center(500, 500), texture.size(), angle)
			.draw(texture);

		Shape2D::Heart(60, Vec2{ 700, 500 })
			.toBuffer2D(Arg::center(700, 500), texture.size(), angle)
			.draw(texture);
	}
}
```

