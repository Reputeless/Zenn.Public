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


## 31.3 シーン間でデータを共有
ゲームのスコアの情報など、シーンをまたいで共有したいデータがある場合、そのデータ型を `SceneManager<>` の 2 つ目のテンプレート引数に追加します。そうすることで、各シーンの関数から `getData()` を通してそのデータにアクセスできるようになります。このデータはシーンマネージャーの作成時に 1 度だけ初期化されます。

```cpp
# include <Siv3D.hpp>

// 共有したいデータ
struct GameData
{
	int32 score = 0;
};

// 共有したいデータの型を指定
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
		// 左クリックで
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


## 31.4 フェードイン・フェードアウトの色や時間をカスタマイズする
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
		// 左クリックで
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


## 31.5

```cpp

```


## 31.6

```cpp

```


## 31.7

```cpp

```

