---
title: "Tutorial 13 | Date and Time"
free: true
---

# 13. Date and Time
この章では、日付、時刻、時間の計算や表示に関する機能を学びます。

## 13.1 時間
整数や浮動小数点数リテラルに、次のようなサフィックスを付けて時間を表現できます。

| サフィックス | 時間 |
|--|--|
|`_d` | 日 |
| `h` | 時 |
| `min` | 分 |
| `s` | 秒 |
| `ms` | ミリ秒 |
| `us` | マイクロ秒 |
| `ns` | ナノ秒 |

次のような、時間を表現する型に時間を格納できます。`F` の付く型は浮動小数点数で値を保持します。Siv3D の API では `SecondsF` 型のエイリアスである `Duration` 型をよく使います。

| 型 | 表現する時間 |
|--|--|
| `Days` または `DaysF` | 日 |
| `Hours` または `HoursF` | 時 |
| `Minutes` または `MinutesF` | 分 |
| `Seconds` または `SecondsF` | 秒 |
| `Milliseconds` または `MillisecondsF` | ミリ秒 |
| `Microseconds` または `MicrosecondsF` | マイクロ秒 |
| `Nanoseconds` または `NanosecondsF` | ナノ秒 |

時間を表現する型は相互に変換可能です。浮動小数点数で表現する時間型から整数で表現する時間型への変換には `DurationCast<Type>()` が必要です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Hours t0 = 3h + 2h;
	Print << t0;
	Print << t0.count(); // 数値型として値を取得するには .count()

	const MinutesF t1 = 1h + 30min + 180s;
	Print << t1;
	Print << t1.count();

	const Duration t2 = 10min + 5.5s;
	Print << t2;
	Print << t2.count();

	while (System::Update())
	{

	}
}
```


## 13.2 ストップウォッチの経過時間の表示
`Stopwatch` は `.format()` を使うと、経過時間を `String` として取得できます。この章では説明しませんが、`format()` に書式を渡し、指定したパターンで時間を文字列に変換することができます。`Stopwatch` を `Print` や `Format()` に渡すと、デフォルトの `.format()` の書式で文字列に変換されます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		ClearPrint();

		const String s = stopwatch.format();

		Print << s;

		Print << stopwatch;
	}
}
```


## 13.3 Stopwatch と時間型の比較
時間型の値は相互に比較できます。また、`Stopwatch` と時間型の値も比較でき、ストップウォッチの経過時間を別の時間と比較することができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		ClearPrint();
		Print << stopwatch;

		if (500ms <= stopwatch)
		{
			Print << U"500msec";
		}

		if (3s <= stopwatch)
		{
			Print << U"3sec";
		}

		if (5.5s <= stopwatch)
		{
			Print << U"5.5sec";
		}
	}
}
```


## 13.4 日付クラス
`Date` は日付を表現するクラスで、`int32 year`, `int32 month`, `int32 day` をメンバ変数に持ちます。`Days` 型の値を使って加減算ができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 現在の日付を取得
	const Date date = Date::Today();
	Print << date;
	Print << date.year;
	Print << date.month;
	Print << date.day;

	// 10 日前の日付
	Print << (date - 10_d);

	// 10 日後の日付
	Print << (date + 10_d);

	// 日付の引き算
	Print << (Date::Tomorrow() - Date::Yesterday());

	while (System::Update())
	{

	}
}
```


## 13.5 日付と時刻クラス
`DateTime` は日付と時刻を表現するクラスで、`int32 year`, `int32 month`, `int32 day`, `int32 hour`, `int32 minute`, `int32 second`, `int32 milliseconds` をメンバ変数に持ちます。時間型の値を使って加減算ができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 現在の日付と時刻を取得
	const DateTime t = DateTime::Now();
	Print << t;
	Print << t.year;
	Print << t.month;
	Print << t.day;
	Print << t.hour;
	Print << t.minute;
	Print << t.second;
	Print << t.milliseconds;

	// 30 分前
	Print << (t - 30min);

	// 来週
	Print << (t + 7_d);

	// 2030 年まであと
	const Duration s = (DateTime{ 2030, 1, 1 } - t);
	Print << s;
	Print << DaysF{ s };
	Print << DurationCast<Days>(s);

	while (System::Update())
	{

	}
}
```


## 13.6 アプリケーションの起動時間
4 章で登場した、シーンの経過時間を返す関数 `Scene::Time()` は、1 フレームで加算される時間の上限設定（デフォルトでは 0.1 秒）が適用され、また `System::Update()` によってしか更新されないため、実際のアプリケーション起動からの経過時間よりも短くなる場合があります。特別な理由で正確な起動時間を取得したい場合は `Time::` 名前空間の次の関数を使います。

| 関数 | 説明 |
|--|--|
| `Time::GetSec()` | アプリケーション起動からの時間（秒）を返します |
| `Time::GetMillisec()` | アプリケーション起動からの時間（ミリ秒）を返します |
| `Time::GetMicrosec()` | アプリケーション起動からの時間（マイクロ秒）を返します |
| `Time::GetNanosec()` | アプリケーション起動からの時間（ナノ秒）を返します |

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << Time::GetSec();
		Print << Time::GetMillisec();
		Print << Time::GetMicrosec();
		Print << Time::GetNanosec();
	}
}
```

`Scene::Time()` の経過時間上限は、パーティクルや物理シミュレーションの負荷抑制のために設定されています。また、最初の `System::Update()` が呼ばれたときからカウントが始まります。

## 13.7 UNIX 時間の取得
UNIX 時間を取得したい場合は `Time::` 名前空間の次の関数を使います。

| 関数 | 説明 |
|--|--|
| `Time::GetSecSinceEpoch()` | 協定世界時 (UTC) で 1970 年 1 月 1 日午前 0 時からの経過時間（秒）を返します |
| `Time::GetMillisecSinceEpoch()` | 協定世界時 (UTC) で 1970 年 1 月 1 日午前 0 時からの経過時間（ミリ秒）を返します |
| `Time::GetMicrosecSinceEpoch()` | 協定世界時 (UTC) で 1970 年 1 月 1 日午前 0 時からの経過時間（マイクロ秒）を返します |
| `Time::UTCOffsetMinutes()` | 使用しているコンピュータの、協定世界時 (UTC) との時差（分）を返します |

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << Time::GetSecSinceEpoch();
		Print << Time::GetMillisecSinceEpoch();
		Print << Time::GetMicrosecSinceEpoch();
		Print << Time::UTCOffsetMinutes();
	}
}
```
