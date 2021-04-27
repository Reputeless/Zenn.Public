---
title: "コース | クイックサンプル集"
free: true
---

Siv3D の面白いサンプル 10 個を体験するコースです。  
どれぐらいのコードでどのようなアプリケーションが作れるのか雰囲気をつかむのに役立ちます。

# 1. 万華鏡ペイント
```cpp
# include <Siv3D.hpp>

void Main()
{
	// キャンバスのサイズ
	constexpr Size canvasSize{ 600, 600 };
	
	// ウィンドウをキャンバスのサイズに
	Window::Resize(canvasSize);
	
	// 分割数
	constexpr int32 N = 12;

	// 背景色
	constexpr Color backgroundColor{ 20, 40, 60 };

	// 書き込み用の画像
	Image image{ canvasSize, backgroundColor };

	// 画像を表示するための動的テクスチャ
	DynamicTexture texture{ image };

	while (System::Update())
	{
		if (MouseL.pressed())
		{
			// 画面の中心が (0, 0) になるようにマウスカーソルの座標を移動
			const Vec2 begin = (MouseL.down() ? Cursor::PosF() : Cursor::PreviousPosF()) - Scene::Center();
			const Vec2 end = (Cursor::PosF() - Scene::Center());

			for (auto i : step(N))
			{
				// 円座標に変換
				std::array<Circular, 2> cs = { begin, end };

				for (auto& c : cs)
				{
					// 角度をずらす
					c.theta = IsEven(i) ? (-c.theta - 2_pi / N * (i - 1)) : (c.theta + 2_pi / N * i);
				}

				// ずらした位置をもとに、画像に線を書き込む
				Line{ cs[0], cs[1] }.moveBy(Scene::Center())
					.overwrite(image, 2, HSV{ Scene::Time() * 60.0, 0.5, 1.0 });
			}

			// 書き込んだ画像でテクスチャを更新
			texture.fillIfNotBusy(image);
		}
		else if (MouseR.down()) // 右クリックされたら
		{
			// 画像を塗りつぶす
			image.fill(backgroundColor);

			// 塗りつぶした画像でテクスチャを更新
			texture.fill(image);
		}

		// テクスチャを描く
		texture.draw();
	}
}
```

# 2. マンデルブロ集合
```cpp
# include <Siv3D.hpp>

int32 Mandelbrot(double x, double y)
{
	double a = 0.0, b = 0.0;

	for (int32 n = 0; n < 360; ++n)
	{
		const double t = a * a - b * b + x;
		const double u = 2.0 * a * b + y;

		if (t * t + u * u > 4.0)
		{
			return n;
		}

		a = t;
		b = u;
	}

	return 0;
}

void Main()
{
	constexpr Size resolutuion{ 640, 480 };
	Window::Resize(resolutuion);

	Vec2 center{ 0, 0 };
	double scale = -4.0;

	// 結果を格納する画像
	Image image{ resolutuion, Palette::Black };

	// 描画用の動的テクスチャ
	DynamicTexture texture{ image };

	while (System::Update())
	{
		const double wheel = Mouse::Wheel();
		const bool clicked = MouseL.down();

		// 最初のフレームか、操作されたときだけ更新
		if (wheel || clicked || (Scene::FrameCount() == 1))
		{
			scale -= wheel;

			const double s = Pow(1.25, scale);
			const double d = (1.0 / s) / resolutuion.x;

			if (clicked)
			{
				center += (Cursor::PosF() - resolutuion / 2) * d;
			}

			const double xb = center.x - d * (resolutuion.x * 0.5);
			const double yb = center.y - d * (resolutuion.y * 0.5);

			for (auto y : step(resolutuion.y))
			{
				const double yPos = yb + (d * y);

				for (auto x : step(resolutuion.x))
				{
					const double xPos = xb + (d * x);

					if (const int32 m = Mandelbrot(xPos, yPos))
					{
						image[y][x] = HSV{ 240 - m, 0.8, 1.0 };
					}
					else
					{
						image[y][x] = Color{ 0 };
					}
				}
			}

			// 動的テクスチャの中身を image で更新
			texture.fill(image);
		}

		// テクスチャを描画
		texture.draw();
	}
}
```

# 3. ライフゲーム
```cpp
# include <Siv3D.hpp>

// 1 セルが 1 バイトになるよう、ビットフィールドを使用
struct Cell
{
	bool previous : 1;
	bool current : 1;
};

// フィールドをランダムなセル値で埋める関数
void RandomFill(Grid<Cell>& grid)
{
	grid.fill({ 0,0 });

	// 境界のセルを除いて更新
	for (auto y : Range(1, grid.height() - 2))
	{
		for (auto x : Range(1, grid.width() - 2))
		{
			grid[y][x] = { 0, RandomBool(0.5) };
		}
	}
}

// フィールドの状態を更新する関数
void Update(Grid<Cell>& grid)
{
	for (auto& cell : grid)
	{
		cell.previous = cell.current;
	}

	// 境界のセルを除いて更新
	for (auto y : Range(1, grid.height() - 2))
	{
		for (auto x : Range(1, grid.width() - 2))
		{
			const int32 c = grid[y][x].previous;

			int32 n = 0;
			n += grid[y - 1][x - 1].previous;
			n += grid[y - 1][x].previous;
			n += grid[y - 1][x + 1].previous;
			n += grid[y][x - 1].previous;
			n += grid[y][x + 1].previous;
			n += grid[y + 1][x - 1].previous;
			n += grid[y + 1][x].previous;
			n += grid[y + 1][x + 1].previous;

			// セルの状態の更新
			grid[y][x].current = (c == 0 && n == 3) || (c == 1 && (n == 2 || n == 3));
		}
	}
}

// フィールドの状態を画像化する関数
void CopyToImage(const Grid<Cell>& grid, Image& image)
{
	for (auto y : step(image.height()))
	{
		for (auto x : step(image.width()))
		{
			image[y][x] = grid[y + 1][x + 1].current
				? Color{ 0, 255, 0 } : Palette::Black;
		}
	}
}

void Main()
{
	// フィールドのセルの数（横）
	constexpr int32 width = 60;

	// フィールドのセルの数（縦）
	constexpr int32 height = 60;

	// 計算をしない境界部分も含めたサイズで二次元配列を確保
	Grid<Cell> grid(width + 2, height + 2, { 0,0 });

	// フィールドの状態を可視化するための画像
	Image image{ width, height, Palette::Black };

	// 動的テクスチャ
	DynamicTexture texture{ image };

	Stopwatch s{ StartImmediately::Yes };

	// 自動再生
	bool autoStep = false;

	// 更新頻度
	double speed = 0.5;

	// グリッドの表示
	bool showGrid = true;

	// 画像の更新の必要があるか
	bool updated = false;

	while (System::Update())
	{
		// フィールドをランダムな値で埋めるボタン
		if (SimpleGUI::ButtonAt(U"Random", Vec2{ 700, 40 }, 170))
		{
			RandomFill(grid);
			updated = true;
		}

		// フィールドのセルをすべてゼロにするボタン
		if (SimpleGUI::ButtonAt(U"Clear", Vec2{ 700, 80 }, 170))
		{
			grid.fill({ 0, 0 });
			updated = true;
		}

		// 一時停止 / 再生ボタン
		if (SimpleGUI::ButtonAt(autoStep ? U"Pause" : U"Run ▶", Vec2{ 700, 160 }, 170))
		{
			autoStep = !autoStep;
		}

		// 更新頻度変更スライダー
		SimpleGUI::SliderAt(U"Speed", speed, 1.0, 0.1, Vec2{ 700, 200 }, 70, 100);

		// 1 ステップ進めるボタン、または更新タイミングの確認
		if (SimpleGUI::ButtonAt(U"Step", Vec2{ 700, 240 }, 170)
			|| (autoStep && s.sF() >= (speed * speed)))
		{
			Update(grid);
			updated = true;
			s.restart();
		}

		// グリッド表示の有無を指定するチェックボックス
		SimpleGUI::CheckBoxAt(showGrid, U"Grid", Vec2{ 700, 320 }, 170);

		// フィールド上でのセルの編集
		if (Rect(0, 0, 599).mouseOver())
		{
			const Point target = (Cursor::Pos() / 10 + Point{ 1, 1 });

			if (MouseL.pressed())
			{
				grid[target].current = true;
				updated = true;
			}
			else if (MouseR.pressed())
			{
				grid[target].current = false;
				updated = true;
			}
		}

		// 画像の更新
		if (updated)
		{
			CopyToImage(grid, image);
			texture.fill(image);
			updated = false;
		}

		// 画像をフィルタなしで拡大して表示
		{
			ScopedRenderStates2D sampler{ SamplerState::ClampNearest };
			texture.scaled(10).draw();
		}

		// グリッドの表示
		if (showGrid)
		{
			for (auto i : step(61))
			{
				Rect{ 0, i * 10, 600, 1 }.draw(ColorF{ 0.4 });
				Rect{ i * 10, 0, 1, 600 }.draw(ColorF{ 0.4 });
			}
		}

		if (Rect(0, 0, 599).mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hidden);
			Rect{ Cursor::Pos() / 10 * 10, 10 }.draw(Palette::Orange);
		}
	}
}
```


# 4. QR コード作成
キーボードでテキストを入力できます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
   
	const Font font{ 40, Typeface::Bold };

	// 変換するテキスト
	String text = U"Abc", previous;

	// QR コードを表示するための動的テクスチャ
	DynamicTexture texture;

	while (System::Update())
	{
		// テキスト入力
		TextInput::UpdateText(text);

		const String current = (text + TextInput::GetEditingText());

		// テキストの更新があれば QR コードを再作成
		if (current != previous)
		{
			// 入力したテキストを QR コードに変換
			if (const auto qr = QR::EncodeText(current))
			{
				// 枠を付けて拡大した画像で動的テクスチャを更新
				texture.fill(QR::MakeImage(qr).scaled(500, 500, InterpolationAlgorithm::Nearest));
			}
		}

		previous = current;

		font(current).draw(60, 50);

		texture.drawAt(640, 400);
	}
}
```


# 5. 物理演算スケッチ
四角や丸を描くと物体が生成されて物理演算をします。  
マウスホイールや右クリックで視点を移動できます。
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

			// 左クリックされたら
			if (MouseL.down())
			{
				points << Cursor::PosF();
			}
			else if (MouseL.pressed() && !Cursor::DeltaF().isZero())
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


# 6. 長方形詰込み
長方形が箱詰めされるのを眺めます。
```cpp
# include <Siv3D.hpp>

// ランダムな長方形の配列を作成
Array<Rect> GenerateRandomRects()
{
	Array<Rect> rects(Random(4, 32));

	for (auto& rect : rects)
	{
		const Point pos = RandomPoint(Rect{ Scene::Size() - Size{ 150, 150 } });
		rect.set(pos, Random(20, 150), Random(20, 150));
	}

	return rects;
}

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.99 });
	Array<Rect> input;
	Array<double> rotations;
	RectanglePack output;
	Point offset{ 0, 0 };
	Stopwatch s;

	while (System::Update())
	{
		if (!s.isStarted() || s > 1.8s)
		{
			input = GenerateRandomRects();
			rotations.resize(input.size());
			rotations.fill(0.0);
			output = RectanglePacking::Pack(input, 1024, AllowFlip::Yes);

			for (size_t i = 0; i < input.size(); ++i)
			{
				if (input[i].w != output.rects[i].w)
				{
					rotations[i] = 270_deg;
				}
			}

			// 画面中央に表示するよう位置を調整
			offset = (Scene::Size() - output.size) / 2;
			for (auto& rect : output.rects)
			{
				rect.moveBy(offset);
			}

			s.restart();
		}

		// アニメーション
		const double k = Min(s.sF() * 10, 1.0);
		const double t = Math::Saturate(s.sF() - 0.2);
		const double e = EaseInOutExpo(t);

		Rect{ offset, output.size }.draw(ColorF{ 0.7, e });

		for (auto i : step(input.size()))
		{
			const RectF in = input[i].scaledAt(input[i].center(), k);
			const RectF out = output.rects[i];
			const Vec2 center = in.center().lerp(out.center(), e);
			const RectF rect{ Arg::center = center, in.size };

			rect.rotatedAt(rect.center(), Math::Lerp(0.0, rotations[i], e))
				.draw(HSV{ i * 25.0, 0.65, 0.9 })
				.drawFrame(2, 0, ColorF{ 0.25 });
		}
	}
}
```


# 7. kd-tree
```cpp
# include <Siv3D.hpp>

struct Unit
{
	Circle circle;

	ColorF color;

	void draw() const
	{
		circle.draw(color);
	}
};

// Unit を KDTree で扱えるようにするためのアダプタ
struct UnitAdapter : KDTreeAdapter<Array<Unit>, Vec2>
{
	static const element_type* GetPointer(const point_type& point)
	{
		return point.getPointer();
	}

	static element_type GetElement(const dataset_type& dataset, size_t index, size_t dim)
	{
		return dataset[index].circle.center.elem(dim);
	}
};

void Main()
{
	// 200 個の Unit を生成
	Array<Unit> units;
	{
		for (size_t i = 0; i < 200; ++i)
		{
			Unit unit
			{
				.circle = Circle{ RandomVec2(Scene::Rect()), 4 },
				.color = RandomColorF()
			};
			units << unit;
		}
	}

	// kd-tree を構築
	KDTree<UnitAdapter> kdTree{ units };

	// radius search する際の探索距離
	constexpr double searchDistance = 80.0;

	while (System::Update())
	{
		const Vec2 cursorPos = Cursor::PosF();

		Circle{ cursorPos, searchDistance }.draw(ColorF{ 1.0, 0.2 });

		// searchDistance 以内の距離にある Unit のインデックスを取得
		for (auto index : kdTree.radiusSearch(cursorPos, searchDistance))
		{
			Line{ cursorPos, units[index].circle.center }.draw(4);
		}

		// ユニットを描画
		for (const auto& unit : units)
		{
			unit.draw();
		}
	}
}
```

# 8. Web カメラと顔検出
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

void Main()
{
	Window::Resize(1280, 720);
	AsyncTask<Webcam> task{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };
	Webcam webcam;
	Image image;
	DynamicTexture texture;
	CascadeClassifier cascade{ U"example/objdetect/haarcascade/frontal_face_alt2.xml" };
	Array<Rect> faces;

	while (System::Update())
	{
		if (!webcam && !task.valid())
		{
			if (SimpleGUI::Button(U"Retry", Vec2{ 20, 20 }))
			{
				task = AsyncTask{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };
			}
		}

		if (task.isReady())
		{
			webcam = task.get();
		}

		if (webcam.hasNewFrame())
		{
			webcam.getFrame(image);

			// 顔を検出
			faces = cascade.detectObjects(image);

			texture.fill(image);
		}

		// Webcam 作成待機中は円を表示
		if (!webcam)
		{
			Circle{ Scene::Center(), 40 }.drawArc(Scene::Time() * 180_deg, 300_deg, 5, 5);
		}

		if (texture)
		{
			texture.draw();
		}

		for (const auto& face : faces)
		{
			face.drawFrame(4, Palette::Red);
		}
	}
}
```

# 9. Joy-Con
事前に PC に Joy-Con を Bluetooth で接続しておきます。

[実行結果のムービー](https://siv3d.github.io/ja-jp/reference/gamepad/#joy-con)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

void Main()
{
	Scene::SetBackground(ColorF{ 0.9 });
	Window::Resize(1280, 720);
	Effect effect;

	Vec2 left{ 640 - 100, 100 }, right{ 640 + 100, 100 };
	double angle = 0_deg;
	double scale = 400.0;
	bool covered = true;

	while (System::Update())
	{
		Circle{ Vec2{ 640 - 300, 450 }, scale / 2 }.drawFrame(scale * 0.1);
		Circle{ Vec2{ 640 + 300, 450 }, scale / 2 }.drawFrame(scale * 0.1);

		// Joy-Con (L) を取得
		if (const auto joy = JoyConL(0))
		{
			joy.drawAt(Vec2(640 - 300, 450), scale, -90_deg - angle, covered);

			if (auto d = joy.povD8())
			{
				left += Circular{ 4, *d * 45_deg };
			}

			if (joy.button2.down())
			{
				effect.add([center = left](double t) {
					Circle{ center, 20 + t * 200 }.drawFrame(10, 0, AlphaF(1.0 - t));
					return t < 1.0;
					});
			}
		}

		// Joy-Con (R) を取得
		if (const auto joy = JoyConR(0))
		{
			joy.drawAt(Vec2{ 640 + 300, 450 }, scale, 90_deg + angle, covered);

			if (auto d = joy.povD8())
			{
				right += Circular{ 4, *d * 45_deg };
			}

			if (joy.button2.down())
			{
				effect.add([center = right](double t) {
					Circle{ center, 20 + t * 200 }.drawFrame(10, 0, AlphaF(1.0 - t));
					return t < 1.0;
					});
			}
		}

		Circle{ left, 30 }.draw(ColorF{ 0.0, 0.75, 0.9 });
		Circle{ right, 30 }.draw(ColorF{ 1.0, 0.4, 0.3 });
		effect.update();

		SimpleGUI::Slider(U"Rotation: ", angle, -180_deg, 180_deg, Vec2{ 20, 20 }, 120, 200);
		SimpleGUI::Slider(U"Scale: ", scale, 100.0, 600.0, Vec2{ 20, 60 }, 120, 200);
		SimpleGUI::CheckBox(covered, U"Covered", Vec2{ 20, 100 });
	}
}
```

# 10. 複雑な 2D 物理演算
スペースキーで粒子を放出します。  
マウスの左ボタンでかごを動かせます。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

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

	Print << U"[スペース] キーで粒子を放出";

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


