---
title: "チュートリアル 23 | ファイルシステム"
free: true
---

# 23. ファイルシステム
この章では、ファイルやディレクトリの情報取得および操作に関する機能を学びます。

ファイルパスを Siv3D のプログラムで表現するときは、`String` 型のエイリアス（別名）である `FilePath` 型を使うとコードが読みやすくなります。また、フォルダのパスは末尾に `/` を付与した形で表現します（例: `U"example/"`）。

## 23.1 ファイルやディレクトリが存在するか調べる
ファイルやディレクトリが存在するか調べるには `FileSystem::Exists(path)` を使います。

ファイルが存在するかを調べるには `FileSystem::IsFile(path)`, ディレクトリが存在するかを調べるには `FileSystem::IsDirectory(path)` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルやディレクトリが存在するか
	Print << FileSystem::Exists(U"example/windmill.png");
	Print << FileSystem::Exists(U"example/video/");
	Print << FileSystem::Exists(U"aaa/bbb.txt");

	// ファイルが存在するか
	Print << FileSystem::IsFile(U"example/windmill.png");
	Print << FileSystem::IsFile(U"example/video/");
	Print << FileSystem::IsFile(U"aaa/bbb.txt");

	// ディレクトリが存在するか
	Print << FileSystem::IsDirectory(U"example/windmill.png");
	Print << FileSystem::IsDirectory(U"example/video/");
	Print << FileSystem::IsDirectory(U"aaa/bbb.txt");

	while (System::Update())
	{

	}
}
```


## 23.2 絶対パスを取得する
ファイルの絶対パスを取得するには `FileSystem::Fullpath(path)` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 絶対パスを取得
	Print << FileSystem::FullPath(U"example/windmill.png");
	Print << FileSystem::FullPath(U"example/video/");

	while (System::Update())
	{

	}
}
```


## 23.3 ファイルの名前や拡張子を取得する
親ディレクトリを含まずに、ファイル名部分だけを取得するには `FileSystem::FileName(path)` を使います。

拡張子を除いたファイル名を取得するには `FileSystem::BaseName(path)` を使います。

ファイルの拡張子 (.を含まない) を小文字で取得するには `FileSystem::Extension(path)` を使います。

![](/images/doc_v6/tutorial/23/3.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイル名を取得
	Print << FileSystem::FileName(U"example/windmill.png");

	// 拡張子を除いたファイル名を取得
	Print << FileSystem::BaseName(U"example/windmill.png");

	// 拡張子を小文字で取得
	Print << FileSystem::Extension(U"example/windmill.png");

	while (System::Update())
	{

	}
}
```


## 23.4 親ディレクトリを取得する
親ディレクトリを取得するには `FileSystem::ParentPath(path)` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 親ディレクトリを取得
	Print << FileSystem::ParentPath(U"example/windmill.png");
	Print << FileSystem::ParentPath(U"example/video/river.mp4");
	Print << FileSystem::ParentPath(U"./");

	while (System::Update())
	{

	}
}
```


## 23.5 ファイルのサイズを取得する
ファイルのサイズを取得するには `FileSystem::FileSize(path)` を使います。

ファイルおよびディレクトリのサイズを取得するには `FileSystem::Size(path)` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルのサイズを取得
	Print << FileSystem::FileSize(U"example/windmill.png");

	// ディレクトリのサイズを取得
	Print << FileSystem::Size(U"example/");

	while (System::Update())
	{

	}
}
```


## 23.6 カレントディレクトリを取得する
現在のカレントディレクトリを取得するには `FileSystem::CurrentDirectory()` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// カレントディレクトリを取得
	Print << FileSystem::CurrentDirectory();

	while (System::Update())
	{

	}
}
```


## 23.7 特殊フォルダへのパスを取得する
`FileSystem::GetFolderPath(SpecialFolder)` を使うと特殊フォルダへのパスを取得できます。存在しない場合は空の文字列を返します。

特殊フォルダの分類を指す `SpecialFolder` は次の値があります。

| 値 | 説明 |
|--|--|
|`SpecialFolder::Desktop`| デスクトップ |
|`SpecialFolder::Documents`| ドキュメント |
|`SpecialFolder::LocalAppData`| アプリケーション・キャッシュ |
|`SpecialFolder::Pictures`| ピクチャー |
|`SpecialFolder::Music`| ミュージック |
|`SpecialFolder::Videos`| ビデオ |
|`SpecialFolder::Caches`| アプリケーション・キャッシュ (`LocalAppData` と同じ) |
|`SpecialFolder::Movies`| ビデオ (`Videos` と同じ) |
|`SpecialFolder::SystemFonts`| システムフォント |
|`SpecialFolder::LocalFonts`| ローカルフォント |
|`SpecialFolder::UserFonts`| ユーザフォント |
|`SpecialFolder::UserProfile`| ユーザープロファイル |
|`SpecialFolder::ProgramFiles`| アプリケーション |


```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << FileSystem::GetFolderPath(SpecialFolder::Desktop);
	Print << FileSystem::GetFolderPath(SpecialFolder::Documents);
	Print << FileSystem::GetFolderPath(SpecialFolder::LocalAppData);
	Print << FileSystem::GetFolderPath(SpecialFolder::Pictures);
	Print << FileSystem::GetFolderPath(SpecialFolder::Music);
	Print << FileSystem::GetFolderPath(SpecialFolder::Videos);
	Print << FileSystem::GetFolderPath(SpecialFolder::SystemFonts);
	Print << FileSystem::GetFolderPath(SpecialFolder::LocalFonts);
	Print << FileSystem::GetFolderPath(SpecialFolder::UserFonts);
	Print << FileSystem::GetFolderPath(SpecialFolder::UserProfile);
	Print << FileSystem::GetFolderPath(SpecialFolder::ProgramFiles);

	while (System::Update())
	{

	}
}
```


## 23.8 フォルダの中身一覧を取得する
フォルダの中身一覧を取得するには `FileSystem::DirectoryContents(path, recursive)` を使います。フォルダ内のフォルダの中身を再帰的に調べない場合は `Recursive::No` を指定します。結果は `Array<FilePath>` で返されます。`FilePath` は `String` のエイリアスです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	for (const auto& path : FileSystem::DirectoryContents(U"example/font"))
	{
		Console << path;
	}

	Console << U"---";

	for (const auto& path : FileSystem::DirectoryContents(U"example/font", Recursive::No))
	{
		Console << path;
	}

	while (System::Update())
	{

	}
}
```


## 23.9 ディレクトリを作成する
ディレクトリを作成するには `FileSystem::CreateDirectories(path)` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ディレクトリを作成
	FileSystem::CreateDirectories(U"aaa/bbb/ccc/");

	while (System::Update())
	{

	}
}
```


## 23.10 ファイルやディレクトリを削除する
ファイルやディレクトリを削除するには `FileSystem::Remove(path, allowUndo)` を使います。ディレクトリの中身だけを削除し、ディレクトリを残す場合は `FileSystem::RemoveContents()` を使います。

`AllowUndo::Yes` を指定すると、可能な場合、ファイルはゴミ箱に送られ、あとで復元できます。


## 23.11 ファイルの変更を検知する
`DirectoryWatcher` を使うと、指定したディレクトリ内でのファイルの変更イベントを検知できます。ユーザがファイルを変更したときに自動でリロードする仕組みの実装に使えます。

ファイルアクションは次の種類があります。

| アクション | 説明 |
|--|--|
|`FileAction::Added`| ファイルが追加された |
|`FileAction::Removed`| ファイルが削除された |
|`FileAction::Modified`| ファイルの中身が変更された |
|`FileAction::Unknown`| 不明なアクション |

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ディレクトリを作成
	FileSystem::CreateDirectories(U"bbb/");

	// bbb/ ディレクトリ内でのイベントを監視
	const DirectoryWatcher watcher{ U"bbb/" };

	while (System::Update())
	{
		// 絶対パスと、アクションの内容
		for (auto [path, action] : watcher.retrieveChanges())
		{
			Print << path;

			if (action == FileAction::Added)
			{
				Print << U"Added";
			}
			else if (action == FileAction::Removed)
			{
				Print << U"Removed";
			}
			else if (action == FileAction::Modified)
			{
				Print << U"Modified";
			}
			if (action == FileAction::Unknown)
			{
				Print << U"Unknown";
			}
		}
	}
}
```

### 特定のファイルの更新を検出するサンプル
何らかの方法であるファイルを変更すると、次のいずれかのイベントが発生します。変更に使うソフトウェアの仕様によっては、1 回の保存で複数の `Modified` イベントが発生することもあります。
- Removed → Added
- Modified

`Added` と `Modified` を検出することで、特定のファイルの更新を取りこぼしなく検出できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 更新を検出したいファイルのパス
	const FilePath filePath = U"bbb/test.txt";

	// 絶対パス
	const FilePath fullPath = FileSystem::FullPath(filePath);

	// 親ディレクトリ
	const FilePath parentDirectory = FileSystem::ParentPath(filePath);

	// 親ディレクトリを監視
	const DirectoryWatcher watcher{ parentDirectory };

	if (not watcher)
	{
		// ディレクトリが存在しない
		return;
	}

	while (System::Update())
	{
		for (auto [path, action] : watcher.retrieveChanges())
		{
			if ((path == fullPath)
				&& (action == FileAction::Added || action == FileAction::Modified))
			{
				Print << U"updated!";
			}
		}
	}
}
```

