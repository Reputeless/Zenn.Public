---
title: "Samples | Devices"
free: true
---

## 1. シリアル通信
シリアル通信クラス `Serial` を使って、外部デバイスとデータの送受信ができます。

### 1.1 シリアル通信の基本

次のサンプルでは、Arduino UNO の LED の点灯/消灯を PC から制御し、1 バイトの数値データをやり取りするサンプルを示します。

#### Arduino UNO 側のコード

```c
void setup()
{
    pinMode(13, OUTPUT); // 13 ピン - LED - 抵抗 - GND

    // 9600bps でシリアルポートを開く
    Serial.begin(9600);
}

unsigned char i = 0; // テスト用に PC 側に送る値

void loop()
{
    // 250 ミリ秒止める
    delay(250);

    // シリアルポートに 1 バイト出力
    Serial.write(i);

    ++i;

    // シリアル通信で受信したデータを読み込む
    const int val = Serial.read();

    if (val == -1) // 受信したデーが無い
    {
        return;
    }

    if (val == 0)
    {
        digitalWrite(13, LOW); // LOW を出力
    }
    else if (val == 1)
    {
        digitalWrite(13, HIGH); // HIGH を出力
    }
    else if (val == 2)
    {
        i = 0;
    }
}
```

#### PC 側のコード

```cpp
# include <Siv3D.hpp>

void Main()
{
	// シリアルポートの一覧を取得
	const Array<SerialPortInfo> infos = System::EnumerateSerialPorts();
	const Array<String> options = infos.map([](const SerialPortInfo& info)
	{
		return U"{} ({})"_fmt(info.port, info.description);
	}) << U"none";

	Serial serial;
	size_t index = (options.size() - 1);

	while (System::Update())
	{
		const bool isOpen = serial.isOpen(); // OpenSiv3D v0.4.2 以前は serial.isOpened()

		if (SimpleGUI::Button(U"Write 0", Vec2{ 200, 20 }, 120, isOpen))
		{
			// 1 バイトのデータ (0) を書き込む
			serial.writeByte(0);
		}

		if (SimpleGUI::Button(U"Write 1", Vec2{ 340, 20 }, 120, isOpen))
		{
			// 1 バイトのデータ (1) を書き込む
			serial.writeByte(1);
		}

		if (SimpleGUI::Button(U"Write 2", Vec2{ 480, 20 }, 120, isOpen))
		{
			// 1 バイトのデータ (2) を書き込む
			serial.writeByte(2);
		}

		if (SimpleGUI::RadioButtons(index, options, Vec2{ 200, 60 }))
		{
			ClearPrint();

			if (index == (options.size() - 1))
			{
				serial = Serial{};
			}
			else
			{
				Print << U"Open {}"_fmt(infos[index].port);

				// シリアルポートをオープン
				if (serial.open(infos[index].port))
				{
					Print << U"Succeeded";
				}
				else
				{
					Print << U"Failed";
				}
			}
		}

		if (const size_t available = serial.available())
		{
			// シリアル通信で受信したデータを読み込んで表示
			Print << U"READ: " << serial.readBytes();
		}
	}
}
```

### 1.2 複数バイトのデータを送る

#### Arduino UNO 側のコード
Arduino UNO では `int` 型は 2 バイトです。

```c
void setup()
{
  Serial.begin(9600);
}

unsigned char i = 0;

void loop()
{
  delay(250);

  if (2 <= Serial.available())
  {
      int low = Serial.read();
      int high = Serial.read();
      int n = high * 256 + low;

      n *= 2;
      Serial.write(lowByte(n));
      Serial.write(highByte(n));
  }
}
```

#### PC 側のコード

```cpp
# include <Siv3D.hpp>

void Main()
{
	// シリアルポートの一覧を取得
	const Array<SerialPortInfo> infos = System::EnumerateSerialPorts();
	const Array<String> options = infos.map([](const SerialPortInfo& info)
	{
		return U"{} ({})"_fmt(info.port, info.description);
	}) << U"none";

	Serial serial;
	size_t index = (options.size() - 1);

	while (System::Update())
	{
		const bool isOpen = serial.isOpen(); // OpenSiv3D v0.4.2 以前は serial.isOpened()

		if (SimpleGUI::Button(U"Write int16", Vec2{ 200, 20 }, 120, isOpen))
		{
			// 2 バイト (int16) のデータを書き込む
			const int16 n = 12300;

			serial.write(n);
		}

		if (SimpleGUI::RadioButtons(index, options, Vec2{ 200, 60 }))
		{
			ClearPrint();

			if (index == (options.size() - 1))
			{
				serial = Serial{};
			}
			else
			{
				Print << U"Open {}"_fmt(infos[index].port);

				// シリアルポートをオープン
				if (serial.open(infos[index].port))
				{
					Print << U"Succeeded";
				}
				else
				{
					Print << U"Failed";
				}
			}
		}

		if (const size_t available = serial.available())
		{
			int16 n;

			if (serial.read(n)) // 2 バイト (int16) のデータを読み込んだら
			{
				Print << U"READ: " << n;
			}
		}
	}
}
```


## 2. ペンタブレット
Windows 版ではペンタブレットのペンの筆圧等の情報を取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 1.0, 0.96, 0.92 });
	Image image{ Scene::Size(), Palette::White };
	DynamicTexture texture{ image };

	while (System::Update())
	{
		ClearPrint();
		Print << U"IsAvailable: " << Pentablet::IsAvailable();
		Print << U"SupportsPressure: " << Pentablet::SupportsPressure();
		Print << U"SupportsTangentPressure: " << Pentablet::SupportsTangentPressure();
		Print << U"SupportsOrientation: " << Pentablet::SupportsOrientation();
		Print << U"Pressure: " << Pentablet::Pressure();
		Print << U"TangentPressure: " << Pentablet::TangentPressure();
		Print << U"Azimuth: " << Pentablet::Azimuth();
		Print << U"Altitude: " << Pentablet::Altitude();
		Print << U"Twist: " << Pentablet::Twist();

		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();

			const double thickness = (Math::Square(Pentablet::Pressure()) * 40.0);
			Line{ from, to }.overwrite(image, static_cast<int32>(thickness), ColorF{ 0.1 });

			texture.fill(image);
		}

		texture.draw();
	}
}
```

