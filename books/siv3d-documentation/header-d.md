---
title: "D から始まるヘッダ"
free: true
---

# 1. `<Distribution.hpp>`

## 1.1 正規分布（ガウス分布）で乱数を生成する

```cpp
# include <Siv3D.hpp>

void Main()
{
	const double mean = 160.0;
	const double stddev = 8.0;
	NormalDistribution<double> normalDist{ mean, stddev };
	auto& rng = GetDefaultRNG();

	Array<int32> counts(200);

	for (size_t i = 0; i < 1000; ++i)
	{
		const double r = normalDist(rng);
		Console << r;

		const int32 ri = static_cast<int32>(r);

		if (InRange<size_t>(ri, 0, counts.size() - 1))
		{
			++counts[ri];
		}
	}

	while (System::Update())
	{
		for (auto [i, count] : Indexed(counts))
		{
			Rect{ Arg::bottomLeft(i * 4, 600), 4, count * 4 }.draw();
		}

		if (const int32 t = (Cursor::Pos().x / 4); 
			InRange<size_t>(t, 0, counts.size() - 1))
		{
			Rect{ t * 4, 0, 4, 600 }.draw(ColorF{ 1.0, 0.5, 0.0, 0.2 });

			PutText(U"{}～{} ({})"_fmt(t, t + 1, counts[t]), Vec2{ t * 4, 20 });
		}
	}
}
```
