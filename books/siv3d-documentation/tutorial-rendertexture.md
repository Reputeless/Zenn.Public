---
title: "チュートリアル 34 | レンダーテクスチャ"
free: true
---

# 34. レンダーテクスチャ
この章では、図形やテクスチャ、フォントの描画先をシーンではなくテクスチャにする方法を学びます。

## 34.1 レンダーテクスチャの基本
これまで、図形やテクスチャ、フォントはシーンにしか描画できませんでしたが、**レンダーテクスチャ**機能を使うと、プログラムで用意した別の（レンダー）テクスチャに描画できるようになります。

`RenderTexture` を作成し、`ScopedRenderTarget2D` オブジェクトのコンストラクタにレンダーテクスチャを渡すと、`ScopedRenderTarget2D` オブジェクトのスコープが有効な間、図形やテクスチャ、フォントがそのレンダーテクスチャに描画されます（**レンダーターゲット（描画先）の変更**）。描画されたレンダーテクスチャは、レンダーターゲットから解除されたあとにテクスチャとして描画に転用できます。`RenderTexture` は `Texture` と同じ描画系のメンバ関数を持ちます。

図形やテクスチャ、フォントの `.draw()` による描画は、前章で扱った `Image` への書き込み (`.paint()` や `.overwrite()`) と異なり、GPU 上で実行されるため圧倒的に高速です。これまで学んだあらゆる描画関数やレンダーステートも使えるため、柔軟でもあります。

`RenderTexture` は `.clear(color)` によって、保持している画像データを指定した色にクリアできます。クリアをしない場合、それまで描いた内容の上に新しい内容を重ねて描くことになります。

![](/images/doc_v6/tutorial/34/1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture emoji{ U"🐈"_emoji };

	// レンダーテクスチャ
	const RenderTexture renderTexture{ 200, 200, Palette::White };

	while (System::Update())
	{
		// レンダーテクスチャを白色でクリア
		renderTexture.clear(Palette::White);

		{
			// レンダーターゲットを renderTexture に変更
			const ScopedRenderTarget2D target{ renderTexture };

			Circle{ 200, 200, 160 }.draw(ColorF{ 0.8, 0.9, 1.0 });

			emoji.rotated(Scene::Time() * 30_deg).drawAt(100, 100);
		} // ここで target のスコープが終了し、レンダーターゲットがシーンに戻る

		// レンダーテクスチャを描画する
		renderTexture.draw(0, 0);
		renderTexture.draw(200, 200);
		renderTexture.draw(400, 400);
	}
}
```

`RenderTexture` の `.clear()` は自身の参照を返すため、次のようにクリアと `ScopedRenderTarget2D` への設定を 1 行に短くまとめて記述することができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture emoji{ U"🐈"_emoji };

	const RenderTexture renderTexture{ 200, 200, Palette::White };

	while (System::Update())
	{
		{
			// renderTexture をクリアし、レンダーターゲットを renderTexture に変更
			const ScopedRenderTarget2D target{ renderTexture.clear(Palette::White) };

			Circle{ 200, 200, 160 }.draw(ColorF{ 0.8, 0.9, 1.0 });

			emoji.rotated(Scene::Time() * 30_deg).drawAt(100, 100);
		}

		renderTexture.draw(0, 0);
		renderTexture.draw(200, 200);
		renderTexture.draw(400, 400);
	}
}
```


## 34.2 マルチサンプル・レンダーテクスチャ
`RenderTexture` への描画では、通常のシーンへの描画と異なり、マルチサンプル・アンチエイリアシングが有効にならないため、斜めの線を含む図形を描画した際にジャギーが生じてしまいます。`MSRenderTexture` を使うと、マルチサンプル・アンチエイリアシングを有効にして描画できます。

`MSRenderTexture` に描画された結果を描画で使う際には、
- `Graphics2D::Flush()` によってその時点までの描画処理をすべて実行（フラッシュ）して `MSRenderTexture` の内容を確定する
- `MSRenderTexture` の `.resolve()` によって、`MSRenderTexture` 内のマルチサンプル・テクスチャを、描画で使用可能な通常のテクスチャに変換（リゾルブ）する

という 2 つの手順が必要になります。

![](/images/doc_v6/tutorial/34/2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// レンダーテクスチャ
	const RenderTexture renderTexture{ 200, 200, Palette::White };

	// マルチサンプル・レンダーテクスチャ
	const MSRenderTexture msRenderTexture{ 200, 200, Palette::White };

	while (System::Update())
	{
		// レンダーテクスチャ
		{
			const ScopedRenderTarget2D target{ renderTexture.clear(Palette::Black) };

			Rect{ Arg::center(100, 100), 80 }
				.rotated(Scene::Time() * 30_deg).draw();
		}

		// マルチサンプル・レンダーテクスチャ
		{
			const ScopedRenderTarget2D target{ msRenderTexture.clear(Palette::Black) };

			Rect{ Arg::center(100, 100), 80 }
				.rotated(Scene::Time() * 30_deg).draw();
		}

		// 2D 描画をフラッシュ
		Graphics2D::Flush();

		// マルチサンプル・テクスチャをリゾルブ
		msRenderTexture.resolve();

		renderTexture.draw(100, 0);
		msRenderTexture.draw(400, 0);
	}
}
```


## 34.3 レンダーテクスチャに対する便利な操作
レンダーテクスチャを使う、次のような高速な画像処理機能が提供されています。

#### void Shader::Copy(const TextureRegion& from, const RenderTexture& to);
- `from`: 入力テクスチャ
- `to`: 出力テクスチャ

`from` のテクスチャの内容を `to` に描画します。`from` と `to` はともに有効なテクスチャで、互いに異なり、領域のサイズが同じでなければなりません。

---

#### void Shader::Downsample(const TextureRegion& from, const RenderTexture& to);
- `from`: 入力テクスチャ
- `to`: 出力テクスチャ

`from` のテクスチャの内容を拡大縮小して `to` に描画します。`from` と `to` はともに有効なテクスチャで、互いに異なるテクスチャでなければなりません。ダウンサンプルと言う名前が付いていますが、拡大にも使えます。

---

#### void Shader::GaussianBlur(const TextureRegion& from, const RenderTexture& internalBuffer, const RenderTexture& to);
- `from`: 入力テクスチャ
- `internalBuffer`: 中間テクスチャ
- `to`: 出力テクスチャ

`from` のテクスチャに縦方向と横方向のガウスブラーをかけて `to` に描画します。`from`, `internalBuffer`, `to` はいずれも有効なテクスチャで、領域のサイズが同じでなければなりません。`from` と `to` は同じテクスチャにできます。

### 34.3.1 ダウンサンプリング

![](/images/doc_v6/tutorial/34/3.1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"example/windmill.png" };

	// 縦、横が 3 分の 1 サイズのレンダーテクスチャ
	const RenderTexture renderTexture{ texture.size() / 3 };

	// ダウンサンプリング
	Shader::Downsample(texture, renderTexture);

	while (System::Update())
	{
		renderTexture.draw();
	}
}
```

### 34.3.2 ガウスぼかしをかける

![](/images/doc_v6/tutorial/34/3.2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"example/windmill.png" };
	const RenderTexture internalTexture{ texture.size() };
	const RenderTexture renderTexture{ texture.size() };

	Shader::GaussianBlur(texture, internalTexture, renderTexture);

	while (System::Update())
	{
		renderTexture.draw();
	}
}
```


## 34.4 （サンプル）部分的に強力なガウスぼかしをかける
ガウスぼかし → 縮小をくり返し、最終結果を拡大描画することで、強いガウスぼかしを実現できます。

![](/images/doc_v6/tutorial/34/4.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを 1280x720 にリサイズ
	Window::Resize(1280, 720);

	// bay.jpg は 2560x1440 なのでサイズを小さくしてロード
	const Texture texture{ Image{ U"example/bay.jpg" }.scale(1280, 720) };

	// ぼかしを適用する領域のサイズ
	constexpr Size blurAreaSize{ 480, 320 };

	// ガウスぼかしの中間で使うレンダーテクスチャを用意
	const RenderTexture gaussianA1{ blurAreaSize }, gaussianB1{ blurAreaSize };
	const RenderTexture gaussianA4{ blurAreaSize / 4 }, gaussianB4{ blurAreaSize / 4 };
	const RenderTexture gaussianA8{ blurAreaSize / 8 }, gaussianB8{ blurAreaSize / 8 };

	while (System::Update())
	{
		const Point cursorPos = Cursor::Pos();

		// 背景画像のうちぼかしを適用する領域
		const Rect blurArea{ cursorPos, blurAreaSize };

		// [オリジナル]->[ガウスぼかし]->[1/4サイズ]->[ガウスぼかし]->[1/8サイズ]->[ガウスぼかし]
		Shader::GaussianBlur(texture(blurArea), gaussianB1, gaussianA1);
		Shader::Downsample(gaussianA1, gaussianA4);
		Shader::GaussianBlur(gaussianA4, gaussianB4, gaussianA4);
		Shader::Downsample(gaussianA4, gaussianA8);
		Shader::GaussianBlur(gaussianA8, gaussianB8, gaussianA8);

		// 背景を描画
		texture.draw();

		// ガウスぼかし後のテクスチャを RoundRect に貼り付けて描画
		RoundRect{ cursorPos, blurAreaSize, 40 }(gaussianA8.resized(blurAreaSize)).draw();
	}
}
```


## 34.5 （サンプル）2D ライトブルーム
ガウスぼかしの結果を加算ブレンドで描画することで、ライトブルームの表現を実現できます。

![](/images/doc_v6/tutorial/34/5.png)
```cpp
# include <Siv3D.hpp>

void DrawScene()
{
	Circle{ 680, 40, 20 }.draw();
	Rect{ Arg::center(680, 110), 30 }.draw();
	Triangle{ 680, 180, 40 }.draw();

	Circle{ 740, 40, 20 }.draw(HSV{ 0 });
	Rect{ Arg::center(740, 110), 30 }.draw(HSV{ 120 });
	Triangle{ 740, 180, 40 }.draw(HSV{ 240 });

	Circle{ 50, 200, 300 }.drawFrame(4);
	Circle{ 550, 450, 200 }.drawFrame(4);

	TextureAsset(U"light").drawAt(Scene::Center());

	for (auto i : step(12))
	{
		const double angle = (i * 30_deg + Scene::Time() * 5_deg);
		const Vec2 pos = OffsetCircular{ Scene::Center(), 200, angle };
		Circle{ pos, 8 }.draw(HSV{ i * 30 });
	}
}

void Main()
{
	TextureAsset::Register(U"light", U"💡"_emoji);

	constexpr Size sceneSize{ 800, 600 };
	const RenderTexture gaussianA1{ sceneSize }, gaussianB1{ sceneSize };
	const RenderTexture gaussianA4{ sceneSize / 4 }, gaussianB4{ sceneSize / 4 };
	const RenderTexture gaussianA8{ sceneSize / 8 }, gaussianB8{ sceneSize / 8 };

	bool lightBloom = true;

	while (System::Update())
	{
		// 通常のシーン描画
		DrawScene();

		{
			// ガウスぼかし用テクスチャにもう一度シーンを描く
			{
				const ScopedRenderTarget2D target{ gaussianA1.clear(ColorF{ 0.0 }) };
				const ScopedRenderStates2D blend{ BlendState::Additive };
				DrawScene();
			}

			// オリジナルサイズのガウスぼかし (A1)
			// A1 を 1/4 サイズにしてガウスぼかし (A4)
			// A4 を 1/2 サイズにしてガウスぼかし (A8)
			Shader::GaussianBlur(gaussianA1, gaussianB1, gaussianA1);
			Shader::Downsample(gaussianA1, gaussianA4);
			Shader::GaussianBlur(gaussianA4, gaussianB4, gaussianA4);
			Shader::Downsample(gaussianA4, gaussianA8);
			Shader::GaussianBlur(gaussianA8, gaussianB8, gaussianA8);
		}

		if (lightBloom)
		{
			const ScopedRenderStates2D blend{ BlendState::Additive };
			gaussianA1.resized(sceneSize).draw(ColorF{ 0.1 });
			gaussianA4.resized(sceneSize).draw(ColorF{ 0.4 });
			gaussianA8.resized(sceneSize).draw(ColorF{ 0.8 });
		}

		SimpleGUI::CheckBox(lightBloom, U"lightBloom", Vec2{ 20,20 });
	}
}
```
