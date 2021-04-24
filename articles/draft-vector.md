---
title: "<vector>"
emoji: "🌱"
type: "tech"
topics: ["cpp","競プロ"]
published: false
---

- 1～??: 動的配列クラス `std::vector` の機能
- ??: `std::vector<bool>`

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

## 5.1 同じ中身であるかを調べる
- ともに同じ型 `Type` の要素を持つ `std::vector<Type>` 型の値 'a', 'b' について、`a == b` は配列の中身が一致するかを `bool` 型の値で返します
- 計算量: `==` は双方の要素数が同じかを調べるため、双方のサイズが異なる場合は $O(1)$, それ以外の場合は $O(N)$ です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::string t = "dog";

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (s == "cat") << '\n';
	std::cout << (t == "cat") << '\n';
	std::cout << (s == s) << '\n';
	std::cout << (s == t) << '\n';
}
```
```txt:出力
true
false
true
false
```

## 5.2 違う文字列であるかを調べる
- `a != b` の少なくとも一方が `std::string` 型の場合、2 つの文字列 `a`, `b` が異なるかを `bool` 型の値で返します
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::string t = "dog";

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (s != "cat") << '\n';
	std::cout << (t != "cat") << '\n';
	std::cout << (s != s) << '\n';
	std::cout << (s != t) << '\n';
}
```
```txt:出力
false
true
false
true
```

## 5.3 文字列の順序比較
- `<`, `<=`, `>`, `>=` 演算子は 2 つの文字列の順序関係を `bool` 型の値で返します
- 基本の順序: `0 < 9 < A < Z < a < z`  
- 参考: [ASCII コード表](https://www.k-cube.co.jp/wakaba/server/ascii_code.html)  
- 順序の例: `A < AA < AB < ABC < Aa < Ab < B < BA < Z < ZZ < a < aA < aa < b < zzz`  
- 大文字と小文字が混在する場合の順序には注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::string t = "dog";

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (s < t) << '\n';
	std::cout << (s < "dog") << '\n';
	std::cout << ("caa" < s) << '\n';
	std::cout << ("Dog" < t) << '\n';
}
```
```txt:出力
true
true
true
true
```


# 6. `std::string` の要素にアクセス

## 6.1 指定した位置の要素にアクセス
- `s[index]` で、指定した位置の要素 (`char` 型) にアクセスします 
- インデックスは最初の文字が `0`, その次が `1`, ..., `(.size() - 1)` です
- 範囲外にアクセスしてはいけません
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s[0] << '\n'; // 0 番目の要素を出力
	std::cout << s[1] << '\n'; // 1 番目の要素を出力
	std::cout << s[2] << '\n'; // 2 番目の要素を出力

	s[2] = 'n'; // 2 番目の要素を書き換え
	std::cout << s << '\n';
}
```
```txt:出力
c
a
t
can
```

## 6.2 先頭の要素にアクセス
- `s[0]` と同等です
- 空の文字列で使うと範囲外アクセスになるため注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s.front() << '\n'; // 先頭の要素を出力

	s.front() = 'h'; // 先頭の要素を書き換え
	std::cout << s << '\n';
}
```
```txt:出力
c
hat
```

## 6.3 末尾の要素にアクセス
- `s[(s.size() - 1)]` と同等です（ただし `0 < s.size()`）
- 空の文字列で使うと範囲外アクセスになるため注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s.back() << '\n'; // 末尾の要素を出力

	s.back() = 'r'; // 末尾の要素を書き換え
	std::cout << s << '\n';
}
```
```txt:出力
t
car
```

## 6.4 指定した位置の要素にアクセス（範囲外アクセスを例外で検出）
- `.at(index)` で、指定した位置の要素 (`char` 型) にアクセスします
- インデックスは最初の文字が `0`, その次が `1`, ..., `(.size() - 1)` です
- `s[index]` と異なり、範囲外アクセスがあった場合に `std::out_of_range` 例外を送出します。そのチェックのための追加のコストがあります
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s.at(0) << '\n'; // 0 番目の要素を出力
	std::cout << s.at(1) << '\n'; // 1 番目の要素を出力
	std::cout << s.at(2) << '\n'; // 2 番目の要素を出力

	s.at(2) = 'n'; // 2 番目の要素を書き換え
	std::cout << s << '\n';
}
```
```txt:出力
c
a
t
can
```

## 6.5 range-based for を使い、各要素に `const` 参照でアクセス
- ループ内で要素を書き換えない場合、`const` 参照を使います
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";

	for (const auto& ch : s) // 要素の個数だけ繰り返すループ、ch は順に各要素への const 参照
	{
		std::cout << ch << '\n';
	}
}
```
```txt:出力
a
p
p
l
e
```

## 6.6 range-based for を使い、各要素に参照でアクセス
- ループ内で要素を書き換える場合、参照を使います
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";

	for (auto& ch : s) // 要素の個数だけ繰り返すループ、ch は順に各要素への参照
	{
		ch += 1;
	}

	std::cout << s << '\n';
}
```
```txt:出力
bqqmf
```


# 7. `std::string` の追加

## 7.1 前後に別の文字列を足した、新しい `std::string` を作成する
- `+` 演算子を使うと、前後に別の文字列を追加した新しい `std::string` を作成できます
- 元の文字列は変更されません
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "school";

	std::string t = ("high " + s); // 前に足した
	std::cout << t << '\n';
	std::cout << t.size() << '\n';

	std::string u = (s + " bus"); // うしろに足した
	std::cout << u << '\n';
	std::cout << u.size() << '\n';

	std::cout << ("my " + s) << '\n'; // 前に足した
}
```
```txt:出力
high school
11
school bus
10
my school
```

## 7.2 末尾に 1 文字追加する
- `.push_back(ch)` で、現在の文字列の末尾に文字 `ch` を追加します
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "t";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.push_back('o'); // 末尾に `o` を追加
	std::cout << s << '\n';
	std::cout << s.size() << '\n';	

	s.push_back('p'); // 末尾に `p` を追加
	std::cout << s << '\n';
	std::cout << s.size() << '\n';	
}
```
```txt:出力
t
1
to
2
top
3
```

## 7.3 末尾に文字列を追加する
- `+= other;` で、現在の文字列の末尾に文字列 `other` を追加します
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "t";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s += "ea"; // 末尾に "ea" を追加
	std::cout << s << '\n';
	std::cout << s.size() << '\n'; 

	std::string t = "cher";
	s += t; // 末尾に "cher" を追加
	std::cout << s << '\n';
	std::cout << s.size() << '\n'; 
}
```
```txt:出力
t
1
tea
3
teacher
7
```


# 8. `std::string` の削除

## 8.1 末尾の要素を削除
- `.pop_back()` は末尾の要素を 1 つ削除します
- 空の文字列で使うと範囲外アクセスになるため注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.pop_back(); // 末尾の要素を削除
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.pop_back(); // 末尾の要素を削除
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:出力
apple
5
appl
4
app
3
```

## 8.2 先頭の要素を削除
- `.erase(it)` は、イテレータ `it` が指す位置の要素を削除します
- 先頭位置のイテレータを返す `.begin()` と組み合わせることで先頭の文字を削除できます
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.erase(s.begin()); // 先頭の要素を削除
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.erase(s.begin()); // 先頭の要素を削除
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:出力
apple
5
pple
4
ple
3
```

## 8.3 文字列を消去
- `.clear()` を使うと、全要素を削除して空の文字列になります
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.clear(); // 要素を全消去
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:出力
apple
5

0
```


# 9. `std::string` の一部の取得

## 9.1 指定した位置以降の文字列を取得
- `.substr(pos)` は、`pos` 番目以降の文字列を新しい `std::string` として返します
- インデックスは 0 から数えます
- 範囲外アクセスに注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "computer";
	std::cout << s.substr(1) << '\n'; // 1 番目の文字以降
	std::cout << s.substr(2) << '\n'; // 2 番目の文字以降
	std::cout << s.substr(5) << '\n'; // 5 番目の文字以降
}
```
```txt:出力
omputer
mputer
ter
```

## 9.2 指定した位置以降、指定した個数文の文字列を取得
- `.substr(pos, count)` は、`pos` 番目以降最大 `count` 個の文字列を新しい `std::string` として返します
- 第 1 引数 `pos` は範囲外アクセスに注意が必要です
- 第 2 引数 `count` は実際の要素数を超えた分は無視されます
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "computer";
	std::cout << s.substr(1, 3) << '\n'; // 1 番目以降最大 3 個
	std::cout << s.substr(2, 2) << '\n'; // 2 番目以降最大 2 個
	std::cout << s.substr(5, 100) << '\n'; // 5 番目以降最大 100 個
}
```
```txt:出力
omp
mp
ter
```


# 10. `std::string` の入れ替え

## 10.1 二つの `std::string` を交換
- `std::string` 型の変数 `a`, `b` の中身を入れ替えるには `std::swap(a, b)` を使います
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::string t = "bird";

	std::swap(s, t);
	std::cout << s << '\n';
	std::cout << t << '\n';
}
```
```txt:出力
bird
apple
```


# 11. `std::string` のイテレータ

削除や挿入、アルゴリズム関数で使うためのイテレータを、以下の関数で取得できます。

## 11.1 先頭位置のイテレータを取得
- `.begin()` は文字列の先頭位置を指すイテレータを返します 
- 空の文字列の場合 `.begin() == .end()` です

## 11.2 終端位置のイテレータを取得
- `.end()` は文字列の終端位置を指すイテレータを返します
- 空の文字列の場合 `.begin() == .end()` です


# 12. 数値から `std::string` への変換

## 12.1 数値を `std::string` に変換
- `std::to_string(n)` は、数 `n` を文字列に変換し、`std::string` 型で返します
```cpp
#include <iostream>
#include <string>

int main()
{
	int a, b;
	std::cin >> a >> b;
	
	std::string s = std::to_string(a + b);
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:入力
300 800
```
```txt:出力
1100
4
```


# 13. 文字列から数値への変換

## 13.1 数が書かれた文字列をパースして整数に変換
- `std::stoi(s, idx, base)` は、文字列 `s` をパースし `int` 型で返します
- `std::stoull(s, idx, base)` は、文字列 `s` をパースし `unsigned long long` 型で返します
- `s` に書かれている数が、変換後の型で表現できる範囲外の場合、`std::out_of_range` 例外が送出されます
```cpp
#include <iostream>
#include <string>
#include <cstdint> // std::uint64_t のため

int main()
{
	std::string s = "1200";
	std::string t = "9876543210";

	int ss = std::stoi(s); // int 型に変換
	std::uint64_t tt = std::stoull(t); // unsigned long long 型に変換

	std::cout << ss << '\n';
	std::cout << tt << '\n';
}
```
```txt:出力
1200
9876543210
```

## 13.2 二進数が書かれた文字列をパースして整数に変換
- `std::stoi(s, idx, base)` は、文字列 `s` を `base` 進数とみなしてパースし `int` 型で返します。`idx` は今回は使いません
- `std::stoull(s, idx, base)` は、文字列 `s` を `base` 進数とみなしてパースし `unsigned long long` 型で返します。`idx` は今回は使いません
- `base` を `2` に、`idx` を `nullptr` にすることで、二進数 01 で書かれた文字列 `s` を十進数の数値に変換できます
- `s` に書かれている数が、変換後の型で表現できる範囲外の場合、`std::out_of_range` 例外が送出されます
```cpp
#include <iostream>
#include <string>
#include <cstdint> // std::uint64_t のため

int main()
{
	std::string s = "0111";
	std::string t = "1000";
	// 1 が 64 個
	std::string u = "1111111111111111111111111111111111111111111111111111111111111111";

	int ss = std::stoi(s, nullptr, 2); // int 型に変換
	int tt = std::stoi(t, nullptr, 2); // int 型に変換
	std::uint64_t uu = std::stoull(u, nullptr, 2); // unsigned long long 型に変換

	std::cout << ss << '\n';
	std::cout << tt << '\n';
	std::cout << uu << '\n';
}
```
```txt:出力
7
8
18446744073709551615
```
