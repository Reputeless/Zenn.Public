---
title: "<SVG.hpp>"
free: true
---

# 1. SVG ファイルの読み込み

## 1.1 SVG ファイルから `Texture` を作成

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
