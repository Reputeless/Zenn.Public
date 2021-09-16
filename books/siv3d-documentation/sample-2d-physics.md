---
title: "サンプル集 | 2D 物理演算"
free: true
---

## Sketch to P2Body
四角や丸を描くと物体が生成されて物理演算をします。  
マウスホイールや右クリックで視点を移動できます。
![](/images/doc_v6/quick-example/5.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを 1280x720 にリサイズ
	Window::Resize(1280, 720);

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double stepSec = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatorSec = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// [_] 地面
	const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -600, 0, 600, 0 });

	// 物体
	Array<P2Body> bodies;

	// 2D カメラ
	Camera2D camera{ Vec2{ 0, -300 } };

	LineString points;

	while (System::Update())
	{
		for (accumulatorSec += Scene::DeltaTime(); stepSec <= accumulatorSec; accumulatorSec -= stepSec)
		{
			// 2D 物理演算のワールドを更新
			world.update(stepSec);
		}

		// 地面より下に落ちた物体は削除
		bodies.remove_if([](const P2Body& b) { return (200 < b.getPos().y); });

		// 2D カメラの更新
		camera.update();
		{
			// 2D カメラから Transformer2D を作成
			const auto t = camera.createTransformer();

			// 左クリックもしくはクリックしたままの移動が発生したら
			if (MouseL.down() ||
				(MouseL.pressed() && (not Cursor::DeltaF().isZero())))
			{
				points << Cursor::PosF();
			}
			else if (MouseL.up())
			{
				points = points.simplified(2.0);

				if (const Polygon polygon = Polygon::CorrectOne(points))
				{
					const Vec2 pos = polygon.centroid();

					bodies << world.createPolygon(P2Dynamic, pos, polygon.movedBy(-pos));
				}

				points.clear();
			}

			// すべてのボディを描画
			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}

			// 地面を描画
			ground.draw(Palette::Skyblue);

			points.draw(3);
		}

		// 2D カメラの操作を描画
		camera.draw(Palette::Orange);
	}
}
```


## 鉄球による破壊

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを 1280x720 にリサイズ
	Window::Resize(1280, 720);

	// 背景色を設定
	Scene::SetBackground(ColorF{ 0.4, 0.7, 1.0 });

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double stepSec = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatorSec = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// [_] 地面
	const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -1600, 0, 1600, 0 });

	// [■] 箱 (Sleep させておく)
	Array<P2Body> boxes;
	{
		for (auto y : Range(0, 12))
		{
			for (auto x : Range(0, 20))
			{
				boxes << world.createRect(P2Dynamic, Vec2{ x * 50, -50 - y * 100 },
					SizeF{ 50, 100 }, P2Material{ .density = 0.02, .restitution = 0.0, .friction = 1.0 })
					.setAwake(false);
			}
		}
	}

	// 振り子の軸の座標
	constexpr Vec2 pivotPos{ 0, -2400 };

	// チェーンを構成するリンク 1 つの長さ
	constexpr double linkLength = 100.0;

	// チェーンを構成するリンクの数
	constexpr int32 linkCount = 16;

	// チェーンの長さ
	constexpr double chainLength = (linkLength * linkCount);

	// 鉄球の半径
	constexpr double ballR = 200;

	// 鉄球の初期座標
	constexpr Vec2 ballCenter = pivotPos.movedBy(-chainLength - ballR, 0);

	// [●] 鉄球
	const P2Body ball = world.createCircle(P2BodyType::Dynamic, ballCenter, ballR,
		P2Material{ .density = 0.5, .restitution = 0.0, .friction = 1.0 });

	// [ ] 振り子の軸（実体がないプレースホルダー）
	const P2Body pivot = world.createPlaceholder(P2BodyType::Static, pivotPos);

	// [-] チェーンを構成するリンク
	Array<P2Body> links;

	// リンクどうしやリンクと鉄球をつなぐジョイント
	Array<P2PivotJoint> joints;
	{
		for (auto i : step(linkCount))
		{
			// リンクの長方形（隣接するリンクと重なるよう少し大きめに）
			const RectF rect{ Arg::rightCenter = pivotPos.movedBy(i * -linkLength, 0), linkLength * 1.2, 20 };

			// categoryBits を 0 にすることで、箱など他の物体と干渉しないようにする
			links << world.createRect(P2Dynamic, rect.center(), rect.size,
				P2Material{ .density = 0.1, .restitution = 0.0, .friction = 1.0 }, P2Filter{ .categoryBits = 0 });

			if (i == 0)
			{
				// 振り子の軸と最初のリンクをつなぐジョイント
				joints << world.createPivotJoint(pivot, links.back(), rect.rightCenter().movedBy(-linkLength * 0.1, 0));
			}
			else
			{
				// 新しいリンクと、一つ前のリンクをつなぐジョイント
				joints << world.createPivotJoint(links[links.size() - 2], links.back(), rect.rightCenter().movedBy(-linkLength * 0.1, 0));
			}
		}

		// 最後のリンクと鉄球をつなぐジョイント
		joints << world.createPivotJoint(links.back(), ball, pivotPos.movedBy(-chainLength, 0));
	}

	// [/] ストッパー
	P2Body stopper = world.createLine(P2Static, ballCenter.movedBy(0, 200), Line{ -400, 200, 400, 0 });

	// 2D カメラ
	Camera2D camera{ Vec2{ 0, -1200 }, 0.25 };

	while (System::Update())
	{
		for (accumulatorSec += Scene::DeltaTime(); stepSec <= accumulatorSec; accumulatorSec -= stepSec)
		{
			// 2D 物理演算のワールドを更新
			world.update(stepSec);

			// 落下した box は削除
			boxes.remove_if([](const P2Body& body) { return (2000 < body.getPos().y); });
		}

		// 2D カメラの更新
		camera.update();
		{
			// 2D カメラから Transformer2D を作成
			const auto t = camera.createTransformer();

			// 地面を描く
			ground.draw(ColorF{ 0.0, 0.5, 0.0 });

			// チェーンを描く
			for (const auto& link : links)
			{
				link.draw(ColorF{ 0.25 });
			}

			// 箱を描く
			for (const auto& box : boxes)
			{
				box.draw(ColorF{ 0.6, 0.4, 0.2 });
			}

			// ストッパーを描く
			stopper.draw(ColorF{ 0.25 });

			// 鉄球を描く
			ball.draw(ColorF{ 0.25 });
		}

		// ストッパーを無くす
		if (stopper && SimpleGUI::Button(U"Go", Vec2(1100, 20)))
		{
			// ストッパーを破棄
			stopper.release();
		}

		// 2D カメラの操作を描画
		camera.draw(Palette::Orange);
	}
}
```


## 台車

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを 1280x720 にリサイズ
	Window::Resize(1280, 720);

	// 背景色を設定
	Scene::SetBackground(ColorF{ 0.4, 0.7, 1.0 });

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double stepSec = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatorSec = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// [_] 地面
	Array<P2Body> floors;
	{
		floors << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -1600, 0, 1600, 0 });

		for (auto i : Range(1, 5))
		{
			if (IsEven(i))
			{
				floors << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ 0, -i * 200, 1600, -i * 200 - 300 });
			}
			else
			{
				floors << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -1600,  -i * 200 - 300, 0, -i * 200 });
			}
		}
	}

	// [🚙] 車
	const P2Body carBody = world.createRect(P2Dynamic, Vec2{ -1500, -1450 }, SizeF{ 200, 40 });
	const P2Body wheelL = world.createCircle(P2Dynamic, Vec2{ -1550, -1430 }, 30);
	const P2Body wheelR = world.createCircle(P2Dynamic, Vec2{ -1450, -1430 }, 30);
	const P2WheelJoint wheelJointL = world.createWheelJoint(carBody, wheelL, wheelL.getPos(), Vec2{ 0, -1 })
		.setLinearStiffness(4.0, 0.7)
		.setLimits(-5, 5).setLimitsEnabled(true);
	const P2WheelJoint wheelJointR = world.createWheelJoint(carBody, wheelR, wheelR.getPos(), Vec2{ 0, -1 })
		.setLinearStiffness(4.0, 0.7)
		.setLimits(-5, 5).setLimitsEnabled(true);

	// マウスジョイント
	P2MouseJoint mouseJoint;

	// 2D カメラ
	Camera2D camera{ Vec2{ 0, -1200 }, 0.25 };

	while (System::Update())
	{
		for (accumulatorSec += Scene::DeltaTime(); stepSec <= accumulatorSec; accumulatorSec -= stepSec)
		{
			world.update(stepSec);
		}

		// 2D カメラの更新
		camera.update();
		{
			// 2D カメラから Transformer2D を作成
			const auto t = camera.createTransformer();

			if (MouseL.down())
			{
				mouseJoint = world.createMouseJoint(carBody, Cursor::PosF())
					.setMaxForce(carBody.getMass() * 5000.0)
					.setLinearStiffness(2.0, 0.7);
			}
			else if (MouseL.pressed())
			{
				mouseJoint.setTargetPos(Cursor::PosF());
			}
			else if (MouseL.up())
			{
				mouseJoint.release();
			}

			// 地面を描く
			for (const auto& floor : floors)
			{
				floor.draw(ColorF{ 0.0, 0.5, 0.0 });
			}

			carBody.draw(Palette::Gray);
			wheelL.draw(Palette::Gray).drawWireframe(1, Palette::Yellow);
			wheelR.draw(Palette::Gray).drawWireframe(1, Palette::Yellow);

			mouseJoint.draw();
			wheelJointL.draw();
			wheelJointR.draw();
		}

		// 2D カメラの操作を描画
		camera.draw(Palette::Orange);
	}
}
```


## 滑車の付いたかご

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを 1280x720 にリサイズ
	Window::Resize(1280, 720);

	// 背景色を設定
	Scene::SetBackground(ColorF{ 0.2 });

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double stepSec = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatorSec = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	const P2Body rail = world.createLineString(P2Static, Vec2{ 0, -400 }, { Vec2{-400, -40}, Vec2{-400, 0}, Vec2{400, 0}, {Vec2{400, -40}} });
	const P2Body wheel = world.createCircle(P2Dynamic, Vec2{ 0, -420 }, 20);
	const P2Body car = world.createCircle(P2Dynamic, Vec2{ 0, -380 }, 10).setFixedRotation(true);

	// ホイールジョイント
	const P2WheelJoint wheelJoint = world.createWheelJoint(car, wheel, wheel.getPos(), Vec2{ 0, 1 })
		.setLimitsEnabled(true);

	const P2Body box = world.createPolygon(P2Dynamic, Vec2{ 0, 0 }, LineString{ Vec2{-100, 0}, Vec2{-100, 100}, Vec2{100, 100}, {Vec2{100, 0}} }.calculateBuffer(5), P2Material{ .friction = 0.0 });

	// 距離ジョイント
	const P2DistanceJoint distanceJointL = world.createDistanceJoint(car, car.getPos(), box, Vec2{ -100, 0 }, 400);
	const P2DistanceJoint distanceJointR = world.createDistanceJoint(car, car.getPos(), box, Vec2{ 100, 0 }, 400);

	Array<P2Body> balls;

	// マウスジョイント
	P2MouseJoint mouseJoint;

	// 2D カメラ
	Camera2D camera{ Vec2{ 0, -150 } };

	Print << U"[space]: make balls";

	while (System::Update())
	{
		for (accumulatorSec += Scene::DeltaTime(); stepSec <= accumulatorSec; accumulatorSec -= stepSec)
		{
			world.update(stepSec);
		}

		// こぼれたボールの削除
		balls.remove_if([](const P2Body& b) { return (600 < b.getPos().y); });

		// 2D カメラの更新
		camera.update();
		{
			// 2D カメラから Transformer2D を作成
			const auto t = camera.createTransformer();

			// マウスジョイントによる干渉
			if (MouseL.down())
			{
				mouseJoint = world.createMouseJoint(box, Cursor::PosF())
					.setMaxForce(box.getMass() * 5000.0)
					.setLinearStiffness(2.0, 0.7);
			}
			else if (MouseL.pressed())
			{
				mouseJoint.setTargetPos(Cursor::PosF());
			}
			else if (MouseL.up())
			{
				mouseJoint.release();
			}

			if (KeySpace.pressed())
			{
				// ボールの追加
				balls << world.createCircle(P2Dynamic, Cursor::PosF(), Random(2.0, 4.0), P2Material{ .density = 0.001, .restitution = 0.5, .friction = 0.0 });
			}

			rail.draw(Palette::Gray);
			wheel.draw(Palette::Gray).drawWireframe(1, Palette::Yellow);
			car.draw(ColorF{ 0.3, 0.8, 0.5 });
			box.draw(ColorF{ 0.3, 0.8, 0.5 });

			for (const auto& ball : balls)
			{
				ball.draw(Palette::Skyblue);
			}

			distanceJointL.draw();
			distanceJointR.draw();

			mouseJoint.draw();
		}

		// 2D カメラの操作を描画
		camera.draw(Palette::Orange);
	}
}
```


## Text to Polygon
![](/images/doc_v6/sample/application/text-to-polygon.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	Scene::SetBackground(ColorF{ 0.94, 0.91, 0.86 });

	const Font font{ 100, Typeface::Bold };

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double stepSec = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatorSec = 0.0;

	// 物理演算のワールド
	P2World world;

	// 床
	const P2Body line = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -1600, 0, 1600, 0 }, OneSided::Yes, P2Material{ 1.0, 0.1, 1.0 });

	// 文字のパーツ
	Array<P2Body> bodies;

	String text;
	int32 generation = 0;
	HashTable<P2BodyID, int32> table;

	// 2D カメラ
	Camera2D camera{ Vec2{ 0, -500 }, 0.38, Camera2DParameters::MouseOnly() };

	constexpr Vec2 textPos{ -400, -500 };

	while (System::Update())
	{
		for (accumulatorSec += Scene::DeltaTime(); stepSec <= accumulatorSec; accumulatorSec -= stepSec)
		{
			// 2D 物理演算のワールドを更新
			world.update(stepSec);
		}

		// 2D カメラの更新
		camera.update();

		// テキストの入力
		TextInput::UpdateText(text);

		{
			// 2D カメラを適用する Transformer2D の作成
			const auto t = camera.createTransformer();

			// 世代に応じた色で Body を描画
			for (const auto& body : bodies)
			{
				body.draw(HSV{ (table[body.id()] * 45 + 30), 0.8, 0.8 });
			}

			// 床を描画
			line.draw(Palette::Green);

			const String currentText = (text + TextInput::GetEditingText());

			// 入力文字を描画
			{
				const Transformer2D scaling(Mat3x2::Scale(2.5));

				font(currentText).draw(textPos, ColorF{ 0.5 });
			}

			// 改行文字が入力されたらテキストを P2Body にする
			if (currentText.includes(U'\n'))
			{
				// 入力文字を PolygonGlyph 化
				const Array<PolygonGlyph> glyphs = font.renderPolygons(currentText.removed(U'\n'));

				// P2Body にする Polygon を得る
				Array<Polygon> polygons;
				{
					Vec2 penPos{ textPos };

					for (const auto& glyph : glyphs)
					{
						for (const auto& polygon : glyph.polygons)
						{
							polygons << polygon
								.movedBy(penPos + glyph.getOffset())
								.scaled(2.5)
								.simplified(2.0);
						}

						penPos.x += glyph.xAdvance;
					}
				}

				for (const auto& polygon : polygons)
				{
					bodies << world.createPolygon(P2Dynamic, Vec2{ 0, 0 }, polygon, P2Material{ 1, 0.0, 0.4 });

					// 現在の世代を保存
					table[bodies.back().id()] = generation;
				}

				text.clear();

				// 世代を進める
				++generation;
			}

			// 2D カメラ、右クリック時の UI を表示
			camera.draw(Palette::Orange);
		}

		// 消去ボタン
		if (SimpleGUI::Button(U"Clear", Vec2{ 1100, 40 }))
		{
			bodies.clear();
		}
	}
}
```


## 

```cpp

```