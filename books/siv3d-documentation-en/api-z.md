---
title: "API リファレンス | Z"
free: true
---

:::message
将来追加予定の API リファレンスの仮ページです。
:::

# 1. `<ZIPReader.hpp>`

## `ZIPReader` クラス

ZIP アーカイブファイルのデータを読み込み、展開するクラスです。

### メンバ関数

---

#### ZIPReader();

デフォルトコンストラクタ

---

#### ZIPReader(FilePathView path);

- `path`: オープンする ZIP アーカイブファイルのパス

コンストラクタ。ZIP アーカイブファイルをオープンします。

---

#### ~ZIPReader();

デストラクタ。ZIP アーカイブファイルをクローズします。

---

#### bool open(FilePathView path);

- `path`: オープンする ZIP アーカイブファイルのパス
- 戻り値: オープンに成功した場合 `true`, それ以外の場合は `false`

ZIP アーカイブファイルをオープンします。すでにオープンしていた場合は、古いほうを先にクローズしてからオープンします。

---

#### void close();

ZIP アーカイブファイルをクローズします。

---

#### bool isOpen() const noexcept;

- 戻り値: オープンしている場合 `true`, それ以外の場合は `false`

ZIP アーカイブファイルをオープンしているかを返します。

---

#### explicit operator bool() const noexcept;

- 戻り値: オープンしている場合 `true`, それ以外の場合は `false`

ZIP アーカイブファイルをオープンしているかを返します。

---

#### const Array<FilePath>& enumPaths() const;

- ZIP アーカイブファイルの内容一覧

ZIP アーカイブファイルの内容一覧を返します。

---

#### bool extractAll(FilePathView targetDirectory) const;

- `targetDirectory`: 展開先のディレクトリ
- 戻り値: 展開に成功した場合 `true`, それ以外の場合は `false`

オープンしている ZIP アーカイブファイルの中身を、指定したディレクトリにすべて展開します。

---

#### bool extractFiles(StringView pattern, FilePathView targetDirectory) const;

- `pattern` 展開対象のファイルをマッチさせるパターン
- `targetDirectory`: 展開先のディレクトリ
- 戻り値: 展開に成功した場合 `true`, それ以外の場合は `false`

指定したパターンにマッチするファイルを、指定したディレクトリにすべて展開します。

---

#### MemoryReader extract(FilePathView filePath) const;

- `filepath` 展開するファイル名
- 戻り値: 展開されたデータ

指定したファイルを展開し、`MemoryReader` オブジェクトに変換します。

---

#### Blob extractToBlob(FilePathView filePath) const;

- `filepath` 展開するファイル名
- 戻り値: 展開されたデータ

指定したファイルを展開し、`Blob` オブジェクトに変換します。

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

# 2. `<Zlib.hpp>`

## `Zlib::` 名前空間

zlib 形式のデータの圧縮・展開を行う関数群です。Siv3D では `Compressoion::` 名前空間で提供される Zstandard 形式の圧縮・展開の使用が推奨されます。

---

#### constexpr int32 DefaultCompressionLevel = 6;

デフォルトの zlib 圧縮レベルです。

---

#### constexpr int32 MinCompressionLevel = 1;

最低の zlib 圧縮レベルです。圧縮率より速度を優先します。

---

#### constexpr int32 MaxCompressionLevel = 9;

最高の zlib 圧縮レベルです。速度より圧縮率を優先します。

---

#### Blob Compress(const void* data, size_t size, int32 compressionLevel = DefaultCompressionLevel);

- `data`: 圧縮するデータの先頭ポインタ
- `size`: 圧縮するデータのサイズ（バイト）
- `compressionLevel`: 圧縮レベル
- 戻り値: 圧縮されたデータ

データを zlib 形式で圧縮します。

---

#### bool Compress(const void* data, size_t size, Blob& dst, int32 compressionLevel = DefaultCompressionLevel);

- `data`: 圧縮するデータの先頭ポインタ
- `size`: 圧縮するデータのサイズ（バイト）
- `dst`: 圧縮されたデータの格納先
- `compressionLevel`: 圧縮レベル
- 戻り値: 圧縮に成功した場合 `true`, それ以外の場合は `false`

データを zlib 形式で圧縮します。

---

#### Blob Compress(const Blob& blob, int32 compressionLevel = DefaultCompressionLevel);

- `blob`: 圧縮するデータ
- `compressionLevel`: 圧縮レベル
- 戻り値: 圧縮されたデータ

データを zlib 形式で圧縮します。

---

#### bool Compress(const Blob& blob, Blob& dst, int32 compressionLevel = DefaultCompressionLevel);

- `blob`: 圧縮するデータ
- `dst`: 圧縮されたデータの格納先
- `compressionLevel`: 圧縮レベル
- 戻り値: 圧縮に成功した場合 `true`, それ以外の場合は `false`

データを zlib 形式で圧縮します。

---

#### Blob Decompress(const void* data, size_t size);

- `data`: 展開するデータの先頭ポインタ
- `size`: 展開するデータのサイズ
- 戻り値: 展開されたデータ

zlib 形式で圧縮されているデータを展開します。

---

#### bool Decompress(const void* data, size_t size, Blob& dst);

- `data`: 展開するデータの先頭ポインタ
- `size`: 展開するデータのサイズ
- `dst`: 展開されたデータの格納先
- 戻り値: 展開に成功した場合 `true`, それ以外の場合は `false`

zlib 形式で圧縮されているデータを展開します。

---

#### Blob Decompress(const Blob& blob);

- `blob`: 展開するデータ
- 戻り値: 展開されたデータ

zlib 形式で圧縮されているデータを展開します。

---

#### bool Decompress(const Blob& blob, Blob& dst);

- `blob`: 展開するデータ
- `dst`: 展開されたデータの格納先
- 戻り値: 展開に成功した場合 `true`, それ以外の場合は `false`

zlib 形式で圧縮されているデータを展開します。

---

