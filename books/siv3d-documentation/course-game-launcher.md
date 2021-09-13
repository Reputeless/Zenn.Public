---
title: "コース | ゲームランチャー"
free: true
---

ゲーム展示イベント等で複数のゲームを展示する際に活用できるランチャーを作ります。このプログランでは、各ゲームのフォルダから設定ファイルや画像をロードします。Web アプリにも対応します。

## 1. ランチャーのプログラム

```cpp
# include <Siv3D.hpp>

// 起動設定
namespace Config
{
	// Web アプリを起動する際に使用する Web ブラウザのパス
	const FilePath BrowserPath = U"C:/Program Files (x86)/Google/Chrome/Application/chrome.exe";
}

// UI オプション
namespace UI
{
	// ウィンドウのサイズ
	constexpr Size WindowSize{ 1280, 640 };

	// ウィンドウのフレームを表示するか
	constexpr bool Frameless = true;

	// タイルの基本サイズ
	constexpr double TileSize = 250;

	// 背景色
	constexpr ColorF BackgroundColor{ 0.85, 0.9, 0.95 };

	// タイル選択の色
	constexpr ColorF TileFrmaeColor{ 1.0, 0.7, 0.3 };

	constexpr Vec2 BaseTilePos{ 240, 200 };

	constexpr RectF InfoArea{ 180, 340, 715, 185 };

	constexpr RectF StaffArea{ 180, 530, 715, 70 };

	constexpr RectF PlayButton{ 900, 340, 220, 85 };

	constexpr ColorF PlayButtonColor{ 0.0, 0.67, 1.0 };

	constexpr RectF ControlArea{ 900, 430, 220, 170 };

	constexpr ColorF InfoAreaMouseOverColor{ 1.0, 0.95, 0.9 };

	constexpr ColorF TextColor{ 0.2 };

	constexpr double InfoAreaRound = 8.0;
}

// ゲームの情報
struct Game
{
	// ゲームのタイトル
	String title;

	// ゲーム実行ファイル または URL
	FilePath path;

	// Web ブラウザで起動
	bool isWebApp = false;

	// ゲームの画像
	Texture texture;

	// ゲームの説明文
	String desc;

	// ゲームの開発スタッフ
	String staff;

	// ゲームの開発ツール
	String tools;

	// マウスを使用するか
	bool useMouse = false;

	// キーボードを使用するか
	bool useKeyboard = false;

	// ゲームパッドを使用するか
	bool useGamepad = false;

	// ランチャー表示優先度（大きいほど優先）
	int32 priority = 0;
};

// ゲームのパスが Web ページかどうかを調べる関数
bool IsURL(StringView path)
{
	return (path.starts_with(U"http://") || path.starts_with(U"https://"));
}

// ゲームの情報をロードする関数
Array<Game> LoadGames()
{
	// ゲームのリスト
	Array<Game> games;

	// ホームディレクトリ
	const FilePath homeDirectory = FileSystem::CurrentDirectory();

	// ホームディレクトリにあるアイテムを検索
	for (const FilePath& gameDirectory : FileSystem::DirectoryContents(homeDirectory, Recursive::No))
	{
		// フォルダでない場合はスキップ
		if (not FileSystem::IsDirectory(gameDirectory))
		{
			continue;
		}

		// launcher_info.ini を読み込む
		const FilePath iniPath = (gameDirectory + U"launcher_info.ini");
		const INI ini{ iniPath };

		// 読み込みに失敗
		if (not ini)
		{
			continue;
		}

		// ゲームの情報を読み込む
		Game game;
		game.title = ini[U"Game.title"];
		game.texture = Texture{ Image{ gameDirectory + ini[U"Game.image"] }.squareClipped(), TextureDesc::Mipped };
		game.desc = ini[U"Game.desc"].replaced(U"\\n", U"\n");
		game.staff = ini[U"Game.staff"];
		game.tools = ini[U"Game.tools"];
		game.useMouse = ini.get<bool>(U"Game.mouse");
		game.useKeyboard = ini.get<bool>(U"Game.keyboard");
		game.useGamepad = ini.get<bool>(U"Game.gamepad");
		game.priority = ini.get<int32>(U"Game.priority");

		const String path = game.path = ini[U"Game.path"];
		game.path = IsURL(path) ? path : (gameDirectory + path);
		game.isWebApp = (not path.ends_with(U".exe")) && (not path.ends_with(U".app"));

		// ゲームのリストに追加
		games << game;
	}

	// プライオリティに基づいてゲームをソート
	return games.sort_by([](const Game& a, const Game& b) { return a.priority > b.priority; });
}

void Main()
{
	// ウィンドウと背景色
	Window::SetTitle(U"Game Launcher");
	Window::Resize(UI::WindowSize);
	Window::SetStyle(UI::Frameless ? WindowStyle::Frameless : WindowStyle::Fixed);
	Scene::SetBackground(UI::BackgroundColor);

	// フォント
	FontAsset::Register(U"Game.Title", 42, Typeface::Heavy);
	FontAsset::Register(U"Game.Desc", 26);
	FontAsset::Register(U"Game.Small", 16);
	FontAsset::Register(U"Game.Play", 30, Typeface::Heavy);

	// 再生アイコン
	TextureAsset::Register(U"Icon.Play", 0xf144_icon, 48);

	// ゲーム情報
	const Array<Game> games = LoadGames();
	if (not games)
	{
		System::MessageBoxOK(U"ゲームがありません。");
		return;
	}

	// 実行中のゲームのプロセス
	Optional<ChildProcess> process;

	// 選択しているタイルのインデックス [0, games.size()-1]
	size_t selectGameIndex = 0;

	// タイルのスクロール用の変数
	double tileOffsetX = 0.0, targetTileOffsetX = 0.0, tileOffsetXVelocity = 0.0;

	while (System::Update())
	{
		// 現在選択されているゲーム
		const Game& game = games[selectGameIndex];

		///////////////////////////////////////////////
		//
		//	ウィンドウの最小化・復帰
		//
		if (process)
		{
			// プロセスが実行中なら
			if (process->isRunning())
			{
				// ウィンドウを最小化
				Window::Minimize();
				continue;
			}
			else // プロセスが終了したら
			{
				// ウィンドウを復帰
				Window::Restore();
				process.reset();
			}
		}

		///////////////////////////////////////////////
		//
		//	ゲームの起動
		//
		if (UI::PlayButton.leftClicked())
		{
			if (game.isWebApp)
			{
				// Web ブラウザを起動
				process = ChildProcess{ Config::BrowserPath, game.path };
			}
			else
			{
				// 実行ファイルを起動
				process = ChildProcess{ game.path };
			}
		}

		///////////////////////////////////////////////
		//
		//	選択しているタイルの変更
		//
		for (auto i : step(games.size()))
		{
			const Vec2 center = UI::BaseTilePos.movedBy(tileOffsetX + i * UI::TileSize, 0);
			const RectF tile{ Arg::center = center, (UI::TileSize - 20) };

			// タイルがクリックされたら選択
			if (tile.leftClicked())
			{
				selectGameIndex = i;
			}
		}

		// [←][→] キーを押して選択の移動
		if (KeyLeft.down())
		{
			selectGameIndex = (selectGameIndex > 0) ? (selectGameIndex - 1) : 0;
		}
		else if (KeyRight.down())
		{
			selectGameIndex = Min(selectGameIndex + 1, games.size() - 1);
		}

		///////////////////////////////////////////////
		//
		//	タイル表示のスクロール更新
		//
		{
			const Vec2 center = UI::BaseTilePos.movedBy(targetTileOffsetX + selectGameIndex * UI::TileSize, 0);
			const RectF tile{ Arg::center = center, (UI::TileSize - 20) };

			// 左端、右端のタイルが画面外ならスクロール
			if (tile.x <= 0)
			{
				targetTileOffsetX += UI::TileSize;
			}
			else if (Scene::Width() <= tile.tr().x)
			{
				targetTileOffsetX -= UI::TileSize;
			}

			// スムーズスクロール
			tileOffsetX = Math::SmoothDamp(tileOffsetX, targetTileOffsetX, tileOffsetXVelocity, 0.1);
		}

		///////////////////////////////////////////////
		//
		//	描画
		//
		for (auto [i, g] : Indexed(games))
		{
			const Vec2 center = UI::BaseTilePos.movedBy(tileOffsetX + i * UI::TileSize, 0);
			const RectF tile{ Arg::center = center, (UI::TileSize - 20) };

			// 選択されていたら、タイルの枠を描画
			if (selectGameIndex == i)
			{
				tile.stretched(6)
					.drawShadow(Vec2{ 0, 3 }, 8, 0)
					.draw(UI::BackgroundColor)
					.drawFrame(4, 0, ColorF(UI::TileFrmaeColor, 0.6 + Periodic::Sine0_1(1s) * 0.4));
			}

			// ゲーム画像を描画
			tile(g.texture).drawAt(center);

			if (tile.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}
		}

		// タイトルと説明
		{
			UI::InfoArea.rounded(UI::InfoAreaRound).draw(UI::InfoArea.mouseOver() ? UI::InfoAreaMouseOverColor : ColorF(1.0));
			FontAsset(U"Game.Title")(game.title).draw(UI::InfoArea.pos.movedBy(30, 20), UI::TextColor);
			FontAsset(U"Game.Desc")(game.desc).draw(UI::InfoArea.stretched(-80, -30, -20, -30), UI::TextColor);
		}

		// スタッフと開発ツール
		{
			UI::StaffArea.rounded(UI::InfoAreaRound).draw(UI::StaffArea.mouseOver() ? UI::InfoAreaMouseOverColor : ColorF(1.0));
			FontAsset(U"Game.Small")(game.staff).draw(UI::StaffArea.pos.movedBy(30, 10), UI::TextColor);
			FontAsset(U"Game.Small")(U"開発ツール: {}"_fmt(game.tools)).draw(UI::StaffArea.pos.movedBy(30, 35), UI::TextColor);
		}

		// プレイボタン
		{
			UI::PlayButton.rounded(UI::InfoAreaRound).draw(UI::PlayButton.mouseOver() ? ColorF(HSV(UI::PlayButtonColor) + HSV(10.0, -0.1, 0.0)) : UI::PlayButtonColor);
			Transformer2D t(Mat3x2::Scale(0.95 + Periodic::Sine0_1(1.2s) * 0.05, UI::PlayButton.center()));
			TextureAsset(U"Icon.Play").drawAt(UI::PlayButton.center().movedBy(-60, 0));
			FontAsset(U"Game.Play")(U"あそぶ").draw(Arg::leftCenter = UI::PlayButton.center().movedBy(-25, 0));

			if (UI::PlayButton.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}
		}

		// 操作方法
		{
			UI::ControlArea.rounded(UI::InfoAreaRound).draw(UI::ControlArea.mouseOver() ? UI::InfoAreaMouseOverColor : ColorF(1.0));
			String control = U"操作\n";
			control += game.useMouse ? U"・マウス\n" : U"";
			control += game.useKeyboard ? U"・キーボード\n" : U"";
			control += game.useGamepad ? U"・ゲームパッド\n" : U"";
			FontAsset(U"Game.Small")(control).draw(UI::ControlArea.pos.movedBy(30, 20), UI::TextColor);
		}
	}
}
```


## 2. ランチャーの配置
1. ランチャーのプログラムをビルドして作成した Launcher.exe もしくは Launcher.app が存在するホームディレクトリに、各ゲームのフォルダを配置します
2. 各ゲームのフォルダに `launcher_info.ini` を配置します

```
Games/ (ホームディレクトリ)
  |
  +-- Launcher.exe (ランチャー)
  |
  +-- FunnyGame/ (ゲームディレクトリ 1)
  |     |
  |     + launcher_info.ini
  |     + ...
  |
  +-- CoolGame/ (ゲームディレクトリ 2)
  |     |
  |     + launcher_info.ini
  |     + ...
  |
  +-- NiceGame/ (ゲームディレクトリ 3)
  |     |
  |     + launcher_info.ini
  |     + ...
  |
  ...
```

## 3. `launcher_info.ini` の書式
ゲームの情報や操作に使うデバイスを記述します。  

:::message
	macOS の場合、実行ファイルのパスはアプリフォルダ `Game.app` ではなく `Game.app/Contents/MacOS/Game` のように実際の実行ファイルの場所を指定します。
:::

```
[Game]
title = タイトル
path = 実行ファイルまたはページのパス（Game.exe, Geme.html, https://example.com/）
image = ランチャーのタイルに表示する画像のパス（相対パス）
desc = 説明文（\n で改行)
staff = 開発スタッフ
tools = 開発に使ったツール
mouse = マウス操作 ; true または false
keyboard = キーボード操作 ; true または false
gamepad = ゲームパッド操作 ; true または false
priority = ランチャーでの表示優先度（大きいほど一覧で先頭に表示） ; 整数値
```
例:
```
[Game]
title = Nice Game
path = NiceGame.exe
image = game.png
desc = ナイスなゲームです\nとてもナイスです
staff = プログラム: 野口英世　イラスト: 福沢諭吉　音楽: 樋口一葉
tools = C++/OpenSiv3D
mouse = true
keyboard = true
gamepad = true
priority = 100
```

## 4. 発展

- ゲームのプレイ動画を動画で保存し、`VideoTexture` で読み込んで再生する
