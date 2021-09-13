---
title: "チュートリアル 01 | Siv3D の基本"
free: true
---


# 1. 基本の構造

この章では、Siv3D プログラミングの基本的な作法を学びます。

## 1.1 インクルードするヘッダ
Siv3D のプログラムを書くときは、いつも `<Siv3D.hpp>` ヘッダをインクルードします。
```cpp
# include <Siv3D.hpp>
```

これだけで、Siv3D のすべての機能を使ってプログラムを書けるようになります。  

C++ の経験者であれば、ほかにも `<iostream>` や `<vector>` などの C++ 標準ライブラリヘッダをインクルードしたくなるかもしれませんが、その必要はありません。すでに `<Siv3D.hpp>` の中で、Siv3D のプログラミングでよく使われる主要な C++ 標準ライブラリヘッダがインクルードされているためです。また、標準ライブラリの機能の多くが、より便利な Siv3D の関数やクラスで再実装されているため、Siv3D の学習の序盤で C++ 標準ライブラリの関数を使うことはめったにありません。

### 不必要なヘッダインクルードの例

- `<string>`
  - Siv3D.hpp 内ですでにインクルードされています。`std::string` の置き換えとして `String` があります 
- `<vector>`
  - Siv3D.hpp 内ですでにインクルードされています。`std::vector` の置き換えとして `Array` があります
- `<fstream>`
  - `std::ofstream` の置き換えとして `TextWriter` や `BinaryWriter`, `std::ifstream` の置き換えとして `TextReader` や `BinaryReader` があります
- `<cmath>`
  - Siv3D.hpp 内ですでにインクルードされています。`Math::` 名前空間に主要な数学関数が用意されています
- `<filesystem>`
  - ファイルシステムに関する機能が `FileSystem::` 名前空間に用意されています 



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

`Main()` が実行される前に初期化処理が完了しているため、ユーザは `Main()` の中で最初から Siv3D の機能を使うことができ、`Main()` の実行が終了したら、ウィンドウの後片付けなどサブシステムの終了処理を Siv3D が自動的に行ってくれます。


## 1.3 最小限のプログラム
これが Siv3D の最小のプログラムです。
```cpp
# include <Siv3D.hpp>

void Main()
{

}
```

ただし、`Main()` は一瞬で終了してしまうので、このプログラムを実行しても、何も起こっていないように見えるでしょう。


## 1.4 ウィンドウを表示し続ける
プログラムがすぐに終了してしまっては、ユーザとインタラクションをするアプリケーションが作れません。`Main()` がずっと続くように **メインループ** を実装します。次のプログラムを実行するとウィンドウが表示され続けます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	// メインループ
	while (System::Update())
	{
		// ここに書いた内容が毎フレーム実行される
	}
}
```
`while` 文によってプログラムが半永久的に続くようになります。くり返しのたびに `System::Update()` がウィンドウの表示や音楽の再生、マウスやキーボードの入力情報などを更新するので、グラフィックスの表示やユーザとのやり取りをプログラムできるようになります。

`System::Update()` は普段は `true` を返すため、メインループはいつまでも続きますが、ウィンドウが閉じられたり、エスケープキーが押されたりするなど、アプリケーションを終了させる特別な **ユーザアクション** が実行されると、以降はずっと `false` を返すようになります。 アプリケーションはこの状態になったら速やかにメインループから抜け、`Main()` を終了させる必要があります。上記のプログラムのように書いていれば、自然にこの通りに動作します。

デフォルトでは次の 3 つのユーザアクションが、アプリケーションを終了させる操作として設定されています

- ウィンドウを閉じる
- エスケープキーを押す
- `System::Exit()` を呼ぶ

この設定は`System::SetTerminationTriggers()` に `UserAction` フラグの組み合わせを渡すことでカスタマイズできます。エスケープキーを押してもすぐに終了しないようにしたいときは、設定をカスタマイズします。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウの閉じるボタンを押すか、System::Exit() を呼ぶのを終了操作に設定,
	// エスケープキーを押しても終了しないようになる
	System::SetTerminationTriggers(UserAction::CloseButtonClicked);

	while (System::Update())
	{

	}
}
```

メインループは、特に指定しない限り、使用しているモニターのリフレッシュレートと同じ速度で繰り返されます。一般的なモニタのリフレッシュレートは毎秒 60, 120, または 144 フレームです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// ウィンドウタイトルに直近のフレームレートを表示
		Window::SetTitle(Profiler::FPS());
	}
}
```


## 1.5 デバッグ表示
画面にメッセージを表示してみましょう。`Print` に向かって、出力の記号 `<<` でテキストを送ると、そのテキストが画面に表示されます。
![](/images/doc_v6/tutorial/1/1.5.png)
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

テキストをプログラムに登場させるときは `U" "` で囲みます。Siv3D では UTF-32 という、日本語を扱いやすい形式でテキストデータを扱うため、ダブルクォーテーション `"` の先頭には常に `U` というプレフィックスを付け、`U"Hello"` のようにします。


## 1.6 さまざまな値の表示
Siv3D で提供される型のほとんどは `Print` で内容を表示できます。
![](/images/doc_v6/tutorial/1/1.6.png)
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
次のように `Print` をメインループの中で使うと、毎フレーム新しいメッセージが追加され、古いメッセージは画面の外に追いやられます。画面外に出た古いメッセージは自動的に消去されます。
![](/images/doc_v6/tutorial/1/1.7.png)
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
![](/images/doc_v6/tutorial/1/1.8.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		// Print したメッセージを消去
		ClearPrint();

		Print << count;

		++count;
	}
}
```


## 1.9 基本的なデータ型
Siv3D でよく使う基本的なデータ型は次のとおりです。

Siv3D で整数を扱うときは、`int` や `unsigned long long` のような標準の型名の代わりに、`int32` や `uint64` のように明示的なサイズを持つ型名を使います。これらの型名を使うことで、プラットフォーム間での移植性が高まり、一貫性のある読みやすいコードになります。

|型名|説明|
|:--:|--|
|`bool`|ブーリアン型 (`false` または `true`)|
|`int8`|符号付き 8-bit 整数型 (-128 ～ 127)|
|`uint8`|符号無し 8-bit 整数型 (0 ～ 255)|
|`int16`|符号付き 16-bit 整数型 (-32,768 ～ 32,767)|
|`uint16`|符号無し 16-bit 整数型 (0 ～ 65,535)|
|`int32`|符号付き 32-bit 整数型 (-2,147,483,648 ～ 2,147,483,647)|
|`uint32`|符号無し 32-bit 整数型 (0 ～ 4,294,967,295)|
|`int64`|符号付き 64-bit 整数型 (-9,223,372,036,854,775,808 ～ 9,223,372,036,854,775,807)|
|`uint64`|符号無し 64-bit 整数型 (0 ～ 18,446,744,073,709,551,615)|
|`int128`|符号付き 128-bit 整数型|
|`uint128`|符号無し 128-bit 整数型|
|`size_t`|オブジェクトのサイズを表現する符号なし 64-bit 整数型 (0 ～ 18,446,744,073,709,551,615)|
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

![](/images/doc_v6/tutorial/1/1.9.png)
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


## 1.10 `Print` 以外の出力
`Print` 以外にも、コンソール出力 `Console`, ログ出力 `Logger`, 音声読み上げ出力 `Say` を使えます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	// コンソール画面に出力
	Console << U"Hello, Console!";

	// ログに出力 (Visual Studio の場合「出力」ウィンドウ）
	Logger << U"Hello, Logger!";

	// 必要に応じて読み上げ言語を設定（OS に読み上げエンジンがインストールされていない場合は使えない）
	TextToSpeech::SetDefaultLanguage(LanguageCode::EnglishUS);
	//TextToSpeech::SetDefaultLanguage(LanguageCode::Japanese);

	// 音声読み上げ出力
	Say << U"Hello, Say!";

	while (System::Update())
	{

	}
}
```


## 1.11 画面の座標系
ウィンドウ内の黒い部分が **画面（シーン）** で、Siv3D はこの領域に文字や図形、画像を表示できます。

画面のサイズは、基本の状態では **幅 800 ピクセル、高さ 600 ピクセル** です。画面上の位置を表す座標系は、一番左上のピクセルを「X 座標 0」「Y 座標 0」を表す `(0, 0)` と表記し、右に進むと X 座標が大きく、下に進むと Y 座標が大きくなります。画面の一番右下のピクセルの座標は `(799, 599)` です。 

`Cursor::Pos()` を使うと、現在のマウスカーソルの座標を `Point` 型で取得できます。`Point` 型の値は X 座標を表す `int32 x` と Y 座標を表す `int32 y` の 2 つの成分を持っています。`Point` 型の値をそのまま丸ごと `Print` に送って表示することもできます。
![](/images/doc_v6/tutorial/1/1.11.png)
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


## 1.12 アプリケーションの基本操作

### プログラムを終了してウィンドウを閉じる

- ウィンドウを閉じる
- エスケープキーを押す
- `System::Exit()` を呼ぶ

この動作をカスタマイズする方法は、チュートリアル 1.4 を参照してください。

### スクリーンショットを保存する

- PrintScreen キーを押す
- F12 キーを押す

実行ファイルがあるディレクトリの `Screenshot` フォルダ、またはピクチャフォルダに実行中の画面のスクリーンショットが保存されます。

（上級者向け）`ScreenCapture::SetShortcutKeys()` を使って、ショートカットキーをカスタマイズできます。

### ライセンス情報を表示する

- F1 キーを押す

アプリケーションで使われているサードパーティ・ソフトウェアのライセンス情報を Web ブラウザで表示します。

（上級者向け）`LicenseManager::` 名前空間の関数を使い、ライセンス情報を追加したり、一覧を取得したりできます。`LicenseManager::ShowInBrowser()` を呼ぶことで明示的にオープンすることもできます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// "Licenses" ボタンが押されたら
		if (SimpleGUI::Button(U"Licenses", Vec2{ 20, 20 }))
		{
			// ライセンス情報を表示
			LicenseManager::ShowInBrowser();
		}
	}
}
```
