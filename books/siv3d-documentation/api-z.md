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

コンストラクタ。ZIP アーカイブファイルをオープンします。

---

**~ZIPReader()**

デストラクタ。ZIP アーカイブファイルをクローズします。

---

**bool open(FilePathView path)**

- `path`: オープンする ZIP アーカイブファイルのパス
- 戻り値: オープンに成功した場合 `true`, それ以外の場合は `false`

ZIP アーカイブファイルをオープンします。すでにオープンしていた場合は、古いほうを先にクローズしてからオープンします。

---

**void close()**

ZIP アーカイブファイルをクローズします。

---

**bool isOpen() const**

- 戻り値: オープンしている場合 `true`, それ以外の場合は `false`

ZIP アーカイブファイルをオープンしているかを返します。

---

**explicit operator bool() const**

- 戻り値: オープンしている場合 `true`, それ以外の場合は `false`

ZIP アーカイブファイルをオープンしているかを返します。

---

**const Array<FilePath>& enumPaths() const**

- ZIP アーカイブファイルの内容一覧

ZIP アーカイブファイルの内容一覧を返します。

---

**bool extractAll(FilePathView targetDirectory) const**

- `targetDirectory`: 展開先のディレクトリ
- 戻り値: 展開に成功した場合 `true`, それ以外の場合は `false`

オープンしている ZIP アーカイブファイルの中身を、指定したディレクトリにすべて展開します。

---

**bool extractFiles(StringView pattern, FilePathView targetDirectory) const**

---

**MemoryReader extract(FilePathView filePath) const**

---

**Blob extractToBlob(FilePathView filePath) const**

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
