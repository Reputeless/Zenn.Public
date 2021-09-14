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
`RenderTexture` への描画では、通常のシーンへの描画と異なり、マルチサンプル・アンチエイリアシングが有効にならないので、斜めの線を含む図形を描画した際にジャギーが生じます。`MSRenderTexture` を使うと、マルチサンプル・アンチエイリアシングを有効にして描画できます。ただし、`MSRenderTexture` に描画された結果を描画で使う際には、
- `Graphics2D::Flush()` によってその時点までの描画処理をすべて実行（フラッシュ）して `MSRenderTexture` の内容を確定する
- `MSRenderTexture` の `.resolve()` によって、`MSRenderTexture` 内のマルチサンプル・テクスチャを、描画で使用可能な通常のテクスチャに変換（リゾルブ）する

という 2 つの手順が必要になります。

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

			Rect(Arg::center(100, 100), 80)
				.rotated(Scene::Time() * 30_deg).draw();
		}

		// マルチサンプル・レンダーテクスチャ
		{
			const ScopedRenderTarget2D target{ msRenderTexture.clear(Palette::Black) };

			Rect(Arg::center(100, 100), 80)
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

#### void Shader::Downsample(const TextureRegion& from, const RenderTexture& to);
- `from`: 入力テクスチャ
- `to`: 出力テクスチャ

`from` のテクスチャの内容を拡大縮小して `to` に描画します。`from` と `to` はともに有効なテクスチャで、互いに異なるテクスチャでなければなりません。ダウンサンプルと言う名前が付いていますが、拡大にも使えます。

#### void Shader::GaussianBlur(const TextureRegion& from, const RenderTexture& internalBuffer, const RenderTexture& to);
- `from`: 入力テクスチャ
- `internalBuffer`: 中間テクスチャ
- `to`: 出力テクスチャ

`from` のテクスチャに縦方向と横方向のガウスブラーをかけて `to` に描画します。`from`, `internalBuffer`, `to` はいずれも有効なテクスチャで、領域のサイズが同じでなければなりません。`from` と `to` は同じテクスチャにできます。

### 34.3.1 ダウンサンプリング

```cpp

```


### 34.3.2 ガウスぼかし

```cpp

```

