---
title: "H から始まるヘッダ"
free: true
---

# 1. `<Hash.hpp>`

## 1.1 ハッシュ値を結合する

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


# 2. `<HTMLWriter.hpp>`

## 2.1 HTML ファイルを書き出す

```cpp
# include <Siv3D.hpp>

void Main()
{
	HTMLWriter html{ U"test.html", U"OpenSiv3D" };
	html.writeHeader(U"1. Header");
	html.writeHeader(U"1.1 Header2", 2);
	html.writeParagraph(U"Hello, OpenSiv3D!");
	html.writeList({ U"Red", U"Green", U"Blue", U"Yellow" });
	html.writeOrderedList({ U"Red", U"Green", U"Blue", U"Yellow" });
	html.writeImage(Image{ U"example/windmill.png" });
	html.writeLine();
	html.writeTable(Grid<String>{ { U"A", U"B" }, { U"a", U"b" } }, true);

	while (System::Update())
	{

	}
}
```

