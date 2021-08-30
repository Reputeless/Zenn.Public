---
title: "チュートリアル 05 | 文字列クラス"
free: true
---

# 5. 文字列クラス
この章では、Siv3D の文字列クラス `String` の基本的な使い方を学びます。

## 5.1 文字列クラス
Siv3D では `String` 型を使って文字列を表現します。`String` は、UTF-32 のコードポイントを表現する `char32` 型（文字）の集合です。UTF-32 の文字や文字列リテラルには、`U'あ'`, `U"Hello"` のように `U` プレフィックスを付けます。

`String` の内部データは `std::u32string` で、格納されている文字列はメモリ上での連続性が保証されています。Siv3D の `String` は C++ の標準ライブラリの文字列クラスより多くのメンバ関数を持ち、様々な便利な機能を提供します。
![](/images/doc_v6/tutorial/5/1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"Siv3D";

	Print << s;

	// 文字列の長さ
	Print << s.size();

	while (System::Update())
	{

	}
}
```


## 5.2 指定したインデックスの文字にアクセス
`[index]` を使って、指定したインデックスの位置にある要素にアクセスできます。`.front()`, `.back()` はそれぞれ先頭、末尾にアクセスします。存在しない要素にアクセスすることはできません。
![](/images/doc_v6/tutorial/5/2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	// 0 番目の要素
	Print << s[0];

	// 2 番目の要素
	Print << s[2];

	// 先頭の要素
	Print << s.front();

	// 末尾の要素
	Print << s.back();

	s[3] = U'4';

	Print << s;

	while (System::Update())
	{

	}
}
```


## 5.3 空の文字列
何も保持していない文字列を「空の文字列」と呼びます。文字列が空であるかは `if (s.isEmpty())` や `if (s)` / `if (not s)` で調べられます。`not` は `!` と同じです。Siv3D では `!` よりも視認性の高い `not` を使うコーディングスタイルを採用しています。
![](/images/doc_v6/tutorial/5/3.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	String s;

	if (s.isEmpty())
	{
		Print << U"s.isEmpty()";
	}

	if (not s)
	{
		Print << U"not s";
	}

	while (System::Update())
	{

	}
}
```


## 5.4 文字・文字列の追加
別の文字を末尾に追加するには `<<` を使います。別の文字列を末尾に追加するには `+=` を使います。
![](/images/doc_v6/tutorial/5/4.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	String s;

	s << U'S' << 'i';

	Print << s;

	s += U"v3D";

	Print << s;

	while (System::Update())
	{

	}
}
```


## 5.5 文字列の消去
`.clear()` を使うと自身の文字列を消去して空の文字列になります。
![](/images/doc_v6/tutorial/5/5.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	Print << s;

	s = U"Hello";

	Print << s;

	s.clear();

	// 何も表示されない
	Print << s;

	s = U"Siv3D";

	Print << s;

	while (System::Update())
	{

	}
}
```


## 5.6 要素の削除
`.pop_front()` は先頭の要素を消去します。`.pop_back()` は末尾の要素を削除します。いずれも空の文字列に対して実行することはできません。
![](/images/doc_v6/tutorial/5/6.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	s.pop_front();

	Print << s;

	s.pop_back();

	Print << s;

	while (System::Update())
	{

	}
}
```


## 5.7 他の文字列型への変換
`String` を `std::string` に変換するには、`.narrow()` を、`std::wstring` に変換するには `.toWstr()` を使います。このほかにも、指定した Unicode 形式に変換する `.toUTF8()`, `.toUTF16()`, `.toUTF32()` などがあります。
![](/images/doc_v6/tutorial/5/7.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const String text = U"Hello, Siv3D!";
	const std::string str = text.narrow();
	const std::wstring wstr = text.toWstr();

	Print << (str == "Hello, Siv3D!");
	Print << (wstr == L"Hello, Siv3D!");

	while (System::Update())
	{

	}
}
```


## 5.8 他の文字列型からの変換
`std::string` を `String` に変換するには `Unicode::Widen()` を、`std::wstring` を `String` に変換するには `Unicode::FromWstring()` を使います。このほかにも、指定した Unicode 形式から変換する `Unicode::FromUTF8()`, `Unicode::FromUTF16()`, `Unicode::FromUTF32()` などがあります。

Siv3D は UTF-32 を標準の文字列エンコーディングとしています。余分な変換のコストが生じるのを防ぐため、`Print` では UTF-32 以外のエンコーディングの文字列を出力しようとするとコンパイルエラーになります。
![](/images/doc_v6/tutorial/5/8.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const std::string str = "Siv3D";
	const std::wstring wstr = L"Siv3D";

	const String text1 = Unicode::Widen(str);
	const String text2 = Unicode::FromWstring(wstr);

	Print << text1;
	Print << text2;

	while (System::Update())
	{

	}
}
```
