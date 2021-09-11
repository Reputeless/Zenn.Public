---
title: "チュートリアル 27 | ファイルダイアログ"
free: true
---

# 27. ファイルダイアログ
この章では、ファイルダイアログを開いて画像やオーディオを読み込んだり、オープンするファイルや、ファイルの保存名を決める方法を学びます。

ダイアログがオープンされている間、メインのスレッドの処理は停止します。OpenSiv3D Web 版では、プラットフォームの制約から、別途用意されたファイルダイアログ関数を使う必要があります。

## 27.1 ファイルダイアログで画像を開く
ファイルダイアログを使って画像ファイルを選択し、テクスチャを作成するには `Dialog::OpenTexture()` を使います。キャンセルされた場合や、テクスチャの作成に失敗した場合は空のテクスチャを返します。`TextureDesc::Mipped` (チュートリアル 8.7 参照) を引数に渡すと、テクスチャの作成時に適用されます。

サンプルでは扱っていませんが、どのフォルダを初期フォルダにするかを設定するオプション引数もあります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルダイアログを使ってテクスチャをオープン
	Texture texture = Dialog::OpenTexture();

	while (System::Update())
	{
		if (texture)
		{
			// シーンのサイズにフィットさせて中心に描く
			texture.fitted(Scene::Size()).drawAt(Scene::Center());
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20,20 }))
		{
			// TextureDesc を指定することもできる
			texture = Dialog::OpenTexture(TextureDesc::Mipped);
		}
	}
}
```


## 27.2 ファイルダイアログでオーディオを開く
ファイルダイアログを使って音声ファイルを選択し、オーディオを作成するには `Dialog::OpenAudio()` を使います。キャンセルされた場合や、オーディオの作成に失敗した場合は空のオーディオを返します。`Audio::Stream` (チュートリアル 19.2 参照) を引数に渡すと、オーディオの作成時に適用されます。

サンプルでは扱っていませんが、どのフォルダを初期フォルダにするかを設定するオプション引数もあります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルダイアログを使ってオーディオをオープン
	Audio audio = Dialog::OpenAudio();

	while (System::Update())
	{
		if (audio && (not audio.isPlaying()))
		{
			audio.play();
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20,20 }))
		{
			// Audio::Stream を指定することもできる
			audio = Dialog::OpenAudio(Audio::Stream);
		}
	}
}
```


## 27.3 ダイアログでフォルダを選択する
フォルダ選択ダイアログを使ってフォルダを選択するには `Dialog::SelectFolder()` を使います。結果は `Optional<FilePath>` 型で返され、キャンセルされた場合は `none` を返します。

サンプルでは扱っていませんが、どのフォルダを初期フォルダにするかを設定するオプション引数もあります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// フォルダ選択ダイアログを使ってフォルダを選択
	Optional<FilePath> path = Dialog::SelectFolder();

	while (System::Update())
	{
		ClearPrint();

		if (path)
		{
			Print << *path;
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20,100 }))
		{
			path = Dialog::SelectFolder();
		}
	}
}
```


## 27.4 オープンするファイルをダイアログで選択する
オープンファイルダイアログを使って 1 つのファイルを選択するには `Dialog::OpenFile()` を使います。結果は `Optional<FilePath>` 型で返され、キャンセルされた場合は `none` を返します。

サンプルでは扱っていませんが、どのフォルダを初期フォルダにするかを設定するオプション引数もあります。

```cpp

```


## 27.5 オープンする複数のファイルをダイアログで選択する
オープンファイルダイアログを使って複数のファイルを選択するには `Dialog::OpenFiles()` を使います。結果は `Array<FilePath>` 型で返され、キャンセルされた場合は空の配列を返します。

サンプルでは扱っていませんが、どのフォルダを初期フォルダにするかを設定するオプション引数もあります。

```cpp

```


## 27.6 保存するファイルパスをダイアログで選択する
セーブファイルダイアログを使って保存するファイル名を決定させるには `Dialog::SaveFile()` を使います。結果は `Optional<FilePath>` 型で返され、キャンセルされた場合は `none` を返します。

サンプルでは扱っていませんが、どのフォルダを初期フォルダにするかを設定するオプション引数もあります。

```cpp

```
