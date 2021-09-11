---
title: "チュートリアル 29 | HTTP クライアント"
free: true
---

# 29. HTTP クライアント
この章では、ファイルのダウンロードなどの HTTP リクエストを行う方法を学びます。

URL を Siv3D のプログラムで表現するときは、`String` 型のエイリアス（別名）である `URL` 型を使うとコードが読みやすくなります。


## 29.1 ファイルを同期ダウンロードする

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Siv3D のロゴ
	const URL url = U"https://raw.githubusercontent.com/Siv3D/siv3d.docs.images/master/logo/logo.png";
	const FilePath saveFilePath = U"logo.png";
	Texture texture;

	// ファイルを同期ダウンロード
	// ステータスコードが 200 (OK) なら
	if (SimpleHTTP::Save(url, saveFilePath).isOK())
	{
		texture = Texture{ saveFilePath };
	}
	else
	{
		Print << U"Failed.";
	}

	while (System::Update())
	{
		if (texture)
		{
			texture.draw();
		}
	}
}
```


## 29.2 レスポンスを可視化する

```cpp

```


## 29.3 ファイルを非同期ダウンロードする

```cpp

```


## 29.4 ファイル非同期ダウンロードする（キャンセル・進捗表示）

```cpp

```


## 29.5 GET リクエスト

```cpp

```


## 29.6 POST リクエスト

```cpp

```

