---
title: "チュートリアル 07 | あたり判定"
free: true
---

# 7. あたり判定
この章では、マウスカーソル vs 図形や、図形 vs 図形の交差判定を処理する方法を学びます。

## 7.1 マウスオーバー
ある図形 `shape` の領域にマウスカーソルが重なっているかを、`shape.mouseOver()` で調べられます。
![](/images/doc_v6/tutorial/7/1.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Circle circle{ Scene::Center(), 100 };

	while (System::Update())
	{
		if (circle.mouseOver())
		{
			// 円にマウスカーソルが重なっていれば水色
			circle.draw(Palette::Skyblue);
		}
		else
		{
			// 重なっていなければ灰色
			circle.draw(Palette::Gray);
		}
	}
}
```

このプログラムは条件演算子を使うともう少し短く書けます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Circle circle{ Scene::Center(), 100 };

	while (System::Update())
	{
		// 円にマウスカーソルが重なっていれば水色、そうでなければ灰色
		circle.draw(circle.mouseOver() ? Palette::Skyblue : Palette::Gray);
	}
}
```


## 7.2 図形のクリック
ある図形 `shape` が左クリック（またはタッチ）されたかを、`shape.leftClicked()` で調べられます。`.leftClicked()` は、クリックのアクションのうち、最初に押し込んだフレームのみ `true` を返します。図形を押し続けていてもそれ以降は `false` を返します。
![](/images/doc_v6/tutorial/7/2.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Circle circle{ Scene::Center(), 100 };

	int32 count = 0;

	while (System::Update())
	{
		ClearPrint();

		Print << count;

		// 円が左クリックされたら
		if (circle.leftClicked())
		{
			++count;
		}

		circle.draw(Palette::Gray);
	}
}
```


## 7.3 図形が押されている
ある図形 `shape` が左クリック（またはタッチ）されているかを、`shape.leftPressed()` で調べられます。`.leftPressed()` は、クリックのアクションのうち、最初に押し込んだフレームおよび、それ以降押され続けている場合に `true` を返します。
![](/images/doc_v6/tutorial/7/3.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Circle circle{ Scene::Center(), 100 };

	while (System::Update())
	{
		// 円が押されていれば水色、そうでなければ灰色
		circle.draw(circle.leftPressed() ? Palette::Skyblue : Palette::Gray);
	}
}
```


## 7.4 図形の交差
2 つの図形 `a` と `b` が交差しているかは、`a.intersects(b)` で調べられます。
![](/images/doc_v6/tutorial/7/4.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	constexpr Rect rect{ 100, 50, 200, 100 };

	constexpr Circle circle{ 200, 400, 100 };

	const Polygon star = Shape2D::Star(200, Vec2{ 550, 300 });

	while (System::Update())
	{
		const Circle c{ Cursor::Pos(), 30 };

		rect.draw(rect.intersects(c) ? Palette::Skyblue : Palette::Gray);

		circle.draw(circle.intersects(c) ? Palette::Skyblue : Palette::Gray);

		star.draw(star.intersects(c) ? Palette::Skyblue : Palette::Gray);

		c.draw(Palette::Seagreen);
	}
}
```


## 7.5 図形を内側に含む
ある図形 `a` が別の図形 `b` を完全に内側に含んでいるかは、`a.contains(b)` で調べられます。次のサンプルでは、マウスカーソルに追従する円が長方形や星などの図形の内部に完全に含まれているときに、その図形の色を変更します。
![](/images/doc_v6/tutorial/7/5.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	constexpr Rect rect{ 100, 50, 200, 100 };

	constexpr Circle circle{ 200, 400, 100 };

	const Polygon star = Shape2D::Star(200, Vec2{ 550, 300 });

	while (System::Update())
	{
		const Circle c{ Cursor::Pos(), 30 };

		rect.draw(rect.contains(c) ? Palette::Skyblue : Palette::Gray);

		circle.draw(circle.contains(c) ? Palette::Skyblue : Palette::Gray);

		star.draw(star.contains(c) ? Palette::Skyblue : Palette::Gray);

		c.draw(Palette::Seagreen);
	}
}
```


## 7.6 線分と交差する点
ある図形 `a` と `b` の交差位置を求めたい場合は、`a.intersectsAt(b)` を使うと、交差情報を `Optional<Array<Vec2>>` 型の値として得ることができます。図形が交差する場合に有効値をもち、その交差点を配列に格納しています。2 つの線分がピッタリオーバーラップするケースでは、有効値として空の配列を返すことがあります。
![](/images/doc_v6/tutorial/7/6.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	constexpr Rect rect{ 100, 50, 200, 100 };

	constexpr Circle circle{ 200, 400, 100 };

	constexpr Triangle triangle{ Vec2{ 500, 100 }, Vec2{ 700, 500 }, Vec2{ 400, 400 } };

	while (System::Update())
	{
		const Line line{ Scene::Center(), Cursor::Pos() };

		// rect と line の交差情報を取得
		if (const auto points = rect.intersectsAt(line))
		{
			rect.draw(Palette::Skyblue);

			// 交差する座標に赤い円を表示
			for (const auto& point : *points)
			{
				Circle{ point, 4 }.draw(Palette::Red);
			}
		}
		else // 交差しない
		{
			rect.draw(Palette::Gray);
		}

		// circle と line の交差情報を取得
		if (const auto points = circle.intersectsAt(line))
		{
			circle.draw(Palette::Skyblue);

			// 交差する座標に赤い円を表示
			for (const auto& point : *points)
			{
				Circle{ point, 4 }.draw(Palette::Red);
			}
		}
		else // 交差しない
		{
			circle.draw(Palette::Gray);
		}

		// triangle と line の交差情報を取得
		if (const auto points = triangle.intersectsAt(line))
		{
			triangle.draw(Palette::Skyblue);

			// 交差する座標に赤い円を表示
			for (const auto& point : *points)
			{
				Circle{ point, 4 }.draw(Palette::Red);
			}
		}
		else // 交差しない
		{
			triangle.draw(Palette::Gray);
		}

		line.draw(2, Palette::Seagreen);
	}
}
```


## 7.7 マウスカーソルのスタイル
マウスカーソルのスタイルを変更したいときは、`Cursor::RequestStyle()` を通して、変更したいカーソルのスタイルをリクエストします。手のアイコンにしたい場合は `CursorStyle::Hand` を、カーソルを非表示にしたい場合は `CursorStyle::Hidden` を指定します。マウスカーソルのリクエストは、そのフレームにのみ適用されるます。変更を維持したい場合は毎フレームにリクエストをし続ける必要があります。

| CursorStyle                  | カーソルの形状   |
|------------------------------|-----------|
| CursorStyle::Arrow           | 矢印（通常）    |
| CursorStyle::IBeam           | I マーク     |
| CursorStyle::Cross           | 十字のマーク    |
| CursorStyle::Hand            | 手のアイコン    |
| CursorStyle::NotAllowed      | 禁止のマーク    |
| CursorStyle::ResizeUpDown    | 上下のリサイズ   |
| CursorStyle::ResizeLeftRight | 左右のリサイズ   |
| CursorStyle::ResizeNWSE      | 左上-右下のリサイズ   |
| CursorStyle::ResizeNESW      | 右上-左下のリサイズ   |
| CursorStyle::ResizeAll       | 上下左右方向のリサイズ   |
| CursorStyle::Hidden          | 非表示       |
| CursorStyle::Default         | Arrow と同じ |

![](/images/doc_v6/tutorial/7/7.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	constexpr ColorF buttonColor{ 0.2, 0.6, 1.0 };
	constexpr Circle button{ 400, 300, 60 };
	Transition press{ 0.05s, 0.05s };

	while (System::Update())
	{
		const bool mouseOver = button.mouseOver();

		// 円の上にマウスカーソルがあれば
		if (mouseOver)
		{
			// マウスカーソルを手の形に
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		press.update(button.leftPressed());

		const double t = press.value();

		button.movedBy(Vec2{ 0, 0 }.lerp(Vec2{ 0, 4 }, t))
			.drawShadow(Vec2{ 0, 6 }.lerp(Vec2{ 0, 1 }, t), (12 - t * 7), (5 - t * 4))
			.draw(buttonColor);
	}
}
```


