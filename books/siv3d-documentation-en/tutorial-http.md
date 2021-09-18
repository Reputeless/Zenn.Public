---
title: "チュートリアル 29 | HTTP クライアント"
free: true
---

# 29. HTTP クライアント
この章では、ファイルのダウンロードなどの HTTP リクエストを行う方法を学びます。

URL を Siv3D のプログラムで表現するときは、`String` 型のエイリアス（別名）である `URL` 型を使うとコードが読みやすくなります。


## 29.1 ファイルを同期ダウンロードする
指定した URL からファイルをダウンロードしたい場合は `SimpleHTTP::Save(url, saveFilePath)` を使うのが簡単です。戻り値の `HTTPResponse` を調べると、リクエストの結果を得られます。`.isOK()` が `true` であれば成功です。

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


## 29.2 （サンプル）レスポンスを可視化する
次のようにレスポンスのステータス行とヘッダーを取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const URL url = U"https://raw.githubusercontent.com/Siv3D/siv3d.docs.images/master/logo/logo.png";
	const FilePath saveFilePath = DateTime::Now().format(U"mmss\'.png\'");

	if (const auto response = SimpleHTTP::Save(url, saveFilePath))
	{
		Console << U"------";
		Console << response.getStatusLine().rtrimmed();
		Console << U"status code: " << FromEnum(response.getStatusCode());
		Console << U"------";
		Console << response.getHeader().rtrimmed();
		Console << U"------";
	}
	else
	{
		Print << U"Failed.";
	}

	Print << saveFilePath;
	const Texture texture{ saveFilePath };

	while (System::Update())
	{
		texture.draw();
	}
}
```


## 29.3 ファイルを非同期ダウンロードする
ファイルのダウンロード中にメインスレッドの処理が止まるのを避けたい場合は、ファイルの非同期ダウンロードタスクを開始する `SimpleHTTP::SaveAsync(url, saveFilePath)` を使います。戻り値の `AsyncHTTPTask` 型のオブジェクトを通して、タスクの完了を調べ、タスクが完了したらレスポンスを調べます。

`AsyncHTTPTask` の `.isReady()` が `true` になったあとに `.getResponse()` でレスポンスを取得すると、それ以降 `.isReady()` は `false` を返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const URL url = U"https://raw.githubusercontent.com/Siv3D/siv3d.docs.images/master/logo/logo.png";
	const FilePath saveFilePath = U"logo2.png";
	Texture texture;

	// ファイルの非同期ダウンロードを開始
	AsyncHTTPTask task = SimpleHTTP::SaveAsync(url, saveFilePath);

	while (System::Update())
	{
		// 非同期タスクが完了した
		if (task.isReady())
		{
			// レスポンスが 200 (OK) なら
			if (task.getResponse().isOK())
			{
				texture = Texture{ saveFilePath };
			}
			else
			{
				Print << U"Failed.";
			}
		}

		if (texture)
		{
			texture.draw();
		}
	}
}
```


## 29.4 ファイル非同期ダウンロードする（キャンセル・進捗表示）
`AsyncHTTPTask` は `.cancel()` でタスクをキャンセルできます。進捗を取得したい場合は `.getProgress()` を使うと `HTTPProgress` 型で返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 24, Typeface::Medium };

	// HTTP 通信の検証用サービス (http://httpbin.org) を利用
	// 1024 バイトのデータを 4 秒かけてダウンロードする URL
	const URL url = U"http://httpbin.org/drip?duration=4&numbytes=1024&code=200&delay=0";
	const FilePath saveFilePath = U"drip.txt";

	AsyncHTTPTask task;

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Download", Vec2{ 20, 20 }, 140, task.isEmpty()))
		{
			task = SimpleHTTP::SaveAsync(url, saveFilePath);
		}

		if (SimpleGUI::Button(U"Cancel", Vec2{ 180, 20 }, 140, (task.getStatus() == HTTPAsyncStatus::Downloading)))
		{
			// タスクをキャンセル
			task.cancel();
		}

		// タスクの進捗
		const HTTPProgress progress = task.getProgress();

		if (progress.status == HTTPAsyncStatus::None_)
		{
			font(U"status: None_").draw(20, 60);
		}
		else if (progress.status == HTTPAsyncStatus::Downloading)
		{
			font(U"status: Downloading").draw(20, 60);
		}
		else if (progress.status == HTTPAsyncStatus::Failed)
		{
			font(U"status: Failed").draw(20, 60);
		}
		else if (progress.status == HTTPAsyncStatus::Canceled)
		{
			font(U"status: Canceled").draw(20, 60);
		}
		else if (progress.status == HTTPAsyncStatus::Succeeded)
		{
			font(U"status: Succeeded").draw(20, 60);
		}

		if (progress.status == HTTPAsyncStatus::Downloading)
		{
			// ダウンロード済みのサイズ（バイト）
			const int64 downloaded = progress.downloaded_bytes;

			// ダウンロードするファイルのサイズ（バイト）。取得できない場合は none
			const Optional<int64> total = progress.download_total_bytes;

			if (total)
			{
				font(U"downloaded: {} bytes / {} bytes"_fmt(downloaded, *total)).draw(20, 100);

				const double progress0_1 = (static_cast<double>(downloaded) / *total);
				const RectF rect{ 20, 140, 500, 30 };
				rect.drawFrame(2, 0);
				RectF{ rect.pos, (rect.w * progress0_1), rect.h }.draw();
			}
			else
			{
				font(U"downloaded: {} bytes"_fmt(downloaded)).draw(20, 100);
			}
		}

		if (task.isReady())
		{
			if (const auto& response = task.getResponse())
			{
				Console << U"------";
				Console << response.getStatusLine().rtrimmed();
				Console << U"status code: " << FromEnum(response.getStatusCode());
				Console << U"------";
				Console << response.getHeader().rtrimmed();
				Console << U"------";

				if (response.isOK())
				{
					Console << FileSystem::FileSize(saveFilePath) << U" bytes";
					Console << U"------";
				}
			}
			else
			{
				Print << U"Failed.";
			}
		}
	}
}
```


## 29.5 （サンプル）GET リクエスト

```cpp
# include <Siv3D.hpp>

void Main()
{
	const URL url = U"https://httpbin.org/bearer";
	const HashTable<String, String> headers = { { U"Authorization", U"Bearer TOKEN123456abcdef" } };
	const FilePath saveFilePath = U"auth_result.json";

	if (const auto response = SimpleHTTP::Get(url, headers, saveFilePath))
	{
		Console << U"------";
		Console << response.getStatusLine().rtrimmed();
		Console << U"status code: " << FromEnum(response.getStatusCode());
		Console << U"------";
		Console << response.getHeader().rtrimmed();
		Console << U"------";

		if (response.isOK())
		{
			Print << TextReader{ saveFilePath }.readAll();
		}
	}
	else
	{
		Print << U"Failed.";
	}

	while (System::Update())
	{

	}
}
```


## 29.6 （サンプル）POST リクエスト

```cpp
# include <Siv3D.hpp>

void Main()
{
	const URL url = U"https://httpbin.org/post";
	const HashTable<String, String> headers = { { U"Content-Type", U"application/json" } };
	const std::string data = JSON
	{
		{ U"body", U"Hello, Siv3D!" },
		{ U"date", DateTime::Now().format() },
	}.formatUTF8();
	const FilePath saveFilePath = U"post_result.json";

	if (auto response = SimpleHTTP::Post(url, headers, data.data(), data.size(), saveFilePath))
	{
		Console << U"------";
		Console << response.getStatusLine().rtrimmed();
		Console << U"status code: " << FromEnum(response.getStatusCode());
		Console << U"------";
		Console << response.getHeader().rtrimmed();
		Console << U"------";

		if (response.isOK())
		{
			Print << TextReader{ saveFilePath }.readAll();
		}
	}
	else
	{
		Print << U"Failed.";
	}

	while (System::Update())
	{

	}
}
```

