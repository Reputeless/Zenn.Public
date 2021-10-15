---
title: "Tutorial 11 | GUI"
free: true
---

# 11. GUI
この章では、ボタンやスライダーなど簡単な GUI を作成する方法を学びます。

## 11.1 ボタン
ボタンの表示と入力の取得を実装するときは `SimpleGUI::Button()` 関数を使うと便利です。関数にはボタンのテキストや位置、幅、状態などを設定できます。`SimpleGUI::Button()` は自身が押されたときに `true` を返します。
![](/images/doc_v6/tutorial/11/1.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Red", Vec2{ 100, 100 }))
		{
			Scene::SetBackground(ColorF{ 0.8, 0.2, 0.2 });
		}

		if (SimpleGUI::Button(U"Green", Vec2{ 100, 150 }))
		{
			Scene::SetBackground(ColorF{ 0.2, 0.8, 0.2 });
		}

		if (SimpleGUI::Button(U"Blue", Vec2{ 100, 200 }))
		{
			Scene::SetBackground(ColorF{ 0.2, 0.2, 0.8 });
		}

		// ボタンの幅を 200px に指定
		if (SimpleGUI::Button(U"White", Vec2{ 100, 250 }, 200))
		{
			Scene::SetBackground(ColorF{ 0.9 });
		}

		if (SimpleGUI::Button(U"Black", Vec2{ 100, 300 }, 200))
		{
			Scene::SetBackground(ColorF{ 0.1 });
		}

		// ボタンを無効化
		if (SimpleGUI::Button(U"Gray", Vec2{ 100, 350 }, 200, false))
		{
			Scene::SetBackground(ColorF{ 0.5 });
		}

		// ボタンを無効化、ボタンの幅はテキストに合わせる
		if (SimpleGUI::Button(U"Yellow", Vec2{ 100, 400 }, unspecified, false))
		{
			Scene::SetBackground(ColorF{ 0.8, 0.8, 0.1 });
		}
	}
}
```


## 11.2 スライダー
スライダーの表示と値の取得を実装するときは `SimpleGUI::Slider()` 関数を使うと便利です。関数にはスライダーのテキストや位置、幅、値の範囲などを設定できます。テキストを持たない縦方向のスライダーは `SimpleGUI::VerticalSlider()` を使います。`SimpleGUI::Slider()` と `SimpleGUI::VerticalSlider()` は値が変更されたときに `true` を返します。
![](/images/doc_v6/tutorial/11/2.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	ColorF color1{ 1.0 };
	ColorF color2{ 1.0, 0.5, 0.0 };
	ColorF color3{ 0.2, 0.6, 0.9 };

	double value1 = 5.0;
	double value2 = 7.0;
	double value3 = 2.0;
	double value4 = 4.0;

	while (System::Update())
	{
		SimpleGUI::Slider(color1.r, Vec2{ 100, 40 });
		SimpleGUI::Slider(color1.g, Vec2{ 100, 80 });
		SimpleGUI::Slider(color1.b, Vec2{ 100, 120 });
		Circle{ 50, 100, 30 }.draw(color1);

		SimpleGUI::Slider(U"Red", color2.r, Vec2{ 100, 200 });
		SimpleGUI::Slider(U"Green", color2.g, Vec2{ 100, 240 });
		SimpleGUI::Slider(U"Blue", color2.b, Vec2{ 100, 280 });
		Circle{ 50, 260, 30 }.draw(color2);

		// スライダーの値を表示、ラベルの幅 100px, スライダーの幅 200px
		SimpleGUI::Slider(U"R {:.2f}"_fmt(color3.r), color3.r, Vec2{ 100, 360 }, 100, 200);
		SimpleGUI::Slider(U"G {:.2f}"_fmt(color3.g), color3.g, Vec2{ 100, 400 }, 100, 200);
		SimpleGUI::Slider(U"B {:.2f}"_fmt(color3.b), color3.b, Vec2{ 100, 440 }, 100, 200);
		Circle{ 50, 420, 30 }.draw(color3);

		// 値の範囲が 0.0～10.0
		SimpleGUI::Slider(U"{:.2f}"_fmt(value1), value1, 0.0, 10.0, Vec2{ 500, 40 }, 60, 150);

		// スライダーを無効化
		SimpleGUI::Slider(U"{:.2f}"_fmt(value2), value2, 0.0, 10.0, Vec2{ 500, 100 }, 60, 150, false);

		// 縦のスライダー
		SimpleGUI::VerticalSlider(value3, 0.0, 10.0, Vec2{ 500, 160 }, 200);
		SimpleGUI::VerticalSlider(value4, 0.0, 10.0, Vec2{ 560, 160 }, 200, false);
	}
}
```


## 11.3 チェックボックス
チェックボックスの表示と入力の取得を実装するときは `SimpleGUI::CheckBox()` 関数を使うと便利です。関数にはチェックボックスのテキストや位置、幅、状態などを設定できます。`SimpleGUI::CheckBox()` は値が変更されたときに `true` を返します。
![](/images/doc_v6/tutorial/11/3.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	bool checked0 = false;
	bool checked1 = true;
	bool checked2 = false;
	bool checked3 = false;
	bool checked4 = false;
	bool checked5 = false;

	while (System::Update())
	{
		SimpleGUI::CheckBox(checked0, U"Label0", Vec2{ 100, 40 });
		SimpleGUI::CheckBox(checked1, U"Label1", Vec2{ 100, 80 });
		SimpleGUI::CheckBox(checked2, U"Label2", Vec2{ 100, 120 });

		// 幅 200px
		SimpleGUI::CheckBox(checked3, U"Label3", Vec2{ 100, 180 }, 200 );

		// 無効化
		SimpleGUI::CheckBox(checked4, U"Label4", Vec2{ 100, 220 }, 200, false);

		// 幅はテキストに合わせる
		SimpleGUI::CheckBox(checked5, U"Label5", Vec2{ 100, 260 }, unspecified, false);
	}
}
```


## 11.4 ラジオボタン
ラジオボタンの表示と入力の取得を実装するときは `SimpleGUI::RadioButtons()` 関数を使うと便利です。関数にはラジオボタンのテキストや位置、幅、状態などを設定できます。`SimpleGUI::RadioButtons()` は値が変更されたときに `true` を返します。
![](/images/doc_v6/tutorial/11/4.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	size_t index0 = 0;
	size_t index1 = 2;
	size_t index2 = 0;
	size_t index3 = 1;
	size_t index4 = 0;

	const Array<String> options = { U"Red", U"Green", U"Blue" };
	constexpr std::array<ColorF, 3> colors = { ColorF{ 0.8, 0.2, 0.2 }, ColorF{ 0.2, 0.8, 0.2 }, ColorF{ 0.2, 0.2, 0.8 } };

	Scene::SetBackground(colors[index1]);

	while (System::Update())
	{
		SimpleGUI::RadioButtons(index0, { U"Option1", U"Option2", U"Option3" }, Vec2{ 100, 40 });

		// 選択肢を Array<String> で指定
		if (SimpleGUI::RadioButtons(index1, options, Vec2{ 100, 180 }))
		{
			Scene::SetBackground(colors[index1]);
		}

		// 幅 200px
		SimpleGUI::RadioButtons(index2, { U"A", U"B" }, Vec2{ 400, 40 }, 200);

		// 無効化
		SimpleGUI::RadioButtons(index3, { U"A", U"B" }, Vec2{ 400, 140 }, 200, false);

		// 幅はテキストに合わせる
		SimpleGUI::RadioButtons(index4, { U"A", U"B" }, Vec2{ 400, 240 }, unspecified, false);
	}
}
```


## 11.5 テキストボックス
テキストボックスを実装するときは `SimpleGUI::TextBox()` 関数を使うと便利です。関数にはテキストボックスの位置、幅、文字数の上限、状態などを設定できます。テキストは `TextEditState` 型のオブジェクトによって管理します。`SimpleGUI::TextBox()` はテキストが変更されたときに `true` を返します。
![](/images/doc_v6/tutorial/11/5.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	TextEditState te0;
	TextEditState te1;
	te1.text = U"Siv3D"; // デフォルトの文字列
	TextEditState te2;
	TextEditState te3;

	while (System::Update())
	{
		ClearPrint();
		Print << te0.active; // アクティブかどうか
		Print << te0.text; // 入力されたテキスト (String)

		SimpleGUI::TextBox(te0, Vec2{ 100, 140 });

		SimpleGUI::TextBox(te1, Vec2{ 100, 200 });

		if (SimpleGUI::Button(U"Clear", Vec2{ 320, 200 }))
		{
			// テキストを消去
			te1.clear();
		}

		// 幅 100px, 文字数を 4 文字までに制限
		SimpleGUI::TextBox(te2, Vec2{ 100, 260 }, 100, 4);

		// 無効化
		SimpleGUI::TextBox(te3, Vec2{ 100, 320 }, 100, 4, false);
	}
}
```

## 11.6 テキストボックスの入力を Tab キーで進める
`SimpleGUI::TextBox()` では、あるテキストボックスがアクティブな時、エンターキーや Tab キーを押したり、無関係な場所をクリックしたりすると、そのテキストボックスが非アクティブになります。

あるテキストボックスが非アクティブ化したときに、テキスト入力に `U'\t` が含まれていたかを `TextInput::GetRawInput().includes(U'\t')` で調べることで、次のテキストボックスへの移動を実現することができます。
![](/images/doc_v6/tutorial/11/6.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	TextEditState te0, te1;

	bool avtivateNextTextBox = false;

	while (System::Update())
	{
		// Tab キーの押下と同じフレームで次のテキストボックスをアクティブ化してしまうと
		// その Tab キーの押下でそのテキストボックスも非アクティブ化してしまうため、1 フレーム後にアクティブ化
		if (avtivateNextTextBox)
		{
			// テキストボックスをアクティブ化
			te1.active = true;

			avtivateNextTextBox = false;
		}

		const bool previous = te0.active;

		SimpleGUI::TextBox(te0, Vec2{ 100, 100 }, 200);

		// 非アクティブ化された
		if (previous && (te0.active == false))
		{
			// Tab キーが入力されていた場合、次のテキストボックスをアクティブ化するフラグを true に
			avtivateNextTextBox = TextInput::GetRawInput().includes(U'\t');
		}

		SimpleGUI::TextBox(te1, Vec2{ 100, 140 }, 200);
	}
}
```


## 11.7 カラーピッカー
カラーピッカーは `SimpleGUI::ColorPicker()` 関数を使うと便利です。関数にはカラーピッカーの位置、状態などを設定できます。`SimpleGUI::ColorPicker()` は色が変更されたときに `true` を返します。
![](/images/doc_v6/tutorial/11/7.gif)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	HSV color0 = Palette::Orange;
	HSV color1 = Palette::Skyblue;

	while (System::Update())
	{
		SimpleGUI::ColorPicker(color0, Vec2{ 100, 100 });
		Rect{ 100, 300, 100 }.draw(color0);

		SimpleGUI::ColorPicker(color1, Vec2{ 300, 100 }, false);
		Rect{ 300, 300, 100 }.draw(color1);
	}
}
```


## 11.8 見出し
GUI の各ウィジェットに見出しを付けたい場合、`SimpleGUI::Headline` を使うと便利です。関数には見出しの位置、幅、状態などを設定できます。ヘッドラインの高さは 40px です。
![](/images/doc_v6/tutorial/11/8.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	bool checked0 = false;
	bool checked1 = true;
	bool checked2 = false;
	HSV color = Palette::Orange;

	while (System::Update())
	{
		SimpleGUI::Headline(U"Checkbox", Vec2{ 100, 60 });
		SimpleGUI::CheckBox(checked0, U"Label0", Vec2{ 100, 100 }, 160);
		SimpleGUI::CheckBox(checked1, U"Label1", Vec2{ 100, 140 }, 160);
		SimpleGUI::CheckBox(checked2, U"Label2", Vec2{ 100, 180 }, 160);

		SimpleGUI::Headline(U"ColorPicker", Vec2{ 300, 60 }, 160, false);
		SimpleGUI::ColorPicker(color, Vec2{ 300, 100 }, false);
	}
}
```

