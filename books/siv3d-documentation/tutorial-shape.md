---
title: "チュートリアル 02 | 図形を描く"
free: true
---

# 2. 図形を描く

この章では、色や図形を表現するクラスを学び、それらを使って画面に図形を描きます。

## 2.1 円を描く
画面に図形を描く方法を学びましょう。Siv3D では、図形オブジェクトを作成し、その `draw()` メンバ関数を呼んで描画を行います。円を描くときは `Circle` を作成し、その `.draw()` を呼びます。
![](/images/doc_v6/tutorial/2/1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中心座標 (400, 300), 半径 20 の円を描く
		Circle{ 400, 300, 20 }.draw();
	}
}
```


## 2.2 円の大きさを変える
`Circle()` の最後に指定するパラメータは円の半径です。この値を大きくすれば、描画される円も大きくなります。
![](/images/doc_v6/tutorial/2/2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中心座標 (400, 300), 半径 100 の円を描く
		Circle{ 400, 300, 100 }.draw();
	}
}
```


## 2.3 X 座標がマウスカーソルと連動する円を描く
円がマウスカーソルの座標に連動して動くようにしてみましょう。`Circle{}` の最初に指定するパラメータは円の中心の X 座標です。この値をマウスカーソルの X 座標にしてみます。
![](/images/doc_v6/tutorial/2/3.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
        // 中心座標 (マウスの X 座標, 300), 半径 100 の円を描く
		Circle{ Cursor::Pos().x, 300, 100 }.draw();
	}
}
```

前章で、`Print` を使って表示したメッセージはいつまでも画面に残りましたが、それは例外的なルールです。通常、`Print` 以外のすべての描画は `System::Update()` のたびに背景の色でリセットされます。


## 2.4 マウスカーソルと連動する円を描く
Siv3D で X 座標、Y 座標 2 つの値を受け取る関数は、多くの場合、1 つの `Point` 型、もしくは `Vec2` 型を受け取る別バージョンの関数（オーバーロード）を提供しているケースがよくあります。`Circle` も、「X 座標」「Y 座標」「半径」の 3 つの引数ではなく、「中心座標 (`Vec2` 型)」「半径」の 2 つの引数を受け取るオーバーロードがあります。これを使って円をマウスカーソルと連動させます。
![](/images/doc_v6/tutorial/2/4.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
        // 中心座標 (マウスの X 座標, マウスの Y 座標), 半径 100 の円を描く
		Circle{ Cursor::Pos(), 100 }.draw();
	}
}
```


## 2.5 色を付ける
図形に色を付けたいときは `draw()` 関数に色を渡します。色の指定の方法は大きく 4 通りあります。

| 色の表現               | 値の範囲                                                                                                  |
|--------------------|-------------------------------------------------------------------------------------------------------|
| Palette::色名        | [Web カラー](https://www.w3.org/wiki/CSS/Properties/color/keywords) の名前で色を指定  |
| ColorF{ r, g, b, a } | 0.0 - 1.0 の範囲で RGBA の各成分を指定 |
| Color{ r, g, b, a }  | 0 - 255 の整数の範囲で RGBA の各成分を指定 |
| HSV{ h, s, v, a }    | 色相 `h`, 彩度 `s`, 明度 `v` とアルファ値 `a` の各成分を指定。<br>h は 0.0 - 360.0 (370.0 は 10.0 と同じ). s, v, a は 0.0 - 1.0,の範囲 |

**Palette::色名** は、`Palette::Orange`, `Palette::Yellow` のように、RGB 値がわからなくても使えます。

**ColorF** は、Siv3D で最も使われる色の表現形式です。

**Color** は、`Image` 型の要素で、Siv3D で画像処理をするときに使われる形式です。

**HSV** は、赤っぽい、青っぽいなど色の種類を表す色相 (hue) と、色の鮮やかを表す彩度 (saturation), 色の明るさを表す明度 (value) の 3 要素を使った HSV 色空間で色を表現します。

`ColorF`, `Color`, `HSV` はいずれも **アルファ値** `a` を持ちます。アルファ値は「不透明度」を表し、最大値 (`ColorF`, `HSV` の場合 1.0, `Color` の場合 255) ではまったく透過しませんが、値を小さくするとそれに応じて背景を透過する半透明になり、0 になると完全に透明になります。

色の指定はプログラムでよく使われるので、次のような短い書き方も用意されています。例えば `ColorF{ 0.5 }` は `ColorF{ 0.5, 0.5, 0.5, 1.0 }` と同等です。

| 短い書き方           | 意味                         |
|-----------------|----------------------------|
| `ColorF{ r, g, b }` | `ColorF{ r, g, b, 1.0 }`       |
| `ColorF{ rgb, a }`  | `ColorF{ rgb, rgb, rgb, a }`   |
| `ColorF{ rgb }`     | `ColorF{ rgb, rgb, rgb, 1.0 }` |
| `Color{ r, g, b }`  | `Color{ r, g, b, 255 }`        |
| `Color{ rgb, a }`   | `Color{ rgb, rgb, rgb, a }`    |
| `Color{ rgb }`      | `Color{ rgb, rgb, rgb, 255 }`  |
| `HSV{ h, s, v }`    | `HSV{ h, s, v, 1.0 }`          |
| `HSV{ h, a }`       | `HSV{ h, 1.0, 1.0, a }`        |
| `HSV{ h }`          | `HSV{ h, 1.0, 1.0, 1.0 }`      |

色の付いたいくつかの円を描いてみましょう。`.draw()` に色を指定しなかった場合のデフォルトの色は `Palette::White` (`ColorF{ 1.0, 1.0, 1.0, 1.0 }`) です。
![](/images/doc_v6/tutorial/2/5.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 左から順に 7 つの円を描く
		Circle{ 100, 200, 40 }.draw();

		Circle{ 200, 200, 40 }.draw(Palette::Green);

		Circle{ 300, 200, 40 }.draw(Palette::Skyblue);

		Circle{ 400, 200, 40 }.draw(ColorF{ 1.0, 0.8, 0.0 });

		Circle{ 500, 200, 40 }.draw(Color{ 255, 127, 127 });

		Circle{ 600, 200, 40 }.draw(HSV{ 160.0, 1.0, 1.0 });

		Circle{ 700, 200, 40 }.draw(HSV{ 160.0, 0.75, 1.0 });

		// 半透明の円
		Circle{ Cursor::Pos(), 80 }.draw(ColorF{ 0.0, 0.5, 1.0, 0.8 });
	}
}
```


## 2.6 背景の色を変える
シーンの背景色を変えるには `Scene::SetBackground()` に色を渡します。新しい背景色は、それ以降の `System::Update()` で画面の描画内容をリセットするときから反映されます。背景色は一度設定すると、再度変更されるまで同じ設定が使われます。
![](/images/doc_v6/tutorial/2/6.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
    // 背景色を ColorF{ 0.3, 0.6, 1.0 } に設定
	Scene::SetBackground(ColorF{ 0.3, 0.6, 1.0 });

	while (System::Update())
	{
		Circle{ Cursor::Pos(), 80 }.draw();
	}
}
```


## 2.7 背景の色を時間の経過とともに変える
`Scene::Time()` はプログラムの経過時間（秒）を `double` 型の値で返します。これを用いて、時間に応じて背景色の色相を変化させてみましょう。
![](/images/doc_v6/tutorial/2/7.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
        // 色相 hue = 経過時間 (秒) * 60
		const double hue = (Scene::Time() * 60);

		Scene::SetBackground(HSV{ hue, 0.6, 1.0 });
	}
}
```


## 2.8 長方形を描く
長方形を描くときは `Rect` を作成して `.draw()` します。
![](/images/doc_v6/tutorial/2/8.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 座標 (20, 40) を左上の基準位置にして、幅 400, 高さ 100 の長方形を描く 
		Rect{ 20, 40, 400, 100 }.draw();

		// 座標 (100, 200) を左上の基準位置にして、幅が 80 の正方形を描く 
		Rect{ 100, 200, 80 }.draw(Palette::Orange);

		// 座標 (400, 300) を中心の基準位置にして、幅 80, 高さ 40 の長方形を描く
		Rect{ Arg::center(400, 300), 80, 40 }.draw(Palette::Pink);

		// マウスカーソルの座標を中心の基準位置にして、幅が 100 の正方形を描く 
		Rect{ Arg::center(Cursor::Pos()), 100 }.draw(ColorF{ 1.0, 0.0, 0.0, 0.5 });

		// 座標や大きさを浮動小数点数 (小数を含む数）で指定したい場合は RectF
		RectF{ 200.4, 450.3, 390.5, 122.5 }.draw(Palette::Skyblue);
	}
}
```

図形は `draw()` した順番に描画されます。このプログラムの、マウスカーソルに追従する赤い正方形と画面の下にある水色の大きな長方形を比べると、後者のほうがあとから描画されます。

`Rect` 型は左上の座標と幅、高さをそれぞれ `int32 x`, `int32 y`, `int32 w`, `int32 h` というメンバ変数で表します。整数ではなく浮動小数点数で扱いたい場合は、すべての要素が `double` 型である `RectF` を使います。


## 2.9 枠を描く
図形の枠だけを描きたい場合、`.draw()` の代わりに `.drawFrame()` を使います。`.drawFrame()` の第 1 引数には図形の内側方向への太さを、第 2 引数には外側方向への太さを指定します。図形の `.draw()` や `.drawFrame()` の戻り値はその図形自身なので、`rect.draw().drawFrame()` のように関数を続けて書くこともできます。
![](/images/doc_v6/tutorial/2/9.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 長方形の内側に 3px の枠を描く
		Rect{ 100, 100, 100, 30 }
			.drawFrame(3, 0);

		// 長方形の外側に 3px の枠を描く
		Rect{ 220, 100, 100, 30 }
			.drawFrame(0, 3);

		// 長方形と、その内側 3px と外側 3px に枠を描く
		Rect{ 200, 200, 400, 100 }
			.draw(Palette::White)
			.drawFrame(3, 3, Palette::Orange);

		// 円の内側 1px と外側 1px に枠を描く
		Circle{ Cursor::Pos(), 40 }
			.drawFrame(1, 1, Palette::Seagreen);
	}
}
```


## 2.10 線分を描く
始点と終点を指定して線分を描くときは `Line` を作成して `.draw()` します。`.draw()` のパラメータには描画する線分の太さと色を指定します。線分の両端を丸くしたり、点線にしたりするなど、スタイルの変更もできます。
![](/images/doc_v6/tutorial/2/10.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 座標 (100, 100) から (400, 150) まで太さ 4px の線分を描く
		Line{ 100, 100, 400, 150 }.draw(4, Palette::Yellow);

		// 座標 (400, 300) からマウスカーソルの座標まで太さ 10px の線分を描く
		Line{ 400, 300, Cursor::Pos() }.draw(10, Palette::Skyblue);

		// 通常の線
		Line{ 100, 400, 700, 400 }.draw(12, Palette::Orange);

		// 両端が丸い線
		Line{ 100, 450, 700, 450 }.draw(LineStyle::RoundCap, 12, Palette::Orange);

		// 四角いドットの線
		Line{ 100, 500, 700, 500 }.draw(LineStyle::SquareDot, 12, Palette::Orange);

		// 丸いドットの線
		Line{ 100, 550, 700, 550 }.draw(LineStyle::RoundDot, 12, Palette::Orange);
	}
}
```

