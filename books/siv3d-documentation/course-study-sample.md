---
title: "Siv3D 勉強会コース | 遊べるサンプルプログラム"
free: true
---

# 勉強会の開始前に遊べるサンプルプログラム

## S1. ブロックくずし
![](/images/doc_v6/quick-example/breakout.gif)

:::details コード
```cpp
# include <Siv3D.hpp>

void Main()
{
	// ブロックのサイズ
	constexpr Size brickSize{ 40, 20 };
	
	// ボールの速さ
	constexpr double speed = 480.0;
	
	// ボールの速度
	Vec2 ballVelocity{ 0, -speed };
	
	// ボール
	Circle ball{ 400, 400, 8 };

	// ブロックの配列
	Array<Rect> bricks;

	// 横 (Scene::Width() / blockSize.x) 個、縦 5 個のブロックを配列に追加する
	for (auto p : step(Size{ (Scene::Width() / brickSize.x), 5 }))
	{
		bricks << Rect{ (p.x * brickSize.x), (60 + p.y * brickSize.y), brickSize };
	}

	while (System::Update())
	{
		// パドル
		const Rect paddle{ Arg::center(Cursor::Pos().x, 500), 60, 10 };

		// ボールを移動
		ball.moveBy(ballVelocity * Scene::DeltaTime());

		// ブロックを順にチェック
		for (auto it = bricks.begin(); it != bricks.end(); ++it)
		{
			// ブロックとボールが交差していたら
			if (it->intersects(ball))
			{
				// ボールの向きを反転する
				(it->bottom().intersects(ball) || it->top().intersects(ball)
					? ballVelocity.y : ballVelocity.x) *= -1;

				// ブロックを配列から削除（イテレータが無効になるので注意）
				bricks.erase(it);

				// これ以上チェックしない
				break;
			}
		}

		// 天井にぶつかったらはね返る
		if (ball.y < 0 && ballVelocity.y < 0)
		{
			ballVelocity.y *= -1;
		}

		// 左右の壁にぶつかったらはね返る
		if ((ball.x < 0 && ballVelocity.x < 0)
			|| (Scene::Width() < ball.x && 0 < ballVelocity.x))
		{
			ballVelocity.x *= -1;
		}

		// パドルにあたったらはね返る
		if (0 < ballVelocity.y && paddle.intersects(ball))
		{
			// パドルの中心からの距離に応じてはね返る方向を変える
			ballVelocity = Vec2{ (ball.x - paddle.center().x) * 10, -ballVelocity.y }.setLength(speed);
		}

		// すべてのブロックを描画する
		for (const auto& brick : bricks)
		{
			brick.stretched(-1).draw(HSV{ brick.y - 40 });
		}

		// ボールを描く
		ball.draw();

		// パドルを描く
		paddle.draw();
	}
}
```
:::


## S2. 万華鏡ペイント
右クリックで描いた内容をクリアできます。

![](/images/doc_v6/quick-example/2.png)

:::details コード
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
:::


## S3. ライフゲーム
ライフゲームとは: [ライフゲーム (Wikipedia)](https://ja.wikipedia.org/wiki/%E3%83%A9%E3%82%A4%E3%83%95%E3%82%B2%E3%83%BC%E3%83%A0)

![](/images/doc_v6/quick-example/3.png)

:::details コード
```cpp
# include <Siv3D.hpp>

// 1 セルが 1 バイトになるよう、ビットフィールドを使用
struct Cell
{
	bool previous : 1 = 0;
	bool current : 1 = 0;
};

// フィールドをランダムなセル値で埋める関数
void RandomFill(Grid<Cell>& grid)
{
	grid.fill(Cell{});

	// 境界のセルを除いて更新
	for (auto y : Range(1, grid.height() - 2))
	{
		for (auto x : Range(1, grid.width() - 2))
		{
			grid[y][x] = Cell{ 0, RandomBool(0.5) };
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
	Grid<Cell> grid(width + 2, height + 2, Cell{ 0,0 });

	// フィールドの状態を可視化するための画像
	Image image{ width, height, Palette::Black };

	// 動的テクスチャ
	DynamicTexture texture{ image };

	Stopwatch stopwatch{ StartImmediately::Yes };

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
			|| (autoStep && stopwatch.sF() >= (speed * speed)))
		{
			Update(grid);
			updated = true;
			stopwatch.restart();
		}

		// グリッド表示の有無を指定するチェックボックス
		SimpleGUI::CheckBoxAt(showGrid, U"Grid", Vec2{ 700, 320 }, 170);

		// フィールド上でのセルの編集
		if (Rect{ 0, 0, 599 }.mouseOver())
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

		if (Rect{ 0, 0, 599 }.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hidden);
			Rect{ Cursor::Pos() / 10 * 10, 10 }.draw(Palette::Orange);
		}
	}
}
```
:::


## S4. QR コード作成
キーボードでテキストを入力します。  
スマートフォンの QR コードリーダーで読み取ることができます。

![](/images/doc_v6/quick-example/4.png)

:::details コード
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 変換するテキスト
	TextEditState textEdit{ U"Abc" };
	String previous;

	// QR コードを表示するための動的テクスチャ
	DynamicTexture texture;

	while (System::Update())
	{
		// テキスト入力
		SimpleGUI::TextBox(textEdit, Vec2{ 20,20 }, 1240);

		// テキストの更新があれば QR コードを再作成
		if (const String current = textEdit.text;
			current != previous)
		{
			// 入力したテキストを QR コードに変換
			if (const auto qr = QR::EncodeText(current))
			{
				// 枠を付けて拡大した画像で動的テクスチャを更新
				texture.fill(QR::MakeImage(qr).scaled(500, 500, InterpolationAlgorithm::Nearest));
			}

			previous = current;
		}

		texture.drawAt(640, 400);
	}
}
```
:::


## S5. 物理演算スケッチ
四角や丸を描くと物体が生成されて物理演算をします。  
マウスホイールや右クリックで視点を移動できます。

![](/images/doc_v6/quick-example/5.png)

:::details コード
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
:::


## S6. 物理演算による破壊
ボールを発射して積み重ねられたボックスを破壊します。

![](https://raw.githubusercontent.com/Reputeless/games/main/games/005/B.png)

:::details コード
```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを 1280x720 にリサイズする
	Window::Resize(1280, 720);

	// 背景色を設定する
	Scene::SetBackground(ColorF{ 0.4, 0.7, 1.0 });

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double StepSec = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatorSec = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// 地面
	const P2Body ground = world.createRect(P2Static, Vec2{ 0, 100 }, SizeF{ 1600, 200 }, P2Material{ .density = 20.0, .restitution = 0.02, .friction = 0.8 });

	// 箱
	Array<P2Body> boxes;
	{
		for (int32 y = 0; y < 6; ++y) // 縦に
		{
			for (int32 x = 0; x < 4; ++x) // 横に
			{
				boxes << world.createRect(P2Dynamic, Vec2{ (300 + x * 20), (-30 - y * 60) }, SizeF{ 20, 60 },
					P2Material{ .density = 40.0, .restitution = 0.05, .friction = 1.0 })
					.setAwake(false); // 初期状態で安定するよう Sleep させておく
			}
		}
	}

	// ボール
	Array<P2Body> balls;

	// ボールの半径 (cm)
	constexpr double BallRaduis = 20;

	// 発射するボールの初期位置
	constexpr Circle StartCircle{ -400, -200, BallRaduis };

	// ボールをつかんでいるか
	// つかんでいる場合は最初につかんだ座標を格納
	Optional<Vec2> grabbed;

	// 新しく発射できるまでのクールタイム
	constexpr Duration CoolTime = 0.3s;

	// 前回の発射からの経過時間を計るストップウォッチ
	// クールタイム経過済みの状態で開始
	Stopwatch timeSinceShot{ CoolTime, StartImmediately::Yes };

	// 2D カメラ
	// 初期中心座標: (0, 200), 拡大倍率: 1.0, 手動操作なし
	Camera2D camera{ Vec2{ 0, -200 }, 1.0, CameraControl::None_ };

	while (System::Update())
	{
		////////////////////////////////
		//
		//	状態更新
		//
		////////////////////////////////

		// 新しいボールを発射できるか
		const bool readyToLaunch = (CoolTime <= timeSinceShot);

		for (accumulatorSec += Scene::DeltaTime(); StepSec <= accumulatorSec; accumulatorSec -= StepSec)
		{
			// 2D 物理演算のワールドを更新する
			world.update(StepSec);
		}

		// 地面より下に落ちた箱を削除する
		boxes.remove_if([](const P2Body& b) { return (200 < b.getPos().y); });

		// 地面より下に落ちたボールを削除する
		balls.remove_if([](const P2Body& b) { return (200 < b.getPos().y); });

		// 2D カメラを更新する
		camera.update();

		// 2D カメラによる座標変換の適用スコープ
		{
			// 2D カメラから Transformer2D を作成する
			const auto tr = camera.createTransformer();

			// 発射可能で、ボールの初期円を左クリックしたら
			if (readyToLaunch && StartCircle.leftClicked())
			{
				// つかむ
				grabbed = Cursor::PosF();
			}

			// 発射するボールの位置
			Vec2 ballPos = StartCircle.center;

			// 発射するボールの, 初期位置からの移動
			Vec2 ballDelta{ 0,0 };

			if (grabbed)
			{
				ballDelta = (*grabbed - Cursor::PosF())
					.limitLength(150); // 移動量を制限

				ballPos -= ballDelta;
			}

			// つかんでいて, 左クリックを離したら
			if (grabbed && MouseL.up())
			{
				// 円を追加
				balls << world
					.createCircle(P2Dynamic, ballPos, BallRaduis,
						P2Material{ .density = 100.0, .restitution = 0.0, .friction = 1.0 })
					.setVelocity(ballDelta * 8); // 発射速度

				// つかんでいる状態を解除
				grabbed.reset();

				// 発射からの経過時間を 0 から測定
				timeSinceShot.restart();
			}

			////////////////////////////////
			//
			//	描画
			//
			////////////////////////////////

			// 地面を描画する
			{
				// 地面の Quad を得る
				const Quad groundQuad = ground.as<P2Rect>(0)->getQuad();

				// Quad から長方形を復元する
				const RectF groundRect{ groundQuad.p0, (groundQuad.p2 - groundQuad.p0) };

				groundRect
					.draw(ColorF{ 0.4, 0.2, 0.0 }) // 土部分
					.drawFrame(40, 0, ColorF{ 0.2, 0.8, 0.4, 0.0 }, ColorF{ 0.2, 0.8, 0.4 }); // 草部分
			}

			// すべてのボックスを描画する
			for (const auto& box : boxes)
			{
				box.draw(ColorF{ 0.6, 0.2, 0.0 })
					.drawFrame(2); // 輪郭
			}

			// すべてのボールを描画する
			for (const auto& ball : balls)
			{
				ball.draw();
			}

			// ボールを操作できるなら
			if (readyToLaunch && (grabbed || StartCircle.mouseOver()))
			{
				// マウスカーソルを手のアイコンにする
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// ボールの初期位置を描く
			StartCircle.drawFrame(2);

			// ボールを描く
			if (readyToLaunch)
			{
				Circle{ ballPos, BallRaduis }.draw();
			}

			// ボールを発射する方向の矢印を描く
			if (20.0 < ballDelta.length())
			{
				Line{ ballPos, (ballPos + ballDelta) }
					.stretched(-10)
					.drawArrow(10, { 20, 20 }, ColorF{ 1.0, 0.0, 0.0, 0.5 });
			}

			// ボールの予測軌道を描く
			if (not ballDelta.isZero())
			{
				// 発射速度
				const Vec2 v0 = (ballDelta * 8);

				// 0.15 秒区切りで 10 地点を表示
				for (int32 i = 1; i <= 10; ++i)
				{
					const double t = (i * 0.15);

					// t 秒後の位置（等加速度運動の式）
					const Vec2 pos = ballPos + (v0 * t) + (0.5 * world.getGravity() * t * t);

					// 予測地点を描く
					Circle{ pos, 6 }
						.draw(ColorF{ 1.0, 0.6 })
						.drawFrame(3);
				}
			}
		}
	}
}
```
:::


## S7. kd-tree
近傍にある点を高速に探索できるデータ構造です。

![](/images/doc_v6/quick-example/7.png)

:::details コード
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
:::


## S8. 複雑な 2D 物理演算
スペースキーで粒子を放出します。  
マウスの左ボタンでかごを動かせます。

![](/images/doc_v6/quick-example/9.png)

:::details コード
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

	Print << U"[Space]: 粒子を放出";

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
:::


## S9. 3D 空間
3D シーンを描くための機能も用意されています。

![](/images/doc_v6/quick-example/10.png)

:::details コード
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	const Texture windmillTexture{ U"example/windmill.png", TextureDesc::MippedSRGB };
	const Texture earthTexture{ U"example/texture/earth.jpg", TextureDesc::MippedSRGB };

	const Plane floorPlane{ { 0, 0.01, 0 }, 20 };
	Image image{ 1000, 1000, Palette::White };
	DynamicTexture dtexture{ image, TextureDesc::MippedSRGB };
	Optional<Vec2> previousPenPos;

	const ColorF backgroundColor = ColorF{ 0.8, 0.9, 1.0 }.removeSRGBCurve();
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	while (System::Update())
	{
		const double t = Scene::Time();
		constexpr double verticalFOV = 30_deg;
		const Vec3 eyePosition = Cylindrical{ 20, (t * 1_deg), (8 + Periodic::Sine0_1(40s) * 8) };
		constexpr Vec3 focusPosition{ 0, 0, 0 };
		const BasicCamera3D camera{ Graphics3D::GetRenderTargetSize(), verticalFOV, eyePosition, focusPosition, Vec3::Up(), 0.1 };

		// [3D シーンの描画]
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			Graphics3D::SetCameraTransform(camera);
			const Ray ray = camera.screenToRay(Cursor::PosF());

			if (const auto pos = ray.intersectsAt(floorPlane))
			{
				Sphere{ *pos, 0.2 }.draw(Linear::Palette::Orange);
				const Vec2 penPos = pos->xz();

				if (MouseL.pressed())
				{
					const Vec2 from = (previousPenPos ? *previousPenPos : penPos);
					const Vec2 to = penPos;
					previousPenPos = penPos;
					Line{ (from * Vec2{ 50, -50 }), (to * Vec2{ 50, -50 }) }
						.movedBy(500, 500)
						.overwrite(image, 5, Linear::Palette::Orange);
					dtexture.fill(image);
				}
				else
				{
					previousPenPos.reset();
				}
			}
			else
			{
				previousPenPos.reset();
			}

			Plane{ {8, 0, 9}, 12 }.draw(Quaternion::RotateY(30_deg), ColorF{ HSV{ 250, 0.5, 0.9 } }.removeSRGBCurve());
			Plane{ {-12, 0, 6}, 12 }.draw(Quaternion::RotateY(50_deg), ColorF{ HSV{ 170, 0.5, 0.9 } }.removeSRGBCurve());
			Plane{ {7, 0, -7}, 12 }.draw(Quaternion::RotateY(10_deg), ColorF{ HSV{ 30, 0.5, 0.9 } }.removeSRGBCurve());
			floorPlane.draw(dtexture);

			for (auto i : step(36))
			{
				const Vec3 pos = Cylindrical{ 3.5, (t + 20_deg + i * 10_deg), (3 + Math::Sin(i * 10_deg * 1 + t * 40_deg)) };
				Box{ pos, 0.25 }
					.draw(Quaternion::RotateX(i * 10_deg), ColorF{ HSV{ i * 10, 0.8, 1.0 } }.removeSRGBCurve());
			}

			Box{ {-8,1,0}, 2 }.draw(ColorF{ 0.25 });
			Box{ {8,1,0}, 2 }.draw(windmillTexture);
			Sphere{ {-2, (1 + Periodic::Jump0_1(2s) * 4), 8}, 1 }.draw(ColorF{ 0.5, 0.8, 0.4 }.removeSRGBCurve());
			Sphere{ {0, (1 + Periodic::Jump0_1(2s, t + 0.3) * 4), 8}, 1 }.draw(ColorF{ 0.8, 0.4, 0.5, }.removeSRGBCurve());
			Sphere{ {2, (1 + Periodic::Jump0_1(2s, t + 0.6) * 4), 8}, 1 }.draw(ColorF{ 0.4, 0.5, 0.8 }.removeSRGBCurve());
			Disc{ {-2, (0.2 + Periodic::Jump0_1(2s) * 4), 5}, 1 }.draw(ColorF{ 0.5, 0.8, 0.4 }.removeSRGBCurve());
			Cylinder{ {0, (1 + Periodic::Jump0_1(2s, t + 0.3) * 4), 5}, 1, 2 }.draw(ColorF{ 0.8, 0.4, 0.5, }.removeSRGBCurve());
			Cylinder{ {2, (1 + Periodic::Jump0_1(2s, t + 0.6) * 4), 5}, 0.1, 2 }.draw(ColorF{ 0.4, 0.5, 0.8 }.removeSRGBCurve());
			Sphere{ {0, 3, 0}, 3 }
				.draw(Quaternion::RotateY(t * -15_deg), earthTexture);
		}

		// [RenderTexture を 2D シーンに描画]
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```
:::


## S10. 地形
左上の高さマップをクリックすると地形の標高を上げることができます。  
W / A / S / D / E / X / 矢印キーでカメラを移動できます。

![](/images/doc_v6/quick-example/12.png)

:::details コード
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	const VertexShader vsTerrain = HLSL{ U"example/shader/hlsl/terrain_forward.hlsl", U"VS" }
		| GLSL{ U"example/shader/glsl/terrain_forward.vert", {{ U"VSPerView", 1 }, { U"VSPerObject", 2 }} };

	const PixelShader psTerrain = HLSL{ U"example/shader/hlsl/terrain_forward.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/terrain_forward.frag", {{ U"PSPerFrame", 0 }, { U"PSPerView", 1 }, { U"PSPerMaterial", 3 }} };

	const PixelShader psNormal = HLSL{ U"example/shader/hlsl/terrain_normal.hlsl", U"PS" }
		| GLSL{ U"example/shader/glsl/terrain_normal.frag", {{U"PSConstants2D", 0}} };

	if ((not vsTerrain) || (not psTerrain) || (not psNormal))
	{
		return;
	}

	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture terrainTexture{ U"example/texture/grass.jpg", TextureDesc::MippedSRGB };
	const Texture rockTexture{ U"example/texture/rock.jpg", TextureDesc::MippedSRGB };
	const Texture brushTexture{ U"example/particle.png" };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	const Mesh gridMesh{ MeshData::Grid({128, 128}, 128, 128) };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };
	RenderTexture heightmap{ Size{ 256, 256 }, ColorF{0.0}, TextureFormat::R32_Float };
	RenderTexture normalmap{ Size{ 256, 256 }, ColorF{0.0, 0.0, 0.0}, TextureFormat::R16G16_Float };

	while (System::Update())
	{
		camera.update(2.0);

		// 3D
		{
			Graphics3D::SetCameraTransform(camera);

			const ScopedCustomShader3D shader{ vsTerrain, psTerrain };
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			const ScopedRenderStates3D ss{ { ShaderStage::Vertex, 0, SamplerState::ClampLinear} };
			Graphics3D::SetVSTexture(0, heightmap);
			Graphics3D::SetPSTexture(1, normalmap);
			Graphics3D::SetPSTexture(2, rockTexture);

			gridMesh.draw(terrainTexture);
		}

		// RenderTexture を 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}

		if (const bool gen = SimpleGUI::Button(U"Random", Vec2{270, 10});
			(gen || (MouseL | MouseR).pressed())) // 地形を編集
		{
			// heightmap を編集
			if (gen)
			{
				const PerlinNoiseF perlin{ RandomUint64() };
				Grid<float> grid(256, 256);
				for (auto p : step(grid.size()))
				{
					grid[p] = perlin.octave2D0_1(p / 256.0f, 5) * 16.0f;
				}
				const RenderTexture noise{ grid };
				const ScopedRenderTarget2D target{ heightmap };
				noise.draw();
			}
			else
			{
				const ScopedRenderTarget2D target{ heightmap };
				const ScopedRenderStates2D blend{ BlendState::Additive };
				brushTexture.scaled(1.0 + MouseL.pressed()).drawAt(Cursor::PosF(), ColorF{ Scene::DeltaTime() * 15.0 });
			}

			// normal map を更新
			{
				const ScopedRenderTarget2D target{ normalmap };
				const ScopedCustomShader2D shader{ psNormal };
				const ScopedRenderStates2D blend{ BlendState::Opaque, SamplerState::ClampLinear };
				heightmap.draw();
			}
		}

		heightmap.draw(ColorF{ 0.1 });
		normalmap.draw(0, 260);
	}
}
```
:::


## S11. 音楽プレーヤー
パソコンに保存されている音楽ファイルを再生します。  
パソコンに再生できる音楽ファイルが無い場合、サンプル用の音楽ファイルが `App/example/test.mp3` にあります。フリーの BGM 素材 (MP3) をダウンロードして試すこともできます。

![](/images/doc_v6/quick-example/13.png)

:::details コード
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 音楽
	Audio audio;

	// FFT の結果
	FFTResult fft;

	// 再生位置の変更の有無
	bool seeking = false;

	while (System::Update())
	{
		ClearPrint();

		// 再生・演奏時間
		const String time = FormatTime(SecondsF{ audio.posSec() }, U"M:ss")
			+ U'/' + FormatTime(SecondsF{ audio.lengthSec() }, U"M:ss");

		// プログレスバーの進み具合
		double progress = static_cast<double>(audio.posSample()) / audio.samples();

		if (audio.isPlaying())
		{
			// FFT 解析
			FFT::Analyze(fft, audio);

			// 結果を可視化
			for (auto i : step(Min(Scene::Width(), static_cast<int32>(fft.buffer.size()))))
			{
				const double size = Pow(fft.buffer[i], 0.6f) * 1000;
				RectF{ Arg::bottomLeft(i, 480), 1, size }.draw(HSV{ 240.0 - i });
			}

			// 周波数表示
			Rect{ Cursor::Pos().x, 0, 1, Scene::Height() }.draw();
			Print << U"{:.2f} Hz"_fmt(Cursor::Pos().x * fft.resolution);
		}

		// 再生
		if (SimpleGUI::Button(U"Play", Vec2{ 40, 500 }, 120, audio && !audio.isPlaying()))
		{
			audio.play(0.2s);
		}

		// 一時停止
		if (SimpleGUI::Button(U"Pause", Vec2{ 170, 500 }, 120, audio.isPlaying()))
		{
			audio.pause(0.2s);
		}

		// フォルダから音楽ファイルを開く
		if (SimpleGUI::Button(U"Open", Vec2{ 300, 500 }, 120))
		{
			audio.stop(0.5s);
			audio = Dialog::OpenAudio();
			audio.play();
		}

		// スライダー
		if (SimpleGUI::Slider(time, progress, Vec2{ 40, 540 }, 130, 590, !audio.isEmpty()))
		{
			audio.pause(0.05s);

			while (audio.isPlaying()) // 再生が止まるまで待機
			{
				System::Sleep(0.01s);
			}

			// 再生位置を変更
			audio.seekSamples(static_cast<size_t>(audio.samples() * progress));

			// ノイズを避けるため、スライダーから手を離すまで再生は再開しない
			seeking = true;
		}
		else if (seeking && MouseL.up())
		{
			// 再生を再開
			audio.play(0.05s);
			seeking = false;
		}
	}

	// 終了時に再生中の場合、音量をフェードアウト
	if (audio.isPlaying())
	{
		audio.fadeVolume(0.0, 0.3s);
		System::Sleep(0.3s);
	}
}
```
:::


## S12. オーディオ処理
音楽にリアルタイムでエフェクトを適用できます。  
パソコンに再生できる音楽ファイルが無い場合、サンプル用の音楽ファイルが `App/example/test.mp3` にあります。フリーの BGM 素材 (MP3) をダウンロードして試すこともできます。

![](/images/doc_v6/quick-example/14.png)

:::details コード
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	Audio audio;
	double posSec = 0.0;
	double volume = 1.0;
	double pan = 0.0;
	double speed = 1.0;
	bool loop = false;

	Array<float> busSamples;
	Array<float> globalSamples;
	FFTResult busFFT;
	FFTResult globalFFT;
	LineString lines(256, Vec2{ 0, 0 });

	bool pitch = false;
	double pitchShift = 0.0;

	bool lpf = false;
	double lpfCutoffFrequency = 800.0;
	double lpfResonance = 0.5;
	double lpfWet = 1.0;

	bool hpf = false;
	double hpfCutoffFrequency = 800.0;
	double hpfResonance = 0.5;
	double hpfWet = 1.0;

	bool echo = false;
	double delay = 0.1;
	double decay = 0.5;
	double echoWet = 0.5;

	bool reverb = false;
	bool freeze = false;
	double roomSize = 0.5;
	double damp = 0.0;
	double width = 0.5;
	double reverbWet = 0.5;

	while (System::Update())
	{
		ClearPrint();
		Print << U"GlobalAudio::GetActiveVoiceCount(): " << GlobalAudio::GetActiveVoiceCount();
		Print << U"isEmpty : " << audio.isEmpty();
		Print << U"isStreaming : " << audio.isStreaming();
		Print << U"sampleRate : " << audio.sampleRate();
		Print << U"samples : " << audio.samples();
		Print << U"lengthSec : " << audio.lengthSec();
		Print << U"posSample : " << audio.posSample();
		Print << U"posSec : " << (posSec = audio.posSec());
		Print << U"isActive : " << audio.isActive();
		Print << U"isPlaying : " << audio.isPlaying();
		Print << U"isPaused : " << audio.isPaused();
		Print << U"samplesPlayed : " << audio.samplesPlayed();
		Print << U"isLoop : " << (loop = audio.isLoop());
		Print << U"getLoopTimingtLoop : " << audio.getLoopTiming().beginPos << U", " << audio.getLoopTiming().endPos;
		Print << U"loopCount : " << audio.loopCount();
		Print << U"getVolume : " << (volume = audio.getVolume());
		Print << U"getPan : " << (pan = audio.getPan());
		Print << U"getSpeed : " << (speed = audio.getSpeed());

		if (SimpleGUI::Button(U"Open audio file", Vec2{ 60, 560 }))
		{
			audio.stop(0.5s);
			audio = Dialog::OpenAudio(Audio::Stream);
		}

		{
			GlobalAudio::BusGetSamples(MixBus0, busSamples);
			GlobalAudio::BusGetFFT(MixBus0, busFFT);

			for (auto [i, s] : Indexed(busSamples))
			{
				lines[i].set((300.0 + i), (200.0 - s * 100.0));
			}

			if (busSamples)
			{
				lines.draw(2, Palette::Orange);
			}

			for (auto [i, s] : Indexed(busFFT.buffer))
			{
				RectF{ Arg::bottomLeft(300 + i, 300), 1, (s * 4) }.draw();
			}
		}

		{
			GlobalAudio::GetSamples(globalSamples);
			GlobalAudio::GetFFT(globalFFT);

			for (auto [i, s] : Indexed(globalSamples))
			{
				lines[i].set((300.0 + i), (550.0 - s * 100.0));
			}

			if (globalSamples)
			{
				lines.draw(2, Palette::Orange);
			}

			for (auto [i, s] : Indexed(globalFFT.buffer))
			{
				RectF{ Arg::bottomLeft(300 + i, 650), 1, (s * 4) }.draw();
			}
		}

		if (SimpleGUI::Button(U"Play", Vec2{ 600, 20 }, 80, !audio.isPlaying()))
		{
			audio.play();
		}

		if (SimpleGUI::Button(U"Pause", Vec2{ 690, 20 }, 80, (audio.isPlaying() && !audio.isPaused())))
		{
			audio.pause();
		}

		if (SimpleGUI::Button(U"Stop", Vec2{ 780, 20 }, 80, (audio.isPlaying() || audio.isPaused())))
		{
			audio.stop();
		}

		if (SimpleGUI::Button(U"Play in 2s", Vec2{ 870, 20 }, 120, !audio.isPlaying()))
		{
			audio.play(2s);
		}

		if (SimpleGUI::Button(U"Pause in 2s", Vec2{ 1000, 20 }, 120, (audio.isPlaying() && !audio.isPaused())))
		{
			audio.pause(2s);
		}

		if (SimpleGUI::Button(U"Stop in 2s", Vec2{ 1130, 20 }, 120, (audio.isPlaying() || audio.isPaused())))
		{
			audio.stop(2s);
		}

		if (SimpleGUI::Slider(U"{:.1f} / {:.1f}"_fmt(posSec, audio.lengthSec()), posSec, 0.0, audio.lengthSec(), Vec2{ 600, 60 }, 160, 360))
		{
			if (MouseL.down() || !Cursor::DeltaF().isZero()) // シークの連続（ノイズの原因）を防ぐ
			{
				audio.seekTime(posSec);
			}
		}

		if (SimpleGUI::CheckBox(loop, U"Loop", Vec2{ 1130, 60 }))
		{
			audio.setLoop(loop);
		}

		if (SimpleGUI::Slider(U"volume: {:.2f}"_fmt(volume), volume, Vec2{ 600, 110 }, 140, 130))
		{
			audio.setVolume(volume);
		}

		if (SimpleGUI::Button(U"0.0 in 2s", Vec2{ 880, 110 }, 110, audio.isActive()))
		{
			audio.fadeVolume(0.0, 2s);
		}

		if (SimpleGUI::Button(U"0.5 in 2s", Vec2{ 1000, 110 }, 110, audio.isActive()))
		{
			audio.fadeVolume(0.5, 2s);
		}

		if (SimpleGUI::Button(U"1.0 in 2s", Vec2{ 1120, 110 }, 110, audio.isActive()))
		{
			audio.fadeVolume(1.0, 2s);
		}

		if (SimpleGUI::Slider(U"pan: {:.2f}"_fmt(pan), pan, -1.0, 1.0, Vec2{ 600, 150 }, 140, 130))
		{
			audio.setPan(pan);
		}

		if (SimpleGUI::Button(U"-1.0 in 2s", Vec2{ 880, 150 }, 110, audio.isActive()))
		{
			audio.fadePan(-1.0, 2s);
		}

		if (SimpleGUI::Button(U"0.0 in 2s", Vec2{ 1000, 150 }, 110, audio.isActive()))
		{
			audio.fadePan(0.0, 2s);
		}

		if (SimpleGUI::Button(U"1.0 in 2s", Vec2{ 1120, 150 }, 110, audio.isActive()))
		{
			audio.fadePan(1.0, 2s);
		}

		if (SimpleGUI::Slider(U"speed: {:.3f}"_fmt(speed), speed, 0.0, 4.0, Vec2{ 600, 190 }, 140, 130))
		{
			audio.setSpeed(speed);
		}

		if (SimpleGUI::Button(U"0.8 in 2s", Vec2{ 880, 190 }, 110, audio.isActive()))
		{
			audio.fadeSpeed(0.8, 2s);
		}

		if (SimpleGUI::Button(U"1.0 in 2s", Vec2{ 1000, 190 }, 110, audio.isActive()))
		{
			audio.fadeSpeed(1.0, 2s);
		}

		if (SimpleGUI::Button(U"1.2 in 2s", Vec2{ 1120, 190 }, 110, audio.isActive()))
		{
			audio.fadeSpeed(1.2, 2s);
		}

		bool updatePitch = false;
		bool updateLPF = false;
		bool updateHPF = false;
		bool updateEcho = false;
		bool updateReverb = false;

		if (SimpleGUI::CheckBox(pitch, U"Pitch", Vec2{ 600, 240 }, 120, GlobalAudio::SupportsPitchShift()))
		{
			if (pitch)
			{
				updatePitch = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 0);
			}
		}
		updatePitch |= SimpleGUI::Slider(U"pitchShift: {:.2f}"_fmt(pitchShift), pitchShift, -12.0, 12.0, Vec2{ 720, 240 }, 160, 300);

		if (SimpleGUI::CheckBox(lpf, U"LPF", Vec2{ 600, 280 }, 120))
		{
			if (lpf)
			{
				updateLPF = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 1);
			}
		}
		updateLPF |= SimpleGUI::Slider(U"cutoffFrequency: {:.0f}"_fmt(lpfCutoffFrequency), lpfCutoffFrequency, 10, 4000, Vec2{ 720, 280 }, 220, 240);
		updateLPF |= SimpleGUI::Slider(U"resonance: {:.2f}"_fmt(lpfResonance), lpfResonance, 0.1, 8.0, Vec2{ 720, 310 }, 220, 240);
		updateLPF |= SimpleGUI::Slider(U"wet: {:.2f}"_fmt(lpfWet), lpfWet, Vec2{ 720, 340 }, 220, 240);

		if (SimpleGUI::CheckBox(hpf, U"HPF", Vec2{ 600, 380 }, 120))
		{
			if (hpf)
			{
				updateHPF = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 2);
			}
		}
		updateHPF |= SimpleGUI::Slider(U"cutoffFrequency: {:.0f}"_fmt(hpfCutoffFrequency), hpfCutoffFrequency, 10, 4000, Vec2{ 720, 380 }, 220, 240);
		updateHPF |= SimpleGUI::Slider(U"resonance: {:.2f}"_fmt(hpfResonance), hpfResonance, 0.1, 8.0, Vec2{ 720, 410 }, 220, 240);
		updateHPF |= SimpleGUI::Slider(U"wet: {:.2f}"_fmt(hpfWet), hpfWet, Vec2{ 720, 440 }, 220, 240);

		if (SimpleGUI::CheckBox(echo, U"Echo", Vec2{ 600, 480 }, 120))
		{
			if (echo)
			{
				updateEcho = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 3);
			}
		}
		updateEcho |= SimpleGUI::Slider(U"delay: {:.2f}"_fmt(delay), delay, Vec2{ 720, 480 }, 220, 240);
		updateEcho |= SimpleGUI::Slider(U"decay: {:.2f}"_fmt(decay), decay, Vec2{ 720, 510 }, 220, 240);
		updateEcho |= SimpleGUI::Slider(U"wet: {:.2f}"_fmt(echoWet), echoWet, Vec2{ 720, 540 }, 220, 240);

		if (SimpleGUI::CheckBox(reverb, U"Reverb", Vec2{ 600, 580 }, 120))
		{
			if (reverb)
			{
				updateReverb = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 4);
			}
		}
		updateReverb |= SimpleGUI::CheckBox(freeze, U"freeze", Vec2{ 720, 580 }, 110);
		updateReverb |= SimpleGUI::Slider(U"roomSize: {:.2f}"_fmt(roomSize), roomSize, 0.001, 1.0, { 830, 580 }, 150, 200);
		updateReverb |= SimpleGUI::Slider(U"damp: {:.2f}"_fmt(damp), damp, Vec2{ 720, 610 }, 220, 240);
		updateReverb |= SimpleGUI::Slider(U"width: {:.2f}"_fmt(width), width, Vec2{ 720, 640 }, 220, 240);
		updateReverb |= SimpleGUI::Slider(U"wet: {:.2f}"_fmt(reverbWet), reverbWet, Vec2{ 720, 670 }, 220, 240);

		if (pitch && updatePitch)
		{
			GlobalAudio::BusSetPitchShiftFilter(MixBus0, 0, pitchShift);
		}

		if (lpf && updateLPF)
		{
			GlobalAudio::BusSetLowPassFilter(MixBus0, 1, lpfCutoffFrequency, lpfResonance, lpfWet);
		}

		if (hpf && updateHPF)
		{
			GlobalAudio::BusSetHighPassFilter(MixBus0, 2, hpfCutoffFrequency, hpfResonance, hpfWet);
		}

		if (echo && updateEcho)
		{
			GlobalAudio::BusSetEchoFilter(MixBus0, 3, delay, decay, echoWet);
		}

		if (reverb && updateReverb)
		{
			GlobalAudio::BusSetReverbFilter(MixBus0, 4, freeze, roomSize, damp, width, reverbWet);
		}
	}

	if (GlobalAudio::GetActiveVoiceCount())
	{
		GlobalAudio::FadeVolume(0.0, 0.5s);
		System::Sleep(0.5s);
	}
}
```
:::
