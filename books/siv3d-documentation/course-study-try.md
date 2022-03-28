---
title: "Siv3D 勉強会コース | 演習 | Siv3D アプリを作ってみよう"
free: true
---

記事中のサンプルコードの右上をクリックするとソースコードをコピーできて便利です。

# T1.（かんたん）おみくじ

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


# T2.（ふつう）アイテムが落ちてくるゲーム

## T2.1 プレイヤーの移動
絵文字検索用: [emojipedia](https://emojipedia.org/)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// プレイヤーの絵文字テクスチャ
	const Texture playerTexture{ U"😃"_emoji };

	// プレイヤーのスピード（ピクセル / 秒)
	const double playerSpeed = 500.0;

	// プレイヤーの座標
	Vec2 playerPos{ 400, 500 };

	while (System::Update())
	{
		////////////////////////////////
		//
		//	状態更新
		//
		////////////////////////////////

		// 前のフレームからの経過時間 (秒)
		const double deltaTime = Scene::DeltaTime();

		// プレイヤーの移動に関する処理
		{
			if (KeyLeft.pressed()) // [←] キーが押されていたら
			{
				playerPos.x -= (playerSpeed * deltaTime);
			}
			else if (KeyRight.pressed()) // [→] キーが押されていたら
			{
				playerPos.x += (playerSpeed * deltaTime);
			}

			// 壁の外に出ないようにする
			// Clamp(x, min, max) は, x を min～max の範囲に収めた値を返す
			playerPos.x = Clamp(playerPos.x, 0.0, 800.0);
		}

		////////////////////////////////
		//
		//	描画
		//
		////////////////////////////////

		// 背景はグラデーションの Rect
		Scene::Rect()
			.draw(Arg::top = ColorF{ 0.1, 0.4, 0.8 }, Arg::bottom = ColorF{ 0.3, 0.7, 1.0 });

		// プレイヤーのテクスチャの描画
		playerTexture.drawAt(playerPos);
	}
}
```

## T2.2 落ちてくるアイテムを追加

```cpp
# include <Siv3D.hpp>

// アイテムの情報の構造体 (★追加★)
struct Item
{
	// アイテムの現在位置
	Vec2 pos;

	// アイテムの種類を表す ID
	int32 type;
};

void Main()
{
	// プレイヤーの絵文字テクスチャ
	const Texture playerTexture{ U"😃"_emoji };

	// プレイヤーのスピード（ピクセル / 秒)
	const double playerSpeed = 500.0;

	// プレイヤーの座標
	Vec2 playerPos{ 400, 500 };

	// アイテムのテクスチャ (★追加★)
	const Texture itemTexture{ U"🍰"_emoji };

	// 現在画面上にあるアイテムの配列 (★追加★)
	Array<Item> items;

	// アイテムが発生する時間間隔（秒） (★追加★)
	const double spawnTime = 0.5;

	// 最後にアイテムが発生してからの経過時間（秒） (★追加★)
	double itemTimer = 0.0;

	// アイテムの落下スピード（ピクセル / 秒) (★追加★)
	const double itemSpeed = 200.0;

	while (System::Update())
	{
		// プログラムの状態の可視化  (★追加★)
		ClearPrint(); //  (★追加★)
		Print << U"ゲーム中のアイテムの個数: " << items.size(); //  (★追加★)

		////////////////////////////////
		//
		//	状態更新
		//
		////////////////////////////////

		// 前のフレームからの経過時間 (秒)
		const double deltaTime = Scene::DeltaTime();

		// プレイヤーの移動に関する処理
		{
			if (KeyLeft.pressed()) // [←] キーが押されていたら
			{
				playerPos.x -= (playerSpeed * deltaTime);
			}
			else if (KeyRight.pressed()) // [→] キーが押されていたら
			{
				playerPos.x += (playerSpeed * deltaTime);
			}

			// 壁の外に出ないようにする
			// Clamp(x, min, max) は x を min～max の範囲に収めた値を返す
			playerPos.x = Clamp(playerPos.x, 0.0, 800.0);
		}

		// アイテムの出現と移動と消滅に関する処理 (★追加★)
		{
			itemTimer += deltaTime; //  (★追加★)

			// spawnTime が経過するごとに新しいアイテムを出現させる (★追加★)
			while (itemTimer >= spawnTime)
			{
				Item item; //  (★追加★)
				item.pos.x = Random(100, 700); // アイテムの X 座標 (★追加★)
				item.pos.y = -100; // アイテムの Y 座標 (★追加★)
				item.type = 0; // アイテムの種類。 = Random(0, 3); とすれば 0～3 のランダムな数に  (★追加★)

				// 配列に追加 (★追加★)
				items << item;

				itemTimer -= spawnTime; //  (★追加★)
			}

			// すべてのアイテムについて移動処理 (★追加★)
			for (auto& item : items)
			{
				item.pos.y += deltaTime * itemSpeed;
			}

			// 画面外に出たアイテムを消去する (★追加★)
			items.remove_if([](const Item& item) { return (item.pos.y > 700); });
		}

		////////////////////////////////
		//
		//	描画
		//
		////////////////////////////////

		// 背景はグラデーションの Rect
		Scene::Rect()
			.draw(Arg::top = ColorF{ 0.1, 0.4, 0.8 }, Arg::bottom = ColorF{ 0.3, 0.7, 1.0 });

		// プレイヤーのテクスチャの描画
		playerTexture.drawAt(playerPos);

		// アイテムの描画 (★追加★)
		for (const auto& item : items)
		{
			itemTexture.drawAt(item.pos);
		}
	}
}
```

## T2.3 アイテムとの当たり判定を追加

```cpp
# include <Siv3D.hpp>

// アイテムの情報の構造体
struct Item
{
	// アイテムの現在位置
	Vec2 pos;

	// アイテムの種類を表す ID
	int32 type;
};

void Main()
{
	// プレイヤーの絵文字テクスチャ
	const Texture playerTexture{ U"😃"_emoji };

	// プレイヤーのスピード（ピクセル / 秒)
	const double playerSpeed = 500.0;

	// プレイヤーの座標
	Vec2 playerPos{ 400, 500 };

	// アイテムのテクスチャ
	const Texture itemTexture{ U"🍰"_emoji };

	// 現在画面上にあるアイテムの配列
	Array<Item> items;

	// アイテムが発生する時間間隔（秒）
	const double spawnTime = 0.5;

	// 最後にアイテムが発生してからの経過時間（秒）
	double itemTimer = 0.0;

	// アイテムの落下スピード（ピクセル / 秒)
	const double itemSpeed = 200.0;

	// スコア (★追加★)
	int32 score = 0;

	while (System::Update())
	{
		// プログラムの状態の可視化
		ClearPrint();
		Print << U"ゲーム中のアイテムの個数: " << items.size();
		Print << U"スコア: " << score; // (★追加★)

		////////////////////////////////
		//
		//	状態更新
		//
		////////////////////////////////

		// 前のフレームからの経過時間 (秒)
		const double deltaTime = Scene::DeltaTime();

		// プレイヤーの移動に関する処理
		{
			if (KeyLeft.pressed()) // [←] キーが押されていたら
			{
				playerPos.x -= (playerSpeed * deltaTime);
			}
			else if (KeyRight.pressed()) // [→] キーが押されていたら
			{
				playerPos.x += (playerSpeed * deltaTime);
			}

			// 壁の外に出ないようにする
			// Clamp(x, min, max) は x を min～max の範囲に収めた値を返す
			playerPos.x = Clamp(playerPos.x, 0.0, 800.0);
		}

		// アイテムの出現と移動と消滅に関する処理
		{
			itemTimer += deltaTime;

			// spawnTime が経過するごとに新しいアイテムを出現させる
			while (itemTimer >= spawnTime)
			{
				Item item;
				item.pos.x = Random(100, 700); // アイテムの X 座標
				item.pos.y = -100; // アイテムの Y 座標
				item.type = 0; // アイテムの種類。 = Random(0, 3); とすれば 0～3 のランダムな数に

				// 配列に追加
				items << item;

				itemTimer -= spawnTime;
			}

			// すべてのアイテムについて移動処理
			for (auto& item : items)
			{
				item.pos.y += deltaTime * itemSpeed;
			}

			// プレイヤーのあたり判定の円 (★追加★)
			const Circle playerCirlce{ playerPos, 60 };

			// アイテムのあたり判定と回収したアイテムの削除 (★追加★)
			for (auto it = items.begin(); it != items.end();)
			{
				// アイテムのあたり判定の円 (★追加★)
				const Circle itemCircle{ it->pos, 60 };

				// 交差したらアイテムを削除 (★追加★)
				if (playerCirlce.intersects(itemCircle))
				{
					// (削除する前に) スコアを加算 (★追加★)
					score += 100;

					// アイテムを削除 (★追加★)
					it = items.erase(it);
				}
				else
				{
					// イテレータを次のアイテムに進める (★追加★)
					++it;
				}
			}

			// 画面外に出たアイテムを消去する
			items.remove_if([](const Item& item) { return (item.pos.y > 700); });
		}

		////////////////////////////////
		//
		//	描画
		//
		////////////////////////////////

		// 背景はグラデーションの Rect
		Scene::Rect()
			.draw(Arg::top = ColorF{ 0.1, 0.4, 0.8 }, Arg::bottom = ColorF{ 0.3, 0.7, 1.0 });

		// プレイヤーのテクスチャの描画
		playerTexture.drawAt(playerPos);

		// アイテムの描画
		for (const auto& item : items)
		{
			itemTexture.drawAt(item.pos);
		}

		// 当たり判定の可視化（デバッグ用） (★追加★)
		Circle{ playerPos, 60 }.drawFrame(2, Palette::Red); // プレイヤーの当たり判定円 (★追加★)
		for (const auto& item : items) // アイテムの当たり判定円 (★追加★)
		{
			Circle{ item.pos, 60 }.drawFrame(2, Palette::Red);
		}
	}
}
```

## T2.4 さらに発展させるには
アイテムの種類 `.type` に応じた処理を入れると、より楽しいゲームになるでしょう。



# T3.（ややむずかしい）Slack で使えるアニメーション絵文字メーカー
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

