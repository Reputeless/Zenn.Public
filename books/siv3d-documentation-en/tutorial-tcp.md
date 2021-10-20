---
title: "Tutorial 40 | TCP Connections"
free: true
---

# 40. TCP Connections
この章では、TCP 通信を使ったネットワークプログラミングの方法を学びます。

## 40.1 （サンプル）1 対 1 の TCP 通信
`TCPServer` クラスおよび `TCPClient` クラスは、`.send()` および `.read()` を使ってデータを送受信します。

次のサンプルでは、お互いのマウスカーソルの座標を送受信します。サーバ側とクライアント側、2 つのアプリケーションを実行します。

### サーバ側

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ポート番号
	constexpr uint16 port = 50000;
	bool connected = false;

	TCPServer server;
	server.startAccept(port);

	Window::SetTitle(U"TCPServer: Waiting for connection...");

	Point clientPlayerPos{ 0, 0 };

	while (System::Update())
	{
		const Point serverPlayerPos = Cursor::Pos();

		if (server.hasSession())
		{
			if (not connected) // クライアントが接続
			{
				connected = true;

				Window::SetTitle(U"TCPServer: Connection established!");
			}

			// 送信
			server.send(serverPlayerPos);

			// 受信
			while (server.read(clientPlayerPos));
		}

		// クライアントが切断
		if (connected && (not server.hasSession()))
		{
			// 切断
			server.disconnect();

			connected = false;

			Window::SetTitle(U"TCPServer: Waiting for connection...");

			// 新たな接続を受け付け開始
			server.startAccept(port);
		}

		Circle{ serverPlayerPos, 30 }.draw(Palette::Skyblue);

		Circle{ clientPlayerPos, 10 }.draw(Palette::Orange);
	}
}
```


### クライアント側

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 接続先の IPv4 アドレス（今回は自身の PC なので Localhost）
	const IPv4Address ip = IPv4Address::Localhost();

	// ポート番号
	constexpr uint16 port = 50000;

	bool connected = false;

	TCPClient client;

	// 接続を試行
	client.connect(ip, port);

	Window::SetTitle(U"TCPClient: Waiting for connection...");

	Point serverPlayerPos{ 0, 0 };

	while (System::Update())
	{
		const Point clientPlayerPos = Cursor::Pos();

		if (client.isConnected())
		{
			if (not connected) // 接続された
			{
				connected = true;

				Window::SetTitle(U"TCPClient: Connection established!");
			}

			// 送信
			client.send(clientPlayerPos);

			// 受信
			while (client.read(serverPlayerPos));
		}

		if (client.hasError()) // 切断/接続エラー
		{
			client.disconnect();

			connected = false;

			Window::SetTitle(U"TCPClient: Waiting for connection...");

			// 接続を再試行
			client.connect(ip, port);
		}

		Circle{ clientPlayerPos, 30 }.draw(Palette::Skyblue);

		Circle{ serverPlayerPos, 30 }.draw(Palette::Orange);
	}
}
```
