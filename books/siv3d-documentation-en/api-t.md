---
title: "API Reference | T"
free: true
---

:::message
将来追加予定の API リファレンスの仮ページです。
:::

# 25. `<TimeProfiler.hpp>`

## 25.1 処理時間のプロファイリングを行う

```cpp
# include <Siv3D.hpp>

void Main()
{
	TimeProfiler profiler{ U"Test" };

	while (System::Update())
	{
		ClearPrint();
		profiler.print();

		{
			profiler.begin(U"Rect");

			for (auto i : step(20))
			{
				Rect{ Arg::center(20 + i * 40, 200), 30 }.draw();
			}

			profiler.end(U"Rect");
		}

		{
			profiler.begin(U"Circle");

			for (auto i : step(20))
			{
				Circle{ 20 + i * 40, 300, 15 }.draw();
			}

			profiler.end(U"Circle");
		}

		{
			profiler.begin(U"Star");

			for (auto i : step(20))
			{
				Shape2D::Star(15, Vec2{ 20 + i * 40, 400 }).draw();
			}

			profiler.end(U"Star");
		}
	}

	profiler.log();
}
```

