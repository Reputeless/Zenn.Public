---
title: "チュートリアル 34 | レンダーテクスチャ"
free: true
---

# 34. レンダーテクスチャ
この章では、図形やテクスチャ、フォントの描画先をシーンではなくテクスチャにする方法を学びます。

## 34.1 レンダーテクスチャの基本
これまで、図形やテクスチャ、フォントはシーンにしか描画できませんでしたが、**レンダーテクスチャ**機能を使うと、プログラムで用意した別の（レンダー）テクスチャに描画できるようになります。

`RenderTexture` を作成し、`ScopedRenderTarget2D` オブジェクトのコンストラクタにレンダーテクスチャを渡すと、`ScopedRenderTarget2D` オブジェクトのスコープが有効な間、図形やテクスチャ、フォントがそのレンダーテクスチャに描画されます（**レンダーターゲット（描画先）の変更**）。描画されたレンダーテクスチャは、レンダーターゲットから解除されたあとにテクスチャとして描画に転用できます。`RenderTexture` は `Texture` と同じ描画系のメンバ関数を持ちます。

図形やテクスチャ、フォントの `.draw()` による描画は、前章で扱った `Image` への書き込み (`.paint()` や `.overwrite()`) と異なり、GPU 上で実行されるため圧倒的に高速です。これまでのさまざまな描画関数やレンダーステートも使えるため、柔軟でもあります。

`RenderTexture` は `.clear(color)` によって、保持している画像データを指定した色にクリアできます。クリアをしない場合、それまで描いた内容の上に新しい内容を重ねて描くことになります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture emoji{ U"🐈"_emoji };

	// レンダーテクスチャ
	RenderTexture renderTexture{ 200, 200, Palette::White };

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

	RenderTexture renderTexture{ 200, 200, Palette::White };

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

```cpp

```


## 34.3 レンダーテクスチャに対する便利な操作

```cpp

```

