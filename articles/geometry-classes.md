---
title: "Siv3D の 2D 図形クラスを俯瞰する"
emoji: "⏹️"
type: "tech"
topics: ["siv3d"]
published: false
---

> この記事は [GameEngineDev Advent Calendar 2022](https://qiita.com/advent-calendar/2022/mygameengine) の参加記事です。

C++ フレームワーク Siv3D では、複雑な視覚要素を短いコードで表現できます。2D 描画のコードに注目すると、2D 図形を表現するクラスとそのメンバ関数が豊富に用意されていることがわかります。本記事では、自作ゲームライブラリ・エンジンで図形クラスを設計するための参考資料として、Siv3D の 2D 図形クラスとその機能の一部を紹介します。


## 二次元ベクトル
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

## 極座標
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

## 線分
```cpp
struct Line
{
	Vec2 begin;
	Vec2 end;
};
```

## 円
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

## 長方形
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

## 三角形
```cpp
struct Triangle
{
	Vec2 p0;
	Vec2 p1;
	Vec2 p2;
};
```

## 四角形
```cpp
struct Quad
{
	Vec2 p0;
	Vec2 p1;
	Vec2 p2;
	Vec2 p3;
};
```

## 楕円
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

## 角丸長方形
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

## 多角形
```cpp
class Polygon
{
	// ...　実装略
};
```

## 多角形の集合
```cpp
class MultiPolygon
{
	Array<Polygon> m_data;
};
```

## ベジェ曲線
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

## 連続する線分
```cpp
class LineString
{
	Array<Vec2> m_data;
};
```

## スプライン曲線
```cpp
class Spline2D
{
	// ...　実装略
};
```


## それ以外のアイデア

### 角の r を個別に設定できる角丸長方形
Siv3D の `RoundRect` はすべての角が同じ r を共有するため、GUI などで使われる、1 つの辺に関する角のみが丸い長方形を表現できない。代わりに `Rect::rounded(double, double, double, double)` の戻り値である `Polygon` 型を用いる必要がある。

### 傾きのある楕円
Siv3D の `Ellipse` では傾きのある楕円を表現できないため、`Mat3x2` によるアフィン変換と組み合わせて描画する。

### 直線
長方形や多角形を直線によって分割したい場合、直線を表現できるクラスがあると便利かもしれない。Siv3D の場合は `Line` の両端を無限に伸ばすことで代わりに表現する。


## おわりに
2D

Siv3D の 2D 図形クラス
