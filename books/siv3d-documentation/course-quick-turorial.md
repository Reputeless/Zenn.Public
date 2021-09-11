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

C++ の経験者であれば、ほかにも `<iostream>` や `<vector>` などの C++ 標準ライブラリヘッダをインクルードしたくなるかもしれませんが、その必要はありません。すでに `<Siv3D.hpp>` の中で、Siv3D プログラミングにおいてよく使われる主要な C++ 標準ライブラリヘッダがインクルードされているためです。また、標準ライブラリの機能の多くが、より便利な Siv3D の関数やクラスで再実装されているため、Siv3D の学習の序盤で C++ 標準ライブラリの関数を使うことはめったにありません。


## 1.2 エントリーポイント
通常、C++ のプログラムのエントリーポイントは `int main()` です。しかし、Siv3D ではこの `main()` 関数はエンジン内部ですでに実装されていて、次のようにユーザの見えないところで、ウィンドウやグラフィックスの初期化処理を行うプログラムがすでに用意されています。
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
`while` 文によってプログラムが半永久的に続くようになります。くり返しのたびに `System::Update` 関数がウィンドウの表示や音楽の再生、マウスやキーボードの入力情報などを更新するので、グラフィックスの表示やユーザとのやり取りをプログラムできるようになります。

`System::Update` は普段は `true` を返すため、メインループはいつまでも続きますが、ウィンドウが閉じられたり、エスケープキーが押されたりするなど、アプリケーションを終了させる特別な **ユーザアクション** が実行されると、以降はずっと `false` を返すようになります。 アプリケーションはこの状態になったら速やかにメインループから抜け、`Main` 関数を終了させる必要があります。といっても上記のプログラムのように書いていれば、自然にこの通りに動作するので心配は不要です。

デフォルトでは「ウィンドウを閉じる」「エスケープキーを押す」「`System::Exit()` を呼ぶ」の 3 つが、アプリケーションを終了させるためのユーザアクションとして設定されています。この設定は`System::SetTerminationTriggers()` に `UserAction` フラグの組み合わせを渡すことでカスタマイズできます。

メインループは、特に指定しない限り、使用しているモニターのリフレッシュレートと同じ速度で繰り返されます。一般的なモニタのリフレッシュレートは毎秒 60, 120, または 144 フレームです。


## 1.5 デバッグ表示
画面にメッセージを表示してみましょう。Siv3D で最も簡単にテキストや数値を画面に表示できる、デバッグ表示機能 `Print` を使います。

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
`ClearPrint()` を使うと、`Print` でデバッグ表示した内容を即座に消去します。メインループの先頭で `ClearPrint()` すると、現在のフレームで `Print` した内容だけが表示されるようになります。
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

```cpp
# include <Siv3D.hpp>

void Main()
{
	bool a = true;
	int32 b = 123;
	double c = 0.5;
	BigInt d = 111222333444555666777888999000_big;
	BigFloat e = 0.1234567890123456789_bigF;
	Byte f{ 0xF7 };
	char32 g = U'あ';
	String h = U"Hello!";
	Array<int32> i = { 10, 20, 30, 40 };
	Array<String> j = { U"aaa", U"bbb" };
	HalfFloat k = 3.333333f;

	Print << a;
	Print << b;
	Print << c;
	Print << d;
	Print << e;
	Print << f;
	Print << g;
	Print << h;
	Print << i << U" : " << i.size();
	Print << j << U" : " << j.size();
	Print << k << U" : " << sizeof(k);

	while (System::Update())
	{

	}
}
```


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

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/2/1-0.png?raw=true)

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

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/2/1-2.gif?raw=true)

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

`Print` で表示したメッセージはいつまでも画面に残りましたが、それは例外的なルールです。通常、`Print` 以外のすべての描画は `System::Update()` のたびに背景の色でリセットされます。


## 2.3 マウスカーソルと連動する円を描く
Siv3D で X 座標、Y 座標 2 つの値を受け取る関数は、多くの場合、1 つの `Point` 型、もしくは `Vec2` 型を受け取る別バージョンの関数（オーバーロード）を提供しているケースがよくあります。`Circle` も、「X 座標」「Y 座標」「半径」の 3 つの引数ではなく、「中心座標 (`Vec2` 型)」「半径」の 2 つの引数を受け取るオーバーロードがあります。これを使って円をマウスカーソルと連動させます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/2/1-3.gif?raw=true)

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

**Palette::色名** は `Palette::Orange`, `Palette::Yellow` のように、RGB 値がわからなくても使えます。

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

色の付いたいくつかの円を描いてみましょう。`.draw()` に色を指定しなかった場合のデフォルトの色は `Palette::White` (`ColorF{ 1.0, 1.0, 1.0, 1.0 }`) です。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/2/2-0.gif?raw=true)

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
背景の色を変えるには `Scene::SetBackground()` に色を渡します。新しい背景色は、次の `System::Update()` で画面の描画内容をリセットするときから反映されます。背景色は一度設定すると、再度変更されるまで同じ設定が使われます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/2/3-0.gif?raw=true)

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
プログラムの経過時間（秒）を `Scene::Time()` で取得して、時間に応じて背景色の色相を変化させてみましょう。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/2/3-1.gif?raw=true)

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
長方形を描くときは `Rect` を作成して `.draw()` します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 座標 (20, 40) を左上の基準位置にして、幅 400, 高さ 100 の長方形を描く 
		Rect{ 20, 40, 400, 100 }.draw();

		// 座標 (100, 200) を左上の基準位置にして、幅が 80 の正方形を描く 
		Rect{ 100, 200, 80 }.draw(Palette::Orange);

		// 座標 (400, 300) を中心の基準位置にして、幅 80, 高さ 40 の長方形を描く
		Rect{ Arg::center(400, 300), 80, 40 }.draw(Palette::Pink);

		// マウスカーソルの座標を中心の基準位置にして、幅が 100 の正方形を描く 
		Rect{ Arg::center(Cursor::Pos()), 100 }.draw(ColorF(1.0, 0.0, 0.0, 0.5));

		// 座標や大きさを浮動小数点数 (小数を含む数）で指定したい場合は RectF
		RectF{ 200.4, 450.3, 390.5, 122.5 }.draw(Palette::Skyblue);
	}
}
```

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/2/4-0.gif?raw=true)

図形は `draw()` した順番に描画されます。このプログラムでは、マウスカーソルに追従する赤い正方形よりも、画面の下にある水色の大きな長方形が上に来ます。

`Rect` 型は左上の座標と幅、高さをそれぞれ `int32 x`, `int32 y`, `int32 w`, `int32 h` というメンバ変数で表します。整数ではなく浮動小数点数で扱いたい場合は、すべての要素が `double` 型である `RectF` を使います。


## 2.8 枠を描く
図形は、`.draw()` の代わりに `.drawFrame()` することで、枠だけを描けます。`.drawFrame()` の第 1 引数には図形の内側方向への太さを、第 2 引数には外側方向への太さを指定します。図形の `.draw()` や `.drawFrame()` の戻り値はその図形自身なので、`rect.draw().drawFrame()` のように関数を続けて書くこともできます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/2/5-0.png?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 長方形の内側に 3px の枠を描く
		Rect{ 100, 100, 100, 30 }
			.drawFrame(3, 0);

		// 長方形の外側に 3px の枠を描く
		Rect{ 220, 100, 100, 30 }
			.drawFrame(0, 3);

		// 長方形と、その内側 3px と外側 3px に枠を描く
		Rect{ 200, 200, 400, 100 }
			.draw(Palette::White)
			.drawFrame(3, 3, Palette::Orange);

		// 円の内側 1px と外側 1px に枠を描く
		Circle{ Cursor::Pos(), 40 }
			.drawFrame(1, 1, Palette::Seagreen);
	}
}
```


## 2.9 線分を描く
始点と終点を指定して線分を描くときは `Line` を作成して `.draw()` します。`.draw()` のパラメータには描画する線分の太さと色を指定します。線分の両端を丸くしたり、点線にしたりするなど、スタイルの変更もできます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/2/6-0.png?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 座標 (100, 100) から (400, 150) まで太さ 4px の線分を描く
		Line{ 100, 100, 400, 150 }.draw(4, Palette::Yellow);

		// 座標 (400, 300) からマウスカーソルの座標まで太さ 10px の線分を描く
		Line{ 400, 300, Cursor::Pos() }.draw(10, Palette::Skyblue);

		// 通常の線
		Line{ 100, 400, 700, 400 }.draw(12, Palette::Orange);

		// 両端が丸い線
		Line{ 100, 450, 700, 450 }.draw(LineStyle::RoundCap, 12, Palette::Orange);

		// 四角いドットの線
		Line{ 100, 500, 700, 500 }.draw(LineStyle::SquareDot, 12, Palette::Orange);

		// 丸いドットの線
		Line{ 100, 550, 700, 550 }.draw(LineStyle::RoundDot, 12, Palette::Orange);
	}
}
```


## 2.10 様々な図形
Siv3D にはこれ以外にも様々な図形クラスが用意されていて、複雑な形状を短いコードで記述できます。

|クラス|図形|
|--|--|
|`Line`|線分|
|`Rect`|長方形 (int32)|
|`RectF`|長方形 (double)|
|`Circle`|円|
|`Ellipse`|楕円|
|`Triangle`|三角形|
|`Quad`|凸な四角形|
|`RoundRect`|角丸四角形|
|`Polygon`|多角形|
|`MultiPolygon`|多角形の集合|
|`Bezier2`|2 次ベジェ曲線|
|`Bezier3`|3 次ベジェ曲線|
|`LineString`|連続する複数の線分|
|`Spline2D`|スプライン曲線|
|`Shape2D`|正多角形や星、ハート、十字などのコレクション|

![](https://storage.googleapis.com/zenn-user-upload/vl799b9wdvudg7iz3f1aq1zl7jiz)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double angle = (Scene::Time() * 60_deg);

		Line{ 20, 20, 80, 80 }.draw(4);
		Rect{ 120, 40, 60, 40 }.draw();
		Circle{ 250, 50, 30 }.draw();
		Ellipse{ 350, 50, 30, 20 }.draw();	
		Triangle{ 450, 60, 70, angle }.draw();
		Quad{ {520, 20}, {580, 40}, {580, 80}, {520, 80} }.draw();
		RoundRect{ 620, 20, 80, 60, 10 }.draw();
		Rect{ 720, 30, 60, 40 }.rounded(20, 0, 20, 0).draw();

		Rect{ 20, 130, 60, 40 }.rotated(angle).draw();
		Polygon{ {120, 120}, {150, 110}, {180, 150}, {150, 140}, {120, 180} }.draw();
		Bezier2{ {220, 180}, {250, 100}, {280, 120} }.draw(3);
		Bezier3{ {320, 180}, {350, 180}, {350, 120}, {380, 120} }.draw(3);
		LineString{ {420, 180}, {450, 120}, {460, 180}, {480, 120} }.draw(3);
		LineString{ {520, 180}, {540, 150}, {560, 160}, {580, 120} }.asSpline().draw(3);
		Shape2D::Plus(40, 20, { 650, 150 }, angle).draw();
		Shape2D::Cross(40, 15, { 750, 150 }, angle).draw();

		Shape2D::Pentagon(40, { 50, 250 }, angle).draw();
		Shape2D::Hexagon(40, { 150, 250 }, angle).draw();
		Shape2D::Ngon(8, 40, { 250, 250 }, angle).draw();
		Shape2D::Star(40, { 350, 250 }, angle).draw();
		Shape2D::NStar(8, 40, 30, { 450, 250 }, angle).draw();
		Line{ 520, 220, 580, 280 }.drawArrow(4, { 20, 30 });
		Shape2D::Rhombus(60, 40, { 650, 250 }, angle).draw();
		Shape2D::RectBalloon(Rect{ 720, 220, 60, 40 }, { 710, 290 }).draw();

		Shape2D::Stairs({ 80, 380 }, 60, 60, 4).draw();
		Shape2D::Heart(40, { 150, 350 }, angle).draw();
		Shape2D::Hexagon(40, { 250, 350 })
			.asPolygon().addHole({ {260, 340}, {240, 340}, {240, 360}, {260, 360} }).draw();
		Rect{ 320, 320, 60, 60 }.drawFrame(5);
		Circle{ 450, 350, 40 }.drawFrame(5);
		Circle{ 550, 350, 40 }.drawPie(angle, 120_deg);
		Circle{ 650, 350, 40 }.drawArc(angle, 240_deg, 10, 0);
		Circle{ 750, 350, 40 }.drawArc(LineStyle::RoundCap, angle, 240_deg, 10, 0);

		Line{ 20, 420, 80, 480 }.draw(LineStyle::RoundCap, 6);
		Line{ 120, 420, 180, 480 }.draw(LineStyle::SquareDot, 6);
		Line{ 220, 420, 280, 480 }.draw(LineStyle::RoundDot, 6);
	}
}
```


## 2.11 グラデーションを描く
`Line` や `Triangle`, `Rect`, `RectF`, `Quad` には、頂点ごとに色を指定し、グラデーションで塗りつぶすオプションがあります。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/2/21-0.png?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Line{ 100, 100, 500, 150 }
			.draw(6, Palette::Yellow, Palette::Red);

		Triangle{ 200, 200, 100 }
			.draw(HSV{ 0 }, HSV{ 120 }, HSV{ 240 });

		// 左から右へのグラデーション
		Rect{ 400, 200, 200, 100 }
			.draw(Arg::left = Palette::Skyblue, Arg::right = Palette::Blue);
		
		// 上から下へのグラデーション
		Rect{ 200, 400, 400, 100 }
			.draw(Arg::top = ColorF{ 1.0, 1.0 }, Arg::bottom = ColorF{ 1.0, 0.0 });
	}
}
```

# 3. 画像を描く

## 3.1 絵文字を描画する
画面に画像を描きたいときは `Texture` を作成し、`.draw()` または `.drawAt()` します。`Texture` は、画像ファイルから画像データを読み込んで作成したり、プログラムで作成した画像データから作成したり、Siv3D が標準で提供している絵文字やアイコンコレクションから作成することができます。

この章の最初では、絵文字コレクションから好きな絵文字を選んで `Texture` を作成し、それを描画するプログラムを書いてみましょう。`Texture` の作成にはメモリ確保などの実行時負荷がかかります。メインループの中で毎フレーム新しい `Texture` を作成するのは避け、作成が 1 回だけで済むようにしましょう。

絵文字コレクションから `Texture` を作成するには、`Texture` のコンストラクタ引数に `U"🐈"_emoji` のように絵文字を渡します。

### Texture::drawAt()
`.drawAt()` では、テクスチャの中心をどこに据えるかを画面の座標で指定します。絵文字やアイコンを描く場合はこちらが便利です。
![](https://storage.googleapis.com/zenn-user-upload/sm7023pc2clnnov9plkxf6if40un)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 🐈 の絵文字からテクスチャを作成
	const Texture texture{ U"🐈"_emoji };

	while (System::Update())
	{
		// テクスチャを座標 (0, 0) を中心に描画
		texture.drawAt(0, 0);

		// テクスチャを画面中央を中心に描画
		texture.drawAt(Scene::Center());
	}
}
```

### Texture::draw()
`.draw()` では、テクスチャの左上をどこに据えるかを画面の座標で指定します。背景画像や UI などを描くときにはこちらが便利な場合があります。
![](https://storage.googleapis.com/zenn-user-upload/t49oyggs2p0yo60qtbther221eox)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 🐈 の絵文字からテクスチャを作成
	const Texture texture{ U"🐈"_emoji };

	while (System::Update())
	{
		// テクスチャを座標 (0, 0) から描画
		texture.draw(0, 0);

		// テクスチャを画面中心から描画
		texture.draw(Scene::Center());
	}
}
```

### Siv3D で使える絵文字の一覧
Siv3D で使える絵文字は約 3,600 種類あります。絵文字は [emojipedia](https://emojipedia.org/) の Categories から探すのが便利です。 OpenSiv3D v0.6 はオープンソースの絵文字フォント Noto Color Emoji (Android 11.0 版) を内蔵しているので、Siv3D アプリはどのプラットフォームでも同じ見た目の絵文字を表示できます。

## 3.2 テクスチャを拡大縮小して描画する

### Texture::scaled()
`Texture::scaled()` に拡大縮小倍率を指定すると、`Texture` にサイズ情報が付加された `TextureRegion` を作成できます。`TextureRegion` は `Texture` のように `.draw()` または `.drawAt()` できます。`Texture` を作成するのと異なり、既存の `Texture` から `TextureRegion` を作成するのは非常に軽い実行時負荷です。サンプルでは示していませんが、縦横で異なる倍率に設定することもできます。
![](https://storage.googleapis.com/zenn-user-upload/6x2n1gnf7niswi6q2i4i4l16kuz3)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	const Texture dog{ U"🐕"_emoji };

	while (System::Update())
	{
		// 2 倍に拡大して描画
		cat.scaled(2.0).drawAt(200, 300);

		// 1.5 倍に拡大して描画
		cat.scaled(1.5).drawAt(400, 300);

		// 半分のサイズに縮小して描画
		dog.scaled(0.5).drawAt(600, 300);
	}
}
```

### Texture::resized()
`Texture::resized()` は、拡大縮小後のサイズを倍率ではなくピクセル単位で指定します。Siv3D 標準の絵文字のサイズは幅が 136 ピクセルなので、136 より大きい数を指定すると拡大、小さい数を指定すると縮小表示になります。サンプルでは示していませんが、縦横で異なるサイズに設定することもできます。
![](https://storage.googleapis.com/zenn-user-upload/8q8tuoga444onyzxg2bo15tsrc5x)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	const Texture dog{ U"🐕"_emoji };

	while (System::Update())
	{
		// 300px に拡大して描画
		cat.resized(300).drawAt(200, 300);

		// 200px に拡大して描画
		cat.resized(200).drawAt(400, 300);

		// 20px に縮小して描画
		dog.resized(20).drawAt(600, 300);
	}
}
```

## 3.3 テクスチャを回転して描画する
`Texture::rotated()` または `Texture::rotatedAt()` によって、テクスチャに回転情報を付加した `TexturedQuad` を作成できます。`TexturedQuad` は `Texture` のように `.draw()` または `.drawAt()` できます。`TextureRegion` と同様、`TexturedQuad` を作成するのは非常に軽い実行時負荷です。

### Texture::rotated()
テクスチャの中心を軸にして回転します。回転角度はラジアンで指定します。
![](https://storage.googleapis.com/zenn-user-upload/itswb5hm08lv2ss9x7o70rc3yqsq)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	while (System::Update())
	{
		// 毎秒 90° 時計回りに回転
		cat.rotated(Scene::Time() * 90_deg).drawAt(Scene::Center());
	}
}
```

### Texture::roatedAt()
テクスチャ上の指定した座標を軸にして回転します。`Vec2{ 0, 0 }` を指定すればテクスチャの左上が軸になります。回転角度はラジアンで指定します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	while (System::Update())
	{
		// 画像中の (40, 20) を軸に回転させて描画
		cat.rotatedAt(Vec2{ 40, 20 }, Scene::Time() * 90_deg).draw(Scene::Center());
	}
}
```

## 3.4 テクスチャを上下・左右反転して描画する
`Texture::flipped()` で上下反転、`Texture::mirrored()` で左右反転した `TextureRegion` を作成できます。それぞれ、引数をとらずに反転する関数、引数の `bool` 値が `true` のときだけ反転する関数の 2 種類のオーバーロードがあります。
![](https://storage.googleapis.com/zenn-user-upload/1rwu7czw650kmyvqf9ancnbe0vtj)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture cat{ U"🐈"_emoji };

	while (System::Update())
	{
		// 上下反転
		cat.flipped().drawAt(200, 300);

		// 左右反転
		cat.mirrored().drawAt(400, 300);

		// 反転を bool 値で指定するオーバーロード
		cat.mirrored(false).drawAt(600, 300);
	}
}
```

## 3.5 アイコンを描画する
アイコンコレクションから `Texture` を作成するには、`Texture` のコンストラクタ引数にアイコンオブジェクトを渡します。アイコンは全部で約 7,700 種類用意されています。

`Icon` のコンストラクタには、[Font Awesome アイコン一覧](https://fontawesome.com/icons?d=gallery&s=brands,solid&m=free) または [Material Design Icons](https://pictogrammers.github.io/@mdi/font/6.1.95/) で調べられる 16 進数コードに `_icon` を付けた値と、アイコンのサイズ（ピクセル単位）を渡します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/5-0.png?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.92 });

	// 歯車 (f013) のアイコン
	const Texture iconConfig{ 0xf013_icon, 80 };

	// 南京錠 (f023) のアイコン
	const Texture iconLock{ 0xf023_icon, 80 };

	// 拡大鏡 (f00e) のアイコン
	const Texture iconZoom{ 0xf00e_icon, 80 };

	while (System::Update())
	{
		iconConfig.drawAt(200, 300, ColorF{ 0.25 });

		iconLock.scaled(1.5).drawAt(400, 300, ColorF{ 0.25 });

		iconZoom.drawAt(600, 300, ColorF{ 0.25 });
	}
}
```

## 3.6 画像ファイルを読み込んで描画する
画像ファイルから `Texture` を作成するには、`Texture` のコンストラクタ引数に、読み込みたい画像ファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/6-0.png?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 風車の画像
	const Texture textureWindmill{ U"example/windmill.png" };

	// Siv3D くん（Siv3D の公式マスコットキャラクター）の画像
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };

	while (System::Update())
	{
		textureWindmill.draw(40, 20);

		textureSiv3DKun.draw(400, 100);
	}
}
```

### 対応している画像フォーマット
OpenSiv3D v0.6 では、8 種類の画像フォーマットの読み込みがサポートされています。

| フォーマット   | 拡張子             | 対応状況       |
|----------|-----------------|:----------:|
| PNG      | png             | ✔          |
| JPEG     | jpg/jpeg        | ✔          |
| BMP      | bmp             | ✔          |
| SVG      | svg             | ✔          |
| GIF      | gif             | ✔          |
| TGA      | tga             | ✔          |
| PPM      | ppm/pgm/pbm/pnm | ✔          |
| WebP     | webp            | ✔          |
| JPEG2000 | jp2             | (将来のバージョン) |
| DDS      | dds             | (将来のバージョン) |
| TIFF     | tif/tiff        | (将来のバージョン) |


## 3.7 テクスチャの一部を描画する
テクスチャの全部ではなく、特定の長方形の領域だけを描画したい場合は `Texture::operator()` で、テクスチャ上の描画したい領域を指定して `TextureRegion` を作成します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/8-0.png?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture textureWindmill{ U"example/windmill.png" };

	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };

	while (System::Update())
	{
		// 画像の (250, 100) から幅 200, 高さ 150 の長方形部分
		textureWindmill(250, 100, 200, 150).draw(40, 20);

		// 画像の (100, 0) から幅 100, 高さ 150 の長方形部分
		textureSiv3DKun(100, 0, 100, 150).draw(400, 100);
	}
}
```


## 3.8 動画をテクスチャとして扱う
`VideoTexture` を使うと、動画ファイルを `Texture` のように扱えます。`VideoTexture` は毎フレーム `.advance()` を呼ぶことで再生位置を進めることができます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	const VideoTexture videoTexture{ U"example/video/river.mp4", Loop::Yes };

	while (System::Update())
	{
		// 動画の時間を進める (デフォルトでは Scece::DeltaTime() 秒)
		videoTexture.advance();

		videoTexture
			.scaled(0.5).draw();

		videoTexture
			.scaled(0.5)
			.rotated(Scene::Time() * 30_deg)
			.drawAt(Cursor::Pos());
	}
}
```


# 4. フォントを使う

## 4.1 Font
前章までテキストの表示に使ってきた `Print` は、フォントのサイズや種類、描画位置に自由度がありませんでした。自由にカスタマイズしたフォントを使ってテキストを描きたいときは `Font` を作成し、描画したい内容を `()` でつなげたあと、`.draw()` または `.drawAt()` します。

`Texture` と同じように、`Font` の作成にはメモリ確保などの実行時負荷がかかります。メインループの中で毎フレーム新しい `Font` を作成するのは避け、作成が 1 回だけで済むようにしましょう。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/8/1-0.png?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// サイズ 50 のフォントを作成
	const Font font{ 50 };

	while (System::Update())
	{
		// 左上位置 (20, 20) からテキストを描く
		font(U"Hello, Siv3D!").draw(20, 20);

		// テキストの中心座標が画面の中心になるようにテキストを描く
		font(U"C++").drawAt(Scene::Center(), Palette::Skyblue);

		// 文字列以外を渡すと Format される
		font(Cursor::Pos()).draw(50, 300);

		// 複数渡すと、Format でつなげられる
		font(123, U"ABC").draw(50, 400);

		font(U"{}/{}/{}"_fmt(2020, 12, 31)).draw(50, 500);
	}
}
```


## 4.2 改行する
テキストの中に改行文字 `\n` が含まれていると、そこで改行されます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/8/2-0.png?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Font font{ 50 };

	while (System::Update())
	{
		font(U"Hello,\nSiv3D\n\n!!!").draw(20, 20);
	}
}
```


## 4.3 フォントのサイズ
`Font` のコンストラクタの第一引数にはフォントのサイズを指定します。単位はピクセルです。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/8/3-0.png?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 大きさ 20 のフォント
	const Font font20{ 20 };

	// 大きさ 40 のフォント
	const Font font40{ 40 };

	// 大きさ 60 のフォント
	const Font font60{ 60 };

	// 大きさ 80 のフォント
	const Font font80{ 80 };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font20(text).draw(20, 20);

		font40(text).draw(20, 60);

		font60(text).draw(20, 120);

		font80(text).draw(20, 200);
	}
}
```


## 4.4 フォントの種類
Siv3D には異なる太さの 7 種類の書体が同梱されています。`Font` のコンストラクタの第二引数において `Typeface::` で太さを指定することで、それらの書体を利用できます。何も指定しなかった場合 `Typeface::Regular` が選択されます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/8/4-0.png?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 細いフォント
	const Font fontThin{ 50, Typeface::Thin };

	// やや細いフォント
	const Font fontLight{ 50, Typeface::Light };

	// 通常のフォント（デフォルト）
	const Font fontRegular{ 50, Typeface::Regular };

	// やや太いフォント
	const Font fontMedium{ 50, Typeface::Medium };

	// 太いフォント
	const Font fontBold{ 50, Typeface::Bold };

	// とても太いフォント
	const Font fontHeavy{ 50, Typeface::Heavy };

	// 非常に太いフォント
	const Font fontBlack{ 50, Typeface::Black };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		fontThin(text).draw(20, 20);
		fontLight(text).draw(20, 70);
		fontRegular(text).draw(20, 120);
		fontMedium(text).draw(20, 170);
		fontBold(text).draw(20, 220);
		fontHeavy(text).draw(20, 270);
		fontBlack(text).draw(20, 320);
	}
}
```


## 4.5 フォントファイルからフォントを読み込んで使う
PC 上にあるフォントファイルから `Font` を作成するには、`Font` のコンストラクタの第二引数に、読み込みたいフォントファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。
![](https://storage.googleapis.com/zenn-user-upload/d2oeqr5lg8eyt73nizj8p0qamu8y)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// RocknRollOne-Regular.ttf をロードして使う
	const Font font{ 50, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"Hello, Siv3D!").draw(20, 20);
	}
}
```


## 4.6 PC にインストールされているフォントを使う
PC にインストールされているフォントは OS ごとに特殊なフォルダに保存されています。そのフォルダのパスを `FileSystem::GetFolderPath()` で取得し、フォントファイル名とつなげることで、ファイルパスを構築できます。`FileSystem::GetFolderPath()` に渡す `SpecialFolder` の種類と OS によって取得できるパスの対応表は次の通りです。macOS のみ 3 つの戻り値が異なります。

|                            | Windows             | macOS                  | Linux       |
|----------------------------|---------------------|------------------------|-------------|
| SpecialFolder::SystemFonts | (OS):/WINDOWS/Fonts/ | /System/Library/Fonts/ | /usr/share/fonts/ |
| SpecialFolder::LocalFonts  | (OS):/WINDOWS/Fonts/ | /Library/Fonts/        | /usr/local/share/fonts/ |
| SpecialFolder::UserFonts   | (OS):/WINDOWS/Fonts/ | ~/Library/Fonts/       | /usr/local/share/fonts/ |

```cpp
# include <Siv3D.hpp>

void Main()
{
# if SIV3D_PLATFORM(WINDOWS)

	const Font font{ 60, FileSystem::GetFolderPath(SpecialFolder::SystemFonts) + U"arial.ttf" };

# elif SIV3D_PLATFORM(MACOS)

	const Font font{ 60, FileSystem::GetFolderPath(SpecialFolder::SystemFonts) + U"Helvetica.dfont" };

# endif

	while (System::Update())
	{
		font(U"Hello, Siv3D!").draw(20, 40);
	}
}
```

`SIV3D_PLATFORM(WINDOWS)` や `SIV3D_PLATFORM(MACOS)` は Siv3D でプラットフォーム別のコードを書くときに使えるマクロです。


# 5. GUI

## 5.1 ボタン
ボタンの表示と入力の取得を実装するときは `SimpleGUI::Button()` 関数を使うと便利です。ボタンのテキストや位置、幅、状態などを設定できます。`SimpleGUI::Button()` は自身が押されたときに `true` を返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/9/1-0.gif?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Red", Vec2{ 100, 100 }))
		{
			Scene::SetBackground(ColorF{ 0.8, 0.1, 0.1 });
		}

		if (SimpleGUI::Button(U"Green", Vec2{ 100, 150 }))
		{
			Scene::SetBackground(ColorF{ 0.1, 0.8, 0.1 });
		}

		if (SimpleGUI::Button(U"Blue", Vec2{ 100, 200 }))
		{
			Scene::SetBackground(ColorF{ 0.1, 0.1, 0.8 });
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

## 5.2 スライダー
スライダーの表示と値の取得を実装するときは `SimpleGUI::Slider()` 関数を使うと便利です。スライダーのテキストや位置、幅、値の範囲などを設定できます。縦方向のスライダーは `SimpleGUI::VerticalSlider()` を使います。`SimpleGUI::Slider()` と `SimpleGUI::VerticalSlider()` は値が変更されたときに `true` を返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/9/2-0.gif?raw=true)

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


## 5.3 チェックボックス
チェックボックスの表示と入力の取得を実装するときは `SimpleGUI::CheckBox()` 関数を使うと便利です。チェックボックスのテキストや位置、幅、状態などを設定できます。`SimpleGUI::CheckBox()` は値が変更されたときに `true` を返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/9/3-0.gif?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	bool checked1 = false;
	bool checked2 = true;
	bool checked3 = false;
	bool checked4 = false;
	bool checked5 = false;
	bool checked6 = false;

	while (System::Update())
	{
		SimpleGUI::CheckBox(checked1, U"Label1", Vec2{ 100, 40 });
		SimpleGUI::CheckBox(checked2, U"Label2", Vec2{ 100, 80 });
		SimpleGUI::CheckBox(checked3, U"Label3", Vec2{ 100, 120 });

		// 幅 200px
		SimpleGUI::CheckBox(checked4, U"Label4", Vec2{ 100, 180 }, 200);

		// 無効化
		SimpleGUI::CheckBox(checked5, U"Label5", Vec2{ 100, 220 }, 200, false);

		// 幅はテキストに合わせる
		SimpleGUI::CheckBox(checked6, U"Label6", Vec2{ 100, 260 }, unspecified, false);
	}
}
```


## 5.4 ラジオボタン
ラジオボタンの表示と入力の取得を実装するときは `SimpleGUI::RadioButtons()` 関数を使うと便利です。ラジオボタンのテキストや位置、幅、状態などを設定できます。`SimpleGUI::RadioButtons()` は値が変更されたときに `true` を返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/9/4-0.gif?raw=true)

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
	constexpr std::array<ColorF, 3> colors = { ColorF{ 0.8, 0.1, 0.1 }, ColorF{ 0.1, 0.8, 0.1 }, ColorF{ 0.1, 0.1, 0.8 } };

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


## 5.5 テキストボックス
テキストボックスを実装するときは `SimpleGUI::TextBox()` 関数を使うと便利です。テキストボックスの位置、幅、文字数の上限、状態などを設定できます。テキストは `TextEditState` 型のオブジェクトによって管理します。`SimpleGUI::TextBox()` は値が変更されたときに `true` を返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/9/5-0.gif?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 30, Typeface::Bold };

	TextEditState tes1;
	TextEditState tes2;
	tes2.text = U"Siv3D"; // デフォルトの文字列
	TextEditState tes3;
	TextEditState tes4;

	while (System::Update())
	{
		SimpleGUI::TextBox(tes1, Vec2{ 100, 40 });

		// .text でテキストにアクセス、.active でアクティブかどうかの状態にアクセス
		font(tes1.text).draw(400, 30, tes1.active ? ColorF{ 1.0, 0.0, 0.0 } : ColorF{ 0.25 });

		SimpleGUI::TextBox(tes2, Vec2{ 100, 100 });

		if (SimpleGUI::Button(U"Clear", Vec2{ 320, 100 }))
		{
			// テキストを消去
			tes2.clear();
		}

		// 幅 100px, 文字数を 4 文字までに制限
		SimpleGUI::TextBox(tes3, Vec2{ 100, 160 }, 100, 4);

		// 無効化
		SimpleGUI::TextBox(tes4, Vec2{ 100, 220 }, 100, 4, false);
	}
}
```


## 5.6 カラーピッカー
![](https://storage.googleapis.com/zenn-user-upload/s4prwn4uxfeju255i6lklk20c6jj)
```cpp
# include <Siv3D.hpp>

void Main()
{
	HSV hsv = Palette::Gray;

	while (System::Update())
	{
		Scene::SetBackground(hsv);

		// カラーピッカー
		SimpleGUI::ColorPicker(hsv, Vec2{ 20, 20 });
	}
}
```


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

```cpp
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
```cpp
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

## 6.3 複数のキーの組み合わせ

### A または B
`|` を使って複数のキーを組み合わせると、そのいずれかが押されているかどうかを判定できます。
```cpp
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
```cpp
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


## 6.4 テキスト入力
`TextInput::UpdateText()` に `String` 型の変数を渡すことで、テキスト入力を処理できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/11/5-0.gif?raw=true)

```cpp
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

## 7.1 マウスカーソルの座標
マウスカーソルの座標は `Cursor::Pos()` を使うと `Point` 型で取得できます。シーンが拡大縮小されている場合には、`Cursor::PosF()` を使うと `Vec2` 型で取得できます。

`Cursor::Pos()` で取得できるマウスカーソル座標は、直前の `System::Update()` の呼び出し時点での座標のため、実際画面に見えているマウスカーソルよりも古い座標を示す場合があります。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/12/1-0.gif?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ Cursor::Pos(), 50 }.draw(Palette::Skyblue);
	}
}
```


## 7.2 マウスカーソルの移動量
直前のフレームからのマウスカーソルの移動量は `Cursor::Delta()` を使うと `Point` 型で取得できます。シーンが拡大縮小されている場合には、`Cursor::DeltaF()` を使うと `Vec2` 型で取得できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/12/2-0.gif?raw=true)

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

図形の `.moveBy()` 関数は、与えられた x, y 成分だけ自身の座標を移動します。一方で似たような名前の `.movedBy()` 関数は、与えられた x, y 成分だけ座標を移動した新しい図形を返し、自身の座標は変更しません。


## 7.3 マウスのボタンの入力状態
マウスのボタンには、以下の `Input` 型のオブジェクトが割り当てられています。

| 定数      | 対応するボタン |
|---------|---------|
| MouseL  | 左ボタン    |
| MouseR  | 右ボタン    |
| MouseM  | 中央ボタン   |
| MouseX1 | 拡張ボタン 1 |
| MouseX2 | 拡張ボタン 2 |
| MouseX3 | 拡張ボタン 3 |
| MouseX4 | 拡張ボタン 4 |
| MouseX5 | 拡張ボタン 5 |

前章のキーボードのキーと同様に、押された瞬間であるかを `.down()`, 押されているかを `.pressed()`, 離された瞬間であるかを `.up()` を使って `bool` 値で取得できます。

|          | down | pressed | up |
|----------|------|---------|----|
| 押していない   |      |         |    |
| 押した瞬間    | ✔    | ✔       |    |
| 押され続けている |      | ✔       |    |
| 離した瞬間    |      |         | ✔  |
| 離され続けている |      |         |    |

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << MouseL.pressed();
		Print << MouseM.pressed();
		Print << MouseR.pressed();
	}
}
```

## 7.4 マウスホイールの回転量
直前のフレームからのマウスホイールのスクロール量は、`Mouse::Wheel()` によって `double` 型で取得できます。水平ホイールのスクロール量は、`Mouse::WheelH()` によって `double` 型で取得できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/12/5-0.gif?raw=true)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Vec2 pos = Scene::Center();

	while (System::Update())
	{
		pos.y -= Mouse::Wheel() * 10;

		pos.x += Mouse::WheelH() * 10;

		RectF{ Arg::center = pos, 200 }.draw();
	}
}
```
