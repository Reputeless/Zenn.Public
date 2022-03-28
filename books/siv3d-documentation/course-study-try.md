---
title: "Siv3D 勉強会コース | 演習"
free: true
---

## 演習 | Siv3D アプリを作ってみよう

### （かんたん）おみくじ

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景色を設定
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 文字列の配列
	const Array<String> items =
	{
		U"大吉", U"吉", U"中吉", U"小吉", U"末吉", U"凶"
	};

	// 抽選中である場合 true
	bool active = true;

	// 選ばれた文字列
	String currentItem;

	// メインループ
	while (System::Update())
	{
		// 抽選中である場合
		if (active)
		{
			// ランダムに 1 個選択
			currentItem = items.choice();
		}

		// (220, 100) の位置に幅 150 のボタンを表示
		// ボタンのラベルは currentItem
		if (SimpleGUI::Button(currentItem, Vec2{ 220, 100 }, 150))
		{
			// もし押されたら
			// active の状態を反転 (true → false, false → true)
			active = !active;
		}
	}
}
```


### （ややむずかしい）アイテムが落ちてくるゲーム

```cpp



```


### （ややむずかしい）Slack で使えるアニメーション絵文字メーカー
プロジェクトの App フォルダに、作成した GIF ファイル `emoji.gif` が保存されます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// GIF アニメ出力クラス
	// writeFrame した内容は emoji.gif に出力
	AnimatedGIFWriter gif{ U"emoji.gif", 64, 64 };

	// 使用するフォント
	const Font font{ 64, Typeface::Heavy };

	// 描画先 (128x128 のテクスチャ)
	MSRenderTexture renderTexture{ 128, 128 };

	// 書き出したフレーム数
	int32 frameCount = 0;

	// メインループ
	while (System::Update())
	{
		// renderTexture の内容をクリア
		renderTexture.clear(Palette::White);
		{
			// renderTexture を描画先に設定する
			ScopedRenderTarget2D target{ renderTexture };

			////////////////////////////////
			//
			//	描画
			//

			// 座標 (64, 64) を中心に回転する座標変換を適用
			// 回転角度 = (18° * frameCount)
			Transformer2D transform{ Mat3x2::Rotate(18_deg * frameCount, Vec2{ 64, 64 }) };

			// テキストを (64, 64) に描画
			font(U"OK").drawAt(64, 64, Palette::Orange);

			//
			////////////////////////////////
		}

		// renderTexture の処理
		{
			Graphics2D::Flush();
			renderTexture.resolve();
		}

		if (frameCount < 20) // 20 フレーム書き出し
		{
			Image image;

			// renderTexture の内容を Image にコピー
			renderTexture.readAsImage(image);

			// Image を 64x64 に縮小し、
			// 0.1 秒分の GIF アニメのフレームとして書き出し
			gif.writeFrame(image.scaled(64, 64), 0.1s);

			// フレームカウントを 1 増やす
			++frameCount;
		}
		else // 20 フレーム書き出したら終了
		{
			break;
		}

		// 画面への描画
		renderTexture.draw(20, 20);
	}
}
```

