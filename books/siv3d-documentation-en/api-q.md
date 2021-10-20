---
title: "API Reference | Q"
free: true
---

:::message
将来追加予定の API リファレンスの仮ページです。
:::

# 1. `<QR.hpp>`

## 1.1 QR コードを SVG 形式で作成

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Grid<bool> qr = QR::EncodeText(U"https://github.com/Siv3D/OpenSiv3D");
	const SVG svg = QR::MakeSVG(qr);
	const Texture t1{ svg.render(100, 100) };
	const Texture t2{ svg.render(200, 200) };
	const Texture t3{ svg.render(800, 800) };

	while (System::Update())
	{
		t1.draw();
		t2.draw(120, 0);
		t3.draw(340, 0);
	}
}
```

