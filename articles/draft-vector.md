---
title: "<vector>"
emoji: "🌱"
type: "tech"
topics: ["cpp","競プロ"]
published: false
---

- 1～10: 動的配列クラス `std::vector` の機能
- 11: `std::vector<bool>` に関する注意

# 1. `std::vector` の構築

## 1.1 リストから構築する
- `= { ... }` の中に値を用意して初期化します
- リスト内の値は `std::vector<Type>` の要素の型 `Type` に合わせます
```cpp
#include <iostream>
#include <string>
#include <vector>

struct Point { int x, y; };

int main()
{
	std::vector<int> numbers = { 10, 20, 50, 100 };
	std::cout << numbers.size() << '\n'; // 要素数を出力
	// 保持している要素を出力
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}

	std::cout << "---\n"; // 実行結果を見やすくするための区切り線

	std::vector<std::string> texts = { "apple", "bird", "cat" };
	std::cout << texts.size() << '\n'; // 要素数を出力
	// 保持している要素を出力
	for (const auto& text : texts)
	{
		std::cout << text << '\n';
	}

	std::cout << "---\n"; // 実行結果を見やすくするための区切り線

	std::vector<Point> points = { Point{ 33, 44 }, Point{ 55, 66 } };
	std::cout << points.size() << '\n'; // 要素数を出力
	// 保持している要素を出力
	for (const auto& point : points)
	{
		std::cout << '(' << point.x << ", "  << point.y << ")\n";
	}
}
```
```txt:出力
4
10
20
50
100
---
3
apple
bird
cat
---
2
(33, 44)
(55, 66)
```

## 1.2 (個数) × (要素) で構築する
- `std::vector<Type> v(n, value);` は、`n` 個の `Type(value)` で初期化します
```cpp
#include <iostream>
#include <string>
#include <vector>

struct Point { int x, y; };

int main()
{
	std::vector<int> numbers(5, 100); // 5 個の int(100)
	std::cout << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}

	std::cout << "---\n";

	std::vector<std::string> texts(3, "apple"); // 3 個の std::string("apple");
	std::cout << texts.size() << '\n';
	for (const auto& text : texts)
	{
		std::cout << text << '\n';
	}

	std::cout << "---\n";

	std::vector<Point> points(2, { 33, 44 }); // 2 個の Point{ 33, 44 }
	std::cout << points.size() << '\n'; // 要素数を出力
	// 保持している要素を出力
	for (const auto& point : points)
	{
		std::cout << '(' << point.x << ", "  << point.y << ")\n";
	}
}
```
```txt:出力
5
100
100
100
100
100
---
3
apple
apple
apple
---
2
(33, 44)
(33, 44)
```

## 1.3 個数だけを指定して構築する
- `std::vector<Type> v(n);` は、`n` 個の `Type()` で初期化します
- `Type()` について: 

|Type()|値|
|--|:--:|
|`bool()`|`false`|
|`int()`|`0`|
|`double()`|`0.0`|
|`std::string()`|空の文字列|
|`std::pair<int, int>()`|`std::pair<int, int>(0, 0)`|
|`struct Point { int x, y; };`<br>のときの<br>`Point()`|`Point{ 0, 0 }`|
|`struct Point { int x = -1, y = -1; };`<br>のときの<br>`Point()`|`Point{ -1, -1 }`|

```cpp
#include <iostream>
#include <string>
#include <vector>

struct Point { int x, y; };

int main()
{
	std::vector<int> numbers(5); // int(), つまり 0 が 5 個
	std::cout << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}

	std::cout << "---\n";

	std::vector<std::string> texts(3); // std::string(), つまり空の文字列が 3 個
	std::cout << texts.size() << '\n';
	for (const auto& text : texts)
	{
		std::cout << text << '\n';
		std::cout << text.size() << '\n';
	}

	std::cout << "---\n";

	std::vector<Point> points(2); // Point(), つまり Point{ 0, 0 } が 2 個
	std::cout << points.size() << '\n';
	for (const auto& point : points)
	{
		std::cout << '(' << point.x << ", "  << point.y << ")\n";
	}
}
```
```txt:出力
5
0
0
0
0
0
---
3

0

0

0
---
2
(0, 0)
(0, 0)
```

## 1.4 別の `std::vector<Type>` から構築する
- 別の `std::vector<Type>` 型の中身をコピーして初期化します
- コピー元とコピー先の要素の型 `Type` は一致している必要があります
```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> v = { 10, 20, 50, 100 };
	std::vector<int> numbers = v; // v の値をコピー 
	std::cout << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}

	std::cout << "---\n";

	std::cout << v.size() << '\n'; // 要素数を出力（コピーしたほうは変化しない）
	for (const auto& n : v) // 保持している要素を出力（コピーしたほうは変化しない）
	{
		std::cout << n << '\n';
	}
}
```
```txt:出力
4
10
20
50
100
---
4
10
20
50
100
```

## 1.5 空の配列
- `std::vector` 型のデフォルトコンストラクタは要素数が 0 の空の配列を構築します  
- 空の配列も、何の問題もなくプログラムで使うことができます
```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<int> numbers; // 空の配列を構築
	std::cout << numbers.size() << '\n'; // 要素数は 0
	for (const auto& number : numbers)
	{	
		// 1 回も実行されない
		std::cout << number << '\n';
	}

	std::cout << "---\n";

	std::vector<std::string> texts; // 空の配列を構築
	std::cout << texts.size() << '\n'; // 要素数は 0
	for (const auto& text : texts)
	{	
		// 1 回も実行されない
		std::cout << text << '\n';
	}
}
```
```txt:出力
0
---
0
```

# 2. `std::vector` の入力

`std::vector<Type>` 型の変数をそのまま `std::cin` で使うことはできないため、以下のいずれかの方法を使います
- 方式 A: `Type` 型の変数に `std::cin` を使って要素 1 個分の入力を読み込み、それを `std::vector` に追加する
- 方式 B: あらかじめ入力個数分用意した `std::vector<Type>` の各要素に `std::cin` を行う

2.1 では A の方法を、2.2 では B の方法を使います。

## 2.1 入力された値を `std::vector` に追加する (方式 A)
- `Type` 型の変数に `std::cin` を使って要素 1 個分の入力を読み込み、それを `std::vector` に追加します
- 入力される個数がプログラムを書く時点でわかっていて、なおかつ少ない場合にこの方法が使えます
```cpp
#include <iostream>
#include <vector>

int main()
{
	// 3 個入力されることがわかっている場合
	int a, b, c;
	std::cin >> a >> b >> c;
	std::vector<int> numbers = { a, b, c };

	// 読み込めていることを確認
	std::cout << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}
}
```
```txt:入力
10 20 50
```
```txt:出力
3
10
20
50
```

## 2.2 入力された値を `std::vector` に追加する (方式 B)
- あらかじめ入力個数分のサイズで用意した `std::vector<Type>` の各要素に `std::cin` を行います
- 入力される個数がプログラムを書く時点でわかっている場合にこの方法が使えます
```cpp
#include <iostream>
#include <string>

int main()
{
	// 3 個入力されることがわかっている場合
	std::vector<int> numbers(3); // int(), つまり 0 が 3 個
	for (auto& number : numbers)  // 3 回繰り返す
	{
		std::cin >> number; // 各要素に入力を読み込み
	}

	// 読み込めていることを確認
	std::cout << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}
}
```
```txt:入力
10 20 50
```
```txt:出力
3
10
20
50
```

## 2.3 入力される個数が実行時に与えられる場合
- 方式 B を応用することで、入力される個数が実行時に与えられるケースにも対応できます
- 最初に入力の個数 `N`, 続いて N 個の入力が与えられる問題において、入力されるすべての値を `std::vector` に読み込んで保存します
```cpp
#include <iostream>
#include <vector>

int main()
{
	size_t N;
	std::cin >> N; // 入力の個数 N を読み込む

	std::vector<int> numbers(N); // int(), つまり 0 が N 個
	for (auto& number : nubers) // N 回繰り返す
	{
		std::cin >> number; // 各要素に入力を読み込み
	}

	// 読み込めていることを確認
	std::cout << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}
}
```
```txt:入力 1
2
111 222
```
```txt:出力 1
2
111
222
```
```txt:入力 2
5
11 22 33 44 55
```
```txt:出力 2
5
11
22
33
44
55
```

## 2.4 入力される個数が与えられない場合
- 入力される個数が実行時に与えられない場合、`std::cin >> s` をループします。入力がこれ以上存在しなくなると `if (std::cin >> s)` が `false` になるので、それを検知したらループを終了します
- 入力された値は `.push_back()` で配列の末尾に追加します
```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers; // 個数が不明なので空の配列からスタート
	int in;
	while (std::cin >> in) // 入力がこれ以上存在しなくなるまで繰り返す
	{
		numbers.push_back(in); // 入力されるたびに、配列の末尾に追加
	}	

	// 読み込めていることを確認
	std::cout << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}
}
```
```txt:入力 1
111 222
```
```txt:出力 1
111
222
```
```txt:入力 2
11 22 33 44 55
```
```txt:出力 2
11
22
33
44
55
```


# 3. `std::vector` の要素数

## 3.1 要素数を調べる
- `.size()` は配列が持つ要素数を符号無し整数型の値で返します
```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50, 100 };
	std::cout << numbers.size() << '\n'; // 要素数を出力

	std::vector<std::string> texts(10, "apple");
	std::cout << texts.size() << '\n'; // 要素数を出力

	std::size_t n = texts.size();
	std::cout << n << '\n';
}
```
```txt:出力
4
10
10
```

## 3.2 空の配列であるかを調べる
- `.empty()` は、要素数が 0 の配列（空の配列）であるかを `bool` 型の値で返します
```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<int> numbers;
	std::vector<std::string> texts(10, "apple");

	if (numbers.empty()) // 空の配列なので true
	{
		std::cout << "numbers is empty.\n";
	}

	if (texts.empty()) // 空の配列ではないので false
	{
		std::cout << "texts is empty.\n";
	}
}
```
```txt:出力
numbers is empty.
```


# 4. `std::vector` の代入

## 4.1 別のリストを代入する
- `=` を使って別のリスト `{ ... }` の値を代入します
- リストの要素の型は、`std::vector<Type>` の `Type` に変換できる型である必要があります
- 異なるサイズの配列を代入すると、それに合わせて自身の配列の要素数が変わります
```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50, 100 };
	std::cout << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}

	std::cout << "---\n";

	numbers = { 333, 222, 111 }; // 代入
	std::cout << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}
}
```
```txt:出力
4
10
20
50
100
---
3
333
222
111
```

## 4.2 別の配列を代入する
- `=` を使って別の `std::string<Type>` 型の値を代入します
- 代入元と代入先の要素の型 `Type` は一致している必要があります
- 異なるサイズの配列を代入すると、それに合わせて自身の配列の要素数が変わります
```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50, 100 };
	std::cout << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}

	std::cout << "---\n";

	std::vector<int> t(5, 333); // 5 個の 333
	numbers = t; // 代入
	std::cout << numbers.size() << '\n';
	for (const auto& number : numbers)
	{
		std::cout << number << '\n';
	}
}
```
```txt:出力
4
10
20
50
100
---
5
333
333
333
333
333
```


# 5. `std::vector` の比較

## 5.1 同じ配列であるかを調べる
- ともに同じ型 `Type` の要素を持つ `std::vector<Type>` 型の値 'a', 'b' について、`a == b` は配列の要素数と中身が一致するかを `bool` 型の値で返します
- 計算量: `==` は最初に双方の要素数が同じかを調べるため、双方のサイズが異なる場合は $O(1)$, それ以外の場合は $O(N)$ です
```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> va = { 10, 20, 50, 100 };
	std::vector<int> vb = { 10, 20, 50, 100 };
	std::vector<int> vc(100, 0); // 100 個の 0

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (va == va) << '\n';
	std::cout << (va == vb) << '\n';
	std::cout << (va == vc) << '\n';
	std::cout << (vc == vc) << '\n';
}
```
```txt:出力
true
true
false
true
```

## 5.2 違う配列であるかを調べる
- ともに同じ型 `Type` の要素を持つ `std::vector<Type>` 型の値 'a', 'b' について、`a != b` は配列の要素数や中身が違うかを `bool` 型の値で返します
- 計算量: `!=` は最初に双方の要素数が違うかを調べるため、双方のサイズが異なる場合は $O(1)$, それ以外の場合は $O(N)$ です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::vector<int> va = { 10, 20, 50, 100 };
	std::vector<int> vb = { 10, 20, 50, 100 };
	std::vector<int> vc(100, 0); // 100 個の 0

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (va != va) << '\n';
	std::cout << (va != vb) << '\n';
	std::cout << (va != vc) << '\n';
	std::cout << (vc != vc) << '\n';
}
```
```txt:出力
false
false
true
false
```


# 6. `std::vector` の要素にアクセス

## 6.1 指定した位置の要素にアクセス
- `v[index]` で、指定した位置の要素 (`std::vectorr<Type>` で指定した `Type` 型) にアクセスします 
- インデックスは先頭の要素が `0`, その次が `1`, ..., `(.size() - 1)` です
- 範囲外にアクセスしてはいけません
```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50 };
	std::cout << numbers[0] << '\n'; // 0 番目の要素を出力
	std::cout << numbers[1] << '\n'; // 1 番目の要素を出力
	std::cout << numbers[2] << '\n'; // 2 番目の要素を出力

	numbers[2] = -1; // 2 番目の要素を書き換え
	std::cout << numbers[2] << '\n';
}
```
```txt:出力
10
20
50
-1
```

## 6.2 先頭の要素にアクセス
- `.front()` で、配列の先頭の要素にアクセスします
- `v[0]` と同等です
- 空の配列で使うと範囲外アクセスになるため注意が必要です
```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50 };
	std::cout << numbers.front() << '\n'; // 先頭の要素を出力

	numbers.front() = 1; // 先頭の要素を書き換え
	std::cout << numbers.front() << '\n';
}
```
```txt:出力
10
1
```

## 6.3 末尾の要素にアクセス
- `.back()` で、配列の末尾の要素にアクセスします
- `v[(v.size() - 1)]` と同等です（ただし `0 < v.size()`）
- 空の配列で使うと範囲外アクセスになるため注意が必要です
```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50 };
	std::cout << numbers.back() << '\n'; // 末尾の要素を出力

	numbers.back() = 500; // 末尾の要素を書き換え
	std::cout << numbers.back() << '\n';
}
```
```txt:出力
50
500
```

## 6.4 指定した位置の要素にアクセス（範囲外アクセスを例外で検出）
- `.at(index)` で、指定した位置の要素にアクセスします
- インデックスは最初の文字が `0`, その次が `1`, ..., `(.size() - 1)` です
- `v[index]` と異なり、範囲外アクセスがあった場合に `std::out_of_range` 例外を送出します。そのチェックのための追加のコストがあります
```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50 };
	std::cout << numbers.at(0) << '\n'; // 0 番目の要素を出力
	std::cout << numbers.at(1) << '\n'; // 1 番目の要素を出力
	std::cout << numbers.at(2) << '\n'; // 2 番目の要素を出力

	numbers.at(2) = -1; // 2 番目の要素を書き換え
	std::cout << numbers.at(2) << '\n';
}
```
```txt:出力
10
20
50
-1
```

## 6.5 range-based for を使い、各要素に `const` 参照でアクセス
- ループ内で要素を書き換えない場合、`const` 参照を使います
```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> texts = { "apple", "bird", "cat" };

	for (const auto& text : texts) // 要素の個数だけ繰り返すループ、text は順に各要素への const 参照
	{
		std::cout << text << '\n';
	}
}
```
```txt:出力
apple
bird
cat
```

## 6.6 range-based for を使い、各要素に参照でアクセス
- ループ内で要素を書き換える場合、参照を使います
```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> texts = { "apple", "bird", "cat" };

	for (auto& text : texts) // 要素の個数だけ繰り返すループ、text は順に各要素への参照
	{
		text.push_back('!');
	}

	for (const auto& text : texts) // 変更しないので const 参照でよい
	{
		std::cout << text << '\n';
	}
}
```
```txt:出力
apple!
bird!
cat!
```


# 7. 要素の追加

## 7.1 末尾に要素を 1 個追加する
- `.push_back(ch)` で、現在の配列の末尾に文字 `ch` を追加します
```cpp

```
```txt:出力

```

## 7.2 先頭に要素を 1 個追加する
- `.insert(it, value)` は、イテレータ `it` が指す位置の前に要素 `value` を追加します
- 先頭位置のイテレータを返す `.begin()` と組み合わせることで、配列の先頭に要素を追加できます
```cpp

```
```txt:出力

```

## 7.3 末尾に別の配列を追加する
- `.insert(it, itFirst, itLast)` は、イテレータ `it` が指す位置の前に範囲 `[itFirst, itLast)` の要素を追加します
- 先頭位置のイテレータを返す `.begin()` と、追加したい配列の `.begin() / .end()` を組み合わせることで末尾に別の配列を追加できます
```cpp

```
```txt:出力

```


# 8. `std::vector` の削除

## 8.1 末尾の要素を削除
- `.pop_back()` は末尾の要素を 1 つ削除します
- 空の配列で使うと範囲外アクセスになるため注意が必要です
```cpp

```
```txt:出力

```

## 8.2 先頭の要素を削除
- `.erase(it)` は、イテレータ `it` が指す位置の要素を削除します
- 先頭位置のイテレータを返す `.begin()` と組み合わせることで先頭の要素を削除できます
```cpp

```
```txt:出力

```

## 8.3 全要素を消去
- `.clear()` を使うと、全要素を削除して空の配列になります
```cpp

```
```txt:出力

```


# 9. `std::vector` の入れ替え

## 9.1 二つの `std::vector` を交換
- `std::vector<Type>` 型の変数 `a`, `b` の中身を入れ替えるには `std::swap(a, b)` を使います
- 双方とも要素の型 `Type` が一致している必要があります
```cpp

```
```txt:出力

```


# 10. `std::vector` のイテレータ

削除や挿入、アルゴリズム関数で使うためのイテレータを、以下の関数で取得できます。

## 10.1 先頭位置のイテレータを取得
- `.begin()` は配列の先頭位置を指すイテレータを返します 
- 空の配列の場合 `.begin() == .end()` です

## 10.2 終端位置のイテレータを取得
- `.end()` は配列の終端位置を指すイテレータを返します
- 空の配列の場合 `.begin() == .end()` です


# 11. `std::vector<bool>` の注意
