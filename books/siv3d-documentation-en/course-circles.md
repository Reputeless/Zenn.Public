---
title: "コース | 円を配置する"
free: true
---

クリックで画面上に円を配置していくプログラムを作ります。

https://youtu.be/Tq6O_d1cASQ

## 1. 基本のプログラム
左クリックされるたびに、`Array<Circle>` に `Circle` 型の要素を追加していきます。  
`Array<Circle>` の各要素には範囲 `for` でアクセスします。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 円の半径
	constexpr double circleR = 24.0;

	// 円の色
	constexpr ColorF circleColor{ 0.4, 0.8, 0.6 };

	// 円を格納する配列
	Array<Circle> circles;

	while (System::Update())
	{
		// もし左クリックされたら
		if (MouseL.down())
		{
			// 配列に円を追加
			circles << Circle{ Cursor::Pos(), circleR };
		}

		// 配列内の円を描画
		for (const auto& circle : circles)
		{
			circle.draw(circleColor);
		}
	}
}
```


## 2. 容量を節約
円の半径が定数の場合、`Circle` を格納する代わりに円の中心座標である `Vec2` を格納するだけでも大丈夫です。これで配列のメモリ消費量が 3 分の 2 になります (1 要素あたり 24 バイト → 16 バイト)。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 円の半径
	constexpr double circleR = 24.0;

	// 円の色
	constexpr ColorF circleColor{ 0.4, 0.8, 0.6 };

	// 円の中心座標を格納する配列
	Array<Vec2> circles;

	while (System::Update())
	{
		// もし左クリックされたら
		if (MouseL.down())
		{
			// 配列に円の中心座標を追加
			circles << Cursor::Pos();
		}

		// 配列内の円の中心座標を使って円を描画
		for (const auto& circle : circles)
		{
			Circle{ circle, circleR }.draw(circleColor);
		}
	}
}
```


## 3. シーン上のクリックに限る
ここまでのプログラムでは、ウィンドウを移動させようと、ウィンドウのタイトルバーをクリックしたときにも円が追加されてしまいます。  
これを避けたい場合、条件を `if (Scene::Rect().leftClicked())` にして、シーンの領域が左クリックされたときのみ、円を追加するようにします。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 円の半径
	constexpr double circleR = 24.0;

	// 円の色
	constexpr ColorF circleColor{ 0.4, 0.8, 0.6 };

	// 円の中心座標を格納する配列
	Array<Vec2> circles;

	while (System::Update())
	{
		// もしシーンが左クリックされたら
		if (Scene::Rect().leftClicked())
		{
			// 配列に円の中心座標を追加
			circles << Cursor::Pos();
		}

		// 配列内の円の中心座標を使って円を描画
		for (const auto& circle : circles)
		{
			Circle{ circle, circleR }.draw(circleColor);
		}
	}
}
```


## 4. 円ごとに色を変える
`Vec2` や `Circle` 型は色の情報を持てないので、位置の情報と合わせて `ColorF` も持つ新しい構造体 `Item` を作ります。

```cpp
# include <Siv3D.hpp>

struct Item
{
	// 円の半径 (定数）
	static constexpr double CircleR = 24.0;

	// 円の中心座標
	Vec2 center;

	// 円の色
	ColorF color;

	// デフォルトコンストラクタ
	Item() = default;

	// 座標、色を初期化するコンストラクタ
	Item(const Vec2& _center, const ColorF& _color)
		: center{ _center }
		, color{ _color } {}

	// 円を描画するメンバ関数
	void draw() const
	{
		Circle{ center, CircleR }.draw(color);
	}
};

void Main()
{
	// Item を格納する配列
	Array<Item> items;

	while (System::Update())
	{
		// もしシーンが左クリックされたら
		if (Scene::Rect().leftClicked())
		{
			// 配列に Item を追加
			items.emplace_back(Cursor::Pos(), RandomColorF());
		}

		// 配列内の Item を描画
		for (const auto& item : items)
		{
			item.draw();
		}
	}
}
```


## 5. Main を短く
`Main()` 関数内の記述を減らすために、管理用のクラス `ItemManager` を作ります。

```cpp
# include <Siv3D.hpp>

struct Item
{
	// 円の半径 (定数）
	static constexpr double CircleR = 24.0;

	// 円の中心座標
	Vec2 center;

	// 円の色
	ColorF color;

	// デフォルトコンストラクタ
	Item() = default;

	// 座標、色を初期化するコンストラクタ
	Item(const Vec2& _center, const ColorF& _color)
		: center{ _center }
		, color{ _color } {}

	// 円を描画するメンバ関数
	void draw() const
	{
		Circle{ center, CircleR }.draw(color);
	}
};

class ItemManager
{
public:

	// デフォルトコンストラクタ
	ItemManager() = default;

	// 新しい Item を追加するメンバ関数
	void add(const Vec2& center, const ColorF& color)
	{
		// 配列に Item を追加
		m_items.emplace_back(center, color);
	}

	// すべての Item を描画するメンバ関数
	void draw() const
	{
		// 配列内の Item を描画
		for (const auto& item : m_items)
		{
			item.draw();
		}
	}

private:

	// Item を格納する配列
	Array<Item> m_items;
};

void Main()
{
	ItemManager itemManager;

	while (System::Update())
	{
		// もしシーンが左クリックされたら
		if (Scene::Rect().leftClicked())
		{
			// 追加
			itemManager.add(Cursor::Pos(), RandomColorF());
		}

		// 描画
		itemManager.draw();
	}
}
```

## 6. エフェクトを付ける
円を追加したときにエフェクトを発生させます。

```cpp
# include <Siv3D.hpp>

struct Item
{
	// 円の半径 (定数）
	static constexpr double CircleR = 24.0;

	// 円の中心座標
	Vec2 center;

	// 円の色
	ColorF color;

	// デフォルトコンストラクタ
	Item() = default;

	// 座標、色を初期化するコンストラクタ
	Item(const Vec2& _center, const ColorF& _color)
		: center{ _center }
		, color{ _color } {}

	// 円を描画するメンバ関数
	void draw() const
	{
		Circle{ center, CircleR }.draw(color);
	}
};

class ItemManager
{
public:

	// デフォルトコンストラクタ
	ItemManager() = default;

	// 新しい Item を追加するメンバ関数
	void add(const Vec2& center, const ColorF& color)
	{
		// 配列に Item を追加
		m_items.emplace_back(center, color);

		// エフェクトを追加
		m_effect.add([center](double t)
		{
			const double e = EaseOutExpo(t);
			Circle{ center, (Item::CircleR + e * 30) }
				.drawFrame(0, 4 * e, ColorF{ 1.0, (1 - e) });
			return t < 1.0;
		});
	}

	// すべての Item とエフェクトを描画するメンバ関数
	void draw() const
	{
		// エフェクトを更新・描画
		m_effect.update();

		// 配列内の Item を描画
		for (const auto& item : m_items)
		{
			item.draw();
		}
	}

private:

	// Item を格納する配列
	Array<Item> m_items;

	// エフェクト
	Effect m_effect;
};

void Main()
{
	ItemManager itemManager;

	while (System::Update())
	{
		// もしシーンが左クリックされたら
		if (Scene::Rect().leftClicked())
		{
			// 追加
			itemManager.add(Cursor::Pos(), RandomColorF());
		}

		// 描画
		itemManager.draw();
	}
}
```
