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


## 22.3 

```cpp

```


## 22.4 

```cpp

```


## 22.5 

```cpp

```


## 22.6 

```cpp

```


この章では扱いませんが、より大量のパーティクルを効率的に制御したい場合は、`ParticleSystem2D` を使うと便利です。
