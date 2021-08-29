---
title: "チュートリアル 03 | 動きを作る"
free: true
---

# 3. 動きを作る
この章では、「動き」の表現に役立つ Siv3D の機能を学びます。

## 3.1 経過時間を使ったアニメーション

### Scene::Time()
`Scene::Time()` はプログラムが起動されてからの経過時間（秒）を `double` 型の値で返します。この値を使って簡単なアニメーションを作成できます。

### Scene::Center()
画面の中心座標を返す関数です。画面のサイズが 800x600 のときには `Point{ 400, 300 }` を返します。


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

### Scene::DeltaTime()
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

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 出現間隔（秒）
	constexpr double spawnTime = 1.0;

	// 蓄積された時間（秒）
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		accumulatedTime += Scene::DeltaTime();

        // 蓄積時間が出現間隔を超えたら
		if (spawnTime <= accumulatedTime)
		{
			accumulatedTime -= spawnTime;

			Print << U"Spawn!";
		}
	}
}
```

もし出現間隔が非常に短い（1 フレームの時間やそれよりも短い）場合、1 フレームで複数回出現させる必要が生じます。そのような状況には、`if` の代わりに `while (spawnTime <= accumulatedTime)` を使うことで対処できます。


## 3.6 ストップウォッチ
`Stopwatch` は、経過時間の計測やリセットを便利に行えるクラスです。

`Stopwatch` のコンストラクタ引数に `StartImmediately::Yes` を渡すと、作成と同時に計測を開始します。`Stopwatch::sF()` はその時点での経過時間（秒）を `double` 型で返します。`Stopwatch::restart()` すると、経過時間をリセットして再び 0 から計測を開始（リスタート）します。

```cpp


```



## 3.7 マウスのクリック
マウスの左ボタンがクリック（タッチディスプレイの場合は画面がタッチ）されたかを、`if (MouseL.down())` で調べられます。次のサンプルでは、画面上をマウスでクリックするたびに `Stopwatch` をリスタートします。



```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Vec2 center = Scene::Center();

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

		// シーンの中心の円の半径が、時間の経過に伴って大きくなる
		Circle{ center, t * 50 }.draw(ColorF{ 0.25 });
	}
}
```

## 3.8 Stopwatch の一時停止と再開

ストップウォッチが計測中かどうかは `if (Stopwatch::isRunning())` で調べられます。ストップウォッチの計測を一時停止するには `Stopwatch::pause()`, 一時停止を解除して計測を再開するには `Stopwatch::resume()` します。



```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Vec2 center = Scene::Center();

	// ストップウォッチ（作成と同時に計測開始）
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
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

		Circle{ center, 120 }.drawArc(t * 140_deg, 240_deg, 60, 0, ColorF{ 0.4 });

		Circle{ center, 180 }.drawArc(t * 90_deg, 160_deg, 60, 0, ColorF{ 0.6 });

		Circle{ center, 240 }.drawArc(t * 50_deg, 120_deg, 60, 0, ColorF{ 0.8 });
	}
}
```

## 3.9 周期的なアニメーション
Siv3D で周期的に移動・点滅・拡大縮小するようなアニメーションを作るときには、`Periodic::` 名前空間に用意されている関数群を使うと便利です。

| 周期関数 | 動き |
|--|--|
|`Square0_1`|![](/images/doc_v6/tutorial/3/9a.png)|
|`Triangle0_1`|![](/images/doc_v6/tutorial/3/9b.png)|
|`Sine0_1`|![](/images/doc_v6/tutorial/3/9c.png)|
|`Sawtooth0_1`|![](/images/doc_v6/tutorial/3/9d.png)|
|`Jump0_1`|![](/images/doc_v6/tutorial/3/9e.png)|



### Periodic::Square0_1()
指定した周期で 0.0 か 1.0 を交互に返す関数です。周期は `2s` (2 秒) や `0.5s` (0.5 秒) のように時間リテラルを使って記述します。周期の前半では 1.0 を、残りの半分では 0.0 を返します。


```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.25));

	while (System::Update())
	{
		// 2 秒周期（1 秒点灯、1 秒消灯）で明滅を繰り返す
		if (Periodic::Square0_1(2s))
		{
			Circle{ Scene::Center(), 200 }.draw();
		}
	}
}
```

### Periodic::Triangle0_1()
0.0 から一定の速度で値が大きくなって 1.0 に、そして一定の速度で小さくなって 0.0 に、という変化を指定した周期で繰り返す関数です。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/3/3-1.gif?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.25));

	while (System::Update())
	{
		// 2 秒周期で、一定速度での左右移動を繰り返す
		const double x = (50 + 700 * Periodic::Triangle0_1(2s));
		
		Circle{ x, 300, 50 }.draw();
	}
}
```

### Periodic::Sine0_1()
指定した周期で、0.0～1.0 の範囲で正弦波（サインカーブ）を描く数値の変化を返す関数です。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/3/3-2.gif?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.25));

	while (System::Update())
	{
		// 2 秒周期で、サインカーブの速度での左右移動を繰り返す
		const double x = (50 + 700 * Periodic::Sine0_1(2s));
		
		Circle{ x, 300, 50 }.draw();
	}
}
```

### Periodic::Sawtooth0_1()
指定した周期で、0.0 → 1.0 への変化を繰り返す関数です。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/3/3-3.gif?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.25));

	while (System::Update())
	{
		// 2 秒周期で、左 → 右への移動を繰り返す 
		const double x = (50 + 700 * Periodic::Sawtooth0_1(2s));
		
		Circle{ x, 300, 50 }.draw();
	}
}
```

### Periodic::Jump0_1()
指定した周期で、地面からジャンプしたときの速度のような数値変化を繰り返す関数です。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/3/3-4.gif?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.25));

	while (System::Update())
	{
		// 2 秒周期で、ジャンプのような移動を繰り返す 
		const double h = (500 * Periodic::Jump0_1(2s));
		
		Circle{ 400, (550 - h), 50 }.draw();
	}
}
```

## 3.4 トランジション

### Transition
値が少しずつ大きくなって最大値に到達する。そこから徐々に小さくなって最小値に戻る、という挙動をプログラムするときには `Transition` を使うと便利です。`Transition` のコンストラクタには、最小値から最大値に増加する所要時間と、最大値から最小値に減少する所要時間を設定します。あとは毎フレーム、`Transition::update()` に、増加の場合は `true` を、減少の場合は `false` を渡せば、設定された速度で値が変化します。`Transition::value()` で現在の値を取得できます。

### MouseL.pressed()
マウスの左ボタンが押されている（タッチディスプレイの場合は画面がタッチされている）かを、`if (MouseL.pressed())` で調べられます。次のサンプルでは、左ボタンが押されていると扇形が大きくなり、離されていると小さくなります。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/3/4-0.gif?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.25 });

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

		Circle{ Scene::Center(), 200 }.drawPie(0_deg, (360_deg * t));
	}
}
```

`MouseL.pressed()` は `bool` 型の値を返すので、上記のプログラムは次のように短く書けます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.25));

	// 2.0 秒かけて 0.0 から 1.0 になる速度で増加し、
	// 0.5 秒かけて 1.0 から 0.0 になる速度で減少するトランジション
	Transition transition(2.0s, 0.5s);

	while (System::Update())
	{
		// マウスの左ボタンが押されていたら増加、押されていなかったら減少
		transition.update(MouseL.pressed());

		const double t = transition.value();

		Circle{ Scene::Center(), 200 }.drawPie(0_deg, (360_deg * t));
	}
}
```


## 3.5 イージング

### Min, Max
`Min()` 関数は、与えられた引数の中の最小値を返します。`Max()` 関数は最大値を返します。

### 線形補間
あるベクトル A から別のベクトル B への線形補間は `A.lerp(B, t)` で計算できます。A と B の中間のベクトルは `A.lerp(B, 0.5)` で計算します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/3/5-0.gif?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.25 });

	constexpr Vec2 begin{ 100, 300 };
	constexpr Vec2 end{ 700, 300 };

	Stopwatch stopwatch;

	while (System::Update())
	{
		if (MouseL.down())
		{
			stopwatch.restart();
		}

		// 移動の割合 0.0～1.0
		const double t = Min(stopwatch.sF(), 1.0);

		// begin と end の線形補間
		const Vec2 pos = begin.lerp(end, t);

		Circle{ pos, 40 }.draw();
	}
}
```

### イージング
0.0 から 1.0 に一定の割合で値を増加させるだけでは単調な動きになってしまいます。はじめは少しずつ加速し、ゴールに近づくとゆっくりになるといったように、速度に変化を与えると、より洗練された視覚効果を実現できます。0.0 → 1.0 の単調増加を、特徴的なカーブに変換できる **イージング関数** を取り入れて、アニメーションの印象を改善しましょう。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/3/5-1.gif?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.25));

	constexpr Vec2 begin{ 100, 300 };
	constexpr Vec2 end{ 700, 300 };

	Stopwatch stopwatch;

	while (System::Update())
	{
		if (MouseL.down())
		{
			stopwatch.restart();
		}

		// 移動の割合 0.0～1.0
		const double t = Min(stopwatch.sF(), 1.0);

		// イージング関数を適用
		const double e = EaseInOutExpo(t);

		// begin と end の線形補間
		const Vec2 pos = begin.lerp(end, e);

		Circle{ pos, 40 }.draw();
	}
}
```

イージング関数は全部で約 30 種類用意されています。一覧は [Easing Functions Cheat Sheet](https://easings.net/) で確認できます。`EaseInOutExpo()` 以外にも、`EaseOutBounce()` や `EaseInOutBack()` など様々なイージング関数を試してみましょう。

