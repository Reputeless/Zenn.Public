---
title: "Siv3D の 2D 図形クラスを俯瞰する"
emoji: "⏹️"
type: "tech"
topics: ["siv3d"]
published: false
---

> この記事は [GameEngineDev Advent Calendar 2022](https://qiita.com/advent-calendar/2022/mygameengine) の参加記事です。

C++ フレームワーク Siv3D を使うと、複雑な視覚要素を短いコードで表現できます。とくに 2D 描画に注目してみると、2D 図形を表現するクラスとそのメンバ関数が豊富に用意されていることがわかります。本記事では、自作ゲームライブラリ・エンジンで図形クラスを設計するための参考資料として、Siv3D の 2D 図形クラスと、その機能の一部を紹介します。


## 二次元ベクトル
二次元平面上での位置 `(x, y)` を表現するクラスです。

```cpp
struct Point
{
	int32 x;
	int32 y;
};

using Size = Point;

struct Float2
{
	float x;
	float y;
};

struct Vec2
{
	double x;
	double y;
};

using SizeF = Vec2;
```

- [📄Point](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Point.hpp)
- [📄Float2 / Vec2](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Vector2D.hpp)

二次元ベクトルは大きさの表現にも使うことができますが、型を区別すると使い分けが面倒です。そこで `Size, SizeF` というエイリアスを提供しています。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/340b318c4eeb-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	constexpr Size SceneSize{ 640, 480 };

	Window::Resize(SceneSize);

	while (System::Update())
	{
		// Scene::Center() はシーンの中心座標を Point 型で返す
		// Point::asCircle(r) は、その座標を中心とする半径 r の Circle を作成する 
		Scene::Center().asCircle(100).draw();
	}
}
```
:::


## 円座標
円座標 `(r, Θ)` を表現するクラスです。`Vec2` 型に暗黙変換できます。

```cpp
struct Circular
{
	double r;
	double theta;
};

struct OffsetCircular
{
	Vec2 center;
	double r;
	double theta;
};
```
- [📄Circular](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Circular.hpp)
- [📄OffsetCircular](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/OffsetCircular.hpp)

`OffsetCircular` は、ある座標を中心として、その周囲に円状に何かを配置するときに便利です。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/64dcc953ea9f-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	Scene::SetBackground(Palette::Whitesmoke);

	while (System::Update())
	{
		for (int32 i = 0; i < 12; ++i)
		{
			// 画面の中心を中心とする半径 160 の円周上、30° ごとに円を描く
			const Vec2 pos = OffsetCircular{ Scene::Center(), 160, (i * 30_deg) };

			pos.asCircle(20).draw(HSV{ (i * 30) });
		}
	}
}
```
:::


## 線分
始点と終点で線分を表現するクラスです。

```cpp
struct Line
{
	Vec2 begin;
	Vec2 end;
};
```
- [📄Line](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Line.hpp)

線分にはいくつかのスタイルが用意されています。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/09c5e54334ed-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	while (System::Update())
	{
		// 通常の線
		Line{ 100, 200, 700, 200 }.draw(12, Palette::Orange);

		// 両端が丸い線
		Line{ 100, 250, 700, 250 }.draw(LineStyle::RoundCap, 12, Palette::Orange);

		// 四角いドットの線
		Line{ 100, 300, 700, 300 }.draw(LineStyle::SquareDot, 12, Palette::Orange);

		// 丸いドットの線
		Line{ 100, 350, 700, 350 }.draw(LineStyle::RoundDot, 12, Palette::Orange);
	}
}
```
:::

`Line::drawArrow()` や `Line::drawDoubleHeadedArrow()` で矢印を描くこともできます。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/5533397e10e5-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	while (System::Update())
	{
		// 線の幅 10px, 三角の幅 20px, 高さ 20px の線分を描く
		Line{ 50, 200, 200, 250 }.draw(5, Palette::Skyblue);

		// 線の幅 10px, 三角の幅 40px, 高さ 80px の単方向矢印を描く
		Line{ 350, 450, 450, 100 }
			.drawArrow(10, Vec2{ 40, 80 }, Palette::Orange);

		// 線の幅 8px, 三角の幅 30px, 高さ 30px の両方向矢印を描く
		Line{ 600, 100, 700, 400 }
			.drawDoubleHeadedArrow(8, Vec2{ 30, 30 }, Palette::Limegreen);
	}
}
```
:::


## 円
中心座標と半径で円を表現するクラスです。

```cpp
struct Circle
{
	union
	{
		Vec2 center;

		struct
		{
			double x;
			double y;
		};
	};

	double r;
};
```
- [📄Circle](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Circle.hpp)

`Circle::drawPie()` で扇形を、`Circle::drawArc()` で円弧を描くことができます。

:::details サンプル
```cpp

```
:::


## 長方形
左上の座標と幅、高さで長方形を表現するクラスです。

```cpp
struct Rect
{
	union
	{
		Point pos;

		struct
		{
			int32 x;
			int32 y;
		};
	};

	union
	{
		Size size;

		struct
		{
			int32 w;
			int32 h;
		};
	};
};

struct RectF
{
	union
	{
		Vec2 pos;

		struct
		{
			double x;
			double y;
		};
	};

	union
	{
		SizeF size;

		struct
		{
			double w;
			double h;
		};
	};
};
```
- [📄Rect](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Rect.hpp)
- [📄RectF](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/RectF.hpp)

:::details サンプル
```cpp

```
:::


## 三角形
3 つの座標で三角形を表現するクラスです。

```cpp
struct Triangle
{
	Vec2 p0;
	Vec2 p1;
	Vec2 p2;
};
```
- [📄Triangle](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Triangle.hpp)

:::details サンプル
```cpp

```
:::


## 四角形
4 つの座標で三角形を表現するクラスです。三角形分割の計算コスト節約のため、凹の角を持つことができない制約があります。

```cpp
struct Quad
{
	Vec2 p0;
	Vec2 p1;
	Vec2 p2;
	Vec2 p3;
};
```
- [📄Quad](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Quad.hpp)


:::details サンプル
```cpp

```
:::


## 楕円
中心と x 軸、y 軸の径で楕円を表現するクラスです。

```cpp
struct Ellipse
{
	union
	{
		Vec2 center;

		struct
		{
			double x;
			double y;
		};
	};

	union
	{
		Vec2 axes;

		struct
		{
			double a;
			double b;
		};
	};
};
```
- [📄Ellipse](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Ellipse.hpp)


## 角丸長方形
長方形と角の r で角丸長方形を表現するクラスです。

```cpp
struct RoundRect
{
	union
	{
		RectF rect;

		struct
		{
			double x;
			double y;
			double w;
			double h;
		};
	};

	double r;
};
```
- [📄RoundRect](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Ellipse.hpp)


:::details サンプル
```cpp

```
:::


## 多角形
外周を表現する頂点配列と、穴を表現する頂点配列の配列で、穴を持てる多角形を表現するクラスです。

```cpp
class Polygon
{
	// ...　実装略
};
```
- [📄Polygon](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Polygon.hpp)


:::details サンプル
```cpp

```
:::


## 特別な多角形


- [📄Shape2D](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Shape2D.hpp)

:::details サンプル
```cpp

```
:::


## 多角形の集合
`Polygon` の集合を表現するクラスです。個々の多角形は互いに重ならないことが期待されます。

```cpp
class MultiPolygon
{
	Array<Polygon> m_data;
};
```
- [📄MultiPolygon](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/MultiPolygon.hpp)


:::details サンプル
```cpp

```
:::


## ベジェ曲線
複数の制御点によりなめらかな曲線を表現するクラスです。

```cpp
struct Bezier2
{
	Vec2 p0;	
	Vec2 p1;
	Vec2 p2;
};

struct Bezier3
{
	Vec2 p0;
	Vec2 p1;
	Vec2 p2;
	Vec2 p3;
};
```
- [📄Bezier2](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Bezier2.hpp)
- [📄Bezier3](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Bezier3.hpp)


:::details サンプル
```cpp

```
:::


## 連続する線分
複数の点を先頭から順につないでできる連続線分を表現するクラスです。

```cpp
class LineString
{
	Array<Vec2> m_data;
};
```
- [📄LineString](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/LineString.hpp)


:::details サンプル
```cpp

```
:::

## スプライン曲線
指定した複数の点を必ず通過する Catmull-Rom スプライン曲線を表現するクラスです。

```cpp
class Spline2D
{
	// ...　実装略
};
```
- [📄Spline2D](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Spline2D.hpp)

:::details サンプル
```cpp

```
:::


## それ以外のアイデア
Siv3D では実装されていませんが、次のような図形クラスを定義することもできるでしょう。

#### 角の r を個別に設定できる角丸長方形
Siv3D の `RoundRect` ではすべての角が同じ r を共有するため、GUI などで使われる、1 つの辺に関する角のみが丸い長方形を表現できません。代わりに `Rect::rounded(double, double, double, double)` の戻り値である `Polygon` 型を用いる必要があります。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/3465915ac567-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	constexpr Rect rect{ 200, 100, 400, 60 };

	while (System::Update())
	{
		// 左上、右上をそれぞれ r = 30px で丸めた Polygon を返す
		rect.rounded(30, 30, 0, 0).draw(Palette::Skyblue);
	}
}
```
:::


#### 傾きのある楕円
Siv3D の `Ellipse` では傾きのある楕円を表現できないため、`Mat3x2` によるアフィン変換と組み合わせて描画します。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/cbdffafadd97-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	constexpr Ellipse ellipse{ 400, 300, 200, 100 };

	while (System::Update())
	{
		// 楕円の中心を回転軸として時計回りに 30° 回転する座標変換を適用
		const Transformer2D tr{ Mat3x2::Rotate(30_deg, ellipse.center) };

		ellipse.draw(Palette::Seagreen);
	}
}
```
:::

#### 直線
長方形や多角形を直線によって分割したい場合、直線を表現できるクラスがあると便利かもしれません。Siv3D の場合は `Line` の両端を無限に伸ばすと見なすことで直線を表現できます。

#### 連続するベジェ曲線
（参考）Siv3D Advent Calendar 2022, 19 日目の記事

https://twitter.com/segawachobbies/status/1604496568664862720


## おわりに
ライブラリやエンジンにおける 2D 図形の表現手法を充実させることで、開発者が画像データに頼らずに複雑な視覚表現をゲーム内に実装できます。副次的な効果として、アセットサイズの削減やメモリ消費量の削減にもつながります。使いやすい図形クラスのアイデアができたら、ぜひ共有してください。
