---
title: "Tutorial 03 | Making Animation"
free: true
---

# 3. 動きを作る
この章では、「動き」の表現に役立つ Siv3D の機能を学びます。

## 3.1 経過時間を使ったアニメーション

#### Scene::Time()
`Scene::Time()` はプログラムが起動されてからのシーンの経過時間（秒）を `double` 型の値で返します。この値を使って簡単なアニメーションを作成できます。

#### Scene::Center()
`Scene::Center()` はシーンの中心座標を `Point` 型で返します。画面のサイズが 800x600 のときには `Point{ 400, 300 }` を返します。

https://youtu.be/X0vml6zZlQs

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const double t = Scene::Time();

		// 円の半径が、時間の経過に伴って大きくなる
		Circle{ Scene::Center(), (t * 50) }.draw(ColorF{ 0.25 });
	}
}
```

#### Scene::DeltaTime()
`Scene::DeltaTime()` は、直前のフレームからの経過時間 (秒) を `double` 型の値で返します。`Scene::Time()` を使うかわりに、この値を加算していくことでアニメーションを作成することもできます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	double t = 0.0;

	while (System::Update())
	{
		// 経過時間を加算
		t += Scene::DeltaTime();

		Circle{ Scene::Center(), (r * 50) }.draw(ColorF{ 0.25 });
	}
}
```

## 3.2 たくさんの円を同時に動かす
`step(N)` は、Siv3D に用意されている、ループを短く書ける機能です。`for (auto i : step(N))` は `for (int i = 0; i < N; ++i)`と同じ働きです。
https://youtu.be/68UrNsZ9eOY
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const double t = Scene::Time();

		for (auto i : step(9))
		{
			Circle{ (i * 100), (t * 100), 20 }.draw(ColorF{ 0.25 });
		}
	}
}
```

## 3.3 円周上に沿って動かす
`OffsetCircular` は円周に沿った動きをつくるのに最適な円座標クラスです。オフセット `offset` と円座標系の動径座標 `r`, 角度座標 θ (`theta`) の 3 つの要素で位置を表現します。

`OffsetCircular{ offset, r, theta }` は、シーン上の座標 `Vec2 offset` を中心とする半径 `double r` の円を考え、その円周上で 12 時の方向を 0° として時計回りに `double theta` の位置を表します。`OffsetCircular` は `Vec2` に変換できます。

![](/images/doc_v6/tutorial/3/3a.png)
https://youtu.be/9CdPtsG5vJE
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const double t = Scene::Time();

		for (auto i : step(6))
		{
			// 円座標系における角度座標
			// 60° ごとに配置し、毎秒 30° の速さで回転する
			const double theta = (i * 60_deg + t * 30_deg);

			const Vec2 pos = OffsetCircular{ Scene::Center(), 200, theta };

			Circle{ pos, 20 }.draw(ColorF{ 0.25 });
		}
	}
}
```


## 3.4 毎フレーム固定の移動はダメ！ 時間を使おう
`Scene::Time()` や `Scene::DeltaTime()` を使わなくても、毎フレーム、固定の値を足していくようなプログラムを書けばアニメーションを作れそうですが、それは**大きな間違い**です。

なぜなら、プログラムが実行されるパソコンのモニタのリフレッシュレートによって、メインループが毎秒何回実行されるかが変わるためです。一般的なモニタのリフレッシュレートは 60Hz で、毎秒 60 回メインループが実行されますが、近年は 120Hz や 144Hz, 240Hz など、より高頻度のリフレッシュレートを持つモニタが増えています。

次のような「毎フレーム 3px ずつ移動」というプログラムでは、60Hz のモニタ上では円は毎秒 180px の速さで移動しますが、120Hz のモニタで実行すると、その倍の毎秒 360px の速さで移動します。もしこれがゲームの敵キャラクターだったら、実行するパソコンによって移動スピードが変わり、ゲームバランスが壊れてしまいます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	double x = 0.0;

	while (System::Update())
	{
		// 毎フレーム 3px 移動（時間ベースではないので不適切！）
		x += 3;

		Circle{ x, 300, 50 }.draw(ColorF{ 0.25 });
	}
}
```

こうした問題を避けるため、アニメーションはフレームではなく時間をベースに計算する必要があります。上記のコードを時間ベースになるよう直したコードは次のとおりです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	double x = 0.0;

	while (System::Update())
	{
		// 毎秒 180px 移動
		x += (Scene::DeltaTime() * 180);

		Circle{ x, 300, 50 }.draw(ColorF{ 0.25 });
	}
}
```


## 3.5 一定時間ごとに出現
N 秒に 1 回の頻度でオブジェクトを出現させる、といった処理を書くときも、フレームベースではなく時間ベースのプログラムにします。
https://youtu.be/nP3bXjQBX3s
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 出現間隔（秒）
	constexpr double spawnTime = 1.0;

	// 蓄積された時間（秒）
	double accumulator = 0.0;

	while (System::Update())
	{
		accumulator += Scene::DeltaTime();

		// 蓄積時間が出現間隔を超えたら
		if (spawnTime <= accumulator)
		{
			accumulator -= spawnTime;

			Print << U"Spawn!";
		}
	}
}
```

もし出現間隔が非常に短い（1 フレームの時間やそれよりも短い）場合、1 フレームで複数回出現させる必要が生じます。そのような状況には、`if` の代わりに `while (spawnTime <= accumulatedTime)` を使うことで対処できます。


## 3.6 マウスのクリック
マウスの左ボタンがクリック（タッチディスプレイの場合は画面がタッチ）されたかを、`if (MouseL.down())` で調べられます。次のサンプルでは、画面上をマウスでクリックするたびに円が大きくなります。
https://youtu.be/8c4C0ilM2CY
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	int32 count = 0;

	while (System::Update())
	{
		// もし左クリックされたら
		if (MouseL.down())
		{
			++count;
		}

		Circle{ Scene::Center(), (count * 20) }.draw(ColorF{ 0.25 });
	}
}
```


## 3.7 ストップウォッチ
`Stopwatch` は、経過時間の計測やリセットを便利に行えるクラスです。`Stopwatch` のコンストラクタ引数に `StartImmediately::Yes` を渡すと、作成と同時に計測を開始します。`Stopwatch::sF()` はその時点での経過時間（秒）を `double` 型で返します。`Stopwatch::restart()` すると、経過時間をリセットして再び 0 から計測を開始（リスタート）します。
https://youtu.be/b-fETvQ7kb0
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// ストップウォッチ（作成と同時に計測開始）
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		// もし左クリックされたら
		if (MouseL.down())
		{
			// ストップウォッチをリセットして再び 0 から計測
			stopwatch.restart();
		}

		// ストップウォッチの経過時間（秒）を double 型で取得 
		const double t = stopwatch.sF();

		Circle{ Scene::Center(), (t * 50) }.draw(ColorF{ 0.25 });
	}
}
```


## 3.8 ストップウォッチの一時停止と再開

ストップウォッチが計測中かどうかは `if (Stopwatch::isRunning())` で調べられます。ストップウォッチの計測を一時停止するには `Stopwatch::pause()`, 一時停止を解除して計測を再開するには `Stopwatch::resume()` します。
https://youtu.be/WFIZBX6bX_8
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// ストップウォッチ（作成と同時に計測開始）
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		// もし左クリックされたら
		if (MouseL.down())
		{
			// ストップウォッチが計測中なら
			if (stopwatch.isRunning())
			{
				// ストップウォッチを一時停止
				stopwatch.pause();
			}
			else // ストップウォッチが一時停止中なら
			{
				// ストップウォッチを再開
				stopwatch.resume();
			}
		}

		// ストップウォッチの経過時間（秒）を double 型で取得 
		const double t = stopwatch.sF();

		Circle{ Scene::Center(), (t * 50) }.draw(ColorF{ 0.25 });
	}
}
```

## 3.9 周期的なアニメーション
Siv3D で周期的に移動・点滅・拡大縮小するようなアニメーションを作るときには、`Periodic::` 名前空間に用意されている関数群を使うと便利です。

| 周期関数 | 動き |
|--|--|
|`Periodic::Square0_1`|![](/images/doc_v6/tutorial/3/9a.png)|
|`Periodic::Triangle0_1`|![](/images/doc_v6/tutorial/3/9b.png)|
|`Periodic::Sine0_1`|![](/images/doc_v6/tutorial/3/9c.png)|
|`Periodic::Sawtooth0_1`|![](/images/doc_v6/tutorial/3/9d.png)|
|`Periodic::Jump0_1`|![](/images/doc_v6/tutorial/3/9e.png)|

周期は `2s` (2 秒) や `0.5s` (0.5 秒) のように時間リテラルを使って記述します。

#### Periodic::Square0_1()
指定した周期で 0.0 か 1.0 を交互に返す関数です。周期の前半では 1.0 を、残りの半分では 0.0 を返します。

#### Periodic::Triangle0_1()
0.0 から一定の速度で値が大きくなって 1.0 に、そして一定の速度で小さくなって 0.0 に、という変化を指定した周期で繰り返す関数です。

#### Periodic::Sine0_1()
指定した周期で、0.0～1.0 の範囲で正弦波（サインカーブ）を描く数値の変化を返す関数です。

#### Periodic::Sawtooth0_1()
指定した周期で、0.0 → 1.0 への変化を繰り返す関数です。

#### Periodic::Jump0_1()
指定した周期で、地面からジャンプしたときの速度のような数値変化を繰り返す関数です。

https://youtu.be/NHooReSLXaA

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const double p0 = Periodic::Square0_1(2s);
		const double p1 = Periodic::Triangle0_1(2s);
		const double p2 = Periodic::Sine0_1(2s);
		const double p3 = Periodic::Sawtooth0_1(2s);
		const double p4 = Periodic::Jump0_1(2s);

		Line{ 100, 0, 100, 600 }.draw(2, ColorF{ 0.8 });
		Line{ 700, 0, 700, 600 }.draw(2, ColorF{ 0.8 });

		Circle{ 100 + p0 * 600, 100, 20 }.draw(ColorF{ 0.25 });
		Circle{ 100 + p1 * 600, 200, 20 }.draw(ColorF{ 0.25 });
		Circle{ 100 + p2 * 600, 300, 20 }.draw(ColorF{ 0.25 });
		Circle{ 100 + p3 * 600, 400, 20 }.draw(ColorF{ 0.25 });
		Circle{ 100 + p4 * 600, 500, 20 }.draw(ColorF{ 0.25 });
	}
}
```


## 3.10 マウスのボタンが押されている
マウスの左ボタンが押されている（タッチディスプレイの場合は画面がタッチされている）かを、`if (MouseL.pressed())` で調べられます。次のサンプルでは、左ボタンが押されている間だけ円が大きくなります。
https://youtu.be/U62vWAmBlgE
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	double r = 0.0;

	while (System::Update())
	{
		// もし左ボタンが押されていたら
		if (MouseL.pressed())
		{
			r += (Scene::DeltaTime() * 100.0);
		}

		Circle{ Scene::Center(), r }.draw(ColorF{ 0.25 });
	}
}
```


## 3.11 トランジション

### Transition
値が少しずつ大きくなって最大値に到達する。そこから徐々に小さくなって最小値に戻る、という挙動をプログラムするときには `Transition` を使うと便利です。`Transition` のコンストラクタには、最小値から最大値に増加する所要時間と、最大値から最小値に減少する所要時間を設定します。あとは毎フレーム、`Transition::update()` に、増加の場合は `true` を、減少の場合は `false` を渡せば、設定された速度で値が変化します。`Transition::value()` で現在の値を取得できます。

次のサンプルでは、左ボタンが押されていると扇形が大きくなり、離されていると小さくなります。

https://youtu.be/2C7CRQGrfGo
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// 2.0 秒かけて 0.0 から 1.0 になる速度で増加し
	// 0.5 秒かけて 1.0 から 0.0 になる速度で減少するトランジション
	Transition transition{ 2.0s, 0.5s };

	while (System::Update())
	{
		if (MouseL.pressed())
		{
			// マウスの左ボタンが押されていたら増加
			transition.update(true);
		}
		else
		{
			// 押されていなかったら減少
			transition.update(false);
		}

		const double t = transition.value();

		Circle{ Scene::Center(), (t * 200) }.draw(ColorF{ 0.25 });
	}
}
```

`MouseL.pressed()` は `bool` 型の値を返すので、上記のプログラムはさらに次のように短く書けます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// 2.0 秒かけて 0.0 から 1.0 になる速度で増加し
	// 0.5 秒かけて 1.0 から 0.0 になる速度で減少するトランジション
	Transition transition{ 2.0s, 0.5s };

	while (System::Update())
	{
		// マウスの左ボタンが押されていたら増加、押されていなかったら減少
		transition.update(MouseL.pressed());

		const double t = transition.value();

		Circle{ Scene::Center(), (t * 200) }.draw(ColorF{ 0.25 });
	}
}
```


## 3.12 線形補間
あるベクトル A から別のベクトル B への線形補間は `A.lerp(B, t)` で計算できます。`t` は　0.0 ～ 1.0 です。また、`Min()` は、渡された引数のうち最小値を、`Max()` は、渡された引数のうち最小値を返します。
https://youtu.be/5z00r9Or_qs
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// スタート位置
	Vec2 from{ 100, 100 };

	// ゴール位置
	Vec2 to{ 700, 500 };

	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		// 移動の割合 0.0～1.0
		const double t = Min(stopwatch.sF(), 1.0);

		// スタート位置からゴール位置へ t の割合だけ進んだ位置
		const Vec2 pos = from.lerp(to, t);

		if (MouseL.down())
		{
			// スタート位置を現在の位置に
			from = pos;

			// ゴール位置をマウスカーソルの位置に
			to = Cursor::Pos();

			stopwatch.restart();
		}

		Circle{ pos, 40 }.draw(ColorF{ 0.25 });
		Circle{ to, 50 }.drawFrame(5, ColorF{ 0.25 });
	}
}
```

## 3.13 イージング
0.0 から 1.0 に一定の速度で値を増加させるだけでは単調な動きになってしまいます。はじめは少しずつ加速し、ゴールに近づくとゆっくりになるといったように、速度に変化を与えると、より洗練された視覚効果を実現できます。0.0 → 1.0 の単調増加を、特徴的なカーブに変換できる **イージング関数** を使ってアニメーションの印象を改善しましょう。

イージング関数は全部で約 30 種類用意されています。一覧は [Easing Functions Cheat Sheet](https://easings.net/) で確認できます。次のプログラムでは `EaseInOutExpo()` を使っています。ほかにも `EaseOutBounce()` や `EaseInOutBack()` など様々なイージング関数を試してみましょう。
https://youtu.be/lmmkji7i6PQ
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// スタート位置
	Vec2 from{ 100, 100 };

	// ゴール位置
	Vec2 to{ 700, 500 };

	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		// 移動の割合 0.0～1.0
		const double t = Min(stopwatch.sF(), 1.0);

		// イージング関数を適用
		const double e = EaseInOutExpo(t);

		// スタート位置からゴール位置へ e の割合だけ進んだ位置
		const Vec2 pos = from.lerp(to, e);

		if (MouseL.down())
		{
			// スタート位置を現在の位置に
			from = pos;

			// ゴール位置をマウスカーソルの位置に
			to = Cursor::Pos();

			stopwatch.restart();
		}

		Circle{ pos, 40 }.draw(ColorF{ 0.25 });
		Circle{ to, 50 }.drawFrame(5, ColorF{ 0.25 });
	}
}
```
