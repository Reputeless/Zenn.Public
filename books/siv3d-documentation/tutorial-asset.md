---
title: "チュートリアル 20 | アセット管理"
free: true
---

# 20. アセット管理
この章では、プログラムのあらゆるところから `Texture`, `Font`, `Audio` などのアセットデータにアクセスできる機能を学びます。

## 20.1 アセット管理の概要
Siv3D は `Texture` や `Font`, `Audio` などのアセットのハンドルに名前をつけ、その名前を通してプログラムのどこからでもグローバル変数のようにアクセスできる「アセット管理」の機能を提供しています。

プログラムでアセット管理を扱う手順は以下の通りです。

1. アセットの「**登録 (Register)**」
2. アセットの「**ロード (Load)**」（省略可能）
3. アセットの「**使用**」
4. アセットの「**リリース (Release)**」（省略可能）
5. アセットの「**登録解除 (Unregister)**」（省略可能）

### 登録
アセットをエンジンに登録します。

アセットの種類（テクスチャであるか、オーディオであるかなど）を関数で指定し、アセットに一意の名前をつけ、ファイル名やプロパティなどの情報を登録します。

特に指定しない限り、この時点ではアセットデータは構築されないので、メモリの消費量が増えることはありません。

### ロード
アセットデータを実際にロードします。

アセットの名前を指定すると、エンジンが該当アセットの登録時に与えられたファイル名やプロパティに従ってメモリにアセットデータを構築します。指定されたアセットがすでにロードされている場合は何もしません。

非同期でロードを行うオプションも提供されています。

### 使用
アセットの名前を指定して、`Texture` や `Audio` を取得し、これを使って、前章までのプログラムのように `.draw()` したり、`.play()` したりできます。

指定されたアセットが登録されていなかった場合は空の `Texture` や `Audio` を返します。アセットがロードされていなかった場合はこのタイミングでロードを行います。

### リリース
アセットデータをメモリ上から解放します。

リリース後もアセットの登録情報は残っているため、再度プリロードしたり、使用したりすることができます。

一度ロードしたアセットをこの先しばらく使わず、メモリ消費を抑えたい場合にアセットをリリースをします。

### 登録解除
アセットの登録情報と名前をアセット管理から削除します。

該当アセットがリリースされていなかった場合はリリースします。

アプリケーション終了時にはすべてのアセットが自動でリリース、登録解除されるため、アセットの登録解除を明示的に行う必要はありません。


## 20.2 Texture アセットのサンプル

```cpp
# include <Siv3D.hpp>

void Draw()
{
	// アセットの使用
	TextureAsset(U"Windmill").draw();
	TextureAsset(U"Siv3D-kun").scaled(0.8).drawAt(200, 200);
}

void Main()
{
	// アセットの登録
	TextureAsset::Register(U"Windmill", U"example/windmill.png");
	TextureAsset::Register(U"Siv3D-kun", U"example/siv3d-kun.png", TextureDesc::Mipped);

	while (System::Update())
	{
		Draw();
	}
}
```


## 20.3 Font アセットのサンプル

```cpp
# include <Siv3D.hpp>

void Draw()
{
	// アセットの使用
	FontAsset(U"Title")(U"My Game").drawAt(400, 100);
	FontAsset(U"Menu")(U"Play").drawAt(400, 400);
	FontAsset(U"Menu")(U"Exit").drawAt(400, 500);
}

void Main()
{
	// アセットの登録
	FontAsset::Register(U"Title", 60, U"example/font/RocknRoll/RocknRollOne-Regular.ttf");
	FontAsset::Register(U"Menu", 40, Typeface::Medium);

	while (System::Update())
	{
		Draw();
	}
}
```


## 20.4 Audio アセットのサンプル

```cpp
# include <Siv3D.hpp>

void MakeSound()
{
	// アセットの使用
	AudioAsset(U"Sound").playOneShot();
}

void Main()
{
	// アセットの登録
	AudioAsset::Register(U"BGM", U"example/test.mp3");
	AudioAsset::Register(U"Sound", GMInstrument::Piano1, PianoKey::A4, 0.5s);

	// アセットの使用
	AudioAsset(U"BGM").setVolume(0.2);
	AudioAsset(U"BGM").play();

	while (System::Update())
	{
		if (MouseL.down())
		{
			MakeSound();
		}
	}
}
```


## 20.5 アセットの事前ロード
各アセットの `::Load()` を使うと、そのアセットが未ロードである場合にロードを即座に行います。ゲームの実行中にアセットのロードが発生してフレームレートが低下するのを防ぎたい場合は、この関数を呼びます。`FontAsset::Load()` では、プリロードするテキストを渡すこともできます。

```cpp

```


## 20.6 アセットの非同期ロード
各アセットの `::LoadAsync()` を使うと、そのアセットが未ロードである場合に、別スレッドを使ったアセットの非同期ロードを開始します。ロードによりメインスレッドの処理が止まるのを避けたい場合はこの関数を呼びます。

アセットの非同期ロードが完了したかは `::IsReady()` で取得できます。非同期ロード中のアセットの使用または `::Wait()` は、ロードが完了するまでメインスレッドの待機を発生させます。

:::message
OpenGL バックエンド (macOS と Linux のデフォルト, および Windows で選択した場合) では、`TextureAsset` の非同期ロードが `System::Update()` 内で完了します。`TextureAsset` の非同期ロード中は `System::Update()` の呼び出しを通常通り行ってください。
:::

```cpp

```


## 20.7 アセットのタグ

```cpp

```


## 20.8 アセット一覧の取得

```cpp

```

