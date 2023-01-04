---
title: "チュートリアル 25 | 設定ファイル"
free: true
---

# 25. 設定ファイル
この章では、CSV, INI, JSON, TOML, XML などの設定ファイルを読み書きする方法を学びます。OpenSiv3D v0.6.6 では次のようなファイル形式の読み書きに対応しています。

| ファイル形式 | 読み込みクラス | 書き出しクラス |
|--|:--:|:--:|
| CSV | `CSV` | `CSV` |
| INI | `INI` | `INI` |
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

	Console << U"-----";

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
	Console << values;

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
TOML ファイルをパースしてデータを読み込むには `TOMLReader` クラスを使います。`TOMLReader` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが存在しない場合やパースに失敗した場合など、ロードに失敗したかどうかは `if (not toml)` で調べられます。

TOML データは次のサンプルの `ShowObject()` 関数のようにして再帰的に全要素を走査できます。また、添え字演算子 `[U"NAME1.NAME2.NAME3..."]` によってパスを指定して目的の値を直接得ることもできます。

```cpp
# include <Siv3D.hpp>

struct Item
{
	// アイテムのラベル
	String label;

	// アイテムの左上座標
	Point pos;
};

// 再帰的に TOML の要素を表示
void ShowTable(const TOMLValue& value)
{
	for (const auto& table : value.tableView())
	{
		switch (table.value.getType())
		{
		case TOMLValueType::Empty:
			Console << U"[Empty]" << table.name;
			break;
		case TOMLValueType::Table:
			Console << U"[Table]" << table.name;
			ShowTable(table.value);
			break;
		case TOMLValueType::Array:
			Console << U"[Array]" << table.name;
			for (const auto& element : table.value.arrayView())
			{
				switch (element.getType())
				{
				case TOMLValueType::String:
					Console << element.getString();
					break;
				case TOMLValueType::Number:
					Console << element.get<double>();
					break;
				case TOMLValueType::Bool:
					Console << element.get<bool>();
					break;
				case TOMLValueType::Date:
					Console << element.getDate();
					break;
				case TOMLValueType::DateTime:
					Console << element.getDateTime();
					break;
				default:
					break;
				}
			}
			break;
		case TOMLValueType::TableArray:
			Console << U"[TableArray]" << table.name;
			for (const auto& table2 : table.value.tableArrayView())
			{
				ShowTable(table2);
			}
			break;
		case TOMLValueType::String:
			Console << U"[String]" << table.name;
			Console << table.value.getString();
			break;
		case TOMLValueType::Number:
			Console << U"[Number]" << table.name;
			Console << table.value.get<double>();
			break;
		case TOMLValueType::Bool:
			Console << U"[Bool]" << table.name;
			Console << table.value.get<bool>();
			break;
		case TOMLValueType::Date:
			Console << U"[Date]" << table.name;
			Console << table.value.getDate();
			break;
		case TOMLValueType::DateTime:
			Console << U"[DateTime]" << table.name;
			Console << table.value.getDateTime();
			break;
		case TOMLValueType::Unknown:
			Console << U"[Unknown]" << table.name;
			break;
		}
	}
}

void Main()
{
	// TOML ファイルからデータを読み込む
	const TOMLReader toml{ U"example/toml/config.toml" };

	if (not toml) // もし読み込みに失敗したら
	{
		throw Error{ U"Failed to load `config.toml`" };
	}

	// TOML データをすべて表示
	ShowTable(toml);

	Console << U"-----";

	// 要素のパスで値を取得
	const String windowTitle = toml[U"Window.title"].getString();
	const int32 windowWidth = toml[U"Window.width"].get<int32>();
	const int32 windowHeight = toml[U"Window.height"].get<int32>();
	const bool windowSizable = toml[U"Window.sizable"].get<bool>();
	const ColorF sceneBackground = toml[U"Scene.background"].get<ColorF>();

	Window::SetTitle(windowTitle);
	Window::Resize(windowWidth, windowHeight);
	Window::SetStyle(windowSizable ? WindowStyle::Sizable : WindowStyle::Fixed);
	Scene::SetBackground(sceneBackground);

	// 数値の配列を TOML データから作成
	Array<int32> values;
	{
		for (const auto& object : toml[U"Array.values"].arrayView())
		{
			values << object.get<int32>();
		}
	}
	Console << values;

	// アイテムの配列を TOML データから作成
	Array<Item> items;
	{
		for (const auto& object : toml[U"Items"].tableArrayView())
		{
			Item item;
			item.label = object[U"label"].getString();
			item.pos = Point{ object[U"pos.x"].get<int32>(), object[U"pos.y"].get<int32>() };
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


## 25.5 XML ファイルからデータを読み込む
XML ファイルをパースしてデータを読み込むには `XMLReader` クラスを使います。`XMLReader` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが存在しない場合やパースに失敗した場合など、ロードに失敗したかどうかは `if (not xml)` で調べられます。

XML データは次のサンプルの `ShowElements()` 関数のようにして再帰的に全要素を走査できます。

```cpp
# include <Siv3D.hpp>

struct Item
{
	// アイテムのラベル
	String label;

	// アイテムの左上座標
	Point pos;
};

// 再帰的に XML の要素を表示
void ShowElements(const XMLElement& element)
{
	for (auto e = element.firstChild(); e; e = e.nextSibling())
	{
		Console << U"<{}>"_fmt(e.name());

		if (const auto attributes = e.attributes())
		{
			Console << attributes;
		}

		if (const auto text = e.text())
		{
			Console << text;
		}

		ShowElements(e);

		Console << U"</{}>"_fmt(e.name());
	}
}

void Main()
{
	// XML ファイルからデータを読み込む
	const XMLReader xml(U"example/xml/config.xml");

	if (not xml) // もし読み込みに失敗したら
	{
		throw Error{ U"Failed to load `config.xml`" };
	}

	// XML データをすべて表示
	ShowElements(xml);

	Console << U"-----";

	String windowTitle;
	int32 windowWidth = Window::DefaultClientSize.x;
	int32 windowHeight = Window::DefaultClientSize.y;
	bool windowSizable = false;
	ColorF sceneBackground{ 0.0 };
	Array<int32> values;
	Array<Item> items;

	// 要素を走査して目的の値を取得
	for (auto elem = xml.firstChild(); elem; elem = elem.nextSibling())
	{
		const String name = elem.name();

		if (name == U"Window")
		{
			for (auto elem2 = elem.firstChild(); elem2; elem2 = elem2.nextSibling())
			{
				const String name2 = elem2.name();

				if (name2 == U"title")
				{
					windowTitle = elem2.text();
				}
				else if (name2 == U"width")
				{
					windowWidth = Parse<int32>(elem2.text());
				}
				else if (name2 == U"height")
				{
					windowHeight = Parse<int32>(elem2.text());
				}
				else if (name2 == U"sizable")
				{
					windowSizable = Parse<bool>(elem2.text());
				}
			}
		}
		else if (name == U"Scene")
		{
			for (auto elem2 = elem.firstChild(); elem2; elem2 = elem2.nextSibling())
			{
				const String name2 = elem2.name();

				if (name2 == U"background")
				{
					sceneBackground = Parse<ColorF>(elem2.text());
				}
			}
		}
		if (name == U"Array")
		{
			for (auto elem2 = elem.firstChild(); elem2; elem2 = elem2.nextSibling())
			{
				values << Parse<int32>(elem2.text());
			}
		}
		if (name == U"Items")
		{
			Item item;

			for (auto elem2 = elem.firstChild(); elem2; elem2 = elem2.nextSibling())
			{
				const String name2 = elem2.name();

				if (name2 == U"label")
				{
					item.label = elem2.text();
				}
				else if (name2 == U"pos")
				{
					Point pos{ 0, 0 };

					for (auto elem3 = elem2.firstChild(); elem3; elem3 = elem3.nextSibling())
					{
						const String name3 = elem3.name();

						if (name3 == U"x")
						{
							pos.x = Parse<int32>(elem3.text());
						}
						else if (name3 == U"y")
						{
							pos.y = Parse<int32>(elem3.text());
						}
					}

					item.pos = pos;
				}
			}

			items << item;
		}
	}

	Window::SetTitle(windowTitle);
	Window::Resize(windowWidth, windowHeight);
	Window::SetStyle(windowSizable ? WindowStyle::Sizable : WindowStyle::Fixed);
	Scene::SetBackground(sceneBackground);

	Console << values;

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


## 25.6 CSV ファイルを書き出す
CSV ファイルを書き出すには、`CSV` の `.writeRow()`, `.write()`, `.newLine()` などで先頭の行からデータを追加し、最後に `.save(path)` で保存します。要素が「,」を含む場合、自動的に「"」で囲んで保存します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	CSV csv;

	// 1 行書き込む場合
	csv.writeRow(U"item", U"price");
	csv.writeRow(U"Sword", 500);

	// 1 項目ずつ書き込む場合
	csv.write(U"Arrow");
	csv.write(400);
	csv.newLine();

	csv.writeRow(U"Shield", 300);
	csv.writeRow(U"Carrot Seed", 20);
	csv.writeRow(Point{ 20,30 }, Palette::Red);

	// 保存
	csv.save(U"tutorial.csv");
	
	while (System::Update())
	{

	}
}
```
出力されるファイル
```csv:tutorial.csv
item,price
Sword,500
Arrow,400
Shield,300
Carrot Seed,20
"(20, 30)","(255, 0, 0, 255)"
```


## 25.7 INI ファイルを書き出す
INI ファイルを書き出すには、`INI` の `.addSection()`, `.write()` でデータを追加し、最後に `.save(path)` で保存します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	INI ini;

	// セクションを追加
	ini.addSection(U"Item");
	ini.addSection(U"Setting");

	ini.write(U"Item", U"Sword", 500);
	ini.write(U"Item", U"Arrow", 400);
	ini.write(U"Item", U"Shield", 300);
	ini.write(U"Item", U"Carrot Seed", 20);
	ini.write(U"Setting", U"pos", Point{ 20, 30 });
	ini.write(U"Setting", U"color", Palette::Red);

	// 保存
	ini.save(U"tutorial.ini");
	
	while (System::Update())
	{

	}
}
```


## 25.8 JSON ファイルを書き出す
JSON ファイルを書き出すには、`JSON` の `operator[]` でデータを追加し、最後に `.save(path)` で保存します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	JSON json;

	json[U"Item"][U"Sword"][U"price"] = 500;
	json[U"Item"][U"Arrow"][U"price"] = 400;
	json[U"Item"][U"Shield"][U"price"] = 300;
	json[U"Item"][U"Carrot Seed"][U"price"] = 20;

	json[U"Setting"][U"pos"] = Point{ 20, 30 };
	json[U"Setting"][U"color"] = Palette::Red;

	json.save(U"tutorial.json");
	
	while (System::Update())
	{

	}
}
```


## 25.9 CSV ファイルを更新する
読み込んだ CSV のデータの一部を変更して保存し直すことができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	CSV csv{ U"example/csv/config.csv" };

	if (not csv)
	{
		throw Error{ U"Failed to load `config.csv`" };
	}

	// データを書き換える
	csv[2][1] = Format(1280);
	csv[3][1] = Format(720);

    // データを追加する
    csv.writeRow(U"Hello.Siv3D", 12345);

	csv.save(U"tutorial.csv");

	while (System::Update())
	{

	}
}
```


## 25.10 INI ファイルを更新する
読み込んだ INI のデータの一部を変更して保存し直すことができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	INI ini{ U"example/ini/config.ini" };

	if (not ini)
	{
		throw Error{ U"Failed to load `config.ini`" };
	}

	// データを書き換える
	ini[U"Window.width"] = 1280;
	ini[U"Window.height"] = 720;

	// データを追加する
	ini.addSection(U"Siv3D");
	ini.write(U"Siv3D", U"message", U"Hello!");

	// データを削除する
	ini.removeSection(U"Item2");

	ini.save(U"tutorial.ini");

	while (System::Update())
	{

	}
}
```


## 25.11 JSON ファイルを更新する
読み込んだ JSON のデータの一部を変更して保存し直すことができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	JSON json = JSON::Load(U"example/json/config.json");

	if (not json)
	{
		throw Error{ U"Failed to load `config.json`" };
	}

	// データを書き換える
	json[U"Window"][U"width"] = 1280;
	json[U"Window"][U"height"] = 720;

	// データを追加する
	json[U"Siv3D"][U"message"] = U"Hello!";

	// データを削除する
	json[U"Items"].erase(2);
	json.erase(U"Array");

	json.save(U"tutorial.json");

	while (System::Update())
	{

	}
}
```


## 25.12 （サンプル）JSON の配列へのアクセス
JSON で配列にアクセスする方法は 2 通りあります。

```cpp
# include <Siv3D.hpp>

JSON MakeJSON()
{
	JSON json;
	json[U"Game"][U"score"] = { 10, 20, 50, 100 };
	return json;
}

void Main()
{
	const JSON json = MakeJSON();

	// by index
	{
		const size_t size = json[U"Game"][U"score"].size();
		for (size_t i = 0; i < size; ++i)
		{
			Print << json[U"Game"][U"score"][i].get<int32>();
		}
	}

	Print << U"----";

	// range based
	{
		for (const auto& elem : json[U"Game"][U"score"].arrayView())
		{
			Print << elem.get<int32>();
		}
	}

	while (System::Update())
	{

	}
}
```