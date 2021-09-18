---
title: "チュートリアル 28 | ドラッグ&ドロップ"
free: true
---

# 28. ドラッグ&ドロップ
この章では、ドラッグ&ドロップされたファイルの情報を取得する方法を学びます。

## 28.1 ドロップされたファイルの情報を取得する
ドラッグ&ドロップされたファイルがあるかを `DragDrop::HasNewFilePaths()` で取得できます。この関数が `true` を返したら、`DragDrop::GetDroppedFilePaths()` を呼ぶと、そのファイルの一覧を `Array<DroppedFilePath>` 型で取得できます。

`DroppedFilePath` のメンバ変数は次の通りです。

| メンバ変数 | 説明 |
|--|--|
| `FilePath path` | ファイルまたはディレクトリの絶対パス |
| `Point pos` | ドロップされた位置（シーン座標） |
| `uint64 timeMillisec` | ドロップされたタイミング (`Time::GetMillisec()` で計測されるアプリ起動時間) |

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{		
		if (DragDrop::HasNewFilePaths())
		{
			for (const auto& dropped : DragDrop::GetDroppedFilePaths())
			{
				Print << dropped.path // ファイルやディレクトリの絶対パス
					<< U" @" << dropped.pos // ドロップされた座標
					<< U" :" << dropped.timeMillisec; // ドロップされたタイミング（Time::GetMillisec())
			}
		}
	}
}
```


## 28.2 ファイルのドロップを禁止する
ウィンドウにファイルをドロップできないようにするには `DragDrop::AcceptFilePaths(false)` を呼びます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルパスのドロップを受け付けないようにする
	DragDrop::AcceptFilePaths(false);

	while (System::Update())
	{		
		// 受け付けないので何もドロップされない
		if (DragDrop::HasNewFilePaths())
		{
			for (const auto& dropped : DragDrop::GetDroppedFilePaths())
			{
				Print << dropped.path
					<< U" @" << dropped.pos
					<< U" :" << dropped.timeMillisec;
			}
		}
	}
}
```


## 28.3 ドロップされたファイルの情報を消去する
ドロップされたファイルの情報は `DragDrop::GetDroppedFilePaths()` を呼ぶと消去されますが、`DragDrop::Clear()` を使って消去することもできます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{		
		if (DragDrop::HasNewFilePaths())
		{
			// ドロップされたアイテムの情報を消去
			DragDrop::Clear();

			// 消去されているので、何も取得されない
			for (const auto& dropped : DragDrop::GetDroppedFilePaths())
			{
				Print << dropped.path
					<< U" @" << dropped.pos
					<< U" :" << dropped.timeMillisec;
			}
		}
	}
}
```


## 28.4 ドラッグ中のアイテムの情報を得る
ウィンドウ上でドラッグ中のアイテムの情報を取得するには `DragDrop::DragOver()` を使います。この関数は `Optional<DragStatus>` を返します。ドラッグ中のアイテムが無い場合は `none` を返します。

`DragStatus` のメンバ変数は次の通りです。

| メンバ変数 | 説明 |
|--|--|
| `DragItemType itemType` | ドラッグしているアイテムの種類。`DragItemType::FilePaths` または `DragItemType::Text` |
| `Point cursorPos` | ドラッグ中のカーソルの位置（シーン座標） |

Siv3D はテキスト (`DragItemType::Text`) のドラッグ&ドロップもサポートしていますが、この章のサンプルでは扱いません。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture icon{ 0xf15b_icon, 40 };

	while (System::Update())
	{
		// ウィンドウ上でドラッグ中のアイテムがある
		if (const auto status = DragDrop::DragOver())
		{
			if (status->itemType == DragItemType::FilePaths)
			{
				// アイコンを表示
				icon.drawAt(status->cursorPos, ColorF{ 0.5 });
			}
		}

		if (DragDrop::HasNewFilePaths())
		{
			for (const auto& dropped : DragDrop::GetDroppedFilePaths())
			{
				Print << dropped.path
					<< U" @" << dropped.pos
					<< U" :" << dropped.timeMillisec;
			}
		}
	}
}
```

