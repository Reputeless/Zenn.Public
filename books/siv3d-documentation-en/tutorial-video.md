---
title: "Tutorial 09 | Videos"
free: true
---

# 9. Videos
この章では、動画や GIF アニメーションをシーンに描く方法を学びます。


## 9.1 動画を描く
シーンに動画を描きたいときは `VideoTexture` を作成し、`.draw()` または `.drawAt()` します。`VideoTexture` は `Texture` とほぼ同じインタフェースを持ち、動画ファイルを `Texture` のように扱えます。`VideoTexture` は毎フレーム `.advance()` を呼ぶことで再生位置を進めます。Siv3D はバックグラウンドのスレッドで動画の次のフレームの先読みを行っています。

`VideoTexture` ではミップマップが作成されないため、動画を縮小して描く場合はあらかじめ小さい解像度の動画を用意しておくことが実行時性能上からも望ましいです。

対応する動画フォーマットはプラットフォームによって異なりますが、MP4 ファイルは Windows, macOS, Linux でサポートされています。音声付き動画の音声をアプリ内で再生する方法は OpenSiv3D v0.6.10 では用意されていません。映像のみです。

Windows では、のちのチュートリアルで説明する「リソースファイルの埋め込み」で埋め込んだ動画ファイルを `VideoTexture` では読み込めないという制約があります。ワークアラウンドとして、[一時ファイルに書き出す方法](https://gist.github.com/Reputeless/3d527302d459792f7a5e1094d30d0529) があります。

https://youtu.be/B4WPEdA4vMI
```cpp
# include <Siv3D.hpp>

void Main()
{
	// ループする場合は Loop::Yes, ループしない場合は Loop::No
	const VideoTexture videoTexture{ U"example/video/river.mp4", Loop::Yes };

	while (System::Update())
	{
		ClearPrint();

		// 再生位置（秒） / 動画の長さ（秒）
		Print << videoTexture.posSec() << U" / " << videoTexture.lengthSec();

		// 動画の時間を進める (デフォルトでは Scece::DeltaTime() 秒)
		videoTexture.advance();

		videoTexture
			.scaled(0.5).draw();

		videoTexture
			.scaled(0.5)
			.rotated(Scene::Time() * 30_deg)
			.drawAt(Cursor::Pos());
	}
}
```


## 9.2 GIF アニメーションを描く
`AnimatedGIFReader` を使うと、各フレームの画像データ `Image` と、次のフレームまでのディレイ（ミリ秒）の配列を取得できます。`Image` の配列から `Texture` の配列を作成し、適切なタイミングでフレームを描くことで、GIF アニメーションを描画できます。ある時間にどのフレームを描けばよいかは、`AnimatedGIFReader::GetFrameIndex()` を使うことで簡単に求められます。

https://youtu.be/E8dtoDeHE3g
```cpp
# include <Siv3D.hpp>

void Main()
{
	// GIF アニメーション画像を開く
	const AnimatedGIFReader gif{ U"example/gif/test.gif" };

	// 各フレームの画像と、次のフレームへのディレイ（ミリ秒）をロード
	Array<Image> images;
	Array<int32> delays;
	gif.read(images, delays);

	// フレーム数
	Print << images.size() << U" frames";

	// 各フレームのディレイ（ミリ秒）一覧
	Print << U"delays: " << delays;

	// 各フレームの Image から Texure を作成
	Array<Texture> textures;
	for (const auto& image : images)
	{
		textures << Texture{ image };
	}

	// 画像データはもう使わないので、消去してメモリ消費を減らす
	images.clear();

	while (System::Update())
	{
		// アニメーションの経過時間
		const double t = Scene::Time();

		// 経過時間と各フレームのディレイに基づいて、何番目のフレームを描けばよいかを計算
		const size_t frameIndex = AnimatedGIFReader::GetFrameIndex(t, delays);

		textures[frameIndex].draw(200, 200);
	}
}
```
