---
title: "チュートリアル 16 | キーボード入力"
free: true
---

# 16. キーボード入力
この章では、キーボードの入力を処理する方法を学びます。

## 16.1 キーの入力状態
キーボードのキーには「Key～」と名付けらえた `Input` 型の値が割り当てられています。

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
| `.released()` | false | false | false | **✔ true** | false |

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


## 16.2 キーが押されていた時間

```cpp

```


## 16.3 キーの名前

```cpp

```



## 16.4 すべてのキー入力の取得

```cpp

```



## 16.5 複数のキーの組み合わせ

### 16.5.1 A または B

```cpp

```

### 16.5.2 A を押しながら B



## 16.6 キーコンフィグ

```cpp

```


## 16.7 テキスト入力

```cpp

```
