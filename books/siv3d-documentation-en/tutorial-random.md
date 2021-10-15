---
title: "Tutorial 12 | Random"
free: true
---

# 12. Random
この章では、数や色、座標をランダムに生成したり、配列から無作為に要素を選択したりする方法を学びます。

Siv3D は乱数に関する次のような機能を提供します。

- 乱数を生成する様々な乱数エンジン
- 乱数を特定の分布に分散せる分布クラス
- それらを組み合わせた使いやすい乱数関数

Siv3D の乱数関数では、特に指定しない場合、Siv3D が内部で確保する乱数エンジンが使われます。内部の乱数エンジンはハードウェアノイズからシード値が決定され、実行するたびに異なる乱数列を生成します。

再現性が必要な場合は、内部の乱数エンジンに固定のシードを与えるか、乱数エンジンオブジェクトをコードで作成し、乱数関数に渡します。

## 12.1 ランダムな整数
`Random<Type>(max)` は 0 から max までのランダムな数を、`Random<Type>(min, max)` は min から　max の範囲でランダムな数を返します。
![](/images/doc_v6/tutorial/12/1.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"int32", Vec2{ 200, 20 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// 0～100 の範囲のランダムな整数
				Print << Random(100);
			}
		}

		if (SimpleGUI::Button(U"double", Vec2{ 200, 60 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// -100.0～100.0 の範囲のランダムな浮動小数点数
				Print << Random(-100.0, 100.0);
			}
		}

		if (SimpleGUI::Button(U"char32", Vec2{ 200, 100 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// A～Z の範囲のランダムな文字
				Print << Random(U'A', U'Z');
			}
		}
	}
}
```


## 12.2 デフォルトの乱数エンジンのシードを設定
`Reseed()` を使うと、内部の乱数エンジンを指定したシードでリセットできます。同じシードからは同じ乱数列が生成されます。
![](/images/doc_v6/tutorial/12/2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// シードを設定
	Reseed(123456789ull);

	for (int32 i = 0; i < 10; ++i)
	{
		Print << Random(100);
	}

	while (System::Update())
	{

	}
}
```


## 12.3 乱数エンジンを渡す
乱数エンジンオブジェクトを作成し、乱数関数に渡すと、内部の乱数エンジンの代わりにその乱数エンジンを使用して乱数を生成します。使用するたびに乱数エンジンの内部状態は更新されます。内部の乱数エンジンに依存したくないケースでこの方法を使います。
![](/images/doc_v6/tutorial/12/3.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 軽量な乱数エンジン
	SmallRNG rng1{ 123456789ull };

	for (int32 i = 0; i < 10; ++i)
	{
		Print << Random(1, 6, rng1);
	}

	while (System::Update())
	{

	}
}
```


## 12.4 半々の確率
`RandomBool()` は 50% の確率で `true`, 50% の確率で `false` を返します。
![](/images/doc_v6/tutorial/12/4.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// 50% の確率で true, 50% の確率で false
				Print << RandomBool();
			}
		}
	}
}
```


## 12.5 確率を指定
`RandomBool(p)` は、0.0 ～ 1.0 の範囲で true になる確率を指定できます。10% の確率で `true` を返してほしい場合は `0.1` を、25% の確率で `true` を返してほしい場合は `0.25` を渡します。
![](/images/doc_v6/tutorial/12/5.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// 30% の確率で true, 70% の確率で false
				Print << RandomBool(0.3);
			}
		}
	}
}
```


## 12.6 ランダムな色
`RandomColorF()` はランダムな色を `HSV{ Random(360.0), 1.0, 1.0 }` という式で生成して `ColorF` 型で返します。
![](/images/doc_v6/tutorial/12/6.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<ColorF> colors;

	for (int32 i = 0; i < 10; ++i)
	{
		// ランダムな色
		colors << RandomColor();
	}

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 20, 20 }))
		{
			for (auto& color : colors)
			{
				color = RandomColor();
			}
		}

		for (size_t i : step(colors.size()))
		{
			Circle{ (50 + i * 50.0), 100, 20 }.draw(colors[i]);
		}
	}
}
```


## 12.7 ランダムな座標
`RandomVec2()` は、条件に沿ったランダムな `Vec2` 型の値を返します。条件は引数として渡す値によって異なります。

| 関数 | 説明 |
|---|---|
|`RandomVec2()`|長さが 1.0 のランダムなベクトル|
|`RandomVec2(double)`|指定した長さを持つランダムなベクトル|
|`RandomVec2(const Line&)`|指定した線分上にあるランダムな点の位置ベクトル|
|`RandomVec2(const Circle&)`|指定した円内にあるランダムな点の位置ベクトル|
|`RandomVec2(const RectF&)`|指定した長方形内にあるランダムな点の位置ベクトル|
|`RandomVec2(const Triangle&)`|指定した三角形内にあるランダムな点の位置ベクトル|
|`RandomVec2(const Quad&)`|指定した四角形内にあるランダムな点の位置ベクトル|

![](/images/doc_v6/tutorial/12/7.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// Line や Circle, Triangle, Quad も OK
	constexpr RectF shape{ 100, 100, 400, 300 };

	Array<Vec2> points;

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Generate", Vec2{ 20, 20 }))
		{
			points.clear();

			for (int32 i = 0; i < 100; ++i)
			{
				// shape 内にあるランダムな点
				points << RandomVec2(shape);
			}
		}

		shape.draw(Palette::Gray);

		for (const auto& point : points)
		{
			Circle{ point, 3 }.draw();
		}
	}
}
```


## 12.8 配列中のランダムな要素
`Array` のメンバ関数 `.choice()` は、配列の中のランダムな要素を返します。空の配列に対して使うことはできません。
![](/images/doc_v6/tutorial/12/8.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<String> options =
	{
		U"Red", U"Green", U"Blue", U"Black", U"White"
	};

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
		{
			ClearPrint();

			// ランダムな要素を返す
			Print << options.choice();
		}
	}
}
```


## 12.9 配列中のランダムな複数の要素
`Array` の `.choice(N)` は、配列の中から重複なく N 個のランダムな要素を選択し、配列で返します。結果の配列の要素の順番は、元の配列内での順序と同じです。
![](/images/doc_v6/tutorial/12/9.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<int32> coins =
	{
		1, 2, 5, 10, 20, 50, 100
	};

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
		{
			ClearPrint();

			// ランダムな要素を 3 つ返す
			Print << coins.choice(3);
		}
	}
}
```


## 12.10 配列のシャッフル
`Array` の `.shuffle()` は配列の要素の順番をランダムにシャッフルします。一方 `.shuffled()` を使うと、自身は変更せずに、シャッフルした新しい配列を作成して返します。
![](/images/doc_v6/tutorial/12/10.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<String> cities =
	{
		U"Guangzhou", U"Tokyo", U"Shanghai", U"Jakarta",
		U"Delhi", U"Metro Manila", U"Mumbai", U"Seoul",
		U"Mexico City", U"São Paulo", U"New York City", U"Cairo",
	};

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Shuffle", Vec2{ 200, 100 }))
		{
			ClearPrint();

			// 要素の順番をシャッフル
			cities.shuffle();

			Print << cities;
		}
	}
}
```


## 12.11 ランダムに 1 個選択
`Sample()` を使うと、`{}` で渡した複数の選択肢から要素をランダムに 1 個選択できます。
![](/images/doc_v6/tutorial/12/11.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
		{
			ClearPrint();

			// ランダムに選択
			Print << Sample({ 1, 2, 5, 10, 20, 50, 100 });
		}
	}
}
```


## 12.12 ランダムに N 個選択
`Sample()` は第 1 引数に個数、第 2 引数に選択肢を渡すこともできます。`Array` の `.choice()` と同様に、結果の配列内の要素の順番は、元の順序と同じです。
![](/images/doc_v6/tutorial/12/12.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
    while (System::Update())
    {
        if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
        {
            ClearPrint();

            // ランダムに 3 個選択
            Print << Sample(3, { 1, 2, 5, 10, 20, 50, 100 });
        }
    }
}
```


## 12.13 出現確率
確率にバイアスがある複数の選択肢からランダムな結果を選択するときは `DiscreteSample` を使います。選択肢を配列で、選択肢の確率分布を `DiscreteDistribution` で準備しておく必要があります。確率分布は `double` 型の値で指定し、合計が特定の数になる必要はありません。`{1, 6, 3}` なら 10%, 60%, 30% と割り振られます。
![](/images/doc_v6/tutorial/12/13.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 選択肢
	const Array<String> options =
	{
		U"$0",
		U"$1",
		U"$5",
		U"$20",
		U"$100",
		U"$500",
		U"$2000",
	};

	// 選択肢に対応する確率分布
	// （$0 は $2000 よりも 1000 倍出やすい）
	DiscreteDistribution distribution(
	{
		1000,
		200,
		50,
		10,
		5,
		2,
		1,
	});

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// 確率分布に基づいてランダムに選択
				Print << DiscreteSample(options, distribution);
			}
		}
	}
}
```
