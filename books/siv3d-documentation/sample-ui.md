---
title: "サンプル集 | UI"
free: true
---

## 点線で囲まれた長方形

![](/images/doc_v6/sample/ui/dotted-box.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Point topleft{ 80, 60 };
	const double thickness = 3.0;
	double offset = 0.0;

	while (System::Update())
	{
		offset += (Scene::DeltaTime() * 10);

		const Rect rect{ topleft, Cursor::Pos() - topleft };
		rect.top().draw(LineStyle::SquareDot(offset), thickness);
		rect.right().draw(LineStyle::SquareDot(offset), thickness);
		rect.bottom().draw(LineStyle::SquareDot(offset), thickness);
		rect.left().draw(LineStyle::SquareDot(offset), thickness);
	}
}
```

## プルダウンメニュー

![](/images/doc_v6/sample/ui/pulldown.gif)
```cpp
# include <Siv3D.hpp>

class Pulldown
{
public:

	Pulldown() = default;

	Pulldown(const Array<String>& items, const Font& font, const Point& pos = { 0,0 })
		: m_font{ font }
		, m_items{ items }
		, m_rect{ pos, 0, (m_font.height() + m_padding.y * 2) }
	{
		for (const auto& item : m_items)
		{
			m_rect.w = Max(m_rect.w, static_cast<int32>(m_font(item).region().w));
		}

		m_rect.w += (m_padding.x * 2 + m_downButtonSize);
	}

	bool isEmpty() const
	{
		return m_items.empty();
	}

	void update()
	{
		if (isEmpty())
		{
			return;
		}

		if (m_rect.leftClicked())
		{
			m_isOpen = (not m_isOpen);
		}

		Point pos = m_rect.pos.movedBy(0, m_rect.h);

		if (m_isOpen)
		{
			for (auto i : step(m_items.size()))
			{
				if (const Rect rect{ pos, m_rect.w, m_rect.h };
					rect.leftClicked())
				{
					m_index = i;
					m_isOpen = false;
					break;
				}

				pos.y += m_rect.h;
			}
		}
	}

	void draw() const
	{
		m_rect.draw();

		if (isEmpty())
		{
			return;
		}

		m_rect.drawFrame(1, 0, m_isOpen ? Palette::Orange : Palette::Gray);

		Point pos = m_rect.pos;

		m_font(m_items[m_index]).draw(pos + m_padding, Palette::Black);

		Triangle{ (m_rect.x + m_rect.w - m_downButtonSize / 2.0 - m_padding.x), (m_rect.y + m_rect.h / 2.0),
			(m_downButtonSize * 0.5), 180_deg }.draw(Palette::Black);

		pos.y += m_rect.h;

		if (m_isOpen)
		{
			const Rect backRect{ pos, m_rect.w, (m_rect.h * m_items.size()) };

			backRect.drawShadow({ 1, 1 }, 4, 1).draw();

			for (const auto& item : m_items)
			{
				if (const Rect rect{ pos, m_rect.size };
					rect.mouseOver())
				{
					rect.draw(Palette::Skyblue);
				}

				m_font(item).draw((pos + m_padding), Palette::Black);

				pos.y += m_rect.h;
			}

			backRect.drawFrame(1, 0, Palette::Gray);
		}
	}

	void setPos(const Point& pos)
	{
		m_rect.setPos(pos);
	}

	const Rect& getRect() const
	{
		return m_rect;
	}

	size_t getIndex() const
	{
		return m_index;
	}

	String getItem() const
	{
		if (isEmpty())
		{
			return{};
		}

		return m_items[m_index];
	}

private:

	Font m_font;

	Array<String> m_items;

	size_t m_index = 0;

	Size m_padding{ 6, 2 };

	Rect m_rect;

	int32 m_downButtonSize = 16;

	bool m_isOpen = false;
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 24 };
	const Array<String> items = { U"日本語", U"English", U"中文", U"Español", U"Français" };
	Pulldown pulldown{ items, font, Point{ 40, 40 } };

	while (System::Update())
	{
		pulldown.update();
		pulldown.draw();
	}
}
```


## (Windows) トースト通知
Windows 版ではトースト通知を出すことができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.9, 0.6, 0.3 });

	// 通知ごとに割り振られる ID
	ToastNotificationID latest = -1;

	// 画像を作成・保存
	Emoji::CreateImage(U"🍕").save(U"pizza.png");

	while (System::Update())
	{
		ClearPrint();

		// 通知の状態
		Print << (int32)Platform::Windows::ToastNotification::GetState(latest);

		// アクションボタンの結果
		Print << U"Action: " << Platform::Windows::ToastNotification::GetAction(latest);

		if (SimpleGUI::Button(U"Send a notification", Vec2{ 10, 70 }))
		{
			ToastNotificationItem toast{
				.title = U"Title", // 通知のタイトル
				.message = U"Message", // 通知の本文
				.imagePath = U"pizza.png", // 大きい画像だと使われないことがある
				.actions = { U"Yes", U"No" } // アクションボタン（不要な場合は設定しない）
			};

			// 通知ごとに割り振られる ID を取得
			latest = Platform::Windows::ToastNotification::Show(toast);
		}
	}
}
```


## 手書き風 UI

https://youtu.be/K6BiJaeA71I

```cpp
# include <Siv3D.hpp>

struct Button
{
	Rect rect;
	String label;
};

class PenEffect
{
public:

	PenEffect() = default;

	explicit PenEffect(const Size& size)
		: m_texture{ size, ColorF{ 1.0, 0.0 } }
	{
		initLines();
	}

	void reset()
	{
		initLines();
		m_texture.clear(ColorF{ 1.0, 0.0 });
		m_texture.resolve();
	}

	void update(double delta)
	{
		m_accumulatedLength = Min(m_accumulatedLength + (m_length * delta), m_length);

		if ((4.0 <= (m_accumulatedLength - m_paintedLength)))
		{
			BlendState bs = BlendState::Default2D;
			bs.srcAlpha = Blend::SrcAlpha;
			bs.dstAlpha = Blend::DestAlpha;
			bs.opAlpha = BlendOp::Max;
			const ScopedRenderStates2D blend(bs);
			const ScopedRenderTarget2D target{ m_texture };

			while (4.0 <= (m_accumulatedLength - m_paintedLength))
			{
				m_lines.calculatePointFromOrigin(m_paintedLength)
					.asCircle(6).draw(ColorF{ 1.0 });

				m_paintedLength += 4.0;
			}

			Graphics2D::Flush();
			m_texture.resolve();
		}
	}

	const Texture& getTexture() const
	{
		return m_texture;
	}

private:

	void initLines()
	{
		m_lines.clear();

		const Size size = m_texture.size();

		Point penPos{ 8, (size.y - Random(8, 24)) };

		for (;;)
		{
			m_lines << penPos;
			penPos.x += Random(18, 28);
			penPos.y = Random(6, 20);

			if ((size.x - 8) < penPos.x)
			{
				break;
			}

			m_lines << penPos;
			penPos.x -= Random(8, 16);
			penPos.y = size.y - Random(6, 20);
		}

		m_length = m_lines.calculateLength();
		m_accumulatedLength = 0.0;
		m_paintedLength = 0.0;
	}

	MSRenderTexture m_texture;

	LineString m_lines;

	double m_length = 0.0;

	double m_accumulatedLength = 0.0;

	double m_paintedLength = 0.0;
};

void Main()
{
	const ColorF backgroundColor{ 1.0, 0.98, 0.96 };
	Scene::SetBackground(backgroundColor);

	const Array<Button> buttons =
	{
		Button{ Rect{ Arg::center(400, 300), 300, 80 }, U"あそぶ" },
		Button{ Rect{ Arg::center(400, 400), 300, 80 }, U"スコア" },
		Button{ Rect{ Arg::center(400, 500), 300, 80 }, U"おわる" },
	};

	const Font font{ FontMethod::MSDF, 40, Typeface::Bold };

	Array<PenEffect> penEffects =
	{
		PenEffect{ Size{300, 90} },
		PenEffect{ Size{300, 90} },
		PenEffect{ Size{300, 90} }
	};

	Optional<size_t> selectedItem;
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Restart", Vec2{ 20,20 }))
		{
			for (auto& penEffect : penEffects)
			{
				penEffect.reset();
			}

			stopwatch.restart();
		}

		for (auto [i, penEffect] : Indexed(penEffects))
		{
			if ((i * 250) < stopwatch.ms())
			{
				penEffects[i].update(Scene::DeltaTime() * 0.5);
			}
		}

		selectedItem.reset();
		
		for (auto [i, button] : Indexed(buttons))
		{
			if (button.rect.mouseOver())
			{
				selectedItem = i;
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			const bool selected = (selectedItem == i);

			penEffects[i].getTexture().drawAt(button.rect.center(), HSV{ 30 + i * 60});

			font(button.label)
				.drawAt(TextStyle::OutlineShadow(0.3, HSV{ backgroundColor } - HSV{0.0, 0.0, 0.5}, Vec2{0, 0}, backgroundColor),
					(selected ? 48 : 40), button.rect.center(), ColorF{ 1.0, 0.0 });
		}
	}
}
```
