---
title: "Samples | Game"
free: true
---

## ブロックくずし
![](/images/doc_v6/quick-example/breakout.gif)
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


## ピンボール
https://youtu.be/f6jU7rujFpw
```cpp
# include <Siv3D.hpp>

// 外周の枠の頂点リストを作成
LineString CreateFrame(const Vec2& leftAnchor, const Vec2& rightAnchor)
{
	Array<Vec2> points = { leftAnchor, Vec2{ -70, -20 } };
	for (auto i : Range(-30, 30))
	{
		points << OffsetCircular(Vec2{ 0.0, -120 }, 70, (i * 3_deg));
	}
	points << Vec2{ 70, -20 } << rightAnchor;
	return LineString{ points };
}

// 接触しているかに応じて色を決定
ColorF GetColor(const P2Body& body, const HashSet<P2BodyID>& list)
{
	return list.contains(body.id()) ? Palette::White : Palette::Orange;
}

void Main()
{
	// 背景色を設定
	Scene::SetBackground(ColorF(0.2, 0.3, 0.4));

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double stepSec = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatorSec = 0.0;

	// 物理演算用のワールド
	P2World world{ 60.0 };

	// 左右フリッパーの軸の座標
	constexpr Vec2 leftFlipperAnchor{ -25, 10 }, rightFlipperAnchor{ 25, 10 };

	// 固定の枠
	Array<P2Body> frames;
	{
		// 外周
		frames << world.createLineString(P2Static, Vec2{ 0, 0 }, CreateFrame(leftFlipperAnchor, rightFlipperAnchor));
		// 左上の (
		frames << world.createLineString(P2Static, Vec2{ 0, 0 }, LineString{ Range(-25, -10).map([=](int32 i) { return OffsetCircular(Vec2{ 0.0, -120 }, 55, (i * 3_deg)).toVec2(); }) });
		// 右上の )
		frames << world.createLineString(P2Static, Vec2{ 0, 0 }, LineString{ Range(10, 25).map([=](int32 i) { return OffsetCircular(Vec2{ 0.0, -120 }, 55, (i * 3_deg)).toVec2(); }) });
	}

	// バンパー
	Array<P2Body> bumpers;
	{
		// ● x3
		{
			const P2Material material{ .restitution = 1.0, .restitutionThreshold = 0.1 };
			bumpers << world.createCircle(P2Static, Vec2{ 0, -170 }, 5, material);
			bumpers << world.createCircle(P2Static, Vec2{ -20, -150 }, 5, material);
			bumpers << world.createCircle(P2Static, Vec2{ 20, -150 }, 5, material);
		}
		// ▲ x2
		{
			const P2Material material{ .restitution = 0.8, .restitutionThreshold = 0.1 };
			bumpers << world.createTriangle(P2Static, Vec2{ 0, 0 }, Triangle{ -60, -50, -40, -15, -60, -30 }, material);
			bumpers << world.createTriangle(P2Static, Vec2{ 0, 0 }, Triangle{ 60, -50, 60, -30, 40, -15 }, material);
		}
	}

	const P2Material softMaterial{ .density = 0.1, .restitution = 0.0 };

	// 左フリッパー
	P2Body leftFlipper = world.createRect(P2Dynamic, leftFlipperAnchor, RectF{ 0, 0.4, 21, 4.5 }, softMaterial);
	// 左フリッパーのジョイント
	const P2PivotJoint leftJoint = world.createPivotJoint(frames[0], leftFlipper, leftFlipperAnchor).setLimits(-20_deg, 25_deg).setLimitsEnabled(true);

	// 右フリッパー
	P2Body rightFlipper = world.createRect(P2Dynamic, rightFlipperAnchor, RectF{ -21, 0.4, 21, 4.5 }, softMaterial);
	// 右フリッパーのジョイント
	const P2PivotJoint rightJoint = world.createPivotJoint(frames[0], rightFlipper, rightFlipperAnchor).setLimits(-25_deg, 20_deg).setLimitsEnabled(true);

	// スピナー ＋
	const P2Body spinner = world.createRect(P2Dynamic, Vec2{ -58, -120 }, SizeF{ 20, 1 }, softMaterial).addRect(RectF{ Arg::center(0, 0), 1, 20 }, P2Material{ 0.01, 0.0 });
	// スピナーのジョイント
	P2PivotJoint spinnerJoint = world.createPivotJoint(frames[0], spinner, Vec2{ -58, -120 }).setMaxMotorTorque(0.05).setMotorSpeed(0).setMotorEnabled(true);

	// 風車の |
	frames << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -40, -60, -40, -40 });
	// 風車の羽 ／
	const P2Body windmillWing = world.createRect(P2Dynamic, Vec2{ -40, -60 }, SizeF{ 30, 2 }, P2Material{ 0.1, 0.8 });
	// 風車のジョイント
	const P2PivotJoint windmillJoint = world.createPivotJoint(frames.back(), windmillWing, Vec2{ -40, -60 }).setMotorSpeed(240_deg).setMaxMotorTorque(10000.0).setMotorEnabled(true);

	// 振り子の軸
	const P2Body pendulumbase = world.createPlaceholder(P2Static, Vec2{ 0, -190 });
	// 振り子 ●
	P2Body pendulum = world.createCircle(P2Dynamic, Vec2{ 0, -120 }, 4, P2Material{ 0.1, 1.0 });
	// 振り子のジョイント
	const P2DistanceJoint pendulumJoint = world.createDistanceJoint(pendulumbase, Vec2{ 0, -190 }, pendulum, Vec2{ 0, -120 }, 70);

	// エレベーターの上部 ●
	const P2Body elevatorA = world.createCircle(P2Static, Vec2{ 40, -100 }, 3);
	// エレベーターの床 －
	const P2Body elevatorB = world.createRect(P2Dynamic, Vec2{ 40, -100 }, SizeF{ 20, 2 });
	// エレベーターのジョイント
	P2SliderJoint elevatorSliderJoint = world.createSliderJoint(elevatorA, elevatorB, Vec2{ 40, -100 }, Vec2::Down()).setLimits(5, 50).setLimitEnabled(true).setMaxMotorForce(10000).setMotorSpeed(-100);

	// ボール 〇
	const P2Body ball = world.createCircle(P2Dynamic, Vec2{ -40, -120 }, 4, P2Material{ 0.05, 0.0 });
	const P2BodyID ballID = ball.id();

	// エレベーターのアニメーション用ストップウォッチ
	Stopwatch sliderStopwatch{ StartImmediately::Yes };

	// 2D カメラ
	const Camera2D camera{ Vec2{ 0, -80 }, 2.4 };

	while (System::Update())
	{
		/////////////////////////////////////////
		//
		// 更新
		//

		if (4s < sliderStopwatch)
		{
			// エレベーターの巻き上げを停止
			elevatorSliderJoint.setMotorEnabled(false);
			sliderStopwatch.restart();
		}
		else if (2s < sliderStopwatch)
		{
			// エレベーターの巻き上げ
			elevatorSliderJoint.setMotorEnabled(true);
		}

		// ボールと接触しているボディの ID
		HashSet<P2BodyID> collidedIDs;

		// 物理演算ワールドの更新
		for (accumulatorSec += Scene::DeltaTime(); stepSec <= accumulatorSec; accumulatorSec -= stepSec)
		{
			// 振り子の揺れをおさえる抵抗
			pendulum.applyForce(Vec2{ (pendulum.getVelocity().x < 0.0) ? 0.0001 : -0.0001, 0.0 });

			// 左フリッパーの操作
			leftFlipper.applyTorque(KeyLeft.pressed() ? -80 : 40);

			// 右フリッパーの操作
			rightFlipper.applyTorque(KeyRight.pressed() ? 80 : -40);

			world.update(stepSec);

			// ボールと接触しているボディの ID を格納
			for (auto [pair, collision] : world.getCollisions())
			{
				if (pair.a == ballID)
				{
					collidedIDs.emplace(pair.b);
				}
				else if (pair.b == ballID)
				{
					collidedIDs.emplace(pair.a);
				}
			}
		}

		/////////////////////////////////////////
		//
		// 描画
		//

		// 描画用の Transformer2D
		const auto transformer = camera.createTransformer();

		// 枠の描画
		for (const auto& frame : frames)
		{
			frame.draw(Palette::Skyblue);
		}

		// スピナーの描画
		spinner.draw(GetColor(spinner, collidedIDs));

		// バンパーの描画
		for (const auto& bumper : bumpers)
		{
			bumper.draw(GetColor(bumper, collidedIDs));
		}

		// 風車の描画
		windmillWing.draw(GetColor(windmillWing, collidedIDs));

		// 振り子の描画
		pendulum.draw(GetColor(pendulum, collidedIDs));

		// エレベーターの描画
		elevatorA.draw(GetColor(elevatorA, collidedIDs));
		elevatorB.draw(GetColor(elevatorB, collidedIDs));

		// ボールの描画
		ball.draw(Palette::White);

		// フリッパーの描画
		leftFlipper.draw(Palette::Orange);
		rightFlipper.draw(Palette::Orange);

		// ジョイントの可視化
		leftJoint.draw(Palette::Red);
		rightJoint.draw(Palette::Red);
		spinnerJoint.draw(Palette::Red);
		windmillJoint.draw(Palette::Red);
		pendulumJoint.draw(Palette::Red);
		elevatorSliderJoint.draw(Palette::Red);
	}
}
```


## 絵文字タワー
https://youtu.be/DjtiTbEPqaw
```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを 1280x720 にリサイズ
	Window::Resize(1280, 720);

	// 背景色を設定
	Scene::SetBackground(ColorF{ 0.2, 0.6, 1.0 });

	// 登場する絵文字
	const Array<String> emojis = { U"🐘", U"🐧", U"🐐", U"🐤" };
	Array<MultiPolygon> polygons;
	Array<Texture> textures;
	for (const auto& emoji : emojis)
	{
		// 絵文字の画像から形状情報を作成
		polygons << Emoji::CreateImage(emoji).alphaToPolygonsCentered().simplified(2.0);

		// 絵文字の画像からテクスチャを作成
		textures << Texture{ Emoji{ emoji } };
	}

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double stepSec = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatorSec = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// [_] 地面
	const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -300, 0, 300, 0 });

	// 動物の物体
	Array<P2Body> bodies;

	// 物体の ID と絵文字のインデックスの対応テーブル
	HashTable<P2BodyID, size_t> table;

	// 絵文字のインデックス
	size_t index = Random(polygons.size() - 1);

	// 2D カメラ
	Camera2D camera{ Vec2{ 0, -200 } };

	while (System::Update())
	{
		for (accumulatorSec += Scene::DeltaTime(); stepSec <= accumulatorSec; accumulatorSec -= stepSec)
		{
			// 2D 物理演算のワールドを更新
			world.update(stepSec);
		}

		// 地面より下に落ちた物体は削除
		for (auto it = bodies.begin(); it != bodies.end();)
		{
			if (100 < it->getPos().y)
			{
				// 対応テーブルからも削除
				table.erase(it->id());

				it = bodies.erase(it);
			}
			else
			{
				++it;
			}
		}

		// 2D カメラの更新
		camera.update();
		{
			// 2D カメラから Transformer2D を作成
			const auto t = camera.createTransformer();

			// 左クリックされたら
			if (MouseL.down())
			{
				// ボディを追加
				bodies << world.createPolygons(P2Dynamic, Cursor::PosF(), polygons[index], P2Material{ 0.1, 0.0, 1.0 });

				// ボディ ID と絵文字のインデックスの組を対応テーブルに追加
				table.emplace(bodies.back().id(), std::exchange(index, Random(polygons.size() - 1)));
			}

			// すべてのボディを描画
			for (const auto& body : bodies)
			{
				textures[table[body.id()]].rotated(body.getAngle()).drawAt(body.getPos());
			}

			// 地面を描画
			ground.draw(Palette::Green);

			// 現在操作できる絵文字を描画
			textures[index].drawAt(Cursor::PosF(), AlphaF(0.5 + Periodic::Sine0_1(1s) * 0.5));
		}

		// 2D カメラの操作を描画
		camera.draw(Palette::Orange);
	}
}
```


## シューティングゲーム
https://youtu.be/ye92bp_odqI
```cpp
# include <Siv3D.hpp>

// 敵の位置をランダムに作成する関数
Vec2 GenerateEnemy()
{
	return RandomVec2({ 50, 750 }, -20);
}

void Main()
{
	while (System::Update())
	{
		if (Scene::Time() > 8)
			break;
	}

	Scene::SetBackground(ColorF{ 0.1, 0.2, 0.7 });

	const Font font{ 30 };

	// 自機テクスチャ
	const Texture playerTexture{ U"🤖"_emoji };
	// 敵テクスチャ
	const Texture enemyTexture{ U"👾"_emoji };

	// 自機
	Vec2 playerPos{ 400, 500 };
	// 敵
	Array<Vec2> enemies = { GenerateEnemy() };

	// 自機ショット
	Array<Vec2> playerBullets;
	// 敵ショット
	Array<Vec2> enemyBullets;

	// 自機のスピード
	constexpr double playerSpeed = 550.0;
	// 自機ショットのスピード
	constexpr double playerBulletSpeed = 500.0;
	// 敵のスピード
	constexpr double enemySpeed = 100.0;
	// 敵ショットのスピード
	constexpr double enemyBulletSpeed = 300.0;

	// 敵の発生間隔の初期値（秒）
	constexpr double initialEnemySpawnTime = 2.0;
	// 敵の発生間隔（秒）
	double enemySpawnTime = initialEnemySpawnTime;
	// 敵の発生の蓄積時間（秒）
	double enemyAccumulator = 0.0;

	// 自機ショットのクールタイム（秒）
	constexpr double playerShotCoolTime = 0.1;
	// 自機ショットのクールタイムタイマー（秒）
	double playerShotTimer = 0.0;

	// 敵ショットのクールタイム（秒）
	constexpr double enemyShotCoolTime = 0.90;
	// 敵ショットのクールタイムタイマー（秒）
	double enemyShotTimer = 0.0;

	Effect effect;

	// ハイスコア
	int32 highScore = 0;
	// 現在のスコア
	int32 score = 0;

	while (System::Update())
	{
		// ゲームオーバー判定
		bool gameover = false;

		const double deltaTime = Scene::DeltaTime();
		enemyAccumulator += deltaTime;
		playerShotTimer = Min((playerShotTimer + deltaTime), playerShotCoolTime);
		enemyShotTimer += deltaTime;

		// 敵の発生
		while (enemySpawnTime <= enemyAccumulator)
		{
			enemyAccumulator -= enemySpawnTime;
			enemySpawnTime = Max(enemySpawnTime * 0.95, 0.3);
			enemies << GenerateEnemy();
		}

		//-------------------
		//
		// 移動
		//

		// 自機の移動
		const Vec2 move = Vec2{ (KeyRight.pressed() - KeyLeft.pressed()), (KeyDown.pressed() - KeyUp.pressed()) }
			.setLength(deltaTime * playerSpeed * (KeyShift.pressed() ? 0.5 : 1.0));
		playerPos.moveBy(move).clamp(Scene::Rect());

		// 自機ショットの発射
		if (playerShotCoolTime <= playerShotTimer)
		{
			playerShotTimer -= playerShotCoolTime;
			playerBullets << playerPos.movedBy(0, -50);
		}

		// 自機ショットの移動
		for (auto& playerBullet : playerBullets)
		{
			playerBullet.y += (deltaTime * -playerBulletSpeed);
		}
		// 画面外に出た自機ショットは消滅
		playerBullets.remove_if([](const Vec2& b) { return (b.y < -40); });

		// 敵の移動
		for (auto& enemy : enemies)
		{
			enemy.y += (deltaTime * enemySpeed);
		}
		// 画面外に出た敵は消滅
		enemies.remove_if([&](const Vec2& e)
		{
			if (700 < e.y)
			{
				// 敵が画面外に出たらゲームオーバー
				gameover = true;
				return true;
			}
			else
			{
				return false;
			}
		});

		// 敵ショットの発射
		if (enemyShotCoolTime <= enemyShotTimer)
		{
			enemyShotTimer -= enemyShotCoolTime;

			for (const auto& enemy : enemies)
			{
				enemyBullets << enemy;
			}
		}

		// 敵ショットの移動
		for (auto& enemyBullet : enemyBullets)
		{
			enemyBullet.y += deltaTime * enemyBulletSpeed;
		}
		// 画面外に出た自機ショットは消滅
		enemyBullets.remove_if([](const Vec2& b) {return (700 < b.y); });

		//-------------------
		//
		// 攻撃判定
		//

		// 敵 vs 自機ショット
		for (auto itEnemy = enemies.begin(); itEnemy != enemies.end();)
		{
			const Circle enemyCircle{ *itEnemy, 40 };
			bool skip = false;

			for (auto itBullet = playerBullets.begin(); itBullet != playerBullets.end();)
			{
				if (enemyCircle.intersects(*itBullet))
				{
					// 爆発エフェクトを追加
					effect.add([pos = *itEnemy](double t)
					{
						const double t2 = (1.0 - t);
						Circle{ pos, 10 + t * 70 }.drawFrame(20 * t2, AlphaF(t2 * 0.5));
						return (t < 1.0);
					});

					itEnemy = enemies.erase(itEnemy);
					playerBullets.erase(itBullet);
					++score;
					skip = true;
					break;
				}

				++itBullet;
			}

			if (skip)
			{
				continue;
			}

			++itEnemy;
		}

		// 敵ショット vs 自機
		for (const auto& enemyBullet : enemyBullets)
		{
			// 敵ショットが playerPos の 20 ピクセル以内に接近したら
			if (enemyBullet.distanceFrom(playerPos) <= 20)
			{
				// ゲームオーバーにする
				gameover = true;
				break;
			}
		}

		// ゲームオーバーならリセット
		if (gameover)
		{
			playerPos = Vec2{ 400, 500 };
			enemies.clear();
			playerBullets.clear();
			enemyBullets.clear();
			enemySpawnTime = initialEnemySpawnTime;
			highScore = Max(highScore, score);
			score = 0;
		}

		//-------------------
		//
		// 描画
		//

		// 背景のアニメーション
		for (auto i : step(12))
		{
			const double a = Periodic::Sine0_1(2s, Scene::Time() - (2.0 / 12 * i));
			Rect{ 0, (i * 50), 800, 50 }.draw(ColorF(1.0, a * 0.2));
		}

		// 自機の描画
		playerTexture.resized(80).flipped().drawAt(playerPos);

		// 自機ショットの描画
		for (const auto& playerBullet : playerBullets)
		{
			Circle{ playerBullet, 8 }.draw(Palette::Orange);
		}

		// 敵の描画
		for (const auto& enemy : enemies)
		{
			enemyTexture.resized(60).drawAt(enemy);
		}

		// 敵ショットの描画
		for (const auto& enemyBullet : enemyBullets)
		{
			Circle{ enemyBullet, 4 }.draw(Palette::White);
		}

		effect.update();

		// スコアの描画
		font(U"{} [{}]"_fmt(score, highScore)).draw(Arg::bottomRight(780, 580));
	}
}
```


## 15 パズル
https://youtu.be/8VtlECZmS9Q
```cpp
# include <Siv3D.hpp>

bool Swappable(int32 a, int32 b)
{
	return (a / 4 == b / 4 && Abs(a - b) == 1) || (a % 4 == b % 4 && Abs(a - b) == 4);
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });
	constexpr int32 cellSize = 100;
	constexpr Point offset{ 60, 40 };

	// ダイアログから画像を選択
	const Image image = Dialog::OpenImage();

	// 正方形に切り抜く
	const Texture texture{ image.squareClipped(), TextureDesc::Mipped };

	Optional<int32> grabbed;

	// ランダムな操作でパズルをシャッフル
	Array<int32> pieces = Range(0, 15);
	{
		int32 pos15 = 15;

		for (int32 i = 0; i < 1000; ++i)
		{
			const int32 to = pos15 + Sample({ -4, -1, 1, 4 });

			if (InRange(to, 0, 15) && Swappable(pos15, to))
			{
				std::swap(pieces[pos15], pieces[to]);
				pos15 = to;
			}
		}
	}

	while (System::Update())
	{
		Rect{ offset, (4 * cellSize) }
			.drawShadow(Vec2{ 0, 2 }, 12, 8)
			.draw(ColorF{ 0.25 })
			.drawFrame(0, 8, ColorF{ 0.3, 0.5, 0.7 });

		if (not MouseL.pressed())
		{
			grabbed = none;
		}

		for (auto i : step(16))
		{
			const int32 pieceID = pieces[i];
			const Rect rect = Rect{ (i % 4 * cellSize), (i / 4 * cellSize), cellSize }.movedBy(offset);

			if (pieceID == 15)
			{
				if (grabbed && rect.mouseOver() && Swappable(i, grabbed.value()))
				{
					std::swap(pieces[i], pieces[grabbed.value()]);
					grabbed = i;
				}

				continue;
			}

			if (rect.leftClicked())
			{
				grabbed = i;
			}

			rect(texture.uv((pieceID % 4 * 0.25), (pieceID / 4 * 0.25), 0.25, 0.25))
				.draw()
				.drawFrame(1, 0, ColorF{ 1.0, 0.75 });

			if (grabbed == i)
			{
				rect.draw(ColorF{ 1.0, 0.5, 0.0, 0.3 });
			}

			if (rect.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}
		}

		texture.resized(180)
			.draw((offset.x + cellSize * 4 + 40), offset.y)
			.drawFrame(0, 4, ColorF{ 0.3, 0.5, 0.7 });
	}
}
```


## 数つなぎ
https://youtu.be/4ecPIdqPrtI
```cpp
# include <Siv3D.hpp>

struct Bubble
{
	// バブルの円の半径
	static constexpr int32 CircleR = 30;

	// バブルの円
	Circle circle;

	// バブルのインデックス
	int32 index;

	// 接続済みなら true に
	bool connected = false;

	void draw() const
	{
		if (connected)
		{
			circle.drawShadow(Vec2{ 1, 2 }, 10, 3).draw()
				.drawFrame(2, 0, ColorF{ 0.3, 0.6, 1.0 });
		}
		else
		{
			circle.draw();
		}

		FontAsset(U"Bubble")(index + 1).drawAt(circle.center, ColorF{ 0.25 });
	}
};

// バブルどうしが重なっていないかチェック
bool CheckBubbles(const Array<Bubble>& bubbles)
{
	for (auto i : step(bubbles.size()))
	{
		for (auto k : step(bubbles.size()))
		{
			// 自分どうしとは交差判定しない
			if (i == k)
			{
				continue;
			}

			// 重なっている
			if (bubbles[i].circle.stretched(5).intersects(bubbles[k].circle.stretched(5)))
			{
				return false;
			}
		}
	}

	return true;
}

// 指定した個数のバブルを重ならないように生成
Array<Bubble> MakeBubbles(int32 count)
{
	Array<Bubble> bubbles(count);

	do
	{
		for (auto i : step(count))
		{
			// バブルのインデックス
			bubbles[i].index = i;

			// バブルの円
			bubbles[i].circle.set(RandomVec2(Circle{ Scene::Center(), (Scene::Height() / 2 - Bubble::CircleR) }), Bubble::CircleR);
		}
	} while (not CheckBubbles(bubbles));

	return bubbles;
}

// 指定したレベルにおけるバブルの個数
constexpr int32 GetBubbleCount(int32 level)
{
	return Min(level, 15);
}

// 指定したレベルにおける制限時間（秒）
constexpr Duration GetTime(int32 level)
{
	return Duration{ (level <= 15) ? 8.0 : 8.0 - Min((level - 15) * 0.05, 2.0) };
}

void Main()
{
	Scene::SetBackground(Palette::White);
	FontAsset::Register(U"Bubble", 36, Typeface::Medium);
	Effect effect;

	// 効果音を作成
	const Array<PianoKey> keys = { PianoKey::C5,  PianoKey::D5, PianoKey::E5, PianoKey::F5, PianoKey::G5,
		PianoKey::A5, PianoKey::B5, PianoKey::C6, PianoKey::D6, PianoKey::E6,
		PianoKey::F6, PianoKey::G6, PianoKey::A6, PianoKey::B6, PianoKey::C7 };
	const Array<Audio> sounds = keys.map([](auto k) { return Audio{ GMInstrument::Glockenspiel, k, 0.3s }; });

	// ハイスコア
	int32 highScore = 0;

	// 現在のレベル
	int32 level = 1;

	// 接続数
	int32 connected = 0;

	// 残り時間のタイマー
	Timer timer{ GetTime(level), StartImmediately::Yes };

	// バブル
	Array<Bubble> bubbles = MakeBubbles(GetBubbleCount(level));

	while (System::Update())
	{
		const double delta = Scene::DeltaTime();

		// 制限時間を表す背景
		RectF{ Scene::Size() * Vec2 { 1, timer.progress0_1() } }.draw(HSV{ (level * 30), 0.3, 0.9 });

		for (auto& bubble : bubbles)
		{
			if ((bubble.index == connected)
				&& (not bubble.connected)
				&& bubble.circle.stretched(10).mouseOver())
			{
				// 接続済みに
				bubble.connected = true;

				// 接続数を増やす
				++connected;

				// エフェクトを追加
				effect.add([pos = Cursor::Pos()](double t)
				{
					Circle{ pos, (Bubble::CircleR + t * 200) }.drawFrame(2, 0, ColorF{ 0.2, 0.5, 1.0, (1.0 - t * 2.5) });
					return (t < 0.4);
				});

				// バブルの数字に応じて効果音を鳴らす
				sounds[bubble.index].playOneShot(0.8);
			}

			// バブルを円周に沿って移動
			bubble.circle.center = OffsetCircular{ Scene::Center(), bubble.circle.center }
				.rotate((IsEven(bubble.index) ? 20_deg : -20_deg) * delta);
		}

		// バブルをすべてつなぐか、時間切れになったら
		if (const bool failed = timer.reachedZero();
			(connected == GetBubbleCount(level)) || failed)
		{
			// レベルを更新
			level = (failed ? 1 : ++level);

			// 接続数をリセット
			connected = 0;

			// 制限時間をリセット
			timer = Timer{ GetTime(level), StartImmediately::Yes };

			// バブルを再生成
			bubbles = MakeBubbles(GetBubbleCount(level));

			// ハイスコアを更新
			highScore = Max(highScore, level);

			// タイトルを更新
			Window::SetTitle(U"Level {} (High score: {})"_fmt(level, highScore));
		}

		// バブルをつなぐ線
		for (int32 i = 0; i < (connected - 1); ++i)
		{
			Line{ bubbles[i].circle.center, bubbles[i + 1].circle.center }.draw(3, Palette::Orange);
		}

		// バブルを描画
		for (const auto& bubble : bubbles)
		{
			bubble.draw();
		}

		effect.update();
	}
}
```


## タイピングゲーム
![](/images/doc_v6/sample/game/typing.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 問題文のリスト
	const Array<String> texts =
	{
		U"Practice makes perfect.",
		U"Don't cry over spilt milk.",
		U"Faith will move mountains.",
		U"Nothing ventured, nothing gained.",
		U"Bad news travels fast.",
	};

	String target = texts.choice(), input;

	const Font font{ 40, Typeface::Bold };

	while (System::Update())
	{
		// テキスト入力（TextInputMode::DenyControl: エンターやタブ、バックスペースは受け付けない）
		TextInput::UpdateText(input, TextInputMode::DenyControl);

		// 誤った入力が含まれていたら削除
		while (not target.starts_with(input))
		{
			input.pop_back();
		}

		// 一致したら次の問題へ
		if (input == target)
		{
			target = texts.choice();
			input.clear();
		}

		font(target).draw(40, 270, ColorF{ 0.75 });

		font(input).draw(40, 270, ColorF{ 0.1 });
	}
}
```


## トランプを描く
トランプカードを描く機能が用意されています。詳しくは `<Siv3D/Experimental/PlayingCard.hpp>` を参照してください。

![](/images/doc_v6/sample/game/playingcard.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(Palette::Darkgreen);

	const PlayingCard::Pack pack{ 75, Palette::Red };
	Array<PlayingCard::Card> cards = PlayingCard::CreateDeck(2);

	while (System::Update())
	{
		for (auto i : step(13 * 4 + 2))
		{
			const Vec2 center{ 100 + i % 13 * 90, 100 + (i / 13) * 130 };

			if (pack.regionAt(center).mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);

				if (MouseL.down())
				{
					cards[i].flip();
				}
			}

			pack(cards[i]).drawAt(center);
		}
	}
}
```
