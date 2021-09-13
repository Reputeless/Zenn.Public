---
title: "API リファレンス | Z から始まるヘッダ"
free: true
---

# 1. `<ZIPReader.hpp>`

## `ZIPReader` クラス

### メンバ関数

---

**ZIPReader()**

デフォルトコンストラクタ

---

**ZIPReader(FilePathView path)**

- `path`: オープンする ZIP アーカイブファイルのパス

ZIP アーカイブファイルをオープンします。

---




## 1.1 ZIP ファイル内に圧縮されている画像から `Texture` を作成する

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
