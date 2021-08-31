---
title: "チュートリアル 13 | 日付と時刻"
free: true
---

# 13. 日付と時刻、時間
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

次のような、時間を表現する型に時間を格納できます。`F` の付く型は浮動小数点数で値を保持します。

| 型 | 表現する時間 |
|--|--|
| `Days` または `DaysF` | 日 |
| `Hours` または `HoursF` | 時 |
| `Minutes` または `MinutesF` | 分 |
| `Seconds` または `SecondsF` | 秒 |
| `Milliseconds` または `MillisecondsF` | ミリ秒 |
| `Microseconds` または `MicrosecondsF` | マイクロ秒 |
| `Nanoseconds` または `NanosecondsF` | ナノ秒 |

時間を表現する型は相互に変換可能です。Siv3D の API では `SecondsF` 型のエイリアスである `Duration` 型をよく使います。

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

```


## 13.3 Stopwatch と時間型の比較
時間型の値は相互に比較できます。また、`Stopwatch` と時間型の値も比較でき、ストップウォッチの経過時間を別の時間と比較することができます。

```cpp

```


## 13.4 日付クラス
`Date` は日付を表現するクラスで、`int32 year`, `int32 month`, `int32 day` をメンバ変数に持ちます。`Days` 型の値を使って加減算ができます。

```cpp

```


## 13.5 日付と時刻クラス
`DateTime` は日付と時刻を表現するクラスで、`int32 year`, `int32 month`, `int32 day`, `int32 hour`, `int32 minute`, `int32 second`, `int32 milliseconds` をメンバ変数に持ちます。時間型の値を使って加減算ができます。

```cpp

```

