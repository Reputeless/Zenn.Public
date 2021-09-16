---
title: "サンプル集 | UI"
free: true
---

## 点線で囲まれた長方形

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

