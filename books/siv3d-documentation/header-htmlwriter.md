---
title: "<HTMLWriter.hpp>"
free: true
---

# 1. HTML ファイルの書き出し

## 1.1 HTML ファイルを書き出す

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
