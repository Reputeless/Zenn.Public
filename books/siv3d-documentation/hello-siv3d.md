---
title: "Hello, Siv3D!"
free: true
---

# 1. 基本のサンプルプログラム

Siv3D プロジェクトを作成すると、最初に用意されているのは次のようなプログラムです。

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.0

void Main()
{
	// 背景の色を設定 | Set background color
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 通常のフォントを作成 | Create a new font
	const Font font{ 60 };

	// 絵文字用フォントを作成 | Create a new emoji font
	const Font emojiFont{ 60, Typeface::ColorEmoji };

	// `font` が絵文字用フォントも使えるようにする | Set emojiFont as a fallback
	font.addFallback(emojiFont);

	// 画像ファイルからテクスチャを作成 | Create a texture from an image file
	const Texture texture{ U"example/windmill.png" };

	// 絵文字からテクスチャを作成 | Create a texture from an emoji
	const Texture emoji{ U"🐈"_emoji };

	// 絵文字を描画する座標 | Coordinates of the emoji
	Vec2 emojiPos{ 300, 150 };

	// テキストを画面にデバッグ出力 | Print a text
	Print << U"Push [A] key";

	while (System::Update())
	{
		// テクスチャを描く | Draw a texture
		texture.draw(200, 200);

		// テキストを画面の中心に描く | Put a text in the middle of the screen
		font(U"Hello, Siv3D!🚀").drawAt(Scene::Center(), Palette::Black);

		// サイズをアニメーションさせて絵文字を描く | Draw a texture with animated size
		emoji.resized(100 + Periodic::Sine0_1(1s) * 20).drawAt(emojiPos);

		// マウスカーソルに追随する半透明な円を描く | Draw a red transparent circle that follows the mouse cursor
		Circle{ Cursor::Pos(), 40 }.draw(ColorF{ 1, 0, 0, 0.5 });

		// もし [A] キーが押されたら | When [A] key is down
		if (KeyA.down())
		{
			// 選択肢からランダムに選ばれたメッセージをデバッグ表示 | Print a randomly selected text
			Print << Sample({ U"Hello!", U"こんにちは", U"你好", U"안녕하세요?" });
		}

		// もし [Button] が押されたら | When [Button] is pushed
		if (SimpleGUI::Button(U"Button", Vec2{ 640, 40 }))
		{
			// 画面内のランダムな場所に座標を移動
			// Move the coordinates to a random position in the screen
			emojiPos = RandomVec2(Scene::Rect());
		}
	}
}
```

一度に全部を理解する必要はありません。  
文字や数字を少しだけカスタマイズして、Siv3D プログラミングを体験してみましょう。  

```cpp
Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });
```

はシーンの背景色の設定で、(R, G, B) = (0.8, 0.9, 1.0) です。数字を 0.0～1.0 の範囲で変更して、背景色を変えてみましょう。

```cpp
const Font font{ 60 };
```

は基本サイズを指定してフォントを作成します。数字を 10～200 の範囲で変更してみて、文字の大きさが変わることを確認しましょう。

```cpp
const Texture emoji{ U"🐈"_emoji };
```

は絵文字データをロードします。🐈 を 🐕 や 🐧, 🍔 に変えてみましょう（絵文字の前後に余計な空白を付けないよう気を付けてください）。Windows 10 では [Windows] + [.] ショートカットキーで絵文字入力メニューが登場します。「いぬ」と日本語で入力して変換することで犬の絵文字を得ることもできます。

```cpp
texture.draw(200, 200);
```

は画像ファイル `windmill.png` から作成したテクスチャを、画面上の位置 (x, y) = (200, 200) を指定して描画します。数字を変えて、描かれる位置を変更してみましょう。

```cpp
font(U"Hello, Siv3D!🚀").drawAt(Scene::Center(), Palette::Black);
```

はテキストを画面に描画します。メッセージを書き換えてみましょう。


```cpp
Circle{ Cursor::Pos(), 40 }.draw(ColorF{ 1, 0, 0, 0.5 });
```

は円をマウスカーソルの位置、半径 40, (R, G, B, 不透明度) = (1.0, 0.0, 0.0, 0.5) で描きます。円の半径や色、不透明度を変更してみましょう。


---

次のページでは、Siv3D の世界の一部を体験できる、いくつかの小さなプログラムを紹介します。
