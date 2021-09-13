---
title: "チュートリアル 31 | シーン管理"
free: true
---

# 31. シーン管理
シーン管理（または **シーン遷移**）を使うと、複雑なアプリケーション（とくにゲーム）を効率よく開発できます。シーン管理では、ゲームのタイトル、ゲームプレイ、リザルトなど、個々の場面（シーン）を個別のクラスに実装し、それらを行き来することで全体の流れを設計します。

![](/images/doc_v6/tutorial/31/0.png)

Siv3D のシーン管理機能 `SceneManager` を使うと、各シーンがデータを共有したり、遷移先のシーンを指定したり、フェードイン・フェードアウトで滑らかに画面を切り替えたりする処理を簡単に記述できます。

:::message
シーン管理における「シーン」とは、個々のゲームの場面や、その実装クラスのことを指します。チュートリアル 15 で説明した画面のことを表すシーンや `Scene::` 名前空間の機能とは異なるので注意しましょう。
:::

## 31.1 シーンの基本
まずは、個々のシーンを区別する値（ステート）の型を決めます。例えば `String` 型を選択したとすると、タイトルシーンは `U"Title"`, ゲームシーンは `U"Game"` のように `String` 型の値で区別することになります。以降のサンプルではクラス名とシーンの名前を一致させていますが、必ずしも従う必要はありません。

ステートとして選択した型を使い `using App = SceneManager<String>;` のようにシーンマネージャークラスの型を決定し `App` と名付けます。次に、各シーンのクラスを `App::Scene` を継承して実装します。通常はコンストラクタと、`.update()`, `.draw()` の 3 つのメンバ関数を実装します。

`Main()` 関数に `App` 型のオブジェクトを作り、各シーンクラスを `.add()` で登録します。あとはメインループの中で `App::update()` を毎フレーム呼び出すと、最初に登録したシーンが自動的に実行されます。シーンでは毎フレーム `.update()` 関数が先に呼ばれ、その次に `.draw()` 関数が呼ばれます。

シーンが 1 つだけのサンプルを見てみましょう。

```cpp
# include <Siv3D.hpp>

using App = SceneManager<String>;

// タイトルシーン
class Title : public App::Scene
{
public:

	// コンストラクタ（必ず実装）
	Title(const InitData& init)
		: IScene{ init }
	{

	}

	// 更新関数（オプション）
	void update() override
	{

	}

	// 描画関数（オプション）
	void draw() const override
	{
		Scene::SetBackground(ColorF{ 0.3, 0.4, 0.5 });

		FontAsset(U"TitleFont")(U"My Game").drawAt(400, 100);

		Circle{ Cursor::Pos(), 50 }.draw(Palette::Orange);
	}
};

void Main()
{
	FontAsset::Register(U"TitleFont", 60, Typeface::Heavy);

	// シーンマネージャーを作成
	App manager;

	// タイトルシーン（名前は "Title"）を登録
	manager.add<Title>(U"Title");

	while (System::Update())
	{
		// 現在のシーンを実行
		// シーンに実装した .update() と .draw() が実行される
		if (not manager.update())
		{
			break;
		}
	}
}
```


## 31.2 シーン遷移
先ほどのサンプルに新しいシーンを追加します。あるシーンの実行中に、別のシーンに遷移したい場合は、シーンの `.update()` 関数内で `.changeScene(nextState)` を呼び、行きたい先のシーンを指定します。プログラムの実行中は、シーンを遷移するたび、古いシーンのクラスが破棄され、新しいシーンのクラスが作成されます。

```cpp
# include <Siv3D.hpp>

using App = SceneManager<String>;

// タイトルシーン
class Title : public App::Scene
{
public:

	Title(const InitData& init)
		: IScene{ init }
	{
		Print << U"Title::Title()";
	}

	~Title()
	{
		Print << U"Title::~Title()";
	}

	void update() override
	{
		// 左クリックで
		if (MouseL.down())
		{
			// ゲームシーンに遷移
			changeScene(U"Game");
		}
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF{ 0.3, 0.4, 0.5 });

		FontAsset(U"TitleFont")(U"My Game").drawAt(400, 100);

		Circle{ Cursor::Pos(), 50 }.draw(Palette::Orange);
	}
};

// ゲームシーン
class Game : public App::Scene
{
public:

	Game(const InitData& init)
		: IScene{ init }
		, m_texture{ U"🐈"_emoji }
	{
		Print << U"Game::Game()";
	}

	~Game()
	{
		Print << U"Game::~Game()";
	}

	void update() override
	{
		// 左クリックで
		if (MouseL.down())
		{
			// タイトルシーンに遷移
			changeScene(U"Title");
		}
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF(0.2, 0.8, 0.6));

		m_texture.drawAt(Cursor::Pos());
	}

private:

	Texture m_texture;
};

void Main()
{
	FontAsset::Register(U"TitleFont", 60, Typeface::Heavy);

	// シーンマネージャーを作成
	App manager;

	// タイトルシーン（名前は "Title"）を登録
	manager.add<Title>(U"Title");

	// ゲームシーン（名前は "Game"）を登録
	manager.add<Game>(U"Game");

	while (System::Update())
	{
		if (not manager.update())
		{
			break;
		}
	}
}
```

## 31.3 指定したシーンから開始する
ゲームの開発中、タイトルシーンを経由せず、ゲームシーンから始めたいこともあるでしょう。その場合は、シーンマネージャーの `.init(state)` を呼びます。

```cpp
# include <Siv3D.hpp>

using App = SceneManager<String>;

// タイトルシーン
class Title : public App::Scene
{
public:

	Title(const InitData& init)
		: IScene{ init }
	{
		Print << U"Title::Title()";
	}

	~Title()
	{
		Print << U"Title::~Title()";
	}

	void update() override
	{
		if (MouseL.down())
		{
			// ゲームシーンに遷移
			changeScene(U"Game");
		}
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF{ 0.3, 0.4, 0.5 });

		FontAsset(U"TitleFont")(U"My Game").drawAt(400, 100);

		Circle{ Cursor::Pos(), 50 }.draw(Palette::Orange);
	}
};

// ゲームシーン
class Game : public App::Scene
{
public:

	Game(const InitData& init)
		: IScene{ init }
		, m_texture{ U"🐈"_emoji }
	{
		Print << U"Game::Game()";
	}

	~Game()
	{
		Print << U"Game::~Game()";
	}

	void update() override
	{
		if (MouseL.down())
		{
			// タイトルシーンに遷移
			changeScene(U"Title");
		}
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF(0.2, 0.8, 0.6));

		m_texture.drawAt(Cursor::Pos());
	}

private:

	Texture m_texture;
};

void Main()
{
	FontAsset::Register(U"TitleFont", 60, Typeface::Heavy);

	// シーンマネージャーを作成
	App manager;
	manager.add<Title>(U"Title");
	manager.add<Game>(U"Game");

	// "Game" シーンから開始
	manager.init(U"Game");

	while (System::Update())
	{
		if (not manager.update())
		{
			break;
		}
	}
}
```


## 31.4 シーン間でデータを共有
ゲームのスコアの情報など、シーンをまたいで共有したいデータがある場合、そのデータ型を `SceneManager<>` の 2 つ目のテンプレート引数に追加します。そうすることで、各シーンの関数から `getData()` を通してそのデータにアクセスできるようになります。このデータはシーンマネージャーの作成時に 1 度だけ初期化されます。

```cpp
# include <Siv3D.hpp>

// 共有するデータ
struct GameData
{
	int32 score = 0;
};

// 共有するデータの型を指定
using App = SceneManager<String, GameData>;

// タイトルシーン
class Title : public App::Scene
{
public:

	Title(const InitData& init)
		: IScene{ init }
	{

	}

	void update() override
	{
		if (MouseL.down())
		{
			// ゲームシーンに遷移
			changeScene(U"Game");
		}
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF{ 0.3, 0.4, 0.5 });

		FontAsset(U"TitleFont")(U"My Game").drawAt(400, 100);

		// 現在のスコアを表示
		FontAsset(U"ScoreFont")(U"Score: {}"_fmt(getData().score)).draw(520, 540);

		Circle{ Cursor::Pos(), 50 }.draw(Palette::Orange);
	}
};

// ゲームシーン
class Game : public App::Scene
{
public:

	Game(const InitData& init)
		: IScene{ init }
		, m_texture{ U"🐈"_emoji }
	{

	}

	void update() override
	{
		if (MouseL.down())
		{
			// タイトルシーンに遷移
			changeScene(U"Title");
		}

		// マウスカーソルの移動でスコアが増加
		getData().score += static_cast<int32>(Cursor::Delta().length() * 10);
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF(0.2, 0.8, 0.6));

		m_texture.drawAt(Cursor::Pos());

		// 現在のスコアを表示
		FontAsset(U"ScoreFont")(U"Score: {}"_fmt(getData().score)).draw(40, 40);
	}

private:

	Texture m_texture;
};

void Main()
{
	FontAsset::Register(U"TitleFont", 60, Typeface::Heavy);
	FontAsset::Register(U"ScoreFont", 30, Typeface::Bold);

	// シーンマネージャーを作成
	// ここで GameData が初期化される
	App manager;
	manager.add<Title>(U"Title");
	manager.add<Game>(U"Game");

	while (System::Update())
	{
		if (not manager.update())
		{
			break;
		}
	}
}
```


## 31.5 フェードイン・フェードアウトの色や時間をカスタマイズする
フェードイン・フェードアウト時の画面の色を変更する場合は `SceneManager` の `.setFadeColor(color)` を呼びます。シーンの切り替えの時間をカスタマイズするには、`.changeScene()` の第 2 引数に時間を指定します（デフォルトでは 1 秒）。

```cpp
# include <Siv3D.hpp>

struct GameData
{
	int32 score = 0;
};

using App = SceneManager<String, GameData>;

// タイトルシーン
class Title : public App::Scene
{
public:

	Title(const InitData& init)
		: IScene{ init }
	{

	}

	void update() override
	{
		if (MouseL.down())
		{
			// 0.3 秒かけてゲームシーンに遷移
			changeScene(U"Game", 0.3s);
		}
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF{ 0.3, 0.4, 0.5 });

		FontAsset(U"TitleFont")(U"My Game").drawAt(400, 100);

		FontAsset(U"ScoreFont")(U"Score: {}"_fmt(getData().score)).draw(520, 540);

		Circle{ Cursor::Pos(), 50 }.draw(Palette::Orange);
	}
};

// ゲームシーン
class Game : public App::Scene
{
public:

	Game(const InitData& init)
		: IScene{ init }
		, m_texture{ U"🐈"_emoji }
	{

	}

	void update() override
	{
		if (MouseL.down())
		{
			// 2 秒かけてタイトルシーンに遷移
			changeScene(U"Title", 2.0s);
		}

		getData().score += static_cast<int32>(Cursor::Delta().length() * 10);
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF(0.2, 0.8, 0.6));

		m_texture.drawAt(Cursor::Pos());

		FontAsset(U"ScoreFont")(U"Score: {}"_fmt(getData().score)).draw(40, 40);
	}

private:

	Texture m_texture;
};

void Main()
{
	FontAsset::Register(U"TitleFont", 60, Typeface::Heavy);
	FontAsset::Register(U"ScoreFont", 30, Typeface::Bold);

	App manager;
	manager.add<Title>(U"Title");
	manager.add<Game>(U"Game");

	// フェードイン・フェードアウト時の画面の色
	manager.setFadeColor(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		if (not manager.update())
		{
			break;
		}
	}
}
```


## 31.6 フェードイン・フェードアウトをカスタマイズする
シーンでは、`.update()` や `.draw()` のほかに、`.updateFadeIn()`, `.updateFadeOut()`, `.drawFadeIn()`, `.drawFadeOut()` メンバ関数をオーバーライドすることで、フェードイン・フェードアウトの最中の挙動をカスタマイズすることもできます。

次のサンプルでは、`.drawFadeIn()`, `.drawFadeOut()` をオーバーライドして、シーン切り替えのエフェクトを描画しています。引数の `double t` はフェード開始時に 0.0, 終了時に 1.0 になる値です。`t` が 1.0 になるまでの所要時間は `.changeScene()` で指定した遷移時間で決まります。

```cpp
# include <Siv3D.hpp>

struct GameData
{
	int32 score = 0;
};

using App = SceneManager<String, GameData>;

// タイトルシーン
class Title : public App::Scene
{
public:

	Title(const InitData& init)
		: IScene{ init }
	{

	}

	void update() override
	{
		if (MouseL.down())
		{
			changeScene(U"Game");
		}
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF{ 0.3, 0.4, 0.5 });

		FontAsset(U"TitleFont")(U"My Game").drawAt(400, 100);

		FontAsset(U"ScoreFont")(U"Score: {}"_fmt(getData().score)).draw(520, 540);

		Circle{ Cursor::Pos(), 50 }.draw(Palette::Orange);
	}

	void drawFadeIn(double t) const override
	{
		draw();

		Circle{ Scene::Center(), 800 }.drawPie(0_deg, (-(1.0 - t) * 360_deg), Palette::Skyblue);
	}

	void drawFadeOut(double t) const override
	{
		draw();

		Circle{ Scene::Center(), 800 }.drawPie(0_deg, (t * 360_deg), Palette::Skyblue);
	}
};

// ゲームシーン
class Game : public App::Scene
{
public:

	Game(const InitData& init)
		: IScene{ init }
		, m_texture{ U"🐈"_emoji }
	{

	}

	void update() override
	{
		if (MouseL.down())
		{
			changeScene(U"Title");
		}

		getData().score += static_cast<int32>(Cursor::Delta().length() * 10);
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF(0.2, 0.8, 0.6));

		m_texture.drawAt(Cursor::Pos());

		FontAsset(U"ScoreFont")(U"Score: {}"_fmt(getData().score)).draw(40, 40);
	}

	void drawFadeIn(double t) const override
	{
		draw();

		Circle{ Scene::Center(), 800 }.drawPie(0_deg, (-(1.0 - t) * 360_deg), Palette::Skyblue);
	}

	void drawFadeOut(double t) const override
	{
		draw();

		Circle{ Scene::Center(), 800 }.drawPie(0_deg, (t * 360_deg), Palette::Skyblue);
	}

private:

	Texture m_texture;
};

void Main()
{
	FontAsset::Register(U"TitleFont", 60, Typeface::Heavy);
	FontAsset::Register(U"ScoreFont", 30, Typeface::Bold);

	App manager;
	manager.add<Title>(U"Title");
	manager.add<Game>(U"Game");

	while (System::Update())
	{
		if (not manager.update())
		{
			break;
		}
	}
}
```


## 31.7 （サンプル）シーン管理でゲームを実装する（Main.cpp 1 ファイル）
タイトルシーン、ゲームシーン、ランキングシーンの 3 つからなるゲームのサンプルです。

```cpp
# include <Siv3D.hpp>

// シーンの名前
enum class State
{
	Title,
	Game,
	Ranking,
};

// 共有するデータ
struct GameData
{
	// 直前のゲームのスコア
	Optional<int32> lastGameScore;

	// ハイスコア
	Array<int32> highScores = { 50, 40, 30, 20, 10 };
};

using App = SceneManager<State, GameData>;

// タイトルシーン
class Title : public App::Scene
{
public:

	Title(const InitData& init)
		: IScene{ init } {}

	void update() override
	{
		m_startTransition.update(m_startButton.mouseOver());
		m_rankingTransition.update(m_rankingButton.mouseOver());
		m_exitTransition.update(m_exitButton.mouseOver());

		if (m_startButton.mouseOver() || m_rankingButton.mouseOver() || m_exitButton.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		if (m_startButton.leftClicked())
		{
			// ゲームシーンへ
			changeScene(State::Game);
		}
		else if (m_rankingButton.leftClicked())
		{
			// ランキングシーンへ
			changeScene(State::Ranking);
		}
		else if (m_exitButton.leftClicked())
		{
			// 終了
			System::Exit();
		}
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF{ 0.2, 0.8, 0.4 });

		FontAsset(U"TitleFont")(U"BREAKOUT")
			.drawAt(TextStyle::OutlineShadow(0.2, ColorF{ 0.2, 0.6, 0.2 }, Vec2{ 3, 3 }, ColorF{ 0.0, 0.5 }), 100, Vec2{ 400, 100 });

		m_startButton.draw(ColorF{ 1.0, m_startTransition.value() }).drawFrame(2);
		m_rankingButton.draw(ColorF{ 1.0, m_rankingTransition.value() }).drawFrame(2);
		m_exitButton.draw(ColorF{ 1.0, m_exitTransition.value() }).drawFrame(2);

		FontAsset(U"Menu")(U"PLAY").drawAt(m_startButton.center(), ColorF{ 0.25 });
		FontAsset(U"Menu")(U"RANKING").drawAt(m_rankingButton.center(), ColorF{ 0.25 });
		FontAsset(U"Menu")(U"EXIT").drawAt(m_exitButton.center(), ColorF{ 0.25 });
	}

private:

	Rect m_startButton{ Arg::center = Scene::Center(), 300, 60 };
	Transition m_startTransition{ 0.4s, 0.2s };

	Rect m_rankingButton{ Arg::center = Scene::Center().movedBy(0, 100), 300, 60 };
	Transition m_exitTransition{ 0.4s, 0.2s };

	Rect m_exitButton{ Arg::center = Scene::Center().movedBy(0, 200), 300, 60 };
	Transition m_rankingTransition{ 0.4s, 0.2s };
};

// ゲームシーン
class Game : public App::Scene
{
public:

	Game(const InitData& init)
		: IScene{ init }
	{
		// 横 (Scene::Width() / blockSize.x) 個、縦 5 個のブロックを配列に追加する
		for (auto p : step(Size{ (Scene::Width() / BrickSize.x), 5 }))
		{
			m_bricks << Rect{ (p.x * BrickSize.x), (60 + p.y * BrickSize.y), BrickSize };
		}
	}

	void update() override
	{
		// ボールを移動
		m_ball.moveBy(m_ballVelocity * Scene::DeltaTime());

		// ブロックを順にチェック
		for (auto it = m_bricks.begin(); it != m_bricks.end(); ++it)
		{
			// ブロックとボールが交差していたら
			if (it->intersects(m_ball))
			{
				// ボールの向きを反転する
				(it->bottom().intersects(m_ball) || it->top().intersects(m_ball)
					? m_ballVelocity.y : m_ballVelocity.x) *= -1;

				// ブロックを配列から削除（イテレータが無効になるので注意）
				m_bricks.erase(it);

				AudioAsset(U"Brick").playOneShot(0.5);

				// スコアを加算
				++m_score;

				// これ以上チェックしない
				break;
			}
		}

		// 天井にぶつかったらはね返る
		if (m_ball.y < 0 && m_ballVelocity.y < 0)
		{
			m_ballVelocity.y *= -1;
		}

		// 左右の壁にぶつかったらはね返る
		if ((m_ball.x < 0 && m_ballVelocity.x < 0)
			|| (Scene::Width() < m_ball.x && 0 < m_ballVelocity.x))
		{
			m_ballVelocity.x *= -1;
		}

		// パドルにあたったらはね返る
		if (const Rect paddle = getPaddle();
			(0 < m_ballVelocity.y) && paddle.intersects(m_ball))
		{
			// パドルの中心からの距離に応じてはね返る方向を変える
			m_ballVelocity = Vec2{ (m_ball.x - paddle.center().x) * 10, -m_ballVelocity.y }.setLength(Speed);
		}

		// 画面外に出るか、ブロックが無くなったら
		if ((Scene::Height() < m_ball.y) || m_bricks.isEmpty())
		{
			// ランキング画面へ
			changeScene(State::Ranking);

			getData().lastGameScore = m_score;
		}
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF{ 0.2 });

		// すべてのブロックを描画する
		for (const auto& brick : m_bricks)
		{
			brick.stretched(-1).draw(HSV{ brick.y - 40 });
		}

		// ボールを描く
		m_ball.draw();

		// パドルを描く
		getPaddle().draw();

		FontAsset(U"GameScore")(m_score).draw(10, 10);
	}

private:

	// ブロックのサイズ
	static constexpr Size BrickSize{ 40, 20 };

	// ボールの速さ
	static constexpr double Speed = 480.0;

	// ボールの速度
	Vec2 m_ballVelocity{ 0, -Speed };

	// ボール
	Circle m_ball{ 400, 400, 8 };

	// ブロックの配列
	Array<Rect> m_bricks;

	// 現在のゲームのスコア
	int32 m_score = 0;

	Rect getPaddle() const
	{
		return{ Arg::center(Cursor::Pos().x, 500), 60, 10 };
	}
};

// ランキングシーン
class Ranking : public App::Scene
{
public:

	Ranking(const InitData& init)
		: IScene{ init }
	{
		auto& data = getData();

		if (data.lastGameScore)
		{
			const int32 lastScore = *data.lastGameScore;

			// ランキングを再構成
			data.highScores << lastScore;
			data.highScores.rsort();
			data.highScores.resize(RankingCount);

			// ランクインしていたら m_rank に順位をセット
			for (int32 i = 0; i < RankingCount; ++i)
			{
				if (data.highScores[i] == lastScore)
				{
					m_rank = i;
					break;
				}
			}

			data.lastGameScore.reset();
		}
	}

	void update() override
	{
		if (MouseL.down())
		{
			// タイトルシーンへ
			changeScene(State::Title);
		}
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF{ 0.4, 0.6, 0.9 });

		FontAsset(U"Ranking")(U"RANKING").drawAt(400, 60);

		const auto& data = getData();

		// ランキングを表示
		for (auto i : step(RankingCount))
		{
			const RectF rect{ 100, 120 + i * 80, 600, 80 };

			rect.draw(ColorF{ 1.0, 1.0 - i * 0.2 });

			FontAsset(U"Ranking")(data.highScores[i]).drawAt(rect.center(), ColorF{ 0.25 });

			// ランクインしていたら
			if (i == m_rank)
			{
				rect.stretched(Periodic::Triangle0_1(0.5s) * 10).drawFrame(10, ColorF{ 0.8, 0.6, 0.4 });
			}
		}
	}

private:

	static constexpr int32 RankingCount = 5;

	int32 m_rank = -1;
};

void Main()
{
	FontAsset::Register(U"TitleFont", FontMethod::MSDF, 50, U"example/font/RocknRoll/RocknRollOne-Regular.ttf");
	FontAsset(U"TitleFont").setBufferThickness(4);
	FontAsset::Register(U"Menu", FontMethod::MSDF, 40, Typeface::Medium);
	FontAsset::Register(U"Ranking", 40, Typeface::Heavy);
	FontAsset::Register(U"GameScore", 30, Typeface::Light);
	AudioAsset::Register(U"Brick", GMInstrument::Woodblock, PianoKey::C5, 0.2s, 0.1s);

	App manager;
	manager.add<Title>(State::Title);
	manager.add<Game>(State::Game);
	manager.add<Ranking>(State::Ranking);

	// ゲームシーンから開始したい場合はこのコメントを外す
	//manager.init(State::Game);

	while (System::Update())
	{
		if (not manager.update())
		{
			break;
		}
	}
}
```


## 31.8 （サンプル）シーン管理でゲームを実装する（ファイル分割）
31.7 のプログラムをファイル分割する場合の構成例です。

```cpp:Main.cpp
# include <Siv3D.hpp>
# include "Common.hpp"
# include "Title.hpp"
# include "Game.hpp"
# include "Ranking.hpp"

void Main()
{
	FontAsset::Register(U"TitleFont", FontMethod::MSDF, 50, U"example/font/RocknRoll/RocknRollOne-Regular.ttf");
	FontAsset(U"TitleFont").setBufferThickness(4);
	FontAsset::Register(U"Menu", FontMethod::MSDF, 40, Typeface::Medium);
	FontAsset::Register(U"Ranking", 40, Typeface::Heavy);
	FontAsset::Register(U"GameScore", 30, Typeface::Light);
	AudioAsset::Register(U"Brick", GMInstrument::Woodblock, PianoKey::C5, 0.2s, 0.1s);

	App manager;
	manager.add<Title>(State::Title);
	manager.add<Game>(State::Game);
	manager.add<Ranking>(State::Ranking);

	// ゲームシーンから開始したい場合はこのコメントを外す
	//manager.init(State::Game);

	while (System::Update())
	{
		if (not manager.update())
		{
			break;
		}
	}
}
```

```cpp:Common.hpp
# pragma once
# include <Siv3D.hpp>

// シーンの名前
enum class State
{
	Title,
	Game,
	Ranking,
};

// 共有するデータ
struct GameData
{
	// 直前のゲームのスコア
	Optional<int32> lastGameScore;

	// ハイスコア
	Array<int32> highScores = { 50, 40, 30, 20, 10 };
};

using App = SceneManager<State, GameData>;
```

```cpp:Title.hpp
# pragma once
# include "Common.hpp"

// タイトルシーン
class Title : public App::Scene
{
public:

	Title(const InitData& init);

	void update() override;

	void draw() const override;

private:

	Rect m_startButton{ Arg::center = Scene::Center(), 300, 60 };
	Transition m_startTransition{ 0.4s, 0.2s };

	Rect m_rankingButton{ Arg::center = Scene::Center().movedBy(0, 100), 300, 60 };
	Transition m_exitTransition{ 0.4s, 0.2s };

	Rect m_exitButton{ Arg::center = Scene::Center().movedBy(0, 200), 300, 60 };
	Transition m_rankingTransition{ 0.4s, 0.2s };
};
```

```cpp:Title.cpp
# include "Title.hpp"

Title::Title(const InitData& init)
	: IScene{ init } {}

void Title::update()
{
	m_startTransition.update(m_startButton.mouseOver());
	m_rankingTransition.update(m_rankingButton.mouseOver());
	m_exitTransition.update(m_exitButton.mouseOver());

	if (m_startButton.mouseOver() || m_rankingButton.mouseOver() || m_exitButton.mouseOver())
	{
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	if (m_startButton.leftClicked())
	{
		// ゲームシーンへ
		changeScene(State::Game);
	}
	else if (m_rankingButton.leftClicked())
	{
		// ランキングシーンへ
		changeScene(State::Ranking);
	}
	else if (m_exitButton.leftClicked())
	{
		// 終了
		System::Exit();
	}
}

void Title::draw() const
{
	Scene::SetBackground(ColorF{ 0.2, 0.8, 0.4 });

	FontAsset(U"TitleFont")(U"BREAKOUT")
		.drawAt(TextStyle::OutlineShadow(0.2, ColorF{ 0.2, 0.6, 0.2 }, Vec2{ 3, 3 }, ColorF{ 0.0, 0.5 }), 100, Vec2{ 400, 100 });

	m_startButton.draw(ColorF{ 1.0, m_startTransition.value() }).drawFrame(2);
	m_rankingButton.draw(ColorF{ 1.0, m_rankingTransition.value() }).drawFrame(2);
	m_exitButton.draw(ColorF{ 1.0, m_exitTransition.value() }).drawFrame(2);

	FontAsset(U"Menu")(U"PLAY").drawAt(m_startButton.center(), ColorF{ 0.25 });
	FontAsset(U"Menu")(U"RANKING").drawAt(m_rankingButton.center(), ColorF{ 0.25 });
	FontAsset(U"Menu")(U"EXIT").drawAt(m_exitButton.center(), ColorF{ 0.25 });
}
```

```cpp:Game.hpp
# pragma once
# include "Common.hpp"

// ゲームシーン
class Game : public App::Scene
{
public:

	Game(const InitData& init);

	void update() override;

	void draw() const override;

private:

	// ブロックのサイズ
	static constexpr Size BrickSize{ 40, 20 };

	// ボールの速さ
	static constexpr double Speed = 480.0;

	// ボールの速度
	Vec2 m_ballVelocity{ 0, -Speed };

	// ボール
	Circle m_ball{ 400, 400, 8 };

	// ブロックの配列
	Array<Rect> m_bricks;

	// 現在のゲームのスコア
	int32 m_score = 0;

	Rect getPaddle() const;
};
```

```cpp:Game.cpp
# include "Game.hpp"

Game::Game(const InitData& init)
	: IScene{ init }
{
	// 横 (Scene::Width() / blockSize.x) 個、縦 5 個のブロックを配列に追加する
	for (auto p : step(Size{ (Scene::Width() / BrickSize.x), 5 }))
	{
		m_bricks << Rect{ (p.x * BrickSize.x), (60 + p.y * BrickSize.y), BrickSize };
	}
}

void Game::update()
{
	// ボールを移動
	m_ball.moveBy(m_ballVelocity * Scene::DeltaTime());

	// ブロックを順にチェック
	for (auto it = m_bricks.begin(); it != m_bricks.end(); ++it)
	{
		// ブロックとボールが交差していたら
		if (it->intersects(m_ball))
		{
			// ボールの向きを反転する
			(it->bottom().intersects(m_ball) || it->top().intersects(m_ball)
				? m_ballVelocity.y : m_ballVelocity.x) *= -1;

			// ブロックを配列から削除（イテレータが無効になるので注意）
			m_bricks.erase(it);

			AudioAsset(U"Brick").playOneShot(0.5);

			// スコアを加算
			++m_score;

			// これ以上チェックしない
			break;
		}
	}

	// 天井にぶつかったらはね返る
	if (m_ball.y < 0 && m_ballVelocity.y < 0)
	{
		m_ballVelocity.y *= -1;
	}

	// 左右の壁にぶつかったらはね返る
	if ((m_ball.x < 0 && m_ballVelocity.x < 0)
		|| (Scene::Width() < m_ball.x && 0 < m_ballVelocity.x))
	{
		m_ballVelocity.x *= -1;
	}

	// パドルにあたったらはね返る
	if (const Rect paddle = getPaddle();
		(0 < m_ballVelocity.y) && paddle.intersects(m_ball))
	{
		// パドルの中心からの距離に応じてはね返る方向を変える
		m_ballVelocity = Vec2{ (m_ball.x - paddle.center().x) * 10, -m_ballVelocity.y }.setLength(Speed);
	}

	// 画面外に出るか、ブロックが無くなったら
	if ((Scene::Height() < m_ball.y) || m_bricks.isEmpty())
	{
		// ランキング画面へ
		changeScene(State::Ranking);

		getData().lastGameScore = m_score;
	}
}

void Game::draw() const
{
	Scene::SetBackground(ColorF{ 0.2 });

	// すべてのブロックを描画する
	for (const auto& brick : m_bricks)
	{
		brick.stretched(-1).draw(HSV{ brick.y - 40 });
	}

	// ボールを描く
	m_ball.draw();

	// パドルを描く
	getPaddle().draw();

	FontAsset(U"GameScore")(m_score).draw(10, 10);
}

Rect Game::getPaddle() const
{
	return{ Arg::center(Cursor::Pos().x, 500), 60, 10 };
}
```

```cpp:Ranking.hpp
# pragma once
# include "Common.hpp"

// ランキングシーン
class Ranking : public App::Scene
{
public:

	Ranking(const InitData& init);

	void update() override;

	void draw() const override;

private:

	static constexpr int32 RankingCount = 5;

	int32 m_rank = -1;
};
```

```cpp:Ranking.cpp
# include "Ranking.hpp"

Ranking::Ranking(const InitData& init)
	: IScene{ init }
{
	auto& data = getData();

	if (data.lastGameScore)
	{
		const int32 lastScore = *data.lastGameScore;

		// ランキングを再構成
		data.highScores << lastScore;
		data.highScores.rsort();
		data.highScores.resize(RankingCount);

		// ランクインしていたら m_rank に順位をセット
		for (int32 i = 0; i < RankingCount; ++i)
		{
			if (data.highScores[i] == lastScore)
			{
				m_rank = i;
				break;
			}
		}

		data.lastGameScore.reset();
	}
}

void Ranking::update()
{
	if (MouseL.down())
	{
		// タイトルシーンへ
		changeScene(State::Title);
	}
}

void Ranking::draw() const
{
	Scene::SetBackground(ColorF{ 0.4, 0.6, 0.9 });

	FontAsset(U"Ranking")(U"RANKING").drawAt(400, 60);

	const auto& data = getData();

	// ランキングを表示
	for (auto i : step(RankingCount))
	{
		const RectF rect{ 100, 120 + i * 80, 600, 80 };

		rect.draw(ColorF{ 1.0, 1.0 - i * 0.2 });

		FontAsset(U"Ranking")(data.highScores[i]).drawAt(rect.center(), ColorF{ 0.25 });

		// ランクインしていたら
		if (i == m_rank)
		{
			rect.stretched(Periodic::Triangle0_1(0.5s) * 10).drawFrame(10, ColorF{ 0.8, 0.6, 0.4 });
		}
	}
}
```
