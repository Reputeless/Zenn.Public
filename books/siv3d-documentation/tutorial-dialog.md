---
title: "チュートリアル 27 | ファイルダイアログ"
free: true
---

# 27. ファイルダイアログ
この章では、ファイルダイアログを開いて画像やオーディオを読み込んだり、オープンするファイルや、ファイルの保存名を決める方法を学びます。

ダイアログがオープンされている間、メインのスレッドの処理は停止します。OpenSiv3D Web 版では、プラットフォームの制約から、別途用意されたファイルダイアログ関数を使う必要があります。

## 27.1 ファイルダイアログで画像を開く
ファイルダイアログを使って画像ファイルを選択し、テクスチャを作成するには `Dialog::OpenTexture()` を使います。キャンセルされた場合や、テクスチャの作成に失敗した場合は空のテクスチャを返します。`TextureDesc::Mipped` (チュートリアル 8.7 参照) を引数に渡すと、テクスチャの作成時に適用されます。

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

```cpp

```


## 27.4 オープンするファイルをダイアログで選択する

```cpp

```


## 27.5 オープンする複数のファイルをダイアログで選択する

```cpp

```


## 27.6 保存するファイルパスをダイアログで選択する

```cpp

```
