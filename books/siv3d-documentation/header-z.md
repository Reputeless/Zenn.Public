---
title: "Z から始まるヘッダ"
free: true
---

# 1. `<ZIPReader.hpp>`

## 1.1 ZIP ファイル内に圧縮されている画像から `Texture` を作成

```cpp
# include <Siv3D.hpp>

void Main()
{
	ZIPReader zip{ U"example/zip/zip_test.zip" };

	// ZIP 内の内容を列挙
	Print << zip.enumPaths();

	// ZIP 内のファイルから Texture を作成
	const Texture texutre{ zip.extract(U"zip_test/image/windmill.png") };

	while (System::Update())
	{
		texutre.draw();
	}
}
```
