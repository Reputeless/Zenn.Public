---
title: "Hello Siv3D Program"
free: true
---

# 1. 基本のサンプルプログラム

Siv3D プロジェクトを作成すると、最初に用意されているのは次のようなプログラムです。

![](https://raw.githubusercontent.com/Siv3D/File/master/v6/screenshot/hello.gif)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景の色を設定する | Set the background color
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 画像ファイルからテクスチャを作成する | Create a texture from an image file
	const Texture texture{ U"example/windmill.png" };

	// 絵文字からテクスチャを作成する | Create a texture from an emoji
	const Texture emoji{ U"🦖"_emoji };

	// 太文字のフォントを作成する | Create a bold font with MSDF method
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// テキストに含まれる絵文字のためのフォントを作成し、font に追加する | Create a font for emojis in text and add it to font as a fallback
	const Font emojiFont{ 48, Typeface::ColorEmoji };
	font.addFallback(emojiFont);

	// ボタンを押した回数 | Number of button presses
	int32 count = 0;

	// チェックボックスの状態 | Checkbox state
	bool checked = false;

	// プレイヤーの移動スピード | Player's movement speed
	double speed = 200.0;

	// プレイヤーの X 座標 | Player's X position
	double playerPosX = 400;

	// プレイヤーが右を向いているか | Whether player is facing right
	bool isPlayerFacingRight = true;

	while (System::Update())
	{
		// テクスチャを描く | Draw the texture
		texture.draw(20, 20);

		// テキストを描く | Draw text
		font(U"Hello, Siv3D!🎮").draw(64, Vec2{ 20, 340 }, ColorF{ 0.2, 0.4, 0.8 });

		// 指定した範囲内にテキストを描く | Draw text within a specified area
		font(U"Siv3D (シブスリーディー) は、ゲームやアプリを楽しく簡単な C++ コードで開発できるフレームワークです。")
			.draw(18, Rect{ 20, 430, 480, 200 }, Palette::Black);

		// 長方形を描く | Draw a rectangle
		Rect{ 540, 20, 80, 80 }.draw();

		// 角丸長方形を描く | Draw a rounded rectangle
		RoundRect{ 680, 20, 80, 200, 20 }.draw(ColorF{ 0.0, 0.4, 0.6 });

		// 円を描く | Draw a circle
		Circle{ 580, 180, 40 }.draw(Palette::Seagreen);

		// 矢印を描く | Draw an arrow
		Line{ 540, 330, 760, 260 }.drawArrow(8, SizeF{ 20, 20 }, ColorF{ 0.4 });

		// 半透明の円を描く | Draw a semi-transparent circle
		Circle{ Cursor::Pos(), 40 }.draw(ColorF{ 1.0, 0.0, 0.0, 0.5 });

		// ボタン | Button
		if (SimpleGUI::Button(U"count: {}"_fmt(count), Vec2{ 520, 370 }, 120, (checked == false)))
		{
			// カウントを増やす | Increase the count
			++count;
		}

		// チェックボックス | Checkbox
		SimpleGUI::CheckBox(checked, U"Lock \U000F033E", Vec2{ 660, 370 }, 120);

		// スライダー | Slider
		SimpleGUI::Slider(U"speed: {:.1f}"_fmt(speed), speed, 100, 400, Vec2{ 520, 420 }, 140, 120);

		// 左キーが押されていたら | If left key is pressed
		if (KeyLeft.pressed())
		{
			// プレイヤーが左に移動する | Player moves left
			playerPosX = Max((playerPosX - speed * Scene::DeltaTime()), 60.0);
			isPlayerFacingRight = false;
		}

		// 右キーが押されていたら | If right key is pressed
		if (KeyRight.pressed())
		{
			// プレイヤーが右に移動する | Player moves right
			playerPosX = Min((playerPosX + speed * Scene::DeltaTime()), 740.0);
			isPlayerFacingRight = true;
		}

		// プレイヤーを描く | Draw the player
		emoji.scaled(0.75).mirrored(isPlayerFacingRight).drawAt(playerPosX, 540);
	}
}
```

このプログラムを最初から全部理解する必要はありません。  
文字や数字を少しだけカスタマイズして、Siv3D プログラミングを体験してみましょう。

Visual Studio や Xcode では、新しいプログラムをビルドするときに、古いプログラムが実行中のままだとビルドが失敗します。実行中のプログラムを終了するには、ウィンドウを閉じるか、エスケープキーを押します。

## プログラムのカスタマイズ

```cpp
Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
```

はシーンの背景色の設定で、(R, G, B) = (0.6, 0.8, 0.7) です。数字を 0.0～1.0 の範囲で変更して、背景色を変えてみましょう。

```cpp
const Texture emoji{ U"🦖"_emoji };
```

は絵文字データをロードしています。🐈 を 🐕 や 🐧, 🍔 に変えてみましょう（絵文字の前後に余計な空白を付けないよう気を付けてください）。Windows では [Windows] + [.] ショートカットキーで絵文字入力メニューが登場します。「いぬ」と日本語で入力して変換することで犬の絵文字を得ることもできます。

```cpp
texture.draw(20, 20);
```

は画像ファイル `windmill.png` から作成したテクスチャを、画面上の位置 (x, y) = (20, 20) を指定して描画します。数字を変えて、描かれる位置を変更してみましょう。

```cpp
font(U"Hello, Siv3D!🎮").draw(64, Vec2{ 20, 340 }, ColorF{ 0.2, 0.4, 0.8 });
```

はテキストを画面に描画します。メッセージを書き換えてみましょう。  
`64` は文字の大きさを指定しています。小さくしたり大きくしたりしてみましょう。

```cpp
Circle{ Cursor::Pos(), 40 }.draw(ColorF{ 1.0, 0.0, 0.0, 0.5 });
```

は円をマウスカーソルの位置、半径 40, (R, G, B, 不透明度) = (1.0, 0.0, 0.0, 0.5) で描きます。円の半径や色、不透明度を変更してみましょう。


---

次のページでは、Siv3D の世界の一部を体験できる、いくつかの小さなプログラムを紹介します。
