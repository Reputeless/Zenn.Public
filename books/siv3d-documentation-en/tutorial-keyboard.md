---
title: "チュートリアル 16 | キーボード入力"
free: true
---

# 16. キーボード入力
この章では、キーボードの入力を処理する方法を学びます。

## 16.1 キーの入力状態を調べる
キーボードのキーには「Key～」と名付けられた `Input` 型の値が割り当てられています。

- A, B, C, ... は `KeyA`, `KeyB`, `KeyC` , ...
- 1, 2, 3, ... は `Key1`, `Key2`, `Key3`, ...
- F1, F2, F3, ... は `KeyF1`, `KeyF2`, `KeyF3`, ...
- ↑, ↓, ←, → は `KeyUp`, `KeyDown`, `KeyLeft`, `KeyRight`
- スペースキーは `KeySpace`
- エンターキーは `KeyEnter`
- バックスペースキーは `KeyBackspace`
- Tab キーは `KeyTab`
- Esc キーは `KeyEscape`
- PageUp, PageDown は `KeyPageUp`, `KeyPageDown`
- Delete キーは `KeyDelete`
- Numpad の 0, 1, 2, ... は `KeyNum0`, `KeyNum1`, `KeyNum2`, ...
- シフトキーは `KeyShift`
- 左シフトキー、右シフトキーは `KeyLShift`, `KeyRShift`
- コントロールキーは `KeyControl`
- (macOS) コマンドキーは `KeyCommand`
- 「,」「.」「/」キーは `KeyComma`, `KeyPeriod`, `KeySlash`
- 上記以外のキーは [`<Siv3D/Keyboard.hpp>`](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Keyboard.hpp) を参照

`Input` 型の値はメンバ関数を持ち、押された瞬間であるかを `.down()`, 押されているかを `.pressed()`, 離された瞬間であるかを `.up()` を使って `bool` 値で取得できます。

| 関数 | 押していないとき | 押した瞬間 | 押され続けている | 離した瞬間 | 離され続けている |
|:--:|:--:|:--:|:--:|:--:|:--:|
| `.down()` | false | **✔ true** | false | false | false |
| `.pressed()` | false | **✔ true** | **✔ true** | false | false |
| `.up()` | false | false | false | **✔ true** | false |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Vec2 pos = Scene::Center();

	while (System::Update())
	{
		const double delta = (Scene::DeltaTime() * 200);

		// 上下左右キーで移動
		if (KeyLeft.pressed())
		{
			pos.x -= delta;
		}

		if (KeyRight.pressed())
		{
			pos.x += delta;
		}

		if (KeyUp.pressed())
		{
			pos.y -= delta;
		}

		if (KeyDown.pressed())
		{
			pos.y += delta;
		}

		// [C] キーが押されたら中央に戻る
		if (KeyC.down())
		{
			pos = Scene::Center();
		}

		Circle{ pos, 50 }.draw();
	}
}
```


## 16.2 キーが押されていた時間を調べる
`Input` の `.pressedDuration()` は、その入力が押され続けている時間を `Duration` 型の値で返します。

押され続けている時間は `.up()` が `true` になるフレームまで有効です。`.up()` されたときに `.pressedDuration()` を調べると、そのキーが離されるまで何秒間押され続けていたかを取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << KeyA.pressedDuration();

		if (1s <= KeySpace.pressedDuration())
		{
			Print << U"Space";
		}
	}
}
```


## 16.3 キーの名前を取得する
`Input` の `.name()` は、そのキーの名前を `String` 型の値で返します。

![](/images/doc_v6/tutorial/16/3.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << KeyA.name();
	Print << KeySpace.name();
	Print << KeyLeft.name();
	Print << Key3.name();
	Print << KeyF11.name();

	while (System::Update())
	{

	}
}
```


## 16.4 すべてのキー入力を取得する
`Keyboard::GetAllInputs()` は、`.down()`, `.pressed()`, `.up()` のいずれかが `true` になっている、アクティブなキーの一覧を `Array<Input>` で返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// down() / pressed() / up() のいずれかが true になっているキー一覧を取得
		const Array<Input> keys = Keyboard::GetAllInputs();

		for (const auto& key : keys)
		{
			Print << key.name() << (key.pressed() ? U" pressed" : U" up");
		}
	}
}
```


## 16.5 複数のキーの組み合わせ

### 16.5.1 A または B
`|` を使って複数のキーを組み合わせると、そのいずれかが押されているかどうかを判定できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// [スペース] または [エンター] が押されている
		if ((KeySpace | KeyEnter).pressed())
		{
			Print << U"KeySpace / KeyEnter";
		}
	}
}
```

### 16.5.2 A を押しながら B
`+` を使って 2 つのキーを組み合わせると、左のキーが押されながら、右のキーが押されたかどうかを判定できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// [Ctrl + C] または [Command + C] が押された
		if ((KeyControl + KeyC).down()
			|| (KeyCommand + KeyC).down())
		{
			Print << U"Ctrl + C / Command + C";
		}
	}
}
```


## 16.6 キーコンフィグ
`InputGroup` 型は `Input` や、`Input` の `|`, `+` による組み合わせを格納できます。これを応用することで、次のようなキーコンフィグを簡単に実現できます。 

![](/images/doc_v6/tutorial/16/6.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	InputGroup inputLeft = KeyLeft;
	InputGroup inputRight = KeyRight;
	InputGroup inputJump = KeySpace;
	size_t index = 0;

	const Array<String> options
	{
		U"[←] [→] [Space]",
		U"[A] [D] [W]",
		U"[←]/[A] [→][D] [Space]/[W]"
	};

	const Texture texture{ U"🐥"_emoji };
	Vec2 pos{ 400, 450 };
	double jumpY = 0.0;

	while (System::Update())
	{
		// 🐥 の移動
		{
			const double deltaTime = Scene::DeltaTime();

			if (inputLeft.pressed())
			{
				pos.x -= (deltaTime * 200);
			}

			if (inputRight.pressed())
			{
				pos.x += (deltaTime * 200);
			}

			if (inputJump.down())
			{
				jumpY = 500.0;
			}

			pos.y = Min(pos.y - deltaTime * jumpY, 450.0);
			jumpY = Max(jumpY - deltaTime * 1000.0, -1000.0);
		}

		// 背景と 🐥 の描画
		{
			Rect{ 800, 500 }
			.draw(Arg::top = ColorF{ 0.1, 0.4, 0.8 }, Arg::bottom = ColorF{ 0.4, 0.7, 1.0 });
			Rect{ 0, 500, 800, 100 }
			.draw(ColorF{ 0.2, 0.5, 0.3 });

			texture.drawAt(pos);
		}

		// キーコンフィグ
		if (SimpleGUI::RadioButtons(index, options, Vec2{ 20, 20 }))
		{
			if (index == 0)
			{
				inputLeft = KeyLeft;
				inputRight = KeyRight;
				inputJump = KeySpace;
			}
			else if (index == 1)
			{
				inputLeft = KeyA;
				inputRight = KeyD;
				inputJump = KeyW;
			}
			else
			{
				inputLeft = (KeyLeft | KeyA);
				inputRight = (KeyRight | KeyD);
				inputJump = (KeySpace | KeyW);
			}
		}
	}
}
```


## 16.7 テキスト入力
`TextInput::UpdateText()` に `String` 型の変数を渡すことで、テキスト入力を処理できます。`TextInput::GetEditingText()` は未変換の文字入力を取得できます。

![](/images/doc_v6/tutorial/16/7.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 30 };

	String text;

	constexpr Rect area{ 50, 50, 700, 300 };

	while (System::Update())
	{
		// キーボードからテキストを入力
		TextInput::UpdateText(text);

		// 未変換の文字入力を取得
		const String editingText = TextInput::GetEditingText();

		area.draw(ColorF{ 0.3 });

		font(text + U'|' + editingText).draw(area.stretched(-20));
	}
}
```
