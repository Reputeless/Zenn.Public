---
title: "チュートリアル 32 | ゲームパッド入力"
free: true
---

# 32. ゲームパッド入力
この章では、ゲームパッドの入力を扱う方法を学びます。

## 32.1 XInput 対応コントローラの入力を扱う
PC に接続されている XInput 対応コントローラには `XInput` を通してアクセスできます。最大 4 台までを同時に扱えます。

```cpp
# include <Siv3D.hpp>

Polygon MakeGamePadPolygon()
{
	Polygon polygon = Ellipse{ 400, 480, 300, 440 }.asPolygon(64);
	polygon = Geometry2D::Subtract(polygon, Ellipse{ 400, 40, 220, 120 }.asPolygon(48)).front();
	polygon = Geometry2D::Subtract(polygon, Circle{ 400, 660, 240 }.asPolygon(48)).front();
	polygon = Geometry2D::Subtract(polygon, Rect{ 0, 540, 800, 60 }.asPolygon()).front();
	return polygon;
}

void Main()
{
	constexpr ColorF backgroundColor{ 0.6, 0.8, 0.7 };
	Scene::SetBackground(backgroundColor);

	constexpr Ellipse buttonLB{ 210, 150, 50, 24 };
	constexpr Ellipse buttonRB{ 590, 150, 50, 24 };
	const Polygon gamepadPolygon = MakeGamePadPolygon();
	constexpr Circle logo{ 400, 250, 25 };
	constexpr RectF leftTrigger{ 210, 16, 40, 100 };
	constexpr RectF rightTrigger{ 550, 16, 40, 100 };
	constexpr Circle leftThumb{ 230, 250, 35 };
	constexpr Circle rightThumb{ 480, 350, 35 };
	constexpr Circle dPad{ 320, 350, 40 };
	constexpr Circle buttonA{ 570, 300, 20 };
	constexpr Circle buttonB{ 620, 250, 20 };
	constexpr Circle buttonX{ 520, 250, 20 };
	constexpr Circle buttonY{ 570, 200, 20 };
	constexpr Circle buttonView{ 330, 250, 15 };
	constexpr Circle buttonMenu{ 470, 250, 15 };

	// プレイヤーインデックス (0 - 3)
	size_t playerIndex = 0;
	const Array<String> options = Range(1, 4).map([](int32 i) {return U"{}P"_fmt(i); });

	// デッドゾーンを有効にするか
	bool enableDeadZone = false;

	// 振動 (0.0 - 1.0)
	XInputVibration vibration;

	while (System::Update())
	{
		// 指定したプレイヤーインデックスの XInput コントローラを取得
		auto controller = XInput(playerIndex);

		// デッドゾーン
		if (enableDeadZone)
		{
			// それぞれデフォルト値を設定
			controller.setLeftTriggerDeadZone();
			controller.setRightTriggerDeadZone();
			controller.setLeftThumbDeadZone();
			controller.setRightThumbDeadZone();
		}
		else
		{
			// デッドゾーンを無効化
			controller.setLeftTriggerDeadZone(DeadZone{});
			controller.setRightTriggerDeadZone(DeadZone{});
			controller.setLeftThumbDeadZone(DeadZone{});
			controller.setRightThumbDeadZone(DeadZone{});
		}

		// 振動
		controller.setVibration(vibration);

		// L ボタン、R ボタン
		{
			buttonLB.draw(ColorF{ controller.buttonLB.pressed() ? 1.0 : 0.7 });
			buttonRB.draw(ColorF{ controller.buttonRB.pressed() ? 1.0 : 0.7 });
		}

		// 本体
		gamepadPolygon.draw(ColorF{ 0.9 });

		// Xbox ボタン
		{
			if (controller.isConnected())
			{
				Circle{ logo.center, 32 }
				.drawPie((-0.5_pi + 0.5_pi * controller.playerIndex), 0.5_pi, ColorF{ 0.6, 0.9, 0.3 });
			}

			logo.draw(ColorF{ 0.6 });
		}

		// 左トリガー
		{
			leftTrigger.draw(AlphaF(0.25));
			leftTrigger.stretched((controller.leftTrigger - 1.0) * leftTrigger.h, 0, 0, 0).draw();
		}

		// 右トリガー
		{
			rightTrigger.draw(AlphaF(0.25));
			rightTrigger.stretched((controller.rightTrigger - 1.0) * rightTrigger.h, 0, 0, 0).draw();
		}

		// 左スティック
		{
			leftThumb.draw(ColorF{ controller.buttonLThumb.pressed() ? 0.85 : 0.5 });
			Circle{ leftThumb.center + 25 * Vec2{ controller.leftThumbX, -controller.leftThumbY }, 20 }.draw();
		}

		// 右スティック
		{
			rightThumb.draw(ColorF{ controller.buttonRThumb.pressed() ? 0.85 : 0.5 });
			Circle{ rightThumb.center + 25 * Vec2{ controller.rightThumbX, -controller.rightThumbY }, 20 }.draw();
		}

		// 方向パッド
		{
			dPad.draw(ColorF{ 0.75 });
			Shape2D::Plus(dPad.r * 0.9, 25, dPad.center).draw(ColorF{ 0.5 });

			const Vec2 direction{
				controller.buttonRight.pressed() - controller.buttonLeft.pressed(),
				controller.buttonDown.pressed() - controller.buttonUp.pressed() };

			if (!direction.isZero())
			{
				Circle{ dPad.center + direction.withLength(25), 15 }.draw();
			}
		}

		// A, B, X, Y ボタン
		{
			buttonA.draw(ColorF{ 0.0, 1.0, 0.3, controller.buttonA.pressed() ? 1.0 : 0.3 });
			buttonB.draw(ColorF{ 1.0, 0.0, 0.3, controller.buttonB.pressed() ? 1.0 : 0.3 });
			buttonX.draw(ColorF{ 0.0, 0.3, 1.0, controller.buttonX.pressed() ? 1.0 : 0.3 });
			buttonY.draw(ColorF{ 1.0, 0.5, 0.0, controller.buttonY.pressed() ? 1.0 : 0.3 });
		}

		// View (Back), Menu (Start) ボタン 
		{
			buttonView.draw(ColorF(controller.buttonView.pressed() ? 1.0 : 0.7));
			buttonMenu.draw(ColorF(controller.buttonMenu.pressed() ? 1.0 : 0.7));
		}

		SimpleGUI::RadioButtons(playerIndex, options, Vec2{ 20, 20 });
		SimpleGUI::CheckBox(enableDeadZone, U"DeadZone", Vec2{ 320, 20 });
		SimpleGUI::Slider(U"leftMotor", vibration.leftMotor, Vec2{ 280, 420 }, 120, 120);
		SimpleGUI::Slider(U"rightMotor", vibration.rightMotor, Vec2{ 280, 460 }, 120, 120);
	}
}
```


## 32.2 Joy-Con の入力を扱う
PC に接続されている Nintendo Switch の Joy-Con の情報は `JoyConL` または `JoyConR` を通して取得できます。

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

void Main()
{
	Scene::SetBackground(ColorF{ 0.9 });
	Window::Resize(1280, 720);
	Effect effect;

	Vec2 left{ 640 - 100, 100 }, right{ 640 + 100, 100 };
	double angle = 0_deg;
	double scale = 400.0;
	bool covered = true;

	while (System::Update())
	{
		Circle{ Vec2{ 640 - 300, 450 }, scale / 2 }.drawFrame(scale * 0.1);
		Circle{ Vec2{ 640 + 300, 450 }, scale / 2 }.drawFrame(scale * 0.1);

		// Joy-Con (L) を取得
		if (const auto joy = JoyConL(0))
		{
			joy.drawAt(Vec2(640 - 300, 450), scale, -90_deg - angle, covered);

			if (auto d = joy.povD8())
			{
				left += Circular{ 4, *d * 45_deg };
			}

			if (joy.button2.down())
			{
				effect.add([center = left](double t) {
					Circle{ center, 20 + t * 200 }.drawFrame(10, 0, AlphaF(1.0 - t));
					return t < 1.0;
					});
			}
		}

		// Joy-Con (R) を取得
		if (const auto joy = JoyConR(0))
		{
			joy.drawAt(Vec2{ 640 + 300, 450 }, scale, 90_deg + angle, covered);

			if (auto d = joy.povD8())
			{
				right += Circular{ 4, *d * 45_deg };
			}

			if (joy.button2.down())
			{
				effect.add([center = right](double t) {
					Circle{ center, 20 + t * 200 }.drawFrame(10, 0, AlphaF(1.0 - t));
					return t < 1.0;
					});
			}
		}

		Circle{ left, 30 }.draw(ColorF{ 0.0, 0.75, 0.9 });
		Circle{ right, 30 }.draw(ColorF{ 1.0, 0.4, 0.3 });
		effect.update();

		SimpleGUI::Slider(U"Rotation: ", angle, -180_deg, 180_deg, Vec2{ 20, 20 }, 120, 200);
		SimpleGUI::Slider(U"Scale: ", scale, 100.0, 600.0, Vec2{ 20, 60 }, 120, 200);
		SimpleGUI::CheckBox(covered, U"Covered", Vec2{ 20, 100 });
	}
}
```


## 32.3 Pro コントローラーの入力を扱う
PC に接続されている Nintendo Switch の Pro コントローラーの情報は `ProController` を通して取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// Pro コントローラーを取得
		if (const auto pro = ProController(0))
		{
			// 各ボタンの状態を表示
			Print << U"A: " << pro.buttonA.pressed();
			Print << U"B: " << pro.buttonB.pressed();
			Print << U"X: " << pro.buttonX.pressed();
			Print << U"Y: " << pro.buttonY.pressed();
			Print << U"L: " << pro.buttonL.pressed();
			Print << U"R: " << pro.buttonR.pressed();
			Print << U"ZL: " << pro.buttonZL.pressed();
			Print << U"ZR: " << pro.buttonZR.pressed();
			Print << U"-: " << pro.buttonMinus.pressed();
			Print << U"+: " << pro.buttonPlus.pressed();
			Print << U"LS: " << pro.buttonLStick.pressed();
			Print << U"RS: " << pro.buttonRStick.pressed();
			Print << U"Screenshot: " << pro.buttonScreenshot.pressed();
			Print << U"Home: " << pro.buttonHome.pressed();
			Print << U"LStick: " << pro.LStick();
			Print << U"RStick: " << pro.RStick();
			Print << U"POV: " << pro.povD8();
		}
		else
		{
			Print << U"No Pro Controller found";
		}
	}
}
```


## 32.4 ゲームパッドの入力を扱う
あらゆる種類のゲームパッドの情報を取得できる汎用的なクラスが `Gamepad` です。ユーザインデックスは `Gamepad.MaxUserCount - 1` で定義される 15 が最大値です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(800, 800);

	const Array<String> indices = Range(0, (Gamepad.MaxPlayerCount - 1)).map(Format);

	// ゲームパッドのプレイヤーインデックス
	size_t playerIndex = 0;

	while (System::Update())
	{
		ClearPrint();

		if (const auto gamepad = Gamepad(playerIndex)) // 接続されていたら
		{
			const auto& info = gamepad.getInfo();

			Print << U"{} (VID: {}, PID: {})"_fmt(info.name, info.vendorID, info.productID);

			for (auto [i, button] : Indexed(gamepad.buttons))
			{
				Print << U"button{}: {}"_fmt(i, button.pressed());
			}

			for (auto [i, axe] : Indexed(gamepad.axes))
			{
				Print << U"axe{}: {}"_fmt(i, axe);
			}

			Print << U"POV: " << gamepad.povD8();
		}

		SimpleGUI::RadioButtons(playerIndex, indices, Vec2{ 500, 20 });
	}
}
```


## 32.5 接続されているゲームパッドを列挙する
PC に接続されているゲームパッドの一覧を `System::EnumerateGamepads()` で取得できます。結果は `Array<GamepadInfo>` 型で返されます。

`GamepadInfo` 型のメンバ変数は次の通りです。

| メンバ変数 | 説明 |
|--|--|
| `playerIndex` | `Gamepad` で使うプレイヤーインデックス |
| `vendorID` | ベンダー ID |
| `productID` | プロダクト ID |
| `name` | ゲームパッドの名称 |

```cpp
# include <Siv3D.hpp>

void Main()
{
	for (const auto& info : System::EnumerateGamepads())
	{
		Print << U"[{}] {} ({:#x} {:#x})"_fmt(info.playerIndex, info.name, info.vendorID, info.productID);
	}

	while (System::Update())
	{

	}
}
```


## 32.6 キーボード入力との連係
`Gamepad` の `buttons` 要素や、XInput の各ボタン、JoyCon の各ボタンは `Input` 型なので、チュートリアル 16.6 のキーコンフィグで用いることができます。

