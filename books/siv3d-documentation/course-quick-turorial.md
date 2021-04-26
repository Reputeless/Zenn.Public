---
title: "コース | クイックチュートリアル"
free: true
---

短い時間で Siv3D プログラミングの要点を学習するコースです。

# 1. 基本の構造

## 1.1 インクルードするヘッダ
Siv3D のプログラムを書くときは、いつも `<Siv3D.hpp>` ヘッダをインクルードします。
```cpp
# include <Siv3D.hpp>
```

これだけで、Siv3D のすべての機能を使ってプログラムを書けるようになります。  

C++ の経験者であれば、ほかにも `<iostream>` や `<vector>` などの C++ 標準ライブラリをインクルードしたくなるかもしれませんが、その必要はありません。すでに `<Siv3D.hpp>` の中で、Siv3D のプログラミングでよく使われる主要な C++ 標準ライブラリがインクルードされているためです。また、標準ライブラリの機能の多くが、より便利な Siv3D の関数やクラスで再実装されているため、Siv3D の学習の序盤で C++ 標準ライブラリの関数を使うことはめったにありません。


## 1.2 エントリーポイント
通常、C++ のエントリーポイントは `int main()` です。しかし、Siv3D ではこの `main()` 関数はエンジン内部ですでに実装されていて、次のようにユーザの見えないところで、ウィンドウやグラフィックスの初期化処理を行うプログラムがすでに用意されています。
```cpp
// 説明のため簡略化したコード
int main()
{
    Siv3D の初期化...

    Main(); // この関数をユーザがプログラムする

    Siv3D の終了処理...
}
```
ユーザが実装するプログラムは、このプログラムの `void Main()` 関数です。

事前に初期化処理が完了しているので、`Main` 関数の中で最初から Siv3D の機能を使うことができ、`Main` 関数の実行が終了したら、ロードしたアセットやウィンドウの後片付けを Siv3D が自動的に行ってくれます。


## 1.3 最小限のプログラム
これが Siv3D の最小のプログラムです。
```cpp
# include <Siv3D.hpp>

void Main()
{

}
```

`Main` 関数は一瞬で終了してしまうので、このプログラムを実行しても、何も起こっていないように見えるでしょう。


## 1.4 ウィンドウを表示し続ける
プログラムがすぐに終了してしまうと、ユーザとインタラクションをするアプリケーションが作れません。`Main` 関数がずっと続くように **メインループ** を実装します。次のプログラムを実行するとウィンドウが表示され続けます。
```cpp
# include <Siv3D.hpp>

void Main()
{
    // メインループ
    while (System::Update())
    {

    }
}
```
`while` 文によってくり返し実行され、プログラムが半永久的に続くようになります。くり返しのたびに `System::Update` 関数がウィンドウの表示や音楽の再生、マウスやキーボードの入力情報などを更新するので、グラフィックスの表示やユーザとのやり取りをプログラムできるようになります。

`System::Update` は普段は `true` を返すため、メインループはいつまでも続きますが、ウィンドウが閉じられたり、エスケープキーが押されたりするなど、アプリケーションを終了させる特別な **ユーザアクション** が実行されると、以降はずっと `false` を返すようになります。 アプリケーションはこの状態になったら速やかにメインループから抜け、`Main` 関数を終了させる必要があります。といっても上記のプログラムのように書いていれば、自然にこの通りに動作するので心配は不要です。

デフォルトでは「ウィンドウを閉じる」「エスケープキーを押す」「`System::Exit()` を呼ぶ」の 3 つが、アプリケーションを終了させるためのユーザアクションとして設定されています。この設定は`System::SetTerminationTriggers()` に `UserAction` フラグの組み合わせを渡すことでカスタマイズできます。

メインループは、特に指定しない場合、使用しているモニターのリフレッシュレートと同じ速度で繰り返されます。一般的なモニタのリフレッシュレートは毎秒 60, 120, または 144 フレームです。


## 1.5 デバッグ表示
画面にメッセージを表示してみましょう。Siv3D で最も簡単にテキストや数値を画面に表示するできる、デバッグ表示機能 `Print` を使います。

`Print` に向かって、出力の記号 `<<` でテキストを送ると、そのテキストが画面に表示されます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"Hello, Siv3D!";

	while (System::Update())
	{
		
	}
}
```

テキストをプログラムに登場させるときは `" "` で囲みます。Siv3D ではさらに、日本語を扱いやすい UTF-32 という形式でテキストデータを扱うため、`"` の先頭には `U` というプレフィックスを付け、`U"Hello"` のようにします。


## 1.6 さまざまな値の表示
`Print` で出力できるのは文字列だけではありません。Siv3D で「Format 可能」な型であれば何でも出力できます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 文字列リテラル
	Print << U"Hello, Siv3D!";

	// << の連続
	Print << U"This" << U" is " << U"fun!";

	// 文字リテラル
	Print << U'あ';

	// 整数
	Print << 100 * 5 + 5;
	
	// 浮動小数点数
	Print << U"π = " << Math::Pi;

	// bool
	Print << Math::IsPrime(65537);

	// 時間型
	Print << 1h + 3s;

	// 数式パーサの結果 (double 型）
	Print << Eval(U"10^3 + sqrt(81) + 0.001");

	// 範囲
	Print << Range(0, 10);

	// 日付と時刻クラス
	Print << DateTime::Now();

	while (System::Update())
	{

	}
}
```


## 1.7 デバッグ表示のあふれ
次のように `Print` をメインループの中で使うと、毎フレーム新しいメッセージが追加されて、古いメッセージを画面の外に追いやります。画面からあふれたメッセージは自動的に消去されます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		Print << count;

		++count;
	}
}
```


## 1.8 デバッグ表示の消去
`ClearPrint()` を使うと、`Print` でデバッグ表示した内容を即座に消去します。つまり、メインループの先頭で `ClearPrint()` すると、その時点で蓄積されていたメッセージが消去され、つねに最新の `Print` した内容だけが表示されるようになります。
```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		ClearPrint(); // Print したメッセージを消去

		Print << count;

		++count;
	}
}
```


## 1.9 基本的なデータ型
Siv3D でよく使う基本的なデータ型は次のとおりです。

Siv3D で整数を扱うときは、`int` や `unsigned long long` のような標準の型名の代わりに、`int32` や `uint64` のように明示的なサイズを持つ型名を使います。これらの型名を使うことで、プラットフォーム間での移植性が高まり、一貫性のある読みやすいコードになります。

|型名|説明|
|--|--|
|`bool`|ブーリアン型|
|`int8`|符号付 8-bit 整数型|
|`uint8`|符号無し 8-bit 整数型|
|`int16`|符号付 16-bit 整数型|
|`uint16`|符号無し 16-bit 整数型|
|`int32`|符号付 32-bit 整数型|
|`uint32`|符号無し 32-bit 整数型|
|`int64`|符号付 64-bit 整数型|
|`uint64`|符号無し 64-bit 整数型|
|`int128`|符号付 128-bit 整数型|
|`uint128`|符号無し 128-bit 整数型|
|`BigInt`|任意精度多倍長整数型|
|`HalfFloat`|半精度浮動小数点数型|
|`float`|単精度浮動小数点数型|
|`double`|倍精度浮動小数点数型|
|`BigFloat`|有効数字100桁の浮動小数点数型|
|`Byte`|ビット列としてのバイトデータを表す型|
|`char32`|UTF-32 のコードポイント（文字）|
|`String`|文字列クラス (C++ の `std::u32string` 相当)|
|`std::array<Type, N>`|固定長配列|
|`Array<Type>`|動的配列 (C++ の `std::vector<Type>` 相当)|
|`Grid<Type>`|二次元配列|
|`Optional<Type>`|無効値を取りうる型 (C++ の `std::optional<Type>` 相当)|
|`HashSet<Key>`|ハッシュセット (C++ の `std::unordered_set<Key>` 相当)|
|`HashTable<Key, Value>`|ハッシュテーブル (C++ の `std::unordered_map<Key, Value>` 相当)|
|`FilePath`|`String` のエイリアス|
|`StringView`|所有権を持たない文字列クラス (C++ の `std::string_view` 相当)|
|`FilePathView`|`StringView` のエイリアス|


## 1.10 画面の座標系
ウィンドウ内の黒い部分が **画面** で、Siv3D はこの領域に文字や図形、画像を表示できます。

画面のサイズは、基本の状態では **幅 800 ピクセル、高さ 600 ピクセル** です。画面上の位置を表す座標系は、一番左上のピクセルを「X 座標 0」「Y 座標 0」を表す `(0, 0)` と表記し、右に進むと X 座標が大きく、下に進むと Y 座標が大きくなります。画面の一番右下のピクセルの座標は `(799, 599)` です。 

`Cursor::Pos()` を使うと、現在のマウスカーソルの座標を `Point` 型で取得できます。`Point` 型の値は X 座標を表す `int32 x` と Y 座標を表す `int32 y` の 2 つの成分を持っています。`Point` 型の値をそのまま丸ごと `Print` に送って表示することもできます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		Print << Cursor::Pos(); // 現在のマウスカーソル座標を表示

		Print << U"X: " << Cursor::Pos().x; // X 座標だけを表示

		Print << U"Y: " << Cursor::Pos().y; // Y 座標だけを表示
	}
}
```


# 2. 図形を描く

## 2.1 円を描く
画面に図形を描く方法を学びましょう。Siv3D では、図形オブジェクトを作成し、その `draw()` メンバ関数を呼んで描画を行うのが基本的なやり方です。円を描くときは `Circle` を作成し、その `.draw()` を呼びます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中心座標 (400, 300), 半径 20 の円を描く
		Circle{ 400, 300, 20 }.draw();
	}
}
```


## 2.2 X 座標がマウスカーソルと連動する円を描く
円がマウスカーソルの座標に連動して動くようにしてみましょう。`Circle{}` の最初に指定するパラメータは円の中心の X 座標です。この値をマウスカーソルの X 座標にしてみます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ Cursor::Pos().x, 300, 100 }.draw();
	}
}
```

`Print` で表示したメッセージはいつまでも画面に残りましたが、Siv3D では `Print` 以外のすべての描画は `System::Update()` のたびに背景の色でリセットされます。


## 2.3 マウスカーソルと連動する円を描く
Siv3D で X 座標、Y 座標 2 つの値を受け取る関数は、多くの場合、1 つの `Point` 型、もしくは `Vec2` 型を受け取る別バージョンの関数（オーバーロード）を提供しているケースがよくあります。`Circle` も、「X 座標」「Y 座標」「半径」の 3 つの引数ではなく、「中心座標 (`Vec2` 型)」「半径」の 2 つの引数を受け取るオーバーロードがあります。これを使って円をマウスカーソルと連動させます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ Cursor::Pos(), 100 }.draw();
	}
}
```


## 2.4 色を付ける
図形に色を付けたいときは `draw()` 関数に色を渡します。色の指定の方法は大きく 4 通りあります。

| 色の表現               | 値の範囲                                                                                                  |
|--------------------|-------------------------------------------------------------------------------------------------------|
| Palette::色名        | [Web カラー](https://ironodata.info/colorscheme/colorname.html) の名前で色を指定  |
| ColorF{ r, g, b, a } | 0.0 - 1.0 の範囲で RGBA の各成分を指定 |
| Color{ r, g, b, a }  | 0 - 255 の整数の範囲で RGBA の各成分を指定 |
| HSV{ h, s, v, a }    | 色相 `h`, 彩度 `s`, 明度 `v` とアルファ値 `a` の各成分を指定。<br>h は 0.0 - 360.0 (370.0 は 10.0 と同じ). s, v, a は 0.0 - 1.0,の範囲 |

**Palette::色名** は RGB 値を覚えていなくても使える色の表現です。

**ColorF** は Siv3D で一番使い勝手が良い色の表現形式です。

**Color** は `Image` 型の要素で、Siv3D で画像処理をするときに使われる形式です。

**HSV** は赤っぽい、青っぽいなど色の種類を表す色相 (hue) と、色の鮮やかを表す彩度 (saturation), 色の明るさを表す明度 (value) の 3 要素を使った HSV 色空間で色を表現します。

`ColorF`, `Color`, `HSV` はいずれも **アルファ値** `a` を持ちます。アルファ値は「不透明度」を表し、最大値 (`ColorF`, `HSV` の場合 1.0, `Color` の場合 255) ではまったく透過しませんが、値を小さくするとそれに応じて背景を透過する半透明になり、0 になると完全に透明になります。

色の指定はプログラムでよく使われるので、次のような短い書き方も用意されています。例えば `ColorF{ 0.5 }` は `ColorF{ 0.5, 0.5, 0.5, 1.0 }` と同等です。

| 短い書き方           | 意味                         |
|-----------------|----------------------------|
| `ColorF{ r, g, b }` | `ColorF{ r, g, b, 1.0 }`       |
| `ColorF{ rgb, a }`  | `ColorF{ rgb, rgb, rgb, a }`   |
| `ColorF{ rgb }`     | `ColorF{ rgb, rgb, rgb, 1.0 }` |
| `Color{ r, g, b }`  | `Color{ r, g, b, 255 }`        |
| `Color{ rgb, a }`   | `Color{ rgb, rgb, rgb, a }`    |
| `Color{ rgb }`      | `Color{ rgb, rgb, rgb, 255 }`  |
| `HSV{ h, s, v }`    | `HSV{ h, s, v, 1.0 }`          |
| `HSV{ h, a }`       | `HSV{ h, 1.0, 1.0, a }`        |
| `HSV{ h }`          | `HSV{ h, 1.0, 1.0, 1.0 }`      |

色の付いたいくつかの円を描いてみましょう。`.draw()` に色を指定しなかった場合の図形の色は `Palette::White` (`ColorF{ 1.0, 1.0, 1.0, 1.0 }`) になります。
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 左から順に 7 つの円を描く
		Circle{ 100, 200, 40 }.draw();

		Circle{ 200, 200, 40 }.draw(Palette::Green);

		Circle{ 300, 200, 40 }.draw(Palette::Skyblue);

		Circle{ 400, 200, 40 }.draw(ColorF{ 1.0, 0.8, 0.0 });

		Circle{ 500, 200, 40 }.draw(Color{ 255, 127, 127 });

		Circle{ 600, 200, 40 }.draw(HSV{ 160.0, 1.0, 1.0 });

		Circle{ 700, 200, 40 }.draw(HSV{ 160.0, 0.75, 1.0 });

		// 半透明の円
		Circle{ Cursor::Pos(), 80 }.draw(ColorF{ 0.0, 0.5, 1.0, 0.8 });
	}
}
```

## 2.5 背景の色を変える
背景の色を変えるには `Scene::SetBackground()` 関数に色を渡します。新しい背景色は、次の `System::Update()` で画面の描画内容をリセットするときから反映されます。背景色は一度設定すると、再度変更されるまで同じ設定が使われます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.3, 0.6, 1.0 });

	while (System::Update())
	{
		Circle{ Cursor::Pos(), 80 }.draw();
	}
}
```

## 2.6 背景の色を時間の経過とともに変える
背景色の変更にコストはかからないので、背景色は毎フレーム変更しても大丈夫です。プログラムの経過時間（秒）を `Scene::Time()` で取得して、時間に応じて背景色の色相を変化させてみましょう。
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double hue = (Scene::Time() * 60.0);

		Scene::SetBackground(HSV{ hue, 0.6, 1.0 });
	}
}
```

## 2.7 長方形を描く



## 2.8 線分を描く



## 2.9 様々な図形



## 2.10 グラデーションを描く



# 3. 画像を描く




## 3.5 動画をテクスチャとして扱う



# 4. フォントを使う


# 5. GUI


# 6. キーボード入力

## 6.1 キーの入力状態
キーボードのキーには `Input` 型のオブジェクトが割り当てられています。

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
- 上記以外のキーは `<Siv3D/Keyboard.hpp>` を参照

押された瞬間であるかを `.down()`, 押されているかを `.pressed()`, 離された瞬間であるかを `.up()` を使って `bool` 値で取得できます。

|          | down | pressed | up |
|----------|------|---------|----|
| 押していない   |      |         |    |
| 押した瞬間    | ✔    | ✔       |    |
| 押され続けている |      | ✔       |    |
| 離した瞬間    |      |         | ✔  |
| 離され続けている |      |         |    |

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/11/1-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Vec2 pos = Scene::Center();

	while (System::Update())
	{
		const double delta = (200 * Scene::DeltaTime());

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


## 6.2 キーが押されている時間
`Input::pressedDuration()` は、そのキーが押され続けている時間を `Duration` 型の値で返します。

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << KeyA.pressedDuration();
	}
}
```


## 6.3 キーの名前
`Input::name()` は、そのキーの名前を `String` 型の値で返します。

```C++
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


## 6.4 複数のキーの組み合わせ

### A または B
`|` を使って複数のキーを組み合わせると、そのいずれかが押されているかどうかを判定できます。
```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		if ((KeySpace | KeyEnter).pressed())
		{
			Print << U"KeySpace / KeyEnter";
		}
	}
}
```

### A を押しながら B
`+` を使って 2 つのキーを組み合わせると、左のキーが押されながら、右のキーが押されたかどうかを判定できます。
```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if ((KeyControl + KeyC).down() || (KeyCommand + KeyC).down())
		{
			Print << U"Ctrl + C / Command + C";
		}
	}
}
```


## 6.5 テキスト入力
`TextInput::UpdateText()` に `String` 型の変数を渡すことで、テキスト入力を処理できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/11/5-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 30 };

	String text;

	const Rect area{ 50, 50, 700, 300 };

	while (System::Update())
	{
		// キーボードからテキストを入力
		TextInput::UpdateText(text);

		area.draw(ColorF{ 0.3 });

		font(text).draw(area.stretched(-20));
	}
}
```


# 7. マウス入力

