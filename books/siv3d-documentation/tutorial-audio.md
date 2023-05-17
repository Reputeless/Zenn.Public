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
OpenSiv3D では、次のような音声フォーマットの読み込みがサポートされています。一部の音声フォーマットは OS によって対応状況が異なります。

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
楽器の種類と音の高さ、長さを指定して、プログラムで楽器の音を生成し、そこから `Audio` を作成できます。`Audio` のコンストラクタに `GMInstrument` で列挙される楽器名、`PianoKey` で列挙される音の高さ、音の長さを渡します。

音の長さは
- ノート・オン（鳴らす）時間
- ノート・オン時間とノート・オフ（鳴らし終える）時間

の 2 通りの設定の仕方があります。前者では 1.0 秒のノート・オフ時間が設定されます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ピアノの C4 (ド) の音
	const Audio piano{ GMInstrument::Piano1, PianoKey::C4, 0.5s };

	// クラリネットの D4 (レ) の音
	const Audio clarinet{ GMInstrument::Clarinet, PianoKey::D4, 0.5s };

	// トランペットの E4 (ミ) の音。ノート・オン 2.0 秒、ノート・オフ 0.5 秒
	const Audio trumpet{ GMInstrument::Trumpet, PianoKey::E4, 2.0s, 0.5s };

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Piano", Vec2{ 20, 20 }))
		{
			piano.play();
		}

		if (SimpleGUI::Button(U"Clarinet", Vec2{ 20, 60 }))
		{
			clarinet.play();
		}

		if (SimpleGUI::Button(U"Trumpet", Vec2{ 20, 100 }))
		{
			trumpet.play();
		}
	}
}
```


## 19.14 同じオーディオを重ねて再生する
1 つの `Audio` を重ねて再生したい場合には `.play()` の代わりに `.playOneShot()` を使います。`.playOneShot()` の引数には音量、パン、再生スピードを渡せます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ピアノの C4 (ド) の音。
	const Audio piano{ GMInstrument::Piano1, PianoKey::C4, 0.5s };

	// クラリネットの D4 (レ) の音
	const Audio clarinet{ GMInstrument::Clarinet, PianoKey::D4, 0.5s };

	// トランペットの E4 (ミ) の音
	const Audio trumpet{ GMInstrument::Trumpet, PianoKey::E4, 0.5s };

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Piano", Vec2{ 20, 20 }))
		{
			// 音量 0.5 で再生
			piano.playOneShot(0.5);
		}

		if (SimpleGUI::Button(U"Clarinet", Vec2{ 20, 60 }))
		{
			clarinet.playOneShot(0.5);
		}

		if (SimpleGUI::Button(U"Trumpet", Vec2{ 20, 100 }))
		{
			trumpet.playOneShot(0.5);
		}
	}
}
```


## 19.15 ミキシングバスとグローバルオーディオ
オーディオを BGM, 環境音、キャラクターボイスなどのグループに分類し、グループごとに音量などを制御したい場合、**ミキシングバス** が役に立ちます。すべてのオーディオは MixBus0 ～ MixBus3 の 4 つのグループのいずれかのミキシングバスを通して再生されます。デフォルトでは MixBus0 が使われます。また、ミキシングバスは最終的に**グローバルオーディオ**によって統合されます。

![](/images/doc_v6/tutorial/19/15.png)

個々のミキシングバスは次のような操作ができます。
- 音量の調整
- 再生中の波形サンプルの取得
- 再生中の波形の FFT の結果取得
- オーディオフィルタの適用（次節で説明）

グローバルオーディオは次のような操作ができます。
- オーディオの一斉停止・再開
- 音量の調整
- 再生中の波形サンプルの取得
- 再生中の波形の FFT の結果取得

最終的な出力音量は、  
**`Audio` で設定された音量 × ミキシングバスの音量 × グローバルオーディオの音量 = 最終出力**
になります。

`Audio` をどのミキシングバスで再生するかを指定するには、`.play()` または `.playOneShot()` の引数に `MixBus0` ～ `MixBus3` を指定します（デフォルトでは `MixBus0`）。

ミキシングバスの音量を調整するには `GlobalAudio::BusSetVolume(busIndex, volume)`, `GlobalAudio::BusFadeVolume(busIndex, volume, duration)` を呼びます。

グローバルオーディオの音量を調整するには `GlobalAudio::SetVolume(volume)`, `GlobalAudio::FadeVolume(volume, duration)` を呼びます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ピアノの C4 (ド) の音
	const Audio pianoC{ GMInstrument::Piano1, PianoKey::C4, 0.5s };

	// ピアノの D4 (レ) の音
	const Audio pianoD{ GMInstrument::Piano1, PianoKey::D4, 0.5s };

	// ピアノの E4 (ミ) の音
	const Audio pianoE{ GMInstrument::Piano1, PianoKey::E4, 0.5s };

	double globalVolume = 1.0, mixBus0Volume = 1.0, mixBus1Volume = 1.0;

	while (System::Update())
	{
		if (SimpleGUI::Slider(U"Global Vol", globalVolume, Vec2{ 20, 20 }, 120, 200))
		{
			// グローバルオーディオの音量を変更
			GlobalAudio::SetVolume(globalVolume);
		}

		if (SimpleGUI::Slider(U"Bus0 Vol", mixBus0Volume, Vec2{ 20, 60 }, 120, 120))
		{
			// MixBus0 の音量を変更
			GlobalAudio::BusSetVolume(MixBus0, mixBus0Volume);
		}

		if (SimpleGUI::Slider(U"Bus1 Vol", mixBus1Volume, Vec2{ 300, 60 }, 120, 120))
		{
			// MixBus1 の音量を変更
			GlobalAudio::BusSetVolume(MixBus1, mixBus1Volume);
		}

		if (SimpleGUI::Button(U"C Bus0", Vec2{ 20, 100 }))
		{
			pianoC.playOneShot(MixBus0, 0.5);
		}

		if (SimpleGUI::Button(U"D Bus0", Vec2{ 20, 140 }))
		{
			pianoD.playOneShot(MixBus0, 0.5);
		}

		if (SimpleGUI::Button(U"E Bus0", Vec2{ 20, 180 }))
		{
			pianoE.playOneShot(MixBus0, 0.5);
		}

		if (SimpleGUI::Button(U"C Bus1", Vec2{ 300, 100 }))
		{
			pianoC.playOneShot(MixBus1, 0.5);
		}

		if (SimpleGUI::Button(U"D Bus1", Vec2{ 300, 140 }))
		{
			pianoD.playOneShot(MixBus1, 0.5);
		}

		if (SimpleGUI::Button(U"E Bus1", Vec2{ 300, 180 }))
		{
			pianoE.playOneShot(MixBus1, 0.5);
		}
	}
}
```


## 19.16 （サンプル）オーディオフィルタ
1 つのミキシングバスには最大 8 つのオーディオフィルタを設定し、オーディオの再生中に、リアルタイムでの音声波形加工ができます。

| 関数（引数は省略) | 説明 |
|--|--|
|`GlobalAudio::BusClearFilter()`| 設定されているフィルタをオフにします |
|`GlobalAudio::BusSetLowPassFilter()`| ローパスフィルタを設定します |
|`GlobalAudio::BusSetHighPassFilter()`| ハイパスフィルタを設定します |
|`GlobalAudio::BusSetEchoFilter()`|  エコーフィルタを設定します |
|`GlobalAudio::BusSetReverbFilter()`|  リバーブフィルタを設定します |
|`GlobalAudio::BusSetPitchShiftFilter()`|  ピッチシフトフィルタを設定します |

OpenSiv3D v0.6.6 Web 版では、ピッチシフトフィルタを利用できません。`GlobalAudio::SupportsPitchShift()` を使うと、現在の実行環境でピッチシフトフィルタを利用できるかを取得できます。

次のサンプルは、オーディオフィルタ機能のデモです。これまでの章では扱っていない、いくつかの高度な機能を含んでいます。「Open audio file」をクリックすると、パソコンに保存されているオーディオファイルをオープンできます。

![](/images/doc_v6/tutorial/19/16.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	Audio audio;
	double posSec = 0.0;
	double volume = 1.0;
	double pan = 0.0;
	double speed = 1.0;
	bool loop = false;

	Array<float> busSamples;
	Array<float> globalSamples;
	FFTResult busFFT;
	FFTResult globalFFT;
	LineString lines(256, Vec2{ 0, 0 });

	bool pitch = false;
	double pitchShift = 0.0;

	bool lpf = false;
	double lpfCutoffFrequency = 800.0;
	double lpfResonance = 0.5;
	double lpfWet = 1.0;

	bool hpf = false;
	double hpfCutoffFrequency = 800.0;
	double hpfResonance = 0.5;
	double hpfWet = 1.0;

	bool echo = false;
	double delay = 0.1;
	double decay = 0.5;
	double echoWet = 0.5;

	bool reverb = false;
	bool freeze = false;
	double roomSize = 0.5;
	double damp = 0.0;
	double width = 0.5;
	double reverbWet = 0.5;

	while (System::Update())
	{
		ClearPrint();
		Print << U"GlobalAudio::GetActiveVoiceCount(): " << GlobalAudio::GetActiveVoiceCount();
		Print << U"isEmpty : " << audio.isEmpty();
		Print << U"isStreaming : " << audio.isStreaming();
		Print << U"sampleRate : " << audio.sampleRate();
		Print << U"samples : " << audio.samples();
		Print << U"lengthSec : " << audio.lengthSec();
		Print << U"posSample : " << audio.posSample();
		Print << U"posSec : " << (posSec = audio.posSec());
		Print << U"isActive : " << audio.isActive();
		Print << U"isPlaying : " << audio.isPlaying();
		Print << U"isPaused : " << audio.isPaused();
		Print << U"samplesPlayed : " << audio.samplesPlayed();
		Print << U"isLoop : " << (loop = audio.isLoop());
		Print << U"getLoopTimingtLoop : " << audio.getLoopTiming().beginPos << U", " << audio.getLoopTiming().endPos;
		Print << U"loopCount : " << audio.loopCount();
		Print << U"getVolume : " << (volume = audio.getVolume());
		Print << U"getPan : " << (pan = audio.getPan());
		Print << U"getSpeed : " << (speed = audio.getSpeed());

		if (SimpleGUI::Button(U"Open audio file", Vec2{ 60, 560 }))
		{
			audio.stop(0.5s);
			audio = Dialog::OpenAudio(Audio::Stream);
		}

		{
			GlobalAudio::BusGetSamples(MixBus0, busSamples);
			GlobalAudio::BusGetFFT(MixBus0, busFFT);

			for (auto [i, s] : Indexed(busSamples))
			{
				lines[i].set((300.0 + i), (200.0 - s * 100.0));
			}

			if (busSamples)
			{
				lines.draw(2, Palette::Orange);
			}

			for (auto [i, s] : Indexed(busFFT.buffer))
			{
				RectF{ Arg::bottomLeft(300 + i, 300), 1, (s * 4) }.draw();
			}
		}

		{
			GlobalAudio::GetSamples(globalSamples);
			GlobalAudio::GetFFT(globalFFT);

			for (auto [i, s] : Indexed(globalSamples))
			{
				lines[i].set((300.0 + i), (550.0 - s * 100.0));
			}

			if (globalSamples)
			{
				lines.draw(2, Palette::Orange);
			}

			for (auto [i, s] : Indexed(globalFFT.buffer))
			{
				RectF{ Arg::bottomLeft(300 + i, 650), 1, (s * 4) }.draw();
			}
		}

		if (SimpleGUI::Button(U"Play", Vec2{ 600, 20 }, 80, !audio.isPlaying()))
		{
			audio.play();
		}

		if (SimpleGUI::Button(U"Pause", Vec2{ 690, 20 }, 80, (audio.isPlaying() && !audio.isPaused())))
		{
			audio.pause();
		}

		if (SimpleGUI::Button(U"Stop", Vec2{ 780, 20 }, 80, (audio.isPlaying() || audio.isPaused())))
		{
			audio.stop();
		}

		if (SimpleGUI::Button(U"Play in 2s", Vec2{ 870, 20 }, 120, !audio.isPlaying()))
		{
			audio.play(2s);
		}

		if (SimpleGUI::Button(U"Pause in 2s", Vec2{ 1000, 20 }, 120, (audio.isPlaying() && !audio.isPaused())))
		{
			audio.pause(2s);
		}

		if (SimpleGUI::Button(U"Stop in 2s", Vec2{ 1130, 20 }, 120, (audio.isPlaying() || audio.isPaused())))
		{
			audio.stop(2s);
		}

		if (SimpleGUI::Slider(U"{:.1f} / {:.1f}"_fmt(posSec, audio.lengthSec()), posSec, 0.0, audio.lengthSec(), Vec2{ 600, 60 }, 160, 360))
		{
			if (MouseL.down() || !Cursor::DeltaF().isZero()) // シークの連続（ノイズの原因）を防ぐ
			{
				audio.seekTime(posSec);
			}
		}

		if (SimpleGUI::CheckBox(loop, U"Loop", Vec2{ 1130, 60 }))
		{
			audio.setLoop(loop);
		}

		if (SimpleGUI::Slider(U"volume: {:.2f}"_fmt(volume), volume, Vec2{ 600, 110 }, 140, 130))
		{
			audio.setVolume(volume);
		}

		if (SimpleGUI::Button(U"0.0 in 2s", Vec2{ 880, 110 }, 110, audio.isActive()))
		{
			audio.fadeVolume(0.0, 2s);
		}

		if (SimpleGUI::Button(U"0.5 in 2s", Vec2{ 1000, 110 }, 110, audio.isActive()))
		{
			audio.fadeVolume(0.5, 2s);
		}

		if (SimpleGUI::Button(U"1.0 in 2s", Vec2{ 1120, 110 }, 110, audio.isActive()))
		{
			audio.fadeVolume(1.0, 2s);
		}

		if (SimpleGUI::Slider(U"pan: {:.2f}"_fmt(pan), pan, -1.0, 1.0, Vec2{ 600, 150 }, 140, 130))
		{
			audio.setPan(pan);
		}

		if (SimpleGUI::Button(U"-1.0 in 2s", Vec2{ 880, 150 }, 110, audio.isActive()))
		{
			audio.fadePan(-1.0, 2s);
		}

		if (SimpleGUI::Button(U"0.0 in 2s", Vec2{ 1000, 150 }, 110, audio.isActive()))
		{
			audio.fadePan(0.0, 2s);
		}

		if (SimpleGUI::Button(U"1.0 in 2s", Vec2{ 1120, 150 }, 110, audio.isActive()))
		{
			audio.fadePan(1.0, 2s);
		}

		if (SimpleGUI::Slider(U"speed: {:.3f}"_fmt(speed), speed, 0.0, 4.0, Vec2{ 600, 190 }, 140, 130))
		{
			audio.setSpeed(speed);
		}

		if (SimpleGUI::Button(U"0.8 in 2s", Vec2{ 880, 190 }, 110, audio.isActive()))
		{
			audio.fadeSpeed(0.8, 2s);
		}

		if (SimpleGUI::Button(U"1.0 in 2s", Vec2{ 1000, 190 }, 110, audio.isActive()))
		{
			audio.fadeSpeed(1.0, 2s);
		}

		if (SimpleGUI::Button(U"1.2 in 2s", Vec2{ 1120, 190 }, 110, audio.isActive()))
		{
			audio.fadeSpeed(1.2, 2s);
		}

		bool updatePitch = false;
		bool updateLPF = false;
		bool updateHPF = false;
		bool updateEcho = false;
		bool updateReverb = false;

		if (SimpleGUI::CheckBox(pitch, U"Pitch", Vec2{ 600, 240 }, 120, GlobalAudio::SupportsPitchShift()))
		{
			if (pitch)
			{
				updatePitch = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 0);
			}
		}
		updatePitch |= SimpleGUI::Slider(U"pitchShift: {:.2f}"_fmt(pitchShift), pitchShift, -12.0, 12.0, Vec2{ 720, 240 }, 160, 300);

		if (SimpleGUI::CheckBox(lpf, U"LPF", Vec2{ 600, 280 }, 120))
		{
			if (lpf)
			{
				updateLPF = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 1);
			}
		}
		updateLPF |= SimpleGUI::Slider(U"cutoffFrequency: {:.0f}"_fmt(lpfCutoffFrequency), lpfCutoffFrequency, 10, 4000, Vec2{ 720, 280 }, 220, 240);
		updateLPF |= SimpleGUI::Slider(U"resonance: {:.2f}"_fmt(lpfResonance), lpfResonance, 0.1, 8.0, Vec2{ 720, 310 }, 220, 240);
		updateLPF |= SimpleGUI::Slider(U"wet: {:.2f}"_fmt(lpfWet), lpfWet, Vec2{ 720, 340 }, 220, 240);

		if (SimpleGUI::CheckBox(hpf, U"HPF", Vec2{ 600, 380 }, 120))
		{
			if (hpf)
			{
				updateHPF = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 2);
			}
		}
		updateHPF |= SimpleGUI::Slider(U"cutoffFrequency: {:.0f}"_fmt(hpfCutoffFrequency), hpfCutoffFrequency, 10, 4000, Vec2{ 720, 380 }, 220, 240);
		updateHPF |= SimpleGUI::Slider(U"resonance: {:.2f}"_fmt(hpfResonance), hpfResonance, 0.1, 8.0, Vec2{ 720, 410 }, 220, 240);
		updateHPF |= SimpleGUI::Slider(U"wet: {:.2f}"_fmt(hpfWet), hpfWet, Vec2{ 720, 440 }, 220, 240);

		if (SimpleGUI::CheckBox(echo, U"Echo", Vec2{ 600, 480 }, 120))
		{
			if (echo)
			{
				updateEcho = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 3);
			}
		}
		updateEcho |= SimpleGUI::Slider(U"delay: {:.2f}"_fmt(delay), delay, Vec2{ 720, 480 }, 220, 240);
		updateEcho |= SimpleGUI::Slider(U"decay: {:.2f}"_fmt(decay), decay, Vec2{ 720, 510 }, 220, 240);
		updateEcho |= SimpleGUI::Slider(U"wet: {:.2f}"_fmt(echoWet), echoWet, Vec2{ 720, 540 }, 220, 240);

		if (SimpleGUI::CheckBox(reverb, U"Reverb", Vec2{ 600, 580 }, 120))
		{
			if (reverb)
			{
				updateReverb = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 4);
			}
		}
		updateReverb |= SimpleGUI::CheckBox(freeze, U"freeze", Vec2{ 720, 580 }, 110);
		updateReverb |= SimpleGUI::Slider(U"roomSize: {:.2f}"_fmt(roomSize), roomSize, 0.001, 1.0, { 830, 580 }, 150, 200);
		updateReverb |= SimpleGUI::Slider(U"damp: {:.2f}"_fmt(damp), damp, Vec2{ 720, 610 }, 220, 240);
		updateReverb |= SimpleGUI::Slider(U"width: {:.2f}"_fmt(width), width, Vec2{ 720, 640 }, 220, 240);
		updateReverb |= SimpleGUI::Slider(U"wet: {:.2f}"_fmt(reverbWet), reverbWet, Vec2{ 720, 670 }, 220, 240);

		if (pitch && updatePitch)
		{
			GlobalAudio::BusSetPitchShiftFilter(MixBus0, 0, pitchShift);
		}

		if (lpf && updateLPF)
		{
			GlobalAudio::BusSetLowPassFilter(MixBus0, 1, lpfCutoffFrequency, lpfResonance, lpfWet);
		}

		if (hpf && updateHPF)
		{
			GlobalAudio::BusSetHighPassFilter(MixBus0, 2, hpfCutoffFrequency, hpfResonance, hpfWet);
		}

		if (echo && updateEcho)
		{
			GlobalAudio::BusSetEchoFilter(MixBus0, 3, delay, decay, echoWet);
		}

		if (reverb && updateReverb)
		{
			GlobalAudio::BusSetReverbFilter(MixBus0, 4, freeze, roomSize, damp, width, reverbWet);
		}
	}

	// 再生中の音声があれば、フェードアウトさせてから終了
	if (GlobalAudio::GetActiveVoiceCount())
	{
		GlobalAudio::FadeVolume(0.0, 0.5s);
		System::Sleep(0.5s);
	}
}
```


## 19.17 空のオーディオ
データを持たない空（から）のオーディオを再生すると、「フワ」と鳴る 0.5 秒の音が再生されます。音声ファイルの読み込みに失敗したときにも空のオーディオが作成されます。  
オーディオが空であるかは `if (audio.isEmpty())` もしくは `if (not audio)` で調べられます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 初期データを与えないと、空のオーディオになる
	Audio audioA;

	if (not audioA)
	{
		Print << U"audioA is empty";
	}

	// 音声ファイルの読み込みに失敗すると、空のオーディオになる
	Audio audioB{ U"aaa/bbb.wav" };

	if (not audioB)
	{
		Print << U"audioB is empty";
	}

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Play A", Vec2{ 200, 20 }))
		{
			audioA.playOneShot();
		}

		if (SimpleGUI::Button(U"Play B", Vec2{ 200, 60 }))
		{
			audioB.playOneShot();
		}
	}
}
```


## 19.18 オーディオの代入
`Audio` は次のように `=` 演算子を使って代入できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Audio audio;

	while (System::Update())
	{
		ClearPrint();

		Print << audio.isEmpty();

		// オーディオが空の状態で、左クリックされたら
		if ((not audio) && MouseL.down())
		{
			// オーディオを作成して代入
			audio = Audio{ Audio::Stream, U"example/test.mp3" };

			audio.play();
		}
	}
}
```

