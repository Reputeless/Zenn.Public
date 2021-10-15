---
title: "Tutorial 02 | Drawing Shapes"
free: true
---

# 2. Drawing Shapes

この章では、色や図形を表現するクラスを学び、それらを使ってシーンに図形を描きます。

## 2.1 円を描く
シーンに図形を描く方法を学びましょう。Siv3D では、図形オブジェクトを作成し、その `draw()` メンバ関数を呼んで描画を行います。円を描くときは `Circle` を作成し、その `.draw()` を呼びます。
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
`Circle{}` の最後に指定するパラメータは円の半径です。この値を大きくすれば、描画される円も大きくなります。
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


## 2.11 三角形を描く
三角形を描くには、`Triangle` を作成して `.draw()` します。`Triangle` を作成するには次の 2 つの方法があります
- 3 つの頂点座標を時計回りに指定する
- 正三角形の重心座標と辺の長さ、回転角度を指定する

Siv3D における角度は、`2π = 360°` のラジアン角で表現します。`Math::ToRadians()` 関数で度数法からラジアン角へ変換できるほか、`_deg` サフィックスを使うことでリテラルで記述することもできます。

X 座標と Y 座標の組は `Point` 型や `Vec2` 型で表現できます。`Point` 型は各成分が `int32` 型で、`Vec2` 型は `double` 型です。
![](/images/doc_v6/tutorial/2/11.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 座標 (100, 100), (400, 300), (100, 300) で構成される三角形を描く
		Triangle{ 100, 100, 400, 300, 100, 300 }.draw();

		// 座標 (300, 100) を重心とする、1 辺が 80px の三角形を描く
		Triangle{ 300, 100, 80 }.draw(Palette::Orange);

		// 時計回りに 15° 回転させて描く
		Triangle{ 400, 100, 80, 15_deg }.draw(Palette::Seagreen);

		// 時計回りに 30° 回転させて描く
		Triangle{ 500, 100, 80, 30_deg }.draw(Palette::Pink);

		// 3 つの頂点座標を Point や Vec2 型で指定
		Triangle{ Cursor::Pos(), Vec2{ 700, 500 }, Vec2{ 100, 500 } }.draw(Palette::Skyblue);
	}
}
```


## 2.12 凸な四角形を描く
`Rect` や `RectF` では、各辺が X 軸、Y 軸に平行な長方形しか定義できませんでしたが、`Quad` を使うと 4 つの頂点座標を時計回りに指定して四角形を定義できます。ただし、`Quad` で定義される四角形は 180° 以上の内角を含まない形状（すべての角が凸）である必要があります。凹角を含む四角形を定義したい場合はのちにで出てくる `Polygon` 型を使います。
![](/images/doc_v6/tutorial/2/12a.png)

![](/images/doc_v6/tutorial/2/12b.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 4 つの頂点座標を指定して四角形を描く
		Quad{ Vec2{ 100, 100 }, Vec2{ 150, 100 }, Vec2{ 300, 300 }, Vec2{ 100, 300 } }.draw();

		Quad{ Vec2{ 300, 400 }, Vec2{ 500, 100 }, Vec2{ 600, 200 }, Vec2{ 500, 500 } }.draw(Palette::Skyblue);
	}
}
```

`Rect` や `RectF` を作成し、`.rotated()` または `.rotatedAt()` を使うと、長方形を回転させて `Quad` を作成できます。その `Quad` を `.draw()` する一連の操作を次のように 1 行で書けます。`Rect::pos` は `Rect` の左上の座標を `Point` 型で、`RectF::pos` は `RectF` の左上の座標を `Vec2` 型で表すメンバ変数です。
![](/images/doc_v6/tutorial/2/12c.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	constexpr Rect rect{ 150, 200, 400, 100 };

	while (System::Update())
	{
		rect.draw();

		// 時計回りに 45° 回転した長方形
		rect.rotated(45_deg).draw(Palette::Orange);

		// 長方形の左上の座標を回転の軸として時計回りに 60° 回転した長方形
		rect.rotatedAt(rect.pos, 60_deg).draw(Palette::Skyblue);
	}
}
```

`Rect` や `RectF` を作成し、`.shearedX()` または `.shearedY()` を使うと、長方形の辺を X 軸または Y 軸に沿ってスライドさせた平行四辺形を `Quad` 型として作成できます。
![](/images/doc_v6/tutorial/2/12d.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 長方形の辺を X 軸方向に 30px ずつスライドさせた平行四辺形
		Rect{ 100, 50, 200, 100 }.drawFrame(1, 0)
			.shearedX(30).draw(Palette::Skyblue);

		// 長方形の辺を Y 軸方向に -50px ずつスライドさせた平行四辺形
		Rect{ 400, 150, 300, 200 }.drawFrame(1, 0)
			.shearedY(-50).draw(Palette::Orange);
	}
}
```


## 2.13 楕円を描く
楕円を描くときは `Ellipse` を作成して `.draw()` します。中心の座標と X 軸方向の半径、Y 軸方向の半径を指定して `Ellipse` を作成します。
![](/images/doc_v6/tutorial/2/13.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中心 (300, 200), X 軸方向の半径 200, Y 軸方向の半径　100 の楕円
		Ellipse{ 300, 200, 200, 100 }.draw(Palette::Skyblue);

		// 中心 (600, 400), X 軸方向の半径 50, Y 軸方向の半径　150 の楕円
		Ellipse{ 600, 400, 50, 150 }.draw(Palette::Orange);
	}
}
```


## 2.14 角丸長方形を描く
角が丸い長方形を描くには、`RoundRect` を作成して `.draw()` します。`RectF` と同じパラメータに加えて、最後に角の曲線の半径を指定します。`Rect` や `RectF` の `.rounded()` メンバ関数を使って、`Rect` や `RectF` から `RoundRect` を作成することもできます。
![](/images/doc_v6/tutorial/2/14.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	constexpr Rect rect{ 100, 350, 500, 200 };

	while (System::Update())
	{
		// RectF(100, 100, 200, 100) の角を 10px 丸めた角丸長方形
		RoundRect{ 100, 100, 200, 100, 10 }.draw();

		// RectF(Arg::center(400, 300), 200, 80) の角を 5px 丸めた角丸長方形
		RoundRect{ Arg::center(400, 300), 200, 80, 5 }.draw(Palette::Skyblue);

		// 長方形 rect の角を 40px 丸めた角丸長方形
		rect.rounded(40).draw(Palette::Orange);
	}
}
```


## 2.15 多角形を描く
複雑な図形を簡単に作成できるいくつかの関数が用意されています。これらの関数の戻り値である `Shape2D` 型のオブジェクトを `.draw()`, `.drawFrame()` することで図形を描けます。関数のうち、引数に `double angle` をとるものは、時計回りの回転の角度を指定できます。

| 関数名                  | 形状       | 引数                                                                                    |
|----------------------|----------|---------------------------------------------------------------------------------------------|
| Shape2D::Cross       | ✖ マーク    | `double r, double width, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`          |
| Shape2D::Plus        | ＋マーク     | `double r, double width, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`          |
| Shape2D::Pentagon    | 正五角形     | `double r, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`                     |
| Shape2D::Hexagon     | 正六角形     | `double r, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`                         |
| Shape2D::Ngon        | 正 N 角形   | `uint32 n, double r, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`                |
| Shape2D::Star        | 五芒星      | `double r, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`                             |
| Shape2D::Nstar       | 星        | `uint32 n, double rOuter, double rInner, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0` |
| Shape2D::Arrow       | 矢印       | `const Vec2& from, const Vec2& to, double width, const Vec2& headSize`       |
| Shape2D::Arrow       | 矢印       | `const Line& line, double width, const Vec2& headSize`                        |
| Shape2D::DoubleHeadedArrow | 両方向矢印 | `const Vec2& from, const Vec2& to, double width, const Vec2& headSize`  |
| Shape2D::DoubleHeadedArrow | 両方向矢印 | `const Line& line, double width, const Vec2& headSize`  |
| Shape2D::Rhombus     | ひし形      | `double w, double h, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`    |
| Shape2D::RectBalloon | 長方形の吹き出し | `const RectF& rect, const Vec2& target, double pointingRootRatio = 0.5`   |
| Shape2D::Stairs      | 階段形      | `const Vec2& base, double w, double h, uint32 steps, bool upStairs = true`  |
| Shape2D::Heart      | ハート形   | `double r, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`  |
| Shape2D::Squircle      | 四角と円の中間形 | `double r, const Vec2& center, uint32 quality`   |

![](/images/doc_v6/tutorial/2/15.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウおよびシーンを 1000x600 にリサイズ
	Window::Resize(1000, 600);

	while (System::Update())
	{
		Shape2D::Cross(80, 10, Vec2{ 100, 100 }).draw(Palette::Skyblue);

		Shape2D::Plus(80, 10, Vec2{ 300, 100 }).draw(Palette::Skyblue);

		Shape2D::Pentagon(80, Vec2{ 500, 100 }).draw(Palette::Skyblue);

		Shape2D::Hexagon(80, Vec2{ 700, 100 }).draw(Palette::Skyblue);

		// 30° 回転させる
		Shape2D::Hexagon(80, Vec2{ 900, 100 }, 30_deg).draw(Palette::Skyblue);


		// 正十角形
		Shape2D::Ngon(10, 80, Vec2{ 100, 300 }).draw(Palette::Skyblue);

		Shape2D::Star(80, Vec2{ 300, 300 }).draw(Palette::Skyblue);

		// rOuter は外周の半径、rInner は内周の半径
		Shape2D::NStar(10, 80, 60, Vec2{ 500, 300 }).draw(Palette::Skyblue);

		// headSize は三角形の幅と高さ
		Shape2D::Arrow(Line{ 640, 340, 760, 260 }, 20, Vec2{ 40, 30 }).draw(Palette::Skyblue);

		Shape2D::DoubleHeadedArrow(Line{ 840, 340, 960, 260 }, 20, Vec2{ 40, 30 }).draw(Palette::Skyblue);


		Shape2D::Rhombus(160, 120, Vec2{ 100, 500 }).draw(Palette::Skyblue);

		// 吹き出しの長方形と、三角形の頂点の置を指定。三角形のサイズは pointingRootRatio で決まる
		Shape2D::RectBalloon(RectF{ 220, 420, 160, 120 }, Vec2{ 220, 580 }).draw(Palette::Skyblue);

		// base には階段の最も高い段の底の端の座標を指定。steps は段数、upStairs を false にすると下りの階段に
		Shape2D::Stairs(Vec2{ 560, 560 }, 120, 120, 4).draw(Palette::Skyblue);

		Shape2D::Heart(80, Vec2{ 700, 500 }).draw(Palette::Skyblue);

		// 第 3 引数は角の丸の分割品質
		Shape2D::Squircle(60, Vec2{ 900, 500 }, 64).draw(Palette::Skyblue);
	}
}
```


## 2.16 自由に多角形を描く
`Shape2D` では表現できない多角形を描くには `Polygon` を作成して `.draw()` します。`Polygon` オブジェクトの作成には、メモリの確保や三角形分割の計算に少しだけ実行時コストがかかるため、ループの内側で作成するのは避けるべきです。`Polygon` を作成するときは、各頂点の座標を時計回りに指定します。
![](/images/doc_v6/tutorial/2/16.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Polygon polygon
	{
		Vec2{ 400, 100 }, Vec2{ 600, 300 }, Vec2{ 500, 500 }, Vec2{ 400, 400 }, Vec2{ 300, 500 }, Vec2{ 200, 300 }
	};

	while (System::Update())
	{
		polygon.draw(Palette::Skyblue);
	}
}
```


## 2.17 穴の開いた多角形を描く
穴の開いた `Polygon` を作るには、外周の時計回りの頂点座標リスト (`Array<Vec2>` 型) と、穴の形状の「反時計回り」の頂点座標リストの配列 (`Array<Array<Vec2>>` 型) から `Polygon` を作成します。
![](/images/doc_v6/tutorial/2/17.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Polygon polygon(
		{ Vec2{ 400, 100 }, Vec2{ 600, 300 }, Vec2{ 500, 500 }, Vec2{ 400, 400 }, Vec2{ 300, 500 }, Vec2{ 200, 300 } },
		{ { Vec2{ 450, 250 }, Vec2{ 350, 250 }, Vec2{ 350, 350 }, Vec2{ 450, 350 } } }
	);

	while (System::Update())
	{
		polygon.draw(Palette::Skyblue);
	}
}
```

`Polygon` よりも少ない実行時コストで図形を描きたい場合は、`Shape2D` や `Buffer2D` クラスの低レイヤ操作を使います。`Shape2D` では、頂点配列のほかにインデックス配列を自前で用意する必要があり、`Buffer2D` ではさらにテクスチャをマッピングするための UV 座標も必要になり、プログラムが複雑になります。今回のチュートリアルでは扱いません。


## 2.18 連続した線分を描く
連続した線分を描くには、`Vec2` 型の頂点の配列から `LineString` を作成して `.draw()` します。`.drawClosed()` では終点と始点を結んだ線も描画されます。
![](/images/doc_v6/tutorial/2/18.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const LineString lineA
	{
		Vec2{ 100, 60 }, Vec2{ 400, 140 },
		Vec2{ 100, 220 }, Vec2{ 400, 300 },
		Vec2{ 100, 380 }, Vec2{ 400, 460 },
		Vec2{ 100, 540 }
	};

	const LineString lineB
	{
		Vec2{ 500, 100 }, Vec2{ 700, 200 },
		Vec2{ 600, 500 },
	};

	while (System::Update())
	{
		// 太さ 8px で描く
		lineA.draw(8, Palette::Skyblue);

		// 太さ 4px で描く（終点から始点も結ぶ）
		lineB.drawClosed(4, Palette::Orange);
	}
}
```


## 2.19 Catmull-Rom スプライン曲線を描く
指定した通過点を必ず通る Catmull-Rom スプライン曲線を描くには、 `Spline2D` を作成して `.draw()` します。`Spline2D` は `Vec2` の配列か `LineString` から作成できます。コンストラクタの第 2 引数に `Close::Ring` を指定することで、終点と始点がつながっているスプライン曲線を作成できます。

サンプルプログラムでは示していませんが、`.draw()` には曲線計算時の品質（分割数）を指定する引数も用意されていて、デフォルトでは `24` になっています。
![](/images/doc_v6/tutorial/2/19.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Spline2D splineA
	{ {
		Vec2{ 100, 60 }, Vec2{ 400, 140 },
		Vec2{ 100, 220 }, Vec2{ 400, 300 },
		Vec2{ 100, 380 }, Vec2{ 400, 460 },
		Vec2{ 100, 540 }
	} };

	// CloseRing::Yes -> 終点から始点も結ぶ
	const Spline2D splineB
	{ {
		Vec2{ 500, 100 }, Vec2{ 700, 200 },
		Vec2{ 600, 500 },
	}, CloseRing::Yes };

	while (System::Update())
	{
		// 太さ 8px で描く
		splineA.draw(8, Palette::Skyblue);

		// 太さ 4px で描く
		splineB.draw(4, Palette::Orange);
	}
}
```


## 2.20 ベジェ曲線を描く
2 次ベジェ曲線を描きたいときは `Bezier2`, 3 次ベジェ曲線を描きたいときは `Bezier3` を作成して `.draw()` します。

サンプルプログラムでは示していませんが、`.draw()` には曲線計算時の品質（分割数）を指定する引数も用意されていて、デフォルトでは `24` になっています。
![](/images/doc_v6/tutorial/2/20.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 2 次ベジェ曲線
		Bezier2{ Vec2{ 100, 400 }, Vec2{ 100, 250 }, Vec2{ 300, 100 } }
			.draw(4, Palette::Skyblue);

		// 3 次ベジェ曲線
		Bezier3{ Vec2{ 300, 400 }, Vec2{ 400, 400 }, Vec2{ 400, 100 }, Vec2{ 500, 100 }}
			.draw(4, Palette::Orange);
	}
}
```


## 2.21 矢印を描く
`Line` には単方向の矢印を描く `.drawArrow()` と、両方向の矢印を描く `.drawDoubleHeadedArrow()` メンバ関数があります。いずれも第 1 引数には線の幅、第 2 引数には三角形の幅と高さを指定します。単方向矢印は、`Line` の始点から終点方向を向きます。
![](/images/doc_v6/tutorial/2/21.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 線の幅 3px, 三角の幅 20px, 高さ 20px の単方向矢印を描く
		Line{ 50, 200, 200, 250 }
			.drawArrow(3, Vec2{ 20, 20 }, Palette::Skyblue);

		// 線の幅 10px, 三角の幅 40px, 高さ 80px の単方向矢印を描く
		Line{ 350, 450, 450, 100 }
			.drawArrow(10, Vec2{ 40, 80 }, Palette::Orange);

		// 線の幅 8px, 三角の幅 30px, 高さ 30px の両方向矢印を描く
		Line{ 600, 100, 700, 400 }
			.drawDoubleHeadedArrow(8, Vec2{ 30, 30 }, Palette::Limegreen);
	}
}
```


## 2.22 扇形を描く
扇形を描くには、扇形のもとになる円 `Circle` を作成し、`.drawPie()` の引数に、12 時の方向を 0° とした時計回りの開始角度と、扇の角の大きさを指定します。`.drawPie()` が元の図形を返すことを利用して、`drawPie().drawPie()` のようにつなげたコードを書くこともできます。
![](/images/doc_v6/tutorial/2/22.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 300, 300, 200 }
			.drawPie(270_deg, 30_deg);

		Circle{ 500, 300, 200 }
			.drawPie(0_deg, 120_deg, Palette::Skyblue)
			.drawPie(120_deg, 70_deg, Palette::Orange);
	}
}
```


## 2.23 円弧を描く
円弧を描くには、円弧のもとになる円 `Circle` を作成し、`.drawArc()` の引数に、12 時の方向を 0° とした時計回りの開始角度と、扇の角の大きさ、弧の内側方向の太さ、外側方向の太さを指定します。
![](/images/doc_v6/tutorial/2/23.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 300, 300, 200 }
			.drawArc(270_deg, 30_deg, 40, 0);

		Circle{ 500, 300, 200 }		
			.drawArc(0_deg, 120_deg, 80, 0, Palette::Skyblue)
			.drawArc(120_deg, 70_deg, 0, 20, Palette::Orange);
	}
}
```


## 2.24 図形の操作
基準になる図形から、少しだけ変化させた形状を描きたいときに便利な機能を紹介します。

多くの図形クラスが `.movedBy()` メンバ関数を持ち、自身の座標を指定したベクトルで平行移動した図形を作成して返します。また、`Rect` や `Circle`, `Line` など一部の図形クラスは `.stretched()` メンバ関数を持ち、自身の幅や高さを変更した図形を作成して返します。
![](/images/doc_v6/tutorial/2/24a.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	constexpr Circle circle{ 100, 100, 60 };

	constexpr Rect rect{ 400, 300, 200 };

	while (System::Update())
	{
		circle.draw();

		// (200, 0) の方向に平行移動
		circle.movedBy(200, 0).draw(Palette::Skyblue);

		// (0, 200) の方向に平行移動
		circle.movedBy(0, 200).draw(Palette::Orange);


		rect.drawFrame(2, 2);

		// 上下左右を 10px 縮小
		rect.stretched(-10).drawFrame(2, 2, Palette::Skyblue);

		// 左右を 40px 拡大、上下を 20px 縮小
		rect.stretched(40, -20).drawFrame(2, 2, Palette::Orange);
	}
}
```

`Polygon` は自身を拡大縮小した新しい `Polygon` を返す `.scaled()` や、回転した `Polygon` を返す `.rotated()`, `.rotatedAt()` などのメンバ関数を持ちます。また、`Shape2D` は `Polygon` に変換可能です。
![](/images/doc_v6/tutorial/2/24b.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Polygon star = Shape2D::Star(150, Vec2{ 0, 0 });

	while (System::Update())
	{
		star.scaled(1.2).movedBy(200, 200).draw(ColorF{ 0.6 });

		star.movedBy(200, 200).draw(ColorF{ 0.8 });

		star.scaled(0.8).movedBy(200, 200).draw(ColorF{ 1.0 });


		star.rotated(-30_deg).movedBy(600, 400).draw(ColorF{ 0.6 });

		star.movedBy(600, 400).draw(ColorF{ 0.8 });

		star.rotated(30_deg).movedBy(600, 400).draw(ColorF{ 1.0 });
	}
}
```


## 2.25 円 / 長方形 / 角丸長方形の影
`Rect`, `RectF`, `Circle`, `RoundRect` は、影を描画する `.drawShadow()` メンバ関数を持っています。第 1 引数で影の位置のオフセット、第 2 引数でぼかしの大きさ、第 3 引数で影の大きさのオフセット、第 4 引数で影の色を指定できます。影は図形で隠れて見えない部分も塗りつぶされて描かれるため、影を描いたあとに上から図形を描く必要があります。
![](/images/doc_v6/tutorial/2/25.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		Rect{ 100, 50, 150, 200 }
			.drawShadow(Vec2{ 2, 2 }, 8, 1)
			.draw();

		Rect{ 300, 50, 150, 200 }
			.drawShadow(Vec2{ 4, 4 }, 16, 2)
			.draw();

		Rect{ 500, 50, 150, 200 }
			.drawShadow(Vec2{ 6, 6 }, 24, 3)
			.draw();

		Circle{ 100, 400, 50 }
			.drawShadow(Vec2{ 0, 3 }, 8, 2)
			.draw();

		Circle{ 300, 400, 50 }
			.drawShadow(Vec2{ 3, 0 }, 8, 2)
			.draw();

		Circle{ 500, 400, 50 }
			.drawShadow(Vec2{ 0, -3 }, 8, 2)
			.draw();

		Circle{ 700, 400, 50 }
			.drawShadow(Vec2{ -3, 0 }, 8, 2)
			.draw();
	}
}
```

これら以外の形状の影を作りたい場合は [サンプル/図形や絵文字に影を付ける](https://zenn.dev/reputeless/books/siv3d-documentation/viewer/sample-visual#%E5%9B%B3%E5%BD%A2%E3%82%84%E7%B5%B5%E6%96%87%E5%AD%97%E3%81%AB%E5%BD%B1%E3%82%92%E4%BB%98%E3%81%91%E3%82%8B) が参考になります。

## 2.26 グラデーション
`Line` や `Triangle`, `Rect`, `RectF`, `Quad` には、頂点ごとに色を指定し、塗りつぶしの色をグラデーションでにするオプションがあります。
![](/images/doc_v6/tutorial/2/26.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Line{ 100, 100, 500, 150 }
			.draw(6, Palette::Yellow, Palette::Red);

		Triangle{ 200, 200, 100 }
			.draw(HSV{ 0 }, HSV{ 120 }, HSV{ 240 });

		// 左から右へのグラデーション
		Rect{ 400, 200, 200, 100 }
			.draw(Arg::left = Palette::Skyblue, Arg::right = Palette::Blue);

		// 上から下へのグラデーション
		Rect{ 200, 400, 400, 100 }
			.draw(Arg::top = ColorF{ 1.0, 1.0 }, Arg::bottom = ColorF{ 1.0, 0.0 });
	}
}
```

