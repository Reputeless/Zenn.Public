---
title: "チュートリアル 17 | マウス入力"
free: true
---

# 17. マウス入力
この章では、マウスの入力を処理する方法を学びます。

## 17.1 マウスカーソルの座標を取得する
マウスカーソルの座標は `Cursor::Pos()` を使うと `Point` 型で取得できます。シーンが実ウィンドウサイズと異なる (チュートリアル 15 参照) 場合、`Cursor::PosF()` を使うと `Vec2` 型で小数点数以下の座標も取得できます。

`Cursor::Pos()` で取得できるマウスカーソル座標は、最後の `System::Update()` の呼び出し時点での座標のため、実際画面に見えているマウスカーソルよりも古い座標を示す場合があります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << Cursor::Pos();
		Print << Cursor::PosF();

		Circle{ Cursor::Pos(), 50 }.draw(Palette::Skyblue);
	}
}
```


## 17.2 マウスカーソルの移動量を取得する
1 フレーム前のマウスカーソル座標は `Cursor::PreviousPos()` / `Cursor::PreviousPosF()` で取得できます。1 フレーム前からのマウスカーソルの移動量は `Cursor::Delta()` / `Cursor::DeltaF()` で取得できます。

`Cursor::Delta() == (Cursor::Pos() - Cursor::PreviousPos())` です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 円をつかんでいるか
	bool grab = false;

	Circle circle{ Scene::Center(), 50 };

	while (System::Update())
	{
		if (grab)
		{
			// 移動量分だけ円を移動
			circle.moveBy(Cursor::Delta());
		}

		if (circle.leftClicked()) // 円を左クリックしたら
		{
			grab = true;
		}
		else if (MouseL.up()) // マウスの左ボタンが離されたら
		{
			grab = false;
		}

		if (grab || circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		circle.draw(Palette::Skyblue);
	}
}
```


## 17.3 マウスカーソルのスクリーン座標を取得する
マウスカーソルがデスクトップ上のどの位置にあるか、スクリーン座標で取得するには `Cursor::ScreenPos()` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// マウスカーソルの位置をスクリーン座標で表示
		Print << Cursor::ScreenPos();
	}
}
```


## 17.4 マウスのボタンの入力状態を調べる
マウスのボタンには、以下の `Input` 型の値が割り当てられています。

| 定数      | 対応するボタン |
|---------|---------|
| `MouseL`  | 左ボタン    |
| `MouseR`  | 右ボタン    |
| `MouseM`  | 中央ボタン   |
| `MouseX1` | 拡張ボタン 1 |
| `MouseX2` | 拡張ボタン 2 |
| `MouseX3` | 拡張ボタン 3 |
| `MouseX4` | 拡張ボタン 4 |
| `MouseX5` | 拡張ボタン 5 |

チュートリアル 16 のキーボードと同様に、押された瞬間であるかを `.down()`, 押されているかを `.pressed()`, 離された瞬間であるかを `.up()` を使って `bool` 値で取得できます。

| 関数 | 押していないとき | 押した瞬間 | 押され続けている | 離した瞬間 | 離され続けている |
|:--:|:--:|:--:|:--:|:--:|:--:|
| `.down()` | false | **✔ true** | false | false | false |
| `.pressed()` | false | **✔ true** | **✔ true** | false | false |
| `.released()` | false | false | false | **✔ true** | false |

```cpp

```


## 17.5 ボタンが押されていた時間を調べる

```cpp

```


## 17.6 すべてのマウス入力を取得する

```cpp

```


## 17.7 マウスホイールの回転量を取得する

```cpp

```


## 17.8 マウスカーソルがクライアント領域にあるかを調べる

```cpp

```


## 17.9 マウスカーソルを指定した位置に移動

```cpp

```


## 17.10 (Windows 版) マウスカーソルの移動を制限する

```cpp

```

