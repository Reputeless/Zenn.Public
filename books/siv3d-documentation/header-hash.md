---
title: "<Hash.hpp>"
free: true
---

# 1. ハッシュ値の結合

```cpp
# include <Siv3D.hpp>

struct Agent
{
	String team;

	String name;
};

template <>
struct std::hash<Agent> // std::hash の特殊化
{
	size_t operator()(const Agent& p) const noexcept
	{
		// 複数のハッシュ値から 1 つのハッシュ値を生成
		size_t seed = 0;
		Hash::Combine(seed, p.team);
		Hash::Combine(seed, p.name);
		return seed;
	}
};

void Main()
{
	const Agent a{ U"Moon", U"Red" };
	const Agent b{ U"Moon", U"Green" };
	const Agent c{ U"Mars", U"Blue" };
	const Agent d{ U"Earth", U"Yellow" };

	Print << std::hash<Agent>{}(a);
	Print << std::hash<Agent>{}(b);
	Print << std::hash<Agent>{}(c);
	Print << std::hash<Agent>{}(d);

	while (System::Update())
	{

	}
}
```