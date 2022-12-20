---
title: "Siv3D の 2D 図形クラスを俯瞰する"
emoji: "⏹️"
type: "tech"
topics: ["siv3d"]
published: true
---

> この記事は [GameEngineDev Advent Calendar 2022](https://qiita.com/advent-calendar/2022/mygameengine) 20 日目の参加記事です。

C++ フレームワーク [Siv3D](https://siv3d.github.io/ja-jp/) を使うと、複雑な視覚要素を短いコードで表現できます。とくに 2D 描画に注目してみると、2D 図形を表現するクラスとそのメンバ関数が豊富に用意されていることがわかります。本記事では、自作ゲームライブラリ・エンジンで図形クラスを設計するための参考資料として、Siv3D の 2D 図形クラスと、その機能の一部を紹介します。


## 二次元ベクトル
二次元平面上の位置 `(x, y)` を表現するクラスです。

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

二次元ベクトルは大きさの表現にも使うことができますが、それらの型を区別すると使い分けが面倒です。そこで `Size, SizeF` というエイリアスを提供しています。

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
![](https://storage.googleapis.com/zenn-user-upload/87c4580a8c8b-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	while (System::Update())
	{
		Circle{ 250, 300, 200 }
			.drawPie(0_deg, 120_deg, Palette::White)
			.drawPie(120_deg, 200_deg, Palette::Seagreen);

		Circle{ 550, 300, 200 }
			.drawArc(0_deg, 120_deg, 80, 0, Palette::Skyblue)
			.drawArc(120_deg, 70_deg, 0, 20, Palette::Orange);
	}
}
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

Siv3D 独自の名前付き引数機能を用いることで、グラデーションの表現ができます。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/cbfebdbc36bd-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	while (System::Update())
	{
		// 通常の塗りつぶし
		Rect{ 200, 100, 400, 100 }.draw(ColorF{ 0.8, 0.9, 1.0 });

		// 左から右へのグラデーション
		Rect{ 200, 220, 400, 100 }
			.draw(Arg::left = Palette::Skyblue, Arg::right = Palette::Seagreen);

		// 上から下へのグラデーション
		Rect{ 200, 340, 400, 100 }
			.draw(Arg::top = ColorF{ 1.0 }, Arg::bottom = ColorF{ 0.3 });
	}
}
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

3 頂点の位置指定ではなく、「重心、一辺の長さ、回転」から正三角形を構築するコンストラクタもあります。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/f3e06b5aa1c4-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	while (System::Update())
	{
		Triangle{ Vec2{ 400, 200 }, 240 }.draw(ColorF{ 1.0, 0.5, 0.5 });

		Triangle{ Vec2{ 400, 400 }, 240, 180_deg }.draw(ColorF{ 0.5, 0.5, 1.0 });
	}
}
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


`Rect::rotated()` から `Quad` を作ることもできます。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/abe795cb1c9a-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	while (System::Update())
	{
		Rect{ 200, 200, 400, 200 }
			.rotated(30_deg) // 戻り値は Quad
			.draw();
	}
}
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

`RoundRect::drawShadow()` によって影を描くことができます。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/a5f74223502c-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		RoundRect{ 100, 50, 400, 200, 20 }
			.drawShadow(Vec2{ 2, 2 }, 6, 0)
			.draw();

		RoundRect{ 100, 300, 400, 200, 20 }
			.drawShadow(Vec2{ 4, 4 }, 16, 1)
			.draw();
	}
}
```
:::


## 多角形
外周を表現する頂点配列と、穴を表現する頂点配列の配列で、穴を持てる多角形を表現するクラスです。

```cpp
class Polygon
{
	// ...　実装略
};

// 簡易的な Polygon
class Shape2D
{
	// ... 実装略
};
```
- [📄Polygon](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Polygon.hpp)
- [📄Shape2D](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Shape2D.hpp)

正六角形や十字、星形などのように、よく使われる多角形を生成する関数が用意されています。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/eb35f4b43576-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
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

GeoJSON ファイルからロードした国土情報は `MulitiPolygon` 型で管理されます。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/d4ade9491754-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	Window::Resize(1280, 720);

	const Array<MultiPolygon> countries = GeoJSONFeatureCollection{ JSON::Load(U"example/geojson/countries.geojson") }.getFeatures()
		.map([](const GeoJSONFeature& f) { return f.getGeometry().getPolygons(); });

	Camera2D camera{ Vec2{ 0, 0 }, 2.0, Camera2DParameters{.maxScale = 4096.0 } };
	Optional<size_t> selected;

	while (System::Update())
	{
		camera.update();
		{
			const auto transformer = camera.createTransformer();
			const double lineThickness = (1.0 / Graphics2D::GetMaxScaling());
			const RectF viewRect = camera.getRegion();

			Rect{ Arg::center(0, 0), 360, 180 }.draw(ColorF{ 0.2, 0.6, 0.9 }); // 海
			{
				for (auto&& [i, country] : Indexed(countries))
				{
					if (!country.computeBoundingRect().intersects(viewRect))
					{
						continue;
					}

					if (country.leftClicked())
					{
						selected = i;
					}

					country.draw((selected == i) ? ColorF{ 0.9, 0.8, 0.7 } : ColorF{ 0.93, 0.99, 0.96 });
					country.drawFrame(lineThickness, ColorF{ 0.25 });
				}
			}
		}
		camera.draw(Palette::Orange);
	}
}
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


`Bezier3` はノードベースの UI の表現に便利です。

:::details サンプル
![](https://storage.googleapis.com/zenn-user-upload/8d439fa9e20b-20221220.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	const Rect rect1{ 100, 300, 200, 150 };

	const Rect rect2{ 500, 100, 200, 150 };

	while (System::Update())
	{
		rect1.draw(Palette::Skyblue);

		rect2.draw(Palette::Skyblue);

		const Vec2 a = rect1.rightCenter();

		const Vec2 b = rect2.leftCenter();

		Bezier3{ a, Vec2{ ((a.x + b.x) * 0.5), a.y }, Vec2{ ((a.x + b.x) * 0.5), b.y }, b }.draw(5);

		a.asCircle(10).draw(Palette::Gray);

		b.asCircle(10).draw(Palette::Gray);
	}
}
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

ある図形の輪郭の一部分を `LineString` で取得することができます。

:::details サンプル
https://twitter.com/Reputeless/status/1351193928104636417

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.15 });

	const Polygon polygon0 = Shape2D::Plus(180, 100, Scene::Center().movedBy(-350, -120));
	const Polygon polygon1 = Shape2D::Heart(180, Scene::Center().movedBy(0, 120));
	const Polygon polygon2 = Shape2D::NStar(8, 180, 140, Scene::Center().movedBy(350, -120));

	while (System::Update())
	{
		const double t = (Scene::Time() * 720);

		polygon0.draw(ColorF{ 0.4 });
		polygon0.outline(t, 200).draw(LineStyle::RoundCap, 8, ColorF{ 0, 1, 0.5 });

		polygon1.draw(ColorF{ 0.4 });
		polygon1.outline(t, 200).draw(LineStyle::RoundCap, 8, ColorF{ 0, 1, 0.5 });

		polygon2.draw(ColorF{ 0.4 });
		polygon2.outline(t, 200).draw(LineStyle::RoundCap, 8, ColorF{ 0, 1, 0.5 });
	}
}
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

`Spline2D::calculateRoundBuffer()` を使うと、曲線を太らせて `Polygon` を作成することができます。`Polygon` からはナビメッシュを作成でき、道路を模したマップの経路探索に使えます。

:::details サンプル
https://youtu.be/RSDZlDs2IFQ

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.6

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.8, 0.9, 0.8 });

	Array<Vec2> points;
	Polygon polygon;
	LineString path;

	constexpr NavMeshConfig config{ .agentRadius = 20.0 };
	NavMesh navMesh;

	while (System::Update())
	{
		if (MouseL.down())
		{
			points << Cursor::Pos();
			polygon = Spline2D{ points }.calculateRoundBuffer(24, 8, 12);
			navMesh.build(polygon, config);
			path = navMesh.query(points.front(), points.back());
		}

		polygon.draw(ColorF{ 1.0 }).drawFrame(2, ColorF{ 0.7 });

		if (path)
		{
			path.draw(8, ColorF{ 0.1, 0.5, 0.9 });
			path.front().asCircle(12).draw(ColorF{ 1.0, 0.3, 0.0 });
			path.back().asCircle(12).draw(ColorF{ 1.0, 0.3, 0.0 });
		}
	}
}
```
:::


## Siv3D の図形関連の機能
[Siv3D サンプル集 | アルゴリズムとデータ構造](https://zenn.dev/reputeless/books/siv3d-documentation/viewer/sample-algorithm) が参考になります。


## それ以外のアイデア
Siv3D では専用の機能として用意されていませんが、次のような図形クラスを定義することもできるでしょう。

#### 角の r を個別に設定できる角丸長方形
Siv3D の `RoundRect` ではすべての角が同じ r を共有するため、GUI などで使われる、1 つの辺に関する角のみが丸い長方形を表現できません。代わりに `Rect::rounded(double, double, double, double)` の戻り値である `Polygon` を用いる必要があります。

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
