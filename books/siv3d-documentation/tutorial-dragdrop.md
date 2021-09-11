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
ウィンドウにファイルをドロップできないようにするには `` を設定します。

```cpp

```


## 28.3 ドロップされたファイルの情報を消去する
ドロップされたファイルの情報は `DragDrop::GetDroppedFilePaths()` を呼ぶと消去されますが、`DragDrop::Clear()` を使って消去することもできます。

```cpp

```


## 28.4 ドラッグ中のアイテムの情報を得る

```cpp

```

