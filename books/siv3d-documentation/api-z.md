---
title: "API リファレンス | Z から始まるヘッダ"
free: true
---

# 1. `<ZIPReader.hpp>`

## `ZIPReader` クラス

| メンバ関数 | 引数 | 説明 | 戻り値 |
|--|--|--|--|
|`ZIPReader()`||デフォルトコンストラクタ||
|`ZIPReader(FilePathView path)`|`path`: オープンする ZIP アーカイブファイルのパス|ZIP アーカイブファイルをオープンします。||
|`~ZIPReader()`||デストラクタ。ZIP アーカイブファイルをクローズします。||
|`bool open(FilePathView path)`|`path`: オープンする ZIP アーカイブファイルのパス|ZIP アーカイブファイルをオープンします。すでにオープンしていた場合は、古いほうを先にクローズしてからオープンします。|オープンに成功した場合 `true`, それ以外の場合は `false`|
|`void close()`||ZIP アーカイブファイルをクローズします。||
|`bool isOpen() const`||ZIP アーカイブファイルをオープンしているかを返します。|オープンしている場合 `true`, それ以外の場合は `false`|
|`explicit operator bool() const`||ZIP アーカイブファイルをオープンしているかを返します。|オープンしている場合 `true`, それ以外の場合は `false`|
|`const Array<FilePath>& enumPaths() const`||ZIP アーカイブファイルの内容一覧を返します。|ZIP アーカイブファイルの内容一覧|
|`bool extractAll(FilePathView targetDirectory) const`|`targetDirectory`: 展開先のディレクトリ|オープンしている ZIP アーカイブファイルを、指定したディレクトリに展開します。|展開に成功した場合 `true`, それ以外の場合は `false`|
|``|``|||
|``|``|||
|``|``|||


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
