---
title: "Siv3D の 2D 図形クラスを俯瞰する"
emoji: "⏹️"
type: "tech"
topics: ["siv3d"]
published: false
---

> この記事は [GameEngineDev Advent Calendar 2022](https://qiita.com/advent-calendar/2022/mygameengine) の参加記事です。

C++ フレームワーク Siv3D では、短いコードで複雑な視覚要素を表現できます。2D 描画のコードに注目すると、2D 図形を表現するクラスとそのメンバ関数が豊富に用意されていることがわかります。本記事では、自作ゲームライブラリ・エンジンで図形クラスを設計するための参考資料として、Siv3D の 2D 図形クラスとその機能の一部を紹介します。

## 二次元ベクトル

### Point
```cpp
struct Point
{
	int32 x;
	int32 y;
};

using Size = Point;
```

### Float2
```cpp
struct Float2
{
	float x;
	float y;
};
```

### Vec2
```cpp
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

```

## 四角形
```cpp

```

## 楕円
```cpp

```

## 角丸長方形
```cpp

```

## 多角形
```cpp

```

## 多角形の集合
```cpp

```

## ベジェ曲線
```cpp

```

## 連続する線分
```cpp

```

## スプライン曲線
```cpp

```


## それ以外のアイデア

- 角の r を個別に設定できる角丸長方形
- 傾きのある楕円
- 直線


## おわりに

Siv3D の 2D 図形クラス
