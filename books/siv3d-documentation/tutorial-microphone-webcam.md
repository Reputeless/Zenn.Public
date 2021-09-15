---
title: "チュートリアル 39 | マイクや Web カメラを使う"
free: true
---

# 39. マイクを使う
この章では、パソコンに内蔵・接続されたマイクや Web カメラから音声波形や映像を取得し、プログラムで活用する方法を学びます。

## 39.1 接続されているマイクを列挙する
PC に接続されているマイクの一覧を `System::EnumerateMicrophones()` で取得できます。結果は `Array<MicrophoneInfo>` 型で返されます。

`MicrophoneInfo` 型のメンバ変数は次の通りです。

| メンバ変数 | 説明 |
|--|--|
| `uint32 icrophoneIndex` | `Microphone` で使うプレイヤーインデックス |
| `String name` | 名前 |
| `Array<uint32> sampleRates` | 対応するサンプルレートの一覧 |
| `uint32 preferredSampleRate` | 推奨サンプルレート |

```cpp
# include <Siv3D.hpp>

void Main()
{
	for (const auto& info : System::EnumerateMicrophones())
	{
		Print << U"[{}] {} {} {}"_fmt(info.microphoneIndex, info.name, info.sampleRates, info.preferredSampleRate);
	}

	while (System::Update())
	{

	}
}
```


## 39.2 マイクから波形情報を得る

```cpp
# include <Siv3D.hpp>

void Main()
{
	if (System::EnumerateMicrophones().isEmpty())
	{
		throw Error{ U"No microphone is connected" };
	}

	// 音声バッファ 5 秒、バッファ終端まで到達したら最初からループ、即座に録音開始
	// デフォルトの音声入力デバイス（マイク）、推奨サンプリングレートを使用
	const Microphone mic{ 5s, Loop::Yes, StartImmediately::Yes };

	if (not mic.isRecording())
	{
		throw Error{ U"Failed to start recording" };
	}

	LineString points(1600, Vec2{ 0, 300 });

	while (System::Update())
	{
		// 音声波形（サンプルの配列）にアクセス
		const Wave& wave = mic.getBuffer();

		// 現在のバッファ位置をもとに、直近の 1600 サンプルの波形を取得
		const size_t writePos = mic.posSample();
		{
			constexpr size_t samples_show = 1600;
			const size_t headLength = Min(writePos, samples_show);
			const size_t tailLength = (samples_show - headLength);
			size_t pos = 0;

			for (size_t i = 0; i < tailLength; ++i)
			{
				const float a = wave[wave.size() - tailLength + i].left;
				points[pos].set(pos * 0.5, 300 + a * 280);
				++pos;
			}

			for (size_t i = 0; i < headLength; ++i)
			{
				const float a = wave[writePos - headLength + i].left;
				points[pos].set(pos * 0.5, 300 + a * 280);
				++pos;
			}
		}

		// 音の波形の平均振幅 [0.0, 1.0]
		const double mean = mic.mean();

		// 音の波形の振幅の二乗平均平方根 [0.0, 1.0]
		const double rootMeanSquare = mic.rootMeanSquare();

		// 音の波形のピーク [0.0, 1.0]
		const double peak = mic.peak();

		// 波形の描画
		points.draw(2);

		Line{ 0, (300 - mean * 280), Arg::direction(800, 0) }.draw(2, HSV{ 200 });
		Line{ 0, (300 - rootMeanSquare * 280), Arg::direction(800, 0) }.draw(2, HSV{ 120 });
		Line{ 0, (300 - peak * 280), Arg::direction(800, 0) }.draw(2, HSV{ 40 });
	}
}
```


## 39.3 マイクからの入力波形のスペクトラムを表示する

```cpp
# include <Siv3D.hpp>

void Main()
{
	if (System::EnumerateMicrophones().isEmpty())
	{
		throw Error{ U"No microphone is connected" };
	}

	// デフォルトの音声入力デバイス（マイク）、推奨サンプリングレート、5 秒間のバッファを使用
	const Microphone mic{ StartImmediately::Yes };

	if (not mic.isRecording())
	{
		throw Error{ U"Failed to start recording" };
	}

	FFTResult fft;

	while (System::Update())
	{
		// FFT の結果を取得
		mic.fft(fft);

		// 結果を可視化
		for (auto i : step(800))
		{
			const double size = Pow(fft.buffer[i], 0.6f) * 1200;
			RectF{ Arg::bottomLeft(i, 600), 1, size }.draw(HSV{ 240 - i });
		}

		// 周波数表示
		Rect{ Cursor::Pos().x, 0, 1, Scene::Height() }.draw();
		ClearPrint();
		Print << U"{:.2f} Hz"_fmt(Cursor::Pos().x * fft.resolution);
	}
}
```


## 39.4 接続されている Web カメラを列挙する

```cpp
# include <Siv3D.hpp>

void Main()
{
	for (const auto& info : System::EnumerateWebcams())
	{
		Print << info.cameraIndex;
		Print << info.name;
		Print << info.uniqueName;
	}

	while (System::Update())
	{

	}
}
```


## 39.5 Web カメラの映像を取得する

```cpp

```


## 39.6 Web カメラの映像を画像処理して表示する

```cpp

```


## 39.7 Web カメラの映像から顔を検出する

```cpp

```


## 39.8 Web カメラの映像から QR コードを検出する

```cpp

```
