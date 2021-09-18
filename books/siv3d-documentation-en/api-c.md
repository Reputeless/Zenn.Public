---
title: "API リファレンス | C"
free: true
---

:::message
将来追加予定の API リファレンスの仮ページです。
:::

# 6. `<ChildProcess.hpp>`

## `ChildProcess` クラス

## 6.1 子プロセスの作成

```cpp
# include <Siv3D.hpp>

void Main()
{
# if SIV3D_PLATFORM(WINDOWS)

	// 子プロセスを作成
	ChildProcess child{ U"C:/Windows/System32/notepad.exe" };

# elif SIV3D_PLATFORM(MACOS)

	// 子プロセスを作成
	ChildProcess child{ U"/System/Applications/Calculator.app/Contents/MacOS/Calculator" };

# elif SIV3D_PLATFORM(LINUX)

	// 子プロセスを作成
	ChildProcess child{ U"/usr/bin/firefox", U"www.mozilla.org" };

# endif

	if (not child)
	{
		throw Error{ U"Failed to create a process" };
	}

	while (System::Update())
	{
		ClearPrint();

		// プロセスが実行中かを取得
		Print << child.isRunning();

		// プロセスが終了した場合、その終了コード
		Print << child.getExitCode();

		if (child.isRunning())
		{
			if (SimpleGUI::Button(U"Terminate", Vec2{ 600, 20 }))
			{
				// プロセスを強制終了
				child.terminate();
			}
		}
	}
}
```

## 6.2 子プロセスとの標準入出力パイプ通信

子プロセスのプログラム
```cpp
# include <iostream>

int main()
{
    int a, b;
    std::cin >> a >> b;
    std::cout << (a + b) << std::endl;
}
```

子プロセスを実行するアプリケーションのプログラム
```cpp
# include <Siv3D.hpp>

void Main()
{
# if SIV3D_PLATFORM(WINDOWS)

	// 子プロセスを作成（パイプライン処理）
	ChildProcess child{ U"console.exe", Pipe::StdInOut };

# else

	// 子プロセスを作成（パイプライン処理）
	ChildProcess child{ U"console", Pipe::StdInOut };

# endif

	if (not child)
	{
		throw Error{ U"Failed to create a process" };
	}

	child.ostream() << 10 << std::endl;
	child.ostream() << 20 << std::endl;

	int32 result;
	child.istream() >> result;
	Print << U"result: " << result;

	while (System::Update())
	{

	}
}
```


# 7. `<Circle.hpp>`

## `Circle` クラス


## 7.1 中心の色と外周の色を指定し、グラデーションで円を描画

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中心が黄色、外周が黒
		Circle{ Scene::Center(), 400 }.draw(Palette::Yellow, Palette::Black);
	}
}
```

