---
title: "チュートリアル 09 | 動画を描く"
free: true
---

# 9. 動画を描く
この章では、動画や GIF アニメーションをシーンに描く方法を学びます。


## 9.1 動画を描く
シーンに動画を描きたいときは `VideoTexture` を作成し、`.draw()` または `.drawAt()` します。`VideoTexture` は `Texture` とほぼ同じインタフェースを持ち、動画ファイルを `Texture` のように扱えます。

`VideoTexture` は毎フレーム `.advance()` を呼ぶことで再生位置を進めることができます。

対応する動画フォーマットはプラットフォームによって異なりますが、MP4 ファイルは Windows, macOS, Linux でサポートされています。

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
			.scaled(0.25)
			.rotated(Scene::Time() * 30_deg)
			.drawAt(Cursor::Pos());
	}
}
```


## 9.2 GIF アニメーションを描く


