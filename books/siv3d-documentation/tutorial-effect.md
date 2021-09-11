---
title: "チュートリアル 22 | エフェクト"
free: true
---

# 22. エフェクト
この章では、ちょっとしたアニメーションやエフェクトの演出に便利な `Effect` クラスの使い方を学びます。

## 22.1 Effect の基本
エフェクト機能を使うには、エフェクトを管理する `Effect` オブジェクトを作成し、`Effect::add<EffectType>()` で個々のエフェクトのパラメータを追加してエフェクトを発生させます。ここでいう `EffectType` は、`IEffect` を継承したクラスです。このクラスに必要な実装は `bool update(double t) override` メンバ関数です。

この関数は、エフェクトが発生してからの経過時間 `t` を受け取り、それに応じたエフェクトの描画を行います。そして、戻り値として、エフェクトを次のフレームも継続させるかを `bool` 値で返します。例えば `return (t < 3.0);` とすれば、エフェクトは 3 秒間継続します。

`Effect` は、時間ベースのアニメーションを簡単に作れるため、ゲームの演出などで重宝します。次のプログラムは、クリックした場所に、1 秒間、時間とともに大きくなる輪を発生させるエフェクトを実装したものです。

```cpp
# include <Siv3D.hpp>

struct RingEffect : IEffect
{
	Vec2 m_pos;

	ColorF m_color;

	// このコンストラクタ引数が、Effect::add<RingEffect>() の引数になる
	explicit RingEffect(const Vec2& pos)
		: m_pos{ pos }
		, m_color{ RandomColorF() } {}

	bool update(double t) override
	{
		// 時間に応じて大きくなる輪
		Circle{ m_pos, (t * 100) }.drawFrame(4, m_color);

		// 1 秒未満なら継続
		return (t < 1.0);
	}
};

void Main()
{
	Effect effect;

	while (System::Update())
	{
		ClearPrint();

		// アクティブなエフェクトの数
		Print << U"Active effects: {}"_fmt(effect.num_effects());

		if (MouseL.down())
		{
			// エフェクトを発生
			effect.add<RingEffect>(Cursor::Pos());
		}

		// アクティブなエフェクトのプログラム IEffect::update() を実行
		effect.update();
	}
}
```


## 22.2 ラムダ式でエフェクトを実装する
`IEffect` の派生クラスの代わりに、引数が `double`, 戻り値が `bool` 型のラムダ式でエフェクトを記述することもできます。数行で書ける単純なエフェクトで、わざわざクラスを定義するのが面倒な場合はこの方法が便利です。

22.1 と同じエフェクトのプログラムをラムダ式で書くと、次のようになります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Effect effect;

	while (System::Update())
	{
		ClearPrint();

		// アクティブなエフェクトの数
		Print << U"Active effects: {}"_fmt(effect.num_effects());

		if (MouseL.down())
		{
			// エフェクトを発生
			effect.add([pos = Cursor::Pos(), color = RandomColorF()](double t)
			{
				// 時間に応じて大きくなる輪
				Circle{ pos, (t * 100) }.drawFrame(4, color);

				// 1 秒未満なら継続
				return (t < 1.0);
			});
		}

		// アクティブなエフェクトのプログラム IEffect::update() を実行
		effect.update();
	}
}
```


## 22.3 イージングをエフェクトで使う
チュートリアル 03 に出てきたイージングと組み合わせると、洗練された動きを作れます。


```cpp
# include <Siv3D.hpp>

struct RingEffect : IEffect
{
	Vec2 m_pos;

	ColorF m_color;

	explicit RingEffect(const Vec2& pos)
		: m_pos{ pos }
		, m_color{ RandomColorF() } {}

	bool update(double t) override
	{
		// イージング
		const double e = EaseOutExpo(t);

		Circle{ m_pos, (e * 100) }.drawFrame((20.0 * (1.0 - e)), m_color);

		return (t < 1.0);
	}
};

void Main()
{
	Effect effect;

	while (System::Update())
	{
		ClearPrint();

		Print << U"Active effects: {}"_fmt(effect.num_effects());

		if (MouseL.down())
		{
			effect.add<RingEffect>(Cursor::Pos());
		}

		effect.update();
	}
}
```


## 22.4 （サンプル）上昇する文字
フォントを使ったエフェクトの例です。

```cpp
# include <Siv3D.hpp>

struct ScoreEffect : IEffect
{
	Vec2 m_start;

	int32 m_score;

	Font m_font;

	ScoreEffect(const Vec2& start, int32 score, const Font& font)
		: m_start{ start }
		, m_score{ score }
		, m_font{ font } {}

	bool update(double t) override
	{
		const HSV color{ (180 - m_score * 1.8), 1.0 - (t * 2.0) };

		m_font(m_score).drawAt(m_start.movedBy(0, t * -120), color);

		return (t < 0.5);
	}
};

void Main()
{
	const Font font{ 50, Typeface::Heavy };

	Effect effect;

	while (System::Update())
	{
		if (MouseL.down())
		{
			effect.add<ScoreEffect>(Cursor::Pos(), Random(0, 100), font);
		}

		effect.update();
	}
}
```


## 22.5 （サンプル）飛び散る破片
一つのエフェクトで複数の図形を描く例です。

```cpp
# include <Siv3D.hpp>

struct Particle
{
	Vec2 start;

	Vec2 velocity;
};

struct Spark : IEffect
{
	Array<Particle> m_particles;

	explicit Spark(const Vec2& start)
		: m_particles(50)
	{
		for (auto& particle : m_particles)
		{
			particle.start = start + RandomVec2(10.0);

			particle.velocity = RandomVec2(1.0) * Random(80.0);
		}
	}

	bool update(double t) override
	{
		for (const auto& particle : m_particles)
		{
			const Vec2 pos = particle.start
				+ particle.velocity * t + 0.5 * t * t * Vec2{ 0, 240 };

			Triangle{ pos, 16.0, (pos.x * 5_deg) }.draw(HSV{ pos.y - 40, (1.0 - t) });
		}

		return (t < 1.0);
	}
};

void Main()
{
	Effect effect;

	while (System::Update())
	{
		if (MouseL.down())
		{
			effect.add<Spark>(Cursor::Pos());
		}

		effect.update();
	}
}
```


## 22.6 （サンプル）飛び散る星
複雑な制御を行うエフェクトの例です。

```cpp
# include <Siv3D.hpp>

struct StarEffect : IEffect
{
	static constexpr Vec2 Gravity{ 0, 160 };

	struct Star
	{
		Vec2 start;
		Vec2 velocity;
		ColorF color;
	};

	Array<Star> m_stars;

	StarEffect(const Vec2& pos, double baseHue)
	{
		for (int32 i = 0; i < 6; ++i)
		{
			const Vec2 velocity = RandomVec2(Circle{ 60 });
			Star star{
				.start = (pos + velocity),
				.velocity = velocity,
				.color = HSV{ baseHue + Random(-20.0, 20.0) },
			};
			m_stars << star;
		}
	}

	bool update(double t) override
	{
		t /= 0.4;

		for (auto& star : m_stars)
		{
			const Vec2 pos = star.start
				+ star.velocity * t + 0.5 * t * t * Gravity;

			const double angle = (pos.x * 3_deg);

			Shape2D::Star((30 * (1.0 - t)), pos, angle)
				.draw(star.color);
		}

		return (t < 1.0);
	}
};

void Main()
{
	Effect effect;
	Circle circle{ Scene::Center(), 30 };
	double baseHue = 180.0;

	while (System::Update())
	{
		if (circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		if (circle.leftClicked())
		{
			effect.add<StarEffect>(Cursor::PosF(), baseHue);
			circle.center = RandomVec2(Scene::Rect().stretched(-80));
			baseHue = Random(0.0, 360.0);
		}

		circle.draw(HSV{ baseHue });
		effect.update();
	}
}
```


## 22.7 （サンプル）泡のようなエフェクト
時間差で図形を登場させる、高度な制御を行うエフェクトの例です。

```cpp
# include <Siv3D.hpp>

struct BubbleEffect : IEffect
{
	struct Bubble
	{
		Vec2 offset;
		double startTime;
		double scale;
		ColorF color;
	};

	Vec2 m_pos;

	Array<Bubble> m_bubbles;

	BubbleEffect(const Vec2& pos, double baseHue)
		: m_pos{ pos }
	{
		for (int32 i = 0; i < 8; ++i)
		{
			Bubble bubble{
				.offset = RandomVec2(Circle{30}),
				.startTime = Random(-0.3, 0.1), // 登場の時間差
				.scale = Random(0.1, 1.2),
				.color = HSV{ baseHue + Random(-30.0, 30.0) }
			};
			m_bubbles << bubble;
		}
	}

	bool update(double t) override
	{
		for (const auto& bubble : m_bubbles)
		{
			const double t2 = (bubble.startTime + t);

			if (not InRange(t2, 0.0, 1.0))
			{
				continue;
			}

			const double e = EaseOutExpo(t2);

			Circle{ (m_pos + bubble.offset + (bubble.offset * 4 * t)), (e * 40 * bubble.scale) }
				.draw(ColorF{ bubble.color, 0.15 })
				.drawFrame((30.0 * (1.0 - e) * bubble.scale), bubble.color);
		}

		return (t < 1.3);
	}
};

void Main()
{
	Effect effect;

	while (System::Update())
	{
		if (MouseL.down())
		{
			effect.add<BubbleEffect>(Cursor::PosF(), Random(0.0, 360.0));
		}

		{
			const ScopedRenderStates2D blend{ BlendState::Additive };
			effect.update();
		}
	}
}
```


## 22.8 （サンプル）クリック時のエフェクト
1 つのエフェクトでたくさんの描画を行う例です。

```cpp
# include <Siv3D.hpp>

struct TouchEffect : IEffect
{
	struct Particle
	{
		Vec2 velocity;
		Vec2 start;
		double r;
		double angle;
		bool cw;
		ColorF color;
	};

	struct Star
	{
		Vec2 velocity;
		Vec2 start;
		double angle;
		double scale;
		ColorF color;
	};

	Vec2 m_pos;

	Array<Particle> m_particles;

	Array<Star> m_stars;

	explicit TouchEffect(const Vec2& pos)
		: m_pos{ pos }
	{
		for (int32 i = 0; i < 200; ++i)
		{
			const Vec2 velocoty = RandomVec2(28.0);
			Particle particle{
				.velocity = velocoty,
				.start = velocoty,
				.r = Random(6.0, 12.0),
				.angle = Random(360_deg),
				.cw = RandomBool(),
				.color = HSV{ Random(50.0, 70.0), 0.4, 1.0 },
			};
			m_particles << particle;
		}

		for (int32 i = 0; i < 8; ++i)
		{
			const Vec2 velocoty = RandomVec2(28.0);
			Star star{
				.velocity = velocoty,
				.start = (velocoty + RandomVec2(2.0)),
				.angle = Random(360_deg),
				.scale = Random(0.6, 1.4),
				.color = HSV{ Random(50.0, 70.0), 0.4, 1.0 },
			};
			m_stars << star;
		}
	}

	bool update(double t) override
	{
		t /= 0.45;

		const double r = (30 + t * 30);
		const ColorF outer = HSV{ 180, 0.8, 1.0, 0.0 };
		const ColorF inner = HSV{ 180, 0.8, 1.0, (0.5 * (1.0 - t)) };

		Circle{ m_pos, r }
			.drawFrame(10, 0, outer, inner)
			.drawFrame(0, 10, inner, outer);

		for (const auto& particle : m_particles)
		{
			const Vec2 pos = m_pos
				+ particle.start
				+ Circular(particle.r, particle.angle + t * 120_deg * (particle.cw ? 1 : -1))
				+ (particle.velocity * t - 0.5 * t * t * particle.velocity);
			const double rOuter = (1.0 * (1.0 - t) * 2);
			const double rInner = (0.8 * (1.0 - t) * 2);

			Shape2D::NStar(2, rOuter, rInner, pos, particle.angle)
				.draw(particle.color);
		}

		for (const auto& star : m_stars)
		{
			const Vec2 pos = m_pos
				+ star.start
				+ (star.velocity * t - 0.5 * t * t * star.velocity);
			const double rOuter = (12 * (1.0 - t) * star.scale);
			const double rInner = (4 * (1.0 - t) * star.scale);
			const double angle = (star.angle + t * 90_deg);

			Shape2D::NStar(4, rOuter, rInner, pos, angle)
				.draw(star.color);
		}

		return (t < 1.0);
	}
};

void Main()
{
	Effect effect;

	while (System::Update())
	{
		if (MouseL.down())
		{
			effect.add<TouchEffect>(Cursor::PosF());
		}

		{
			const ScopedRenderStates2D blend{ BlendState::Additive };
			effect.update();
		}
	}
}
```


## 22.9 エフェクトの一時停止と速度変更、消去
`Effect` の `.pause()` でエフェクトの更新を一時停止、`.resume()` 再開、`.setSpeed(double)` でスピードの変更、`.clear()` でアクティブなエフェクトをすべて消去できます。


```cpp
# include <Siv3D.hpp>

struct RingEffect : IEffect
{
	Vec2 m_pos;

	explicit RingEffect(const Vec2& pos)
		: m_pos{ pos } {}

	bool update(double t) override
	{
		Circle{ m_pos, (t * 100) }.drawFrame(4);

		return (t < 1.0);
	}
};

void Main()
{
	Effect effect;

	// 出現間隔（秒）
	constexpr double spawnTime = 0.15;

	// 蓄積された時間（秒）
	double accumulator = 0.0;

	while (System::Update())
	{
		ClearPrint();
		Print << U"Active effects: {}"_fmt(effect.num_effects());
		Print << U"speed: {}"_fmt(effect.getSpeed());

		if (not effect.isPaused())
		{
			accumulator += (Scene::DeltaTime() * effect.getSpeed());
		}

		// 蓄積時間が出現間隔を超えたら
		if (spawnTime <= accumulator)
		{
			accumulator -= spawnTime;

			effect.add<RingEffect>(Cursor::Pos());
		}

		effect.update();

		if (effect.isPaused())
		{
			if (SimpleGUI::Button(U"Resume", Vec2{ 600, 20 }, 100))
			{
				// エフェクトの更新を再開
				effect.resume();
			}
		}
		else
		{
			if (SimpleGUI::Button(U"Pause", Vec2{ 600, 20 }, 100))
			{
				// エフェクトの更新を一時停止
				effect.pause();
			}
		}

		if (SimpleGUI::Button(U"x2.0", Vec2{ 600, 60 }, 100))
		{
			// 2.0 倍速に
			effect.setSpeed(2.0);
		}

		if (SimpleGUI::Button(U"x1.0", Vec2{ 600, 100 }, 100))
		{
			// 1.0 倍速に
			effect.setSpeed(1.0);
		}

		if (SimpleGUI::Button(U"x0.5", Vec2{ 600, 140 }, 100))
		{
			// 0.5 倍速に
			effect.setSpeed(0.5);
		}

		if (SimpleGUI::Button(U"Clear", Vec2{ 600, 180 }, 100))
		{
			// 発生中のエフェクトをすべて消去
			effect.clear();
		}
	}
}
```

この章では扱いませんが、より大量のパーティクルを効率的に制御したい場合は、`ParticleSystem2D` を使うと便利です。
