---
title: "Siv3D の 2D 図形クラスを俯瞰する"
emoji: "⏹️"
type: "tech"
topics: ["siv3d"]
published: false
---

> この記事は [GameEngineDev Advent Calendar 2022](https://qiita.com/advent-calendar/2022/mygameengine) の参加記事です。

C++ フレームワーク Siv3D では、複雑な視覚要素を短いコードで表現できます。とくに 2D 描画に注目すると、2D 図形を表現するクラスとそのメンバ関数が豊富に用意されていることがわかります。本記事では、自作ゲームライブラリ・エンジンで図形クラスを設計するための参考資料として、Siv3D の 2D 図形クラスと、その機能の一部を紹介します。


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

- [Point](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Point.hpp)
- [Float2 / Vec2](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Vector2D.hpp)

二次元ベクトルは大きさの表現にも使うことができますが、型を区別すると使い分けが面倒です。そこで `Size, SizeF` というエイリアスを提供しています。

:::details サンプル
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
円座標 `(r, Θ)` を表現するクラスです。

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
- [Circular](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Circular.hpp)
- [OffsetCircular](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/OffsetCircular.hpp)

`OffsetCircular` は、ある座標を中心として、その周囲に円状に何かを配置するときに便利です。

:::details サンプル
```cpp

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
- [Line](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Line.hpp)

`Line::drawArrow()` で矢印を描くこともできます。

```cpp

```


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
- [Circle](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Circle.hpp)

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
- [Rect](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Rect.hpp)
- [RectF](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/RectF.hpp)

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
- [Triangle](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Triangle.hpp)

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
- [Quad](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Quad.hpp)

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
- [Ellipse](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Ellipse.hpp)

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
- [RoundRect](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Ellipse.hpp)

## 多角形
外周を表現する頂点配列と、穴を表現する頂点配列の配列で、穴を持てる多角形を表現するクラスです。

```cpp
class Polygon
{
	// ...　実装略
};
```
- [Polygon](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Polygon.hpp)


## 多角形の集合
`Polygon` の集合を表現するクラスです。個々の多角形は互いに重ならないことが期待されます。

```cpp
class MultiPolygon
{
	Array<Polygon> m_data;
};
```
- [MultiPolygon](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/MultiPolygon.hpp)


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
- [Bezier2](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Bezier2.hpp)
- [Bezier3](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Bezier3.hpp)


## 連続する線分
複数の点を先頭から順につないでできる連続線分を表現するクラスです。

```cpp
class LineString
{
	Array<Vec2> m_data;
};
```
- [LineString](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/LineString.hpp)


## スプライン曲線
指定した複数の点を必ず通過する Catmull-Rom スプライン曲線を表現するクラスです。

```cpp
class Spline2D
{
	// ...　実装略
};
```
- [Spline2D](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Spline2D.hpp)


## それ以外のアイデア
Siv3D では実装されていませんが、次のような図形クラスを定義することもできるでしょう。

### 角の r を個別に設定できる角丸長方形
Siv3D の `RoundRect` ではすべての角が同じ r を共有するため、GUI などで使われる、1 つの辺に関する角のみが丸い長方形を表現できません。代わりに `Rect::rounded(double, double, double, double)` の戻り値である `Polygon` 型を用いる必要があります。

### 傾きのある楕円
Siv3D の `Ellipse` では傾きのある楕円を表現できないため、`Mat3x2` によるアフィン変換と組み合わせて描画します。

### 直線
長方形や多角形を直線によって分割したい場合、直線を表現できるクラスがあると便利かもしれません。Siv3D の場合は `Line` の両端を無限に伸ばすと見なすことで直線を表現できます。

### 連続するベジェ曲線
（参考）Siv3D Advent Calendar 2022, 19 日目の記事

https://twitter.com/segawachobbies/status/1604496568664862720


## おわりに
ライブラリやエンジンにおける 2D 図形の表現手法を充実させることで、開発者が画像データに頼らずに複雑な視覚表現をゲーム内に実装できます。副次的な効果として、アセットサイズの削減やメモリ消費量の削減にもつながります。使いやすい図形クラスのアイデアができたら、ぜひ共有してください。
