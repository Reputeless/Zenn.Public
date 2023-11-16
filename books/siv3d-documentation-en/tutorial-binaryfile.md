---
title: "Tutorial 26 | Binary File"
free: true
---

# 26. Binary File
この章では、文字列ではなくバイナリでデータをファイルに保存・読み込みする方法を学びます。

**バイナリファイル**は、データをテキストではなく **バイナリデータ**形式で保存したものです。バイナリファイルでは、`299792458` という `int32` 型の数値を、`"299792458"` という 9 文字（9 バイト) のデータではなく、`00010001110111100111100001001010` のビット列で表される 4 バイトのデータとして保存します。

データをテキスト形式で扱うと、人間には読みやすいですが、プログラムの実行時に数値からテキスト、テキストから数値への変換に計算コストがかかり、消費するファイルサイズも大きくなります。一方、バイナリデータは変換のコストがかからず、最小限のデータ容量しか消費しません。

`int32` や `double` などのプリミティブ型や、プリミティブ型で構成された `trivially copyable` なクラス (`Point`, `Vec2`, `Rect`, `ColorF`) などは、単純にメモリ上のデータをコピーするだけで容易にバイナリデータとして扱えますが、`Array` や `String` など、内部データをポインタで管理するクラスをバイナリデータとして適切に扱うには少し手間がかかります。

この章の後半で説明する **シリアライズ機能** を使うと、`Array` や `String`, その他いくつかの Siv3D の `trivially copyable` でないクラスを簡単にバイナリデータとして扱えるようになります。独自に定義した型をシリアライズに対応させることもできます。


## 26.1 バイナリファイルに単純な値を書き込む
ファイルにバイナリデータを書き込むには `BinaryWriter` クラスの機能を使います。`BinaryWriter` のコンストラクタ引数に、書き込み先のファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが使用中だったり、ファイル名が不正なものだったりしてオープンに失敗したかどうかは `if (!writer)` で調べられます。

同名のファイルがすでに存在する場合はそれを破棄してからオープンし、存在しない場合は新しい空のファイルを作成してオープンします。

`.write()` に `trivially copyable` な型の値を渡すと、その値のバイナリデータをコピーしてファイルの末尾に追加で書き込みます。

ファイルはデストラクタでクローズされるので、明示的にクローズする必要はありません。ファイルをオープン・クローズするタイミングを制御したい場合は、`TextWriter` と同じように、`.open()` を使ってファイルをオープン, `.close()` を使ってファイルをクローズできます。

次のサンプルを実行すると、36 バイトのバイナリファイルが作成されます。

```cpp
# include <Siv3D.hpp>

// ゲームのスコアを記録する構造体
struct GameScore
{
	int32 a, b, c, d;
};

void Main()
{
	// 書き込み用のバイナリファイルをオープン
	BinaryWriter writer{ U"tutorial1.bin" };

	if (not writer) // もしオープンに失敗したら
	{
		throw Error{ U"Failed to open `tutorial1.bin`" };
	}

	// int32 型の値 (4 バイト) を書き込む
	writer.write(777);

	// double 型の値 (8 バイト) を書き込む
	writer.write(3.1415);

	// Point 型の値 (8 バイト) を書き込む
	writer.write(Point{ 123, 456 });

	// GameScore 型の値 (16 バイト) を書き込む
	const GameScore s = { 10, 20, 30, 40 };
	writer.write(s);

	while (System::Update())
	{

	}

	// writer のデストラクタで自動的にファイルがクローズされる
}
```


## 26.2 バイナリファイルから単純な値を読み込む
バイナリファイルからバイナリデータを読み込むには `BinaryReader` クラスの機能を使います。`BinaryReader` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。リリース用のアプリを作るときには埋め込みリソースパスの使用を推奨します。ファイルが存在しない場合など、オープンに失敗したかどうかは `if (not reader)` で調べられます。

オープン直後は、読み込み位置はファイルの先頭にセットされています。`.read()` に `trivially copyable` な型の変数を参照で渡すと、読み込み位置を始点とし、その値のサイズ分のバイナリデータを読み込んでその変数にコピーし、読み込み位置をその分進めて `true` を返します。読み込み位置がすでにファイルの終端にあって、これ以上ファイルから読み込めないときには `false` を返します。

次のサンプルでは、26.1 で作成したバイナリファイルに保存されている値を順に読み出します。

```cpp
# include <Siv3D.hpp>

// ゲームのスコアを記録する構造体
struct GameScore
{
	int32 a, b, c, d;
};

void Main()
{
	// 先ほどのサンプルで作ったバイナリファイルをオープン
	BinaryReader reader{ U"tutorial1.bin" };

	if (not reader) // もしオープンに失敗したら
	{
		throw Error{ U"Failed to open `tutorial1.bin`" };
	}

	// int32 型の値 (4 バイト) を読み込む
	int32 n;
	reader.read(n);
	Print << n;

	// double 型の値 (8 バイト) を読み込む
	double d;
	reader.read(d);
	Print << d;

	// Point 型の値 (8 バイト) を読み込む
	Point pos;
	reader.read(pos);
	Print << pos;

	// GameScore 型の値 (16 バイト) を読み込む
	GameScore s;
	reader.read(s);
	Print << U"{}, {}, {}, {}"_fmt(s.a, s.b, s.c, s.d);

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```


## 26.3 バイナリファイルの読み込み位置を変更する
`BinaryReader` の `.setPos(pos)` で、読み込み位置を指定した場所に移動できます。`.skip(size)` を使うと、指定したサイズ分読み飛ばすことができます。

次のサンプルでは、26.1 で作成したバイナリファイルに保存されている値の一部を読み出します。

```cpp
# include <Siv3D.hpp>

// ゲームのスコアを記録する構造体
struct GameScore
{
	int32 a, b, c, d;
};

void Main()
{
	// 先ほどのサンプルで作ったバイナリファイルをオープン
	BinaryReader reader{ U"tutorial1.bin" };

	if (not reader) // もしオープンに失敗したら
	{
		throw Error{ U"Failed to open `tutorial1.bin`" };
	}

	// 先頭から 4 バイト進んだ位置に移動
	reader.setPos(4);

	// double 型の値 (8 バイト) を読み込む
	double d;
	reader.read(d);
	Print << d;

	// 8 バイト分読み飛ばす
	reader.skip(8);

	// GameScore 型の値 (16 バイト) を読み込む
	GameScore s;
	reader.read(s);
	Print << U"{}, {}, {}, {}"_fmt(s.a, s.b, s.c, s.d);

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```


## 26.4 バイナリファイルに複雑なデータを書き込む
`String` や `Array` のように単純にコピーできない (`trivially copyable` でない) データをバイナリファイルで扱う場合、各種オブジェクトのデータがメモリ上にどのように配置されているかの知識が必要になります。後述するシリアライズ機能を使えば簡単になりますが、まずはシリアライズ機能を使わないサンプルを紹介します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 書き込み用のバイナリファイルをオープン
	BinaryWriter writer{ U"tutorial2.bin" };

	if (not writer) // もしオープンに失敗したら
	{
		throw Error{ U"Failed to open `tutorial2.bin`" };
	}

	// 書き込みたいテキスト
	const String text = U"Hello, Siv3D";

	// 書き込みたいテキストの長さ
	const uint64 length = text.length();

	// テキストの長さを書き込む (8 バイト)
	writer.write(length);

	// 格納されてるデータの先頭ポインタから
	// (4 バイト × 長さ）分のテキストデータを書き込む 
	writer.write(text.data(), (sizeof(char32) * length));

	while (System::Update())
	{

	}

	// writer のデストラクタで自動的にファイルがクローズされる
}
```


## 26.5 バイナリファイルから複雑なデータを読み込む
前項に続いて、`String` や `Array` のように単純にコピーできない (`trivially copyable` でない) データを、シリアライズ機能を使わずにバイナリファイルで扱う方法です。

次のサンプルでは 26.4 で作成したバイナリファイルから、保存した文字列を読み込みます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// バイナリファイルをオープン
	BinaryReader reader{ U"tutorial2.bin" };

	if (not reader) // もしオープンに失敗したら
	{
		throw Error{ U"Failed to open `tutorial2.bin`" };
	}

	// 読み込むテキストの長さを格納する変数
	uint64 length = 0;

	// 読み込んだテキストの格納先
	String text;

	// テキストの長さを読み込む
	reader.read(length);

	if (0 < length)
	{
		// テキストデータの読み込みのためにリサイズ
		text.resize(length);

		// テキストのサイズ分だけデータを読み込む
		reader.read(text.data(), (sizeof(char32) * length));
	}

	Print << length;

	Print << text;

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```


## 26.6 シリアライズ機能を使って、バイナリファイルに複雑なデータを書き込む
シリアライズ機能を使うと、シリアライズに対応したデータ型 (`trivially copyable` でない型も含む) を、バイナリファイルで扱いやすくなります。ファイルにシリアライズ機能を使ってバイナリデータを書き込むには `Serializer<BinaryWriter>` クラスの機能を使います。`Serializer<BinaryWriter>` のコンストラクタ引数に、書き込み先のファイルのパスを渡します。実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが使用中だったり、ファイル名が不正なものだったりしてオープンに失敗したかどうかは `if (not writer)` で調べられます。

`Serializer<BinaryWriter>` に `()` で値を渡すと、そのデータをシリアライズしてファイルの末尾に追加で書き込みます。シリアライズに対応していない型を渡した場合はビルドエラーになります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 書き込み用のバイナリファイルをオープン
	Serializer<BinaryWriter> writer{ U"tutorial3.bin" };

	if (not writer) // もしオープンに失敗したら
	{
		throw Error{ U"Failed to open `tutorial3.bin`" };
	}

	// 書き込みたいテキスト
	const String text = U"Hello, Siv3D";

	// 書き込みたいデータ
	int a = 123, b = 456;

	// バイナリファイルにシリアライズ対応型のデータを書き込む
	writer(text);

	// まとめて記述することもできる
	writer(a, b);

	while (System::Update())
	{

	}

	// writer のデストラクタで自動的にファイルがクローズされる
}
```


## 26.7 シリアライズ機能を使って、バイナリファイルから複雑なデータを読み込む
シリアライズ機能を使って書き込んだデータを読み込むには `Deserializer<BinaryReader>` クラスの機能を使います。`Deserializer<BinaryReader>` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。リリース用のアプリを作るときには埋め込みリソースパスの使用を推奨します。ファイルが存在しない場合など、オープンに失敗したかどうかは `if (not reader)` で調べられます。

`Deserializer<BinaryReader>` に `()` で変数の参照を渡すと、ファイルからデータをデシリアライズし、その値に代入します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// バイナリファイルをオープン
	Deserializer<BinaryReader> reader{ U"tutorial3.bin" };

	if (not reader) // もしオープンに失敗したら
	{
		throw Error{ U"Failed to open `tutorial3.bin`" };
	}

	// 読み込み先のテキスト
	String text;

	// 読み込み先の変数
	int32 a, b;

	// バイナリファイルからシリアライズ対応型のデータを読み込む
	// （文字列や Array は自動でリサイズが行われる）
	reader(text);
	reader(a, b);

	Print << text.length();

	Print << text;

	Print << a << U", " << b;

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```


## 26.8 ユーザ定義型をシリアライズに対応させる
ユーザが定義したクラスをシリアライズに対応させるには、`template <class Archive> void SIV3D_SERIALIZE(Archive& archive)` という public メンバ関数をクラスに実装します。`archive()` に、シリアライズに対応したオブジェクトを渡すコードを書くと、そのクラスはシリアライズ可能になり、`Serializer` や `Deserializer` で使用できるようになります。

```cpp
# include <Siv3D.hpp>

// ユーザデータとゲームのスコアを記録する構造体
struct GameScore
{
	String name;

	int32 id;

	int32 score;

	// シリアライズに対応させるためのメンバ関数を定義する
	template <class Archive>
	void SIV3D_SERIALIZE(Archive& archive)
	{
		archive(name, id, score);
	}
};

void Main()
{
	{
		// 記録したいデータ
		const Array<GameScore> scores =
		{
			{ U"Player1", 111, 1000 },
			{ U"Player2", 222, 2000 },
			{ U"Player3", 333, 3000 },
		};

		// バイナリファイルをオープン
		Serializer<BinaryWriter> writer{ U"tutorial4.bin" };

		if (not writer) // もしオープンに失敗したら
		{
			throw Error{ U"Failed to open `tutorial4.bin`" };
		}

		// シリアライズに対応したデータを記録
		writer(scores);

		// writer のデストラクタで自動的にファイルがクローズされる
	}

	// 読み込み先のデータ
	Array<GameScore> scores;
	{
		// バイナリファイルをオープン
		Deserializer<BinaryReader> reader{ U"tutorial4.bin" };

		if (not reader) // もしオープンに失敗したら
		{
			throw Error{ U"Failed to open `tutorial4.bin`" };
		}

		// バイナリファイルからシリアライズ対応型のデータを読み込む
		// （Array は自動でリサイズが行われる）
		reader(scores);

		// reader のデストラクタで自動的にファイルがクローズされる
	}

	// 読み込んだスコアを確認
	for (const auto& score : scores)
	{
		Print << U"{}(id: {}): {}"_fmt(score.name, score.id, score.score);
	}

	while (System::Update())
	{

	}
}
```

