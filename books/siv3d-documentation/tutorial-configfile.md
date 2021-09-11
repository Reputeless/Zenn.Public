---
title: "チュートリアル 25 | 設定ファイル"
free: true
---

# 25. 設定ファイル
この章では、CSV, INI, JSON, TOML, XML などの設定ファイルを読み書きする方法を学びます。OpenSiv3D v0.6.0 では次のようなファイル形式の読み書きに対応しています。

| ファイル形式 | 読み込みクラス | 書き出しクラス |
|--|:--:|:--:|
| CSV | `CSV` | `CSV` |
| INI | `CSV` | `CSV` |
| JSON | `JSON` | `JSON` |
| TOML | `TOMLReader` |  |
| XML | `XMLReader` |  |


## 25.1 CSV ファイルからデータを読み込む
CSV ファイルをパースしてデータを読み込むには `CSV` クラスを使います。`CSV` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが存在しない場合やパースに失敗した場合など、ロードに失敗したかどうかは `if (not csv)` で調べられます。

CSV データは `Array<Array<String>>` の形式で読み込まれ、添え字演算子 `[row][col]` によって row 行 col 列目のテキストを取得できます。row, col は 0 からカウントすることに注意しましょう。

```cpp
# include <Siv3D.hpp>

struct Item
{
	// アイテムのラベル
	String label;

	// アイテムの左上座標
	Point pos;
};

void Main()
{
	// CSV ファイルからデータを読み込む
	const CSV csv{ U"example/csv/config.csv" };

	if (not csv) // もし読み込みに失敗したら
	{
		throw Error{ U"Failed to load `config.csv`" };
	}

	// 全ての行を列挙
	for (size_t row = 0; row < csv.rows(); ++row)
	{
		// 行 (Array<String>) の要素を表示
		Print << csv[row];
	}
	Print << U"-----";

	// 1 行 1 列目のテキストを取得 (0 行, 0 列からカウント）
	const String windowTitle = csv[1][1];

	// 2 行 1 列目のテキストを int32 型の値に変換
	const int32 windowWidth = Parse<int32>(csv[2][1]);

	// 3 行 1 列目のテキストを int32 型の値に変換
	const int32 windowHeight = Parse<int32>(csv[3][1]);

	// 4 行 1 列目のテキストを bool 型の値に変換
	const bool windowSizable = Parse<bool>(csv[4][1]);

	// 5 行 1 列目のテキストを ColorF 型の値に変換
	const ColorF sceneBackground = Parse<ColorF>(csv[5][1]);

	Window::SetTitle(windowTitle);
	Window::Resize(windowWidth, windowHeight);
	Window::SetStyle(windowSizable ? WindowStyle::Sizable : WindowStyle::Fixed);
	Scene::SetBackground(sceneBackground);

	// 6 行 1 列目のテキストを ',' で区切って配列にし、それぞれの要素を int32 型の値に変換
	Array<int32> values = csv[6][1].split(U',').map(Parse<int32>);
	Print << values;

	// アイテムの配列を CSV データから作成
	Array<Item> items;
	{
		const size_t itemsCount = Parse<size_t>(csv[7][1]);
		const size_t baseLine = 8;

		for (auto i : step(itemsCount))
		{
			Item item;
			item.label = csv[baseLine + i * 2][1];
			item.pos = Parse<Point>(csv[baseLine + i * 2 + 1][1]);
			items << item;
		}
	}

	// アイテム描画用のフォント
	const Font font{ 30, Typeface::Bold };

	while (System::Update())
	{
		// アイテムを描画
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };

			rect.draw();

			font(item.label).drawAt(rect.center(), ColorF{ 0.25 });
		}
	}
}
```


## 25.2 INI ファイルからデータを読み込む
INI ファイルをパースしてデータを読み込むには `INI` クラスを使います。`INI` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが存在しない場合やパースに失敗した場合など、ロードに失敗したかどうかは `if (not ini)` で調べられます。

INI データはセクションごとに `HashTable<String, String>` の形式で読み込まれ、添え字演算子 `[U"SECTION.NAME"]` によってセクション SECTION にある名前 NAME のテキストを取得できます。

```cpp
# include <Siv3D.hpp>

struct Item
{
	// アイテムのラベル
	String label;

	// アイテムの左上座標
	Point pos;
};

void Main()
{
	// INI ファイルからデータを読み込む
	const INI ini{ U"example/ini/config.ini" };

	if (not ini) // もし読み込みに失敗したら
	{
		throw Error{ U"Failed to load `config.ini`" };
	}

	// 全てのセクションを列挙
	for (const auto& section : ini.sections())
	{
		// セクション名
		Print << U"[{}]"_fmt(section.section);

		// セクション内のすべてのキーを列挙
		for (const auto& key : section.keys)
		{
			// キーの名前と値
			Print << U"{} = {}"_fmt(key.name, key.value);
		}
	}
	Print << U"-----";

	// Windows セクションの title キーの値（テキスト）を取得
	const String windowTitle = ini[U"Window.title"];

	// Windows セクションの width キーの値（テキスト）を int32 型の値に変換
	const int32 windowWidth = Parse<int32>(ini[U"Window.width"]);

	// Windows セクションの height キーの値（テキスト）を int32 型の値に変換
	const int32 windowHeight = Parse<int32>(ini[U"Window.height"]);

	// Windows セクションの sizable キーの値（テキスト）を bool 型の値に変換
	const bool windowSizable = Parse<bool>(ini[U"Window.sizable"]);

	// Scene セクションの background キーの値（テキスト）を ColorF 型の値に変換
	const ColorF sceneBackground = Parse<ColorF>(ini[U"Scene.background"]);

	Window::SetTitle(windowTitle);
	Window::Resize(windowWidth, windowHeight);
	Window::SetStyle(windowSizable ? WindowStyle::Sizable : WindowStyle::Fixed);
	Scene::SetBackground(sceneBackground);

	// Array セクションの values キーの値（テキスト）を ',' で区切って配列にし、それぞれの要素を int32 型の値に変換
	Array<int32> values = ini[U"Array.values"].split(U',').map(Parse<int32>);
	Print << values;

	// アイテムの配列を INI データから作成
	Array<Item> items;
	{
		const size_t itemsCount = Parse<size_t>(ini[U"Items.count"]);

		for (auto i : step(itemsCount))
		{
			Item item;
			item.label = ini[U"Item{}.label"_fmt(i)];
			item.pos = Parse<Point>(ini[U"Item{}.pos"_fmt(i)]);
			items << item;
		}
	}

	// アイテム描画用のフォント
	const Font font{ 30, Typeface::Bold };

	while (System::Update())
	{
		// アイテムを描画
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };

			rect.draw();

			font(item.label).drawAt(rect.center(), ColorF{ 0.25 });
		}
	}
}
```


## 25.3 JSON ファイルからデータを読み込む
JSON ファイルをパースしてデータを読み込むには `JSON` クラスを使います。読み込みたいテキストファイルのパスを `JSON::Load()` に渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが存在しない場合やパースに失敗した場合など、ロードに失敗したかどうかは `if (not json)` で調べられます。

JSON データは次のサンプルの `ShowObject()` 関数のようにして再帰的に全要素を走査できます。また、添え字演算子 `[U"NAME1"][U"NAME2]...` によってパスを指定して目的の値を直接得ることもできます。

```cpp
# include <Siv3D.hpp>

struct Item
{
	// アイテムのラベル
	String label;

	// アイテムの左上座標
	Point pos;
};

// 再帰的に JSON の要素を表示
void ShowObject(const JSON& value)
{
	switch (value.getType())
	{
	case JSONValueType::Empty:
		Console << U"empty";
		break;
	case JSONValueType::Null:
		Console << U"null";
		break;
	case JSONValueType::Object:
		for (const auto& object : value)
		{
			Console << U"[" << object.key << U"]";
			ShowObject(object.value);
		}
		break;
	case JSONValueType::Array:
		for (const auto& element : value.arrayView())
		{
			ShowObject(element);
		}
		break;
	case JSONValueType::String:
		Console << value.getString();
		break;
	case JSONValueType::Number:
		Console << value.get<double>();
		break;
	case JSONValueType::Bool:
		Console << value.get<bool>();
		break;
	}
}

void Main()
{
	// JSON ファイルからデータを読み込む
	const JSON json = JSON::Load(U"example/json/config.json");

	if (not json) // もし読み込みに失敗したら
	{
		throw Error{ U"Failed to load `config.json`" };
	}

	// JSON データをすべて表示
	ShowObject(json);

	Print << U"-----";

	// 要素のパスで値を取得
	const String windowTitle = json[U"Window"][U"title"].getString();
	const int32 windowWidth = json[U"Window"][U"width"].get<int32>();
	const int32 windowHeight = json[U"Window"][U"height"].get<int32>();
	const bool windowSizable = json[U"Window"][U"sizable"].get<bool>();
	const ColorF sceneBackground = json[U"Scene"][U"background"].get<ColorF>();

	Window::SetTitle(windowTitle);
	Window::Resize(windowWidth, windowHeight);
	Window::SetStyle(windowSizable ? WindowStyle::Sizable : WindowStyle::Fixed);
	Scene::SetBackground(sceneBackground);

	// 数値の配列を JSON データから作成
	Array<int32> values;
	{
		for (const auto& object : json[U"Array"][U"values"].arrayView())
		{
			values << object.get<int32>();
		}
	}
	Print << values;

	// アイテムの配列を JSON データから作成
	Array<Item> items;
	{
		for (const auto& object : json[U"Items"].arrayView())
		{
			Item item;
			item.label = object[U"label"].getString();
			item.pos = Point{ object[U"pos"][U"x"].get<int32>(), object[U"pos"][U"y"].get<int32>() };
			items << item;
		}
	}

	// アイテム描画用のフォント
	const Font font{ 30, Typeface::Bold };

	while (System::Update())
	{
		// アイテムを描画
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };

			rect.draw();

			font(item.label).drawAt(rect.center(), ColorF{ 0.25 });
		}
	}
}
```


## 25.4 TOML ファイルからデータを読み込む

```cpp

```


## 25.5 XML ファイルからデータを読み込む

```cpp

```


## 25.6 CSV ファイルを書き出す

```cpp

```


## 25.7 INI ファイルを書き出す

```cpp

```


## 25.8 JSON ファイルを書き出す

```cpp

```


## 25.9 CSV ファイルを更新する

```cpp

```


## 25.10 INI ファイルを更新する

```cpp

```


## 25.11 JSON ファイルを更新する

```cpp

```

