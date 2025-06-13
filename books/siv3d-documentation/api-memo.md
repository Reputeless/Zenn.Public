---
title: "公式サイト未移転の API リファレンス"
free: true
---

# 1. `<Buffer2D.hpp>`

## 1.1 Buffer2D による Polygon へのテクスチャ貼り付け

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"🥺"_emoji };

	while (System::Update())
	{
		const double angle = (Scene::Time() * 30_deg);

		Shape2D::Pentagon(60, Vec2{ 100, 100 }, angle)
			.toBuffer2D(Arg::center(100, 100), texture.size())
			.draw(texture);

		Shape2D::Ngon(7, 60, Vec2{ 300, 100 }, angle)
			.toBuffer2D(Arg::center(300, 100), texture.size())
			.draw(texture);

		Shape2D::Plus(60, 30, Vec2{ 500, 100 }, angle)
			.toBuffer2D(Arg::center(500, 100), texture.size())
			.draw(texture);

		Shape2D::Heart(60, Vec2{ 700, 100 }, angle)
			.toBuffer2D(Arg::center(700, 100), texture.size())
			.draw(texture);


		Shape2D::Pentagon(60, Vec2{ 100, 300 }, angle)
			.toBuffer2D(Arg::center(100, 300), texture.size(), angle)
			.draw(texture);

		Shape2D::Ngon(7, 60, Vec2{ 300, 300 }, angle)
			.toBuffer2D(Arg::center(300, 300), texture.size(), angle)
			.draw(texture);

		Shape2D::Plus(60, 30, Vec2{ 500, 300 }, angle)
			.toBuffer2D(Arg::center(500, 300), texture.size(), angle)
			.draw(texture);

		Shape2D::Heart(60, Vec2{ 700, 300 }, angle)
			.toBuffer2D(Arg::center(700, 300), texture.size(), angle)
			.draw(texture);


		Shape2D::Pentagon(60, Vec2{ 100, 500 })
			.toBuffer2D(Arg::center(100, 500), texture.size(), angle)
			.draw(texture);

		Shape2D::Ngon(7, 60, Vec2{ 300, 500 })
			.toBuffer2D(Arg::center(300, 500), texture.size(), angle)
			.draw(texture);

		Shape2D::Plus(60, 30, Vec2{ 500, 500 })
			.toBuffer2D(Arg::center(500, 500), texture.size(), angle)
			.draw(texture);

		Shape2D::Heart(60, Vec2{ 700, 500 })
			.toBuffer2D(Arg::center(700, 500), texture.size(), angle)
			.draw(texture);
	}
}
```


# 2. `<PerlinNoise.hpp>`

## 2.1 PerlinNoise を生成

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	Image image1{ 512, 512, Palette::White };
	Image image2{ 512, 512, Palette::White };
	DynamicTexture texture1{ image1 };
	DynamicTexture texture2{ image2 };

	PerlinNoise noise;
	size_t oct = 5;
	double persistence = 0.5;
	const Array<String> options = Range(1, 6).map(Format);

	while (System::Update())
	{
		SimpleGUI::RadioButtons(oct, options, Vec2{ 1040, 40 });

		SimpleGUI::Slider(U"{:.2f}"_fmt(persistence), persistence, Vec2{ 1040, 280 });

		if (SimpleGUI::Button(U"Generate", Vec2{ 1040, 320 }))
		{
			noise.reseed(RandomUint64());
			const int32 octaves = static_cast<int32>(oct + 1);

			for (auto p : step(image1.size()))
			{
				image1[p] = ColorF{ noise.normalizedOctave2D0_1(p / 128.0, octaves, persistence) };
			}
			for (auto p : step(image2.size()))
			{
				image2[p] = ColorF{ noise.octave2D0_1(p / 128.0, octaves, persistence) };
			}
			texture1.fill(image1);
			texture2.fill(image2);
		}

		texture1.draw();
		texture2.draw(512, 0);
	}
}
```


# 3. `<PoissonDisk2D.hpp>`

## 3.1 ほどよい距離で重ならない点群を生成する

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.2, 0.3, 0.4 });

	constexpr Rect rect{ 100, 100, 600, 400 };

	double r = 15.0;

	// 点群を生成
	PoissonDisk2D pd{ rect.size, r };

	while (System::Update())
	{
		rect.drawFrame(1, 1, ColorF{ 0.2 });

		for (const auto& point : pd.getPoints())
		{
			Circle{ point, (r / 4) }.movedBy(rect.pos).draw();
		}

		if (SimpleGUI::Slider(r, 5.0, 40.0, Vec2{ 10, 10 }))
		{
			pd = PoissonDisk2D{ rect.size, r };
		}
	}
}
```


# 4. `<ChildProcess.hpp>`

## `ChildProcess` クラス

## 4.1 子プロセスの作成

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

## 4.2 子プロセスとの標準入出力パイプ通信

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


# 5. `<SimpleAnimation.hpp>`

## 5.1 キーフレームアニメーション

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.3, 0.5, 0.4 });
	const Texture texture1{ U"🐥"_emoji };
	const Texture texture2{ U"🐢"_emoji };

	SimpleAnimation a1;
	a1.setLoop(12s)
		.set(U"r", { 0.5s, 0 }, { 1.5s, 1 }, EaseOutBounce)
		.set(U"g", { 1s, 0 }, { 2s, 1 }, EaseOutBounce)
		.set(U"b", { 1.5s, 0 }, { 2.5s, 1 }, EaseOutBounce)
		.set(U"angle", { 3s, 0_deg }, { 8.5s, 720_deg }, EaseOutBounce)
		.set(U"size", { 0s, 0 }, { 0.5s, 320 }, EaseOutExpo)
		.set(U"size", { 9s, 320 }, { 9.5s, 0 }, EaseOutExpo)
		.start();

	SimpleAnimation a2;
	a2.setLoop(6s)
		.set(U"x", { 1s, 150 }, { 3s, 650 }, EaseInOutExpo)
		.set(U"y", { 0s, 350 }, { 1s, 150 }, EaseOutBack)
		.set(U"y", { 3s, 150 }, { 4s, 350 }, EaseInQuad)
		.set(U"t", { 0s, 0 }, { 4s, 12_pi }, EaseInOutQuad)
		.set(U"a", { 5s, 1 }, { 6s, 0 }, EaseOutCubic)
		.start();

	SimpleAnimation a3;
	a3.setLoop(6s)
		.set(U"x", { 0s, 100 }, { 3s, 700 }, EaseInOutQuad)
		.set(U"x", { 3s, 700 }, { 6s, 100 }, EaseInOutQuad)
		.set(U"mirrored", { 0s, 1 }, { 3s, 1 })
		.set(U"mirrored", { 3s, 0 }, { 6s, 0 })
		.start();

	while (System::Update())
	{
		Triangle{ Scene::Center(), a1[U"size"], a1[U"angle"] }
		.draw(ColorF{ a1[U"r"], 0, 0 }, ColorF{ 0, a1[U"g"], 0 }, ColorF{ 0, 0, a1[U"b"] });

		texture1
			.drawAt(a2[U"x"], a2[U"y"] + Math::Sin(a2[U"t"]) * 20.0, ColorF{ 1, a2[U"a"] });

		texture2
			.mirrored(a3[U"mirrored"])
			.drawAt(a3[U"x"], 500);
	}
}
```


# 6. `<SVG.hpp>`

## 6.1 SVG ファイルから Texture を作成

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	const SVG svg{ U"example/svg/cat.svg" };

	const Array<Texture> textures =
	{
		Texture{ svg.render() }, // オリジナルのサイズでレンダリング
		Texture{ svg.render(svg.size() * 2) },
		Texture{ svg.render(svg.size() * 3) },
		Texture{ svg.render(svg.size() * 4) },
	};

	while (System::Update())
	{
		for (auto[i, texture] : Indexed(textures))
		{
			texture.draw(i * 200.0, 20);

			PutText(U"{}"_fmt(texture.size()), Vec2{ (i * 200 + 60), 40 });
		}
	}
}
```


# 7. `<TimeProfiler.hpp>`

## 7.1 処理時間のプロファイリングを行う

```cpp
# include <Siv3D.hpp>

void Main()
{
	TimeProfiler profiler{ U"Test" };

	while (System::Update())
	{
		ClearPrint();
		profiler.print();

		{
			profiler.begin(U"Rect");

			for (auto i : step(20))
			{
				Rect{ Arg::center(20 + i * 40, 200), 30 }.draw();
			}

			profiler.end(U"Rect");
		}

		{
			profiler.begin(U"Circle");

			for (auto i : step(20))
			{
				Circle{ 20 + i * 40, 300, 15 }.draw();
			}

			profiler.end(U"Circle");
		}

		{
			profiler.begin(U"Star");

			for (auto i : step(20))
			{
				Shape2D::Star(15, Vec2{ 20 + i * 40, 400 }).draw();
			}

			profiler.end(U"Star");
		}
	}

	profiler.log();
}
```


# 8. `<UserAction.hpp>`

## 8.1 ユーザアクションを取得する
エスケープキーが押されたり、ウィンドウの閉じるボタンが押されたりしたときに、プログラムを終了せず別のアクションを実行するためのサンプルです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	System::SetTerminationTriggers(UserAction::NoAction);

	while (System::Update())
	{
		const auto userAction = System::GetUserActions();

		if (userAction & UserAction::CloseButtonClicked)
		{
			Print << U"CloseButtonClicked";
		}

		if (userAction & UserAction::EscapeKeyDown)
		{
			Print << U"EscapeKeyDown";
		}

		if (userAction & UserAction::WindowDeactivated)
		{
			Print << U"WindowDeactivated";
		}

		if (userAction & UserAction::AnyKeyDown)
		{
			Print << U"AnyKeyDown";
		}

		if (userAction & UserAction::MouseButtonDown)
		{
			Print << U"MouseButtonDown";
		}

		if (SimpleGUI::Button(U"Exit", Vec2{ 700, 20 }))
		{
			System::Exit();
		}
	}
}
```


# 9. `<ZIPReader.hpp>`

## `ZIPReader` クラス

ZIP アーカイブファイルのデータを読み込み、展開するクラスです。

### メンバ関数

---

#### ZIPReader();

デフォルトコンストラクタ

---

#### ZIPReader(FilePathView path);

- `path`: オープンする ZIP アーカイブファイルのパス

コンストラクタ。ZIP アーカイブファイルをオープンします。

---

#### ~ZIPReader();

デストラクタ。ZIP アーカイブファイルをクローズします。

---

#### bool open(FilePathView path);

- `path`: オープンする ZIP アーカイブファイルのパス
- 戻り値: オープンに成功した場合 `true`, それ以外の場合は `false`

ZIP アーカイブファイルをオープンします。すでにオープンしていた場合は、古いほうを先にクローズしてからオープンします。

---

#### void close();

ZIP アーカイブファイルをクローズします。

---

#### bool isOpen() const noexcept;

- 戻り値: オープンしている場合 `true`, それ以外の場合は `false`

ZIP アーカイブファイルをオープンしているかを返します。

---

#### explicit operator bool() const noexcept;

- 戻り値: オープンしている場合 `true`, それ以外の場合は `false`

ZIP アーカイブファイルをオープンしているかを返します。

---

#### const Array<FilePath>& enumPaths() const;

- ZIP アーカイブファイルの内容一覧

ZIP アーカイブファイルの内容一覧を返します。

---

#### bool extractAll(FilePathView targetDirectory) const;

- `targetDirectory`: 展開先のディレクトリ
- 戻り値: 展開に成功した場合 `true`, それ以外の場合は `false`

オープンしている ZIP アーカイブファイルの中身を、指定したディレクトリにすべて展開します。

---

#### bool extractFiles(StringView pattern, FilePathView targetDirectory) const;

- `pattern` 展開対象のファイルをマッチさせるパターン
- `targetDirectory`: 展開先のディレクトリ
- 戻り値: 展開に成功した場合 `true`, それ以外の場合は `false`

指定したパターンにマッチするファイルを、指定したディレクトリにすべて展開します。

---

#### MemoryReader extract(FilePathView filePath) const;

- `filepath` 展開するファイル名
- 戻り値: 展開されたデータ

指定したファイルを展開し、`MemoryReader` オブジェクトに変換します。

---

#### Blob extractToBlob(FilePathView filePath) const;

- `filepath` 展開するファイル名
- 戻り値: 展開されたデータ

指定したファイルを展開し、`Blob` オブジェクトに変換します。

---

## 9.1 ZIP ファイル内に圧縮されている画像から `Texture` を作成する

```cpp
# include <Siv3D.hpp>

void Main()
{
	ZIPReader zip{ U"example/zip/zip_test.zip" };

	// ZIP 内の内容を列挙
	Print << zip.enumPaths();

	// ZIP 内のファイルから Texture を作成
	const Texture texutre{ zip.extract(U"zip_test/image/windmill.png") };

	while (System::Update())
	{
		texutre.draw();
	}
}
```

# 10. `<Zlib.hpp>`

## `Zlib::` 名前空間

zlib 形式のデータの圧縮・展開を行う関数群です。Siv3D では `Compressoion::` 名前空間で提供される Zstandard 形式の圧縮・展開の使用が推奨されます。

---

#### constexpr int32 DefaultCompressionLevel = 6;

デフォルトの zlib 圧縮レベルです。

---

#### constexpr int32 MinCompressionLevel = 1;

最低の zlib 圧縮レベルです。圧縮率より速度を優先します。

---

#### constexpr int32 MaxCompressionLevel = 9;

最高の zlib 圧縮レベルです。速度より圧縮率を優先します。

---

#### Blob Compress(const void* data, size_t size, int32 compressionLevel = DefaultCompressionLevel);

- `data`: 圧縮するデータの先頭ポインタ
- `size`: 圧縮するデータのサイズ（バイト）
- `compressionLevel`: 圧縮レベル
- 戻り値: 圧縮されたデータ

データを zlib 形式で圧縮します。

---

#### bool Compress(const void* data, size_t size, Blob& dst, int32 compressionLevel = DefaultCompressionLevel);

- `data`: 圧縮するデータの先頭ポインタ
- `size`: 圧縮するデータのサイズ（バイト）
- `dst`: 圧縮されたデータの格納先
- `compressionLevel`: 圧縮レベル
- 戻り値: 圧縮に成功した場合 `true`, それ以外の場合は `false`

データを zlib 形式で圧縮します。

---

#### Blob Compress(const Blob& blob, int32 compressionLevel = DefaultCompressionLevel);

- `blob`: 圧縮するデータ
- `compressionLevel`: 圧縮レベル
- 戻り値: 圧縮されたデータ

データを zlib 形式で圧縮します。

---

#### bool Compress(const Blob& blob, Blob& dst, int32 compressionLevel = DefaultCompressionLevel);

- `blob`: 圧縮するデータ
- `dst`: 圧縮されたデータの格納先
- `compressionLevel`: 圧縮レベル
- 戻り値: 圧縮に成功した場合 `true`, それ以外の場合は `false`

データを zlib 形式で圧縮します。

---

#### Blob Decompress(const void* data, size_t size);

- `data`: 展開するデータの先頭ポインタ
- `size`: 展開するデータのサイズ
- 戻り値: 展開されたデータ

zlib 形式で圧縮されているデータを展開します。

---

#### bool Decompress(const void* data, size_t size, Blob& dst);

- `data`: 展開するデータの先頭ポインタ
- `size`: 展開するデータのサイズ
- `dst`: 展開されたデータの格納先
- 戻り値: 展開に成功した場合 `true`, それ以外の場合は `false`

zlib 形式で圧縮されているデータを展開します。

---

#### Blob Decompress(const Blob& blob);

- `blob`: 展開するデータ
- 戻り値: 展開されたデータ

zlib 形式で圧縮されているデータを展開します。

---

#### bool Decompress(const Blob& blob, Blob& dst);

- `blob`: 展開するデータ
- `dst`: 展開されたデータの格納先
- 戻り値: 展開に成功した場合 `true`, それ以外の場合は `false`

zlib 形式で圧縮されているデータを展開します。

---

