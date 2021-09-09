---
title: "チュートリアル 19 | オーディオ再生"
free: true
---

# 19. オーディオ再生
この章では、効果音や音楽の再生を制御する方法を学びます。

## 19.1 音声ファイルの読み込みと再生
オーディオを再生したいときは `Audio` を作成し、`.play()` で再生します。再生中のオーディオに `.play()` をしても何も起こりません。同じオーディオを重ねて再生したい場合は、この章の後半に出てくる `.playOneShot()` を使います。

音声ファイルから `Audio` を作成するには、`Audio` のコンストラクタ引数に、読み込みたい音声ファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します。リリース用のアプリを作るときには、のちの章で説明する「リソース」パスの使用を推奨します。

`Texture` や `Font` と同じように、`Audio` の作成にはメモリ確保などの実行時負荷がかかります。メインループの中で毎フレーム新しい `Audio` を作成するのは避け、作成が 1 回だけで済むようにしましょう。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 音声ファイルを読み込んで Audio を作成
	const Audio audio{ U"example/test.mp3" };

	// オーディオを再生
	audio.play();

	while (System::Update())
	{

	}
}
```

### 対応している音声フォーマット
OpenSiv3D v0.6.0 では、次のような音声フォーマットの読み込みがサポートされています。一部の音声フォーマットは OS によって対応状況が異なります。

| フォーマット    | 拡張子                | Windows | macOS | Linux | Web |
|-----------|--------------------|:-------:|:-----:|:-----:|:---:|
| WAVE      | .wav               | ✔       | ✔     | ✔     | ✔   |
| MP3       | .mp3               | ✔       | ✔     | ✔     | ✔*  |
| AAC       | .m4a               | ✔       | ✔     | ✔     | ✔*  |
| OggVorbis | .ogg               | ✔       | ✔     | ✔     | ✔   |
| Opus      | .opus              | ✔       | ✔     | ✔     | ✔   |
| MIDI      | .mid               | ✔       | ✔     | ✔     | ✔   |
| WMA       | .wma               | ✔       |       |       |     |
| FLAC      | .flac              | ✔       | ✔     |       |     |
| AIFF      | .aif, .aiff, .aifc |         | ✔     |       |     |

(*) ビルドオプションの設定と、特別な関数の使用が必要です。


## 19.2 ストリーミング再生
Siv3D では、WAVE, MP3, OggVorbis, FLAC 形式の音声ファイルのストリーミング再生に対応しています。ストリーミング再生とは、最初にファイル内容の全部を読み込むのではなく、一部だけを読み込んでオーディオを再生しながら、続く部分を逐次読み込む方式のことです。ストリーミング再生を使うと、メモリの使用量やファイルのロード時間が大幅に改善されます。

`Audio` でストリーミング再生を有効にするには、`Audio` のコンストラクタに `Audio::Stream` を渡してストリーミング再生をリクエストします。もし `Audio::Stream` を指定したファイルがストリーミング再生をサポートしていなかった場合は、自動的に通常の読み込みが行われます。ある `Audio` でストリーミング再生が有効になっているかは、`.isStreaming()` で調べられます。

ストリーミング再生では
- 区間ループ再生において、ループ末尾位置をオーディオ終端以外に設定できない
- （音声波形処理）オーディオ全体の音声波形にアクセスできない
- （音声波形処理）FFT のサンプル数が少なくなる

といった一部制約が生じますが、通常のオーディオ再生用途では問題になりません。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 音声ファイルを読み込んで Audio を作成（ストリーミング再生をリクエスト）
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	// ストリーミング再生になるかを取得
	Print << audio.isStreaming();

	// オーディオを再生
	audio.play();

	while (System::Update())
	{

	}
}
```


## 19.3 一時停止と停止
再生中のオーディオを一時停止するには `.pause()`, 停止して再生位置を最初に戻すには `.stop()` を呼びます。

オーディオが再生中であるかは `.isPlaying()`, 一時停止中であるかは `.isPaused()` で取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	while (System::Update())
	{
		ClearPrint();

		// 再生されているか
		Print << U"isPlaying: " << audio.isPlaying();

		// 一時停止中であるか
		Print << U"isPaused: " << audio.isPaused();

		if (SimpleGUI::Button(U"Play", Vec2{ 200, 20 }))
		{
			// 再生・再開
			audio.play();
		}

		if (SimpleGUI::Button(U"Pause", Vec2{ 200, 60 }))
		{
			// 一時停止
			audio.pause();
		}

		if (SimpleGUI::Button(U"Stop", Vec2{ 200, 100 }))
		{
			// 停止して再生位置を最初に戻す
			audio.stop();
		}
	}
}
```


## 19.4 音量を変える
音量を変えるには `.setVolume()` に 0.0～1.0 の値を設定します。デフォルトでは最大の 1.0 です。

現在の音量は `.getVolume()` で取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	double volume = 1.0;

	while (System::Update())
	{
		ClearPrint();

		// 現在の音量を取得
		Print << audio.getVolume();

		if (SimpleGUI::Slider(U"volume: {:.2f}"_fmt(volume), volume, Vec2{ 200, 20 }, 160, 140))
		{
			// 音量を設定
			audio.setVolume(volume);
		}
	}
}
```


## 19.5 フェードイン・フェードアウト
`.play()`, `.pause()`, `.stop()` に時間を設定すると、その時間をかけて音量がフェードイン・フェードアウトします。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	while (System::Update())
	{
		ClearPrint();

		// 再生されているか
		Print << U"isPlaying: " << audio.isPlaying();

		// 一時停止中であるか
		Print << U"isPaused: " << audio.isPaused();

		// 現在の音量
		Print << audio.getVolume();

		if (SimpleGUI::Button(U"Play", Vec2{ 200, 20 }))
		{
			// 2 秒かけて再生・再開
			audio.play(2s);
		}

		if (SimpleGUI::Button(U"Pause", Vec2{ 200, 60 }))
		{
			// 2 秒かけて一時停止
			audio.pause(2s);
		}

		if (SimpleGUI::Button(U"Stop", Vec2{ 200, 100 }))
		{
			// 2 秒かけて停止して再生位置を最初に戻す
			audio.stop(2s);
		}
	}
}
```


## 19.6 再生中に音量を徐々に変える
`.fadeVolume(volume, duration)` を使うと、指定した時間 `duration` だけかけて、音量が徐々に `volume` に変化します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 現在の音量
		Print << audio.getVolume();

		if (SimpleGUI::Button(U"1.0", Vec2{ 200, 20 }))
		{
			// 2 秒かけて音量を 1.0 に
			audio.fadeVolume(1.0, 2s);
		}

		if (SimpleGUI::Button(U"0.5", Vec2{ 200, 60 }))
		{
			// 1 秒かけて音量を 0.5 に
			audio.fadeVolume(0.5, 1s);
		}

		if (SimpleGUI::Button(U"0.1", Vec2{ 200, 100 }))
		{
			// 1.5 秒かけて音量を 0.1 に
			audio.fadeVolume(0.1, 1.5s);
		}
	}
}
```


## 19.7 再生スピードを変える
再生スピードを変えるには `.setSpeed(speed)` または `.fadeSpeed(speed, duration)` を使って、再生スピードの倍率を設定します。デフォルトでは 1.0 です。再生スピードが速くなると音は高く聞こえ、遅くなると低く聞こえます。スピードを早くしても音の高低が発生しないようにするには、この章の後半に出てくる「ピッチシフト機能」と組み合わせます。

現在の再生スピードは `.getSpeed()` で取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 現在のスピード
		Print << audio.getSpeed();

		if (SimpleGUI::Button(U"1.2", Vec2{ 200, 20 }))
		{
			// 2 秒かけてスピードを 1.2 に
			audio.fadeSpeed(1.2, 2s);
		}

		if (SimpleGUI::Button(U"1.0", Vec2{ 200, 60 }))
		{
			// 1 秒かけてスピードを 1.0 に
			audio.fadeSpeed(1.0, 1s);
		}

		if (SimpleGUI::Button(U"0.8", Vec2{ 200, 100 }))
		{
			// 1.5 秒かけてスピードを 0.8 に
			audio.fadeSpeed(0.8, 1.5s);
		}
	}
}
```


## 19.8 パンを変える
左右の音量バランス（パン）を変えるには `.setPan(pan)` または `.fadePan(pan, duration)` を使って、パンを -1.0～1.0 の範囲で設定します。デフォルトでは 0.0 で、左が負の方向、右が正の方向です。

現在のパンは `.getPan()` で取得できます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 現在のパン
		Print << audio.getPan();

		if (SimpleGUI::Button(U"0.9", Vec2{ 200, 20 }))
		{
			// 2 秒かけてパンを 0.9 に
			audio.fadePan(0.9, 2s);
		}

		if (SimpleGUI::Button(U"0.0", Vec2{ 200, 60 }))
		{
			// 1 秒かけてパンを 0.0 に
			audio.fadePan(0.0, 1s);
		}

		if (SimpleGUI::Button(U"-0.9", Vec2{ 200, 100 }))
		{
			// 1.5 秒かけてパンを -0.9 に
			audio.fadePan(-0.9, 1.5s);
		}
	}
}
```


## 19.9 再生位置を取得する
オーディオの合計再生時間（秒）は `.lengthSec()`, 合計再生サンプルは `.samples()` で取得できます。現在の再生位置を `.posSec()` では秒、 `.posSample()` ではサンプル数で取得できます。

音楽のタイミングに合わせた演出や、音楽ゲームの判定などは、この再生位置を基準にします。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 曲全体の長さ
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());

		// 現在の再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
	}
}
```


## 19.10 再生位置を変更する
再生位置を変更するには、`.seekSamples()` で移動先の位置をサンプル単位で指定するか、`.seekTime()` で移動先の位置を時間（秒）で指定します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 曲全体
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());

		// 再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());

		if (SimpleGUI::Button(U"0 samples", Vec2{ 300, 20 }))
		{
			// 0 サンプル目（曲の先頭）に移動
			audio.seekSamples(0);
		}

		if (SimpleGUI::Button(U"441,000 samples", Vec2{ 300, 60 }))
		{
			// 441,000 サンプル目に移動
			audio.seekSamples(441000);
		}

		if (SimpleGUI::Button(U"20.0 sec", Vec2{ 300, 100 }))
		{
			// 20 秒の位置に移動
			audio.seekTime(20.0);
		}
	}
}
```


## 19.11 ループ再生する
曲の再生が終端に到達したとき、自動的に先頭からループ再生させたい場合は、`Audio` のコンストラクタに `Loop::Yes` を指定します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3", Loop::Yes };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// ループが設定されているか
		Print << audio.isLoop();

		// ループ回数
		Print << audio.loopCount();

		// 曲全体
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());

		// 再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
	}
}
```


## 19.12 区間を指定してループ再生する
オーディオの再生が指定したループ終端位置に到達したとき、指定したループ先頭位置に戻って区間ループ再生させるには、ループ区間の先頭位置を `Arg::loopBegin`, 終端位置を `Arg::loopEnd` で指定します。位置の指定方法はサンプル数か時間かを選べますが、begin と end で揃える必要があります。

`Arg::loopEnd` を指定するとそれ以降のオーディオデータは保持せず、メモリの消費量が節約されます。

ストリーミング再生では、`Arg::loopEnd` を指定できない制約があります。必要な場合は音声データをあらかじめカットします。

ループの末尾と先頭の波形にずれがあると、ループの瞬間にノイズ音が生じます。サンプル単位でタイミングを調整する必要があります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 1.5 秒～44.5 秒の区間をループ
	const Audio audio{ U"example/test.mp3", Arg::loopBegin = 1.5s, Arg::loopEnd = 44.5s };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// ループが設定されているか
		Print << audio.isLoop();

		// ループ回数
		Print << audio.loopCount();

		// 曲全体
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());

		// 再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
	}
}
```


## 19.13 楽器の音を生成する

```cpp

```


## 19.14 同じオーディオを重ねて再生する

```cpp

```


## 19.15 バスとグローバルオーディオの設定

```cpp

```


## 19.16 オーディオフィルタ

```cpp

```


## 19.17 空のオーディオ

```cpp

```


## 19.18 オーディオの代入

```cpp

```

