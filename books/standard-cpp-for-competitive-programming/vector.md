---
title: "std::vector"
free: true
---

`std::vector`（ベクター）は C++ で動的配列を便利に扱うためのクラスです。標準ライブラリの `<vector>` ヘッダに定義されています。

C++ では `new` やスマートポインタを使って動的配列を扱うこともできますが、`std::vector` が最も簡潔に、かつ安全に動的配列を扱うことができます。特別な理由がない限り、C++ で動的配列を扱う際には `std::vector` を使うことが推奨されます。


# 1. `std::vector` の特徴

## 1.1 高速なランダムアクセス
`std::vector` はメモリ上で要素を連続した領域に格納するため、各要素へのランダムアクセスを高速に行えます。


## 1.2 メモリの動的確保戦略
初期状態でのメモリは、必要な要素数分だけ確保されます。要素数が増えると、より大きなメモリ領域が確保され、既存の要素は新しい領域にコピーされます。この際、新たに確保するメモリ領域のサイズは通常、旧領域の 1.5 倍から 2 倍に設定されており、これによって頻繁なメモリの再確保を抑制しています。このため、`.push_back()` による要素追加は、償却計算量（ならし計算量） $O(1)$ です。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/zenn/competitive-programming/vector/1.png)


## 1.3 性能に関する注意点
`std::vector` の要素は、常に、確保したメモリ領域の先頭から連続して配置されます。配列の末尾への要素の追加や削除は、他の要素の移動が不要であるため高速です（末尾への追加は償却計算量 $O(1)$, 末尾からの削除は $O(1)$）。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/zenn/competitive-programming/vector/2.png)

一方で、配列の先頭または途中での要素の追加・削除は、それ以降の要素が移動する必要があるため、非効率です（計算量 $O(N)$）。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/zenn/competitive-programming/vector/3.png)

基本的に、`std::vector` は末尾での要素の追加・削除を高速に行う設計となっています。


# 2. `std::vector` の構築

## 2.1 リストから構築する
- リスト `{ ... }` に用意した値から `std::vector` を構築します。
- リスト内の値は `std::vector<Type>` の要素の型 `Type` に合わせます。

#### `int` 型の要素を持つ例

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50, 100 };

	// 要素数を出力する
	std::cout << numbers.size() << '\n';

	// 格納している要素を出力する
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	
	std::cout << '\n';
}
```
```txt:出力
4
10 20 50 100
```

### `std::string` 型の要素を持つ例

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words = { "apple", "bird", "cat" };

	// 要素数を出力する
	std::cout << words.size() << '\n';

	// 格納している要素を出力する
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	
	std::cout << '\n';
}
```
```txt:出力
3
apple bird cat
```

### ユーザー定義型の要素を持つ例

```cpp
#include <iostream>
#include <vector>

struct Point { int x, y; };

int main()
{
	std::vector<Point> points = { { 33, 44 }, { 55, 66 } };

	// 要素数を出力する
	std::cout << points.size() << '\n';

	// 格納している要素を出力する
	for (const auto& point : points)
	{
		std::cout << '(' << point.x << ", "  << point.y << ") ";
	}
	
	std::cout << '\n';
}
```
```txt:出力
2
(33, 44) (55, 66)
```


## 2.2 (個数) × (要素) で構築する
- `std::vector<Type> v(n, value);` は、`n` 個の `Type(value)` からなる配列を構築します。
- `n` と `value` の順番を思い出せない時は、「5 個の a」を英語にすると `5 a's` になることから、`n` が先にくることを思い出せます。

### `int` 型の要素を持つ例

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers(5, 100); // 5 個の int(100)
	
	// 要素数を出力する
	std::cout << numbers.size() << '\n';
	
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	
	std::cout << '\n';
}
```
```txt:出力
5
100 100 100 100 100
```

### `std::string` 型の要素を持つ例

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words(3, "apple"); // 3 個の std::string("apple");
	
	// 要素数を出力する
	std::cout << words.size() << '\n';
	
	for (const auto& word : words)
	{
		std::cout << word << ' ';
	}
	
	std::cout << '\n';
}
```
```txt:出力
3
apple apple apple
```

### ユーザー定義型の要素を持つ例

```cpp
#include <iostream>
#include <vector>

struct Point { int x, y; };

int main()
{
	std::vector<Point> points(2, { 33, 44 }); // 2 個の Point({ 33, 44 })
	
	// 要素数を出力する
	std::cout << points.size() << '\n';
	
	// 格納している要素を出力する
	for (const auto& point : points)
	{
		std::cout << '(' << point.x << ", "  << point.y << ") ";
	}
	
	std::cout << '\n';
}
```
```txt:出力
2
(33, 44) (33, 44)
```


## 2.3 要素の個数だけを指定して構築する
- `std::vector<Type> v(n);` は、`n` 個の `Type()` からなる `std::vector` を構築します。
- `Type()` は型によって次のような値になります。

|`Type()`|値|
|--|:--:|
|`bool()`|`false`|
|`int()`|`0`|
|`double()`|`0.0`|
|`std::string()`|空（要素数が 0）の文字列|
|`std::vector<int>()`|空（要素数が 0）の vector|
|`std::pair<int, int>()`|`std::pair<int, int>(0, 0)`|
|`struct Point { int x, y; };`<br>のときの<br>`Point()`|`Point{ 0, 0 }`|
|`struct Point { int x = -1, y = -1; };`<br>のときの<br>`Point()`|`Point{ -1, -1 }`|

### `int` 型の要素を持つ例

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers(5); // 5 個の int()
	
	// 要素数を出力する
	std::cout << numbers.size() << '\n';
	
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}
	
	std::cout << '\n';
}
```
```txt:出力
5
0 0 0 0 0
```

### `std::string` 型の要素を持つ例

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words(3); // 3 個の std::string()
	
	// 要素数を出力する
	std::cout << words.size() << '\n';
	
	for (const auto& word : words)
	{
		std::cout << word << ',';
	}

	std::cout << '\n';
}
```
```txt:出力
3
,,,
```

### ユーザー定義型の要素を持つ例

```cpp
#include <iostream>
#include <vector>

struct Point { int x, y; };

int main()
{
	std::vector<Point> points(2); // 2 個の Point()
	
	// 要素数を出力する
	std::cout << points.size() << '\n';
	
	// 格納している要素を出力する
	for (const auto& point : points)
	{
		std::cout << '(' << point.x << ", "  << point.y << ") ";
	}

	std::cout << '\n';
}
```
```txt:出力
2
(0, 0) (0, 0)
```


## 2.4 別の `std::vector` から構築する
- 別の `std::vector<Type>` オブジェクトをコピーして新しい `std::vector<Type>` を構築します。
- コピー元とコピー先の要素の型 `Type` は一致している必要があります。
```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> v1 = { 10, 20, 50, 100 };
	
	std::vector<int> v2 = v1; // v1 の値をコピーする
	
	std::cout << v2.size() << '\n';
	
	for (const auto& v : v2)
	{
		std::cout << v << ' ';
	}

	std::cout << '\n';

	std::cout << "---\n";

	std::cout << v1.size() << '\n'; // コピー元は変化していない
	
	for (const auto& v : v1) // コピー元は変化していない
	{
		std::cout << v << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
4
10 20 50 100
---
4
10 20 50 100
```

## 2.5 空の配列
- `std::vector` 型のデフォルトコンストラクタは、要素数が 0 である空の `std::vector` を構築します。  
- 空の配列であっても、何の問題もなくプログラムで扱うことができます。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words; // 空の配列を構築する

	std::cout << words.size() << '\n'; // 要素数は 0
	
	for (const auto& word : words)
	{	
		// 1 回も実行されない
		std::cout << word << ',';
	}

	std::cout << '\n';
}
```
```txt:出力
0

```


## 2.6 テンプレート引数を省略する
- `std::vector<Type>` を構築するときのテンプレート引数 `Type` は、与えられたコードからコンパイラが型推論できる場合、省略することができます。
- 多少のコードの短縮になりますが、型推論できないケースもあるため、必ずしも省略する必要はありません。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector numbers = { 10, 20, 50, 100 }; // std::vector<int> numbers = { 10, 20, 50, 100 }; と同じ

	std::cout << numbers.size() << '\n';
	
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
4
10 20 50 100
```

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector numbers(5, 0.1); // std::vector<double> numbers(5, 0.1); と同じ

	std::cout << numbers.size() << '\n';
	
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
5
0.1 0.1 0.1 0.1 0.1
```

以下のケースでは、`std::vector points = { { 33, 44 }, { 55, 66 } };` では型推論できず、かえって長いコードになってしまいます。

```cpp
#include <iostream>
#include <vector>

struct Point { int x, y; };

int main()
{
	std::vector points = { Point{ 33, 44 }, Point{ 55, 66 } }; // std::vector<Point> points = { { 33, 44 }, { 55, 66 } }; と同じ

	std::cout << points.size() << '\n';
	
	for (const auto& point : points)
	{
		std::cout << '(' << point.x << ", "  << point.y << ") ";
	}

	std::cout << '\n';
}
```
```txt:出力
2
(33, 44) (55, 66)
```

文字列リテラルは `std::string` 型ではないため、`std::vector words = { "apple", "bird", "cat" };` の型推論結果は `std::vector<const char*>` となることに注意が必要です。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector words = { "apple", "bird", "cat" }; // std::vector<const char*> words = { "apple", "bird", "cat" }; と同じ

	std::cout << words.size() << '\n';
	
	for (const auto& word : words)
	{
		// std::string 型ではないため、.size() などのメンバ関数は使えない
		//std::cout << word.size() << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
3

```


# 3. `std::vector` への入力
`std::vector<Type>` 型の変数を直接 `std::cin` で使うことはできないため、いくつかの方法から選んでください。

## 3.1 入力された値を `std::vector` に追加する (方法 1)
- 要素 1 個分の入力を `std::cin` を使って読み込み、それを配列に追加する方法です。
- プログラムを書く時点で入力される個数がわかっていて、なおかつ個数が少ない場合に使えます。

```cpp
#include <iostream>
#include <vector>

int main()
{
	// 3 個入力されることがわかっている場合
	int a, b, c;
	std::cin >> a >> b >> c;
	std::vector<int> numbers = { a, b, c };

	// 読み込めていることを確認する
	std::cout << numbers.size() << '\n';
	
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:入力
10 20 50
```
```txt:出力
3
10 20 50
```

## 3.2 入力された値を `std::vector` に追加する (方法 2)
- あらかじめ入力個数分のサイズを用意した `std::vector<Type>` の各要素に、範囲ベース for 文を使って `std::cin` を行います。
- プログラムを書く時点で入力される個数がわかっている場合に使えます。

```cpp
#include <iostream>
#include <vector>

int main()
{
	// 3 個入力されることがわかっている場合
	std::vector<int> numbers(3);
	
	// 先頭から順に、各要素に値を読み込む
	for (auto& number : numbers)
	{
		std::cin >> number;
	}

	// 読み込めていることを確認する
	std::cout << numbers.size() << '\n';

	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:入力
10 20 50
```
```txt:出力
3
10 20 50
```

## 3.3 入力される個数が実行時に与えられる場合
- 3.2 を応用することで、入力される個数が実行時に与えられるケースに対応します。
- 最初に入力の個数 $N$, 続いて $N$ 個の値が入力される問題において、入力されるすべての値を `std::vector` に読み込んで格納するには次のようにします。

```cpp
#include <iostream>
#include <vector>

int main()
{
	int N;
	std::cin >> N; // 入力の個数 N を読み込む

	std::vector<int> numbers(N);

	// 先頭から順に、各要素に値を読み込む
	for (auto& number : numbers)
	{
		std::cin >> number;
	}

	// 読み込めていることを確認する
	std::cout << numbers.size() << '\n';

	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:入力 1
2
111 222
```
```txt:出力 1
2
111 222
```
```txt:入力 2
5
11 22 33 44 55
```
```txt:出力 2
5
11 22 33 44 55
```

## 3.4 入力される個数が与えられない場合
- 一部のコンテストでは、3.4 のような問題で、入力される値の個数が与えられないことがあります。その場合は `std::cin >> s` を `while( )` でループします。
- 入力がこれ以上存在しなくなったとき、`(std::cin >> s)` の条件部分が `false` になり、それに伴ってループが終了します。

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

	// 読み込めていることを確認する
	std::cout << numbers.size() << '\n';

	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:入力 1
111 222
```
```txt:出力 1
2
111 222
```
```txt:入力 2
11 22 33 44 55
```
```txt:出力 2
5
11 22 33 44 55
```


# 4. `std::vector` の要素数

## 4.1 要素数を調べる
- `.size()` は、配列のサイズ（要素数）を符号無し整数型の値で返します。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50, 100 };
	std::cout << numbers.size() << '\n'; // 要素数を出力する

	std::vector<std::string> words(10, "apple");
	std::cout << words.size() << '\n';

	std::size_t n = words.size();
	std::cout << n << '\n';
}
```
```txt:出力
4
10
10
```

## 4.2 空の配列であるかを調べる
- `.empty()` は、配列のサイズが 0（空の配列）であるかを `bool` 型の値で返します。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<int> numbers;
	std::vector<std::string> words(10, "apple");

	if (numbers.empty()) // 空の配列なので true
	{
		std::cout << "numbers is empty.\n";
	}

	if (words.empty()) // 空の配列ではないので false
	{
		std::cout << "words is empty.\n";
	}
}
```
```txt:出力
numbers is empty.
```


## 4.3 内部で確保している配列のサイズを調べる
- 1.2 で説明したように、`std::vector` は内部で実際に必要な要素数よりも大きい配列を確保することがあります。`.capacity()` は、現在確保している配列に格納可能な最大の要素数を返します。
- `.size() <= .capacity()` が常に成り立ちます。
- `.capacity()` が返すサイズ以下の要素数を格納するかぎりにおいては、新しい配列の再確保は行われません。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> v = { 10, 20, 30, 40, 50 };
	std::cout << v.size() << '\n'; // 要素数を出力する
	std::cout << v.capacity() << '\n'; // 確保している配列のサイズを出力する

	for (int i = 0; i < 100; ++i)
	{
		v.push_back(i);
	}

	std::cout << v.size() << '\n';
	std::cout << v.capacity() << '\n';
}
```
```txt:出力例
5
5
105
160
```


# 5. `std::vector` への代入

## 5.1 別のリストを代入する
- `=` を使って別のリスト `{ ... }` の値を代入します。
- リストの要素の型は、`std::vector<Type>` の `Type` に変換できる型である必要があります。
- 現在の配列と要素数が異なるリストを代入すると、それに合わせて自身の配列の要素数が変わります。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50, 100 };
	
	std::cout << numbers.size() << '\n';
	
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';

	std::cout << "---\n";

	numbers = { 333, 222, 111 }; // 別のリストを代入する
	
	std::cout << numbers.size() << '\n';
	
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
4
10 20 50 100
---
3
333 222 111
```

## 5.2 別の `std::vector` を代入する
- `=` を使って別の `std::vector<Type>` オブジェクトの値を代入します。
- 代入元と代入先の要素の型 `Type` は一致している必要があります。
- 異なる要素数の配列を代入すると、それに合わせて自身の配列の要素数が変わります。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50, 100 };
	
	std::cout << numbers.size() << '\n';
	
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';

	std::cout << "---\n";

	std::vector<int> t(5, 333); // 5 個の 333
	
	numbers = t; // t を代入する
	
	std::cout << numbers.size() << '\n';
	
	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
4
10 20 50 100
---
5
333 333 333 333 333
```


# 6. `std::vector` の比較

## 6.1 同じ配列であるかを調べる
- ともに同じ型 `Type` の要素を持つ `std::vector<Type>` 型の値 `a`, `b` について、`a == b` は配列の要素が順序も含め一致するかを `bool` 型の値で返します。
> - `std::vector` どうしの `==` の計算量: 最初に双方の要素数が同じかを調べるため、双方のサイズが異なる場合は $O(1)$, それ以外の場合は $O(N)$

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> v1 = { 10, 20, 50, 100 };
	std::vector<int> v2 = { 10, 20, 50, 100 };
	std::vector<int> v3(100, 0); // 100 個の 0

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (v1 == v1) << '\n';
	std::cout << (v1 == v2) << '\n';
	std::cout << (v1 == v3) << '\n';
	std::cout << (v3 == v3) << '\n';
}
```
```txt:出力
true
true
false
true
```

## 6.2 違う配列であるかを調べる
- ともに同じ型 `Type` の要素を持つ `std::vector<Type>` 型の値 `a`, `b` について、`a != b` は配列の要素が順序を含め異なるかを `bool` 型の値で返します。
> - `std::vector` どうしの `!=` の計算量: 最初に双方の要素数が同じかを調べるため、双方のサイズが異なる場合は $O(1)$, それ以外の場合は $O(N)$

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> v1 = { 10, 20, 50, 100 };
	std::vector<int> v2 = { 10, 20, 50, 100 };
	std::vector<int> v3(100, 0); // 100 個の 0

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (v1 != v1) << '\n';
	std::cout << (v1 != v2) << '\n';
	std::cout << (v1 != v3) << '\n';
	std::cout << (v3 != v3) << '\n';
}
```
```txt:出力
false
false
true
false
```


# 7. 要素へのアクセス

## 7.1 指定した位置の要素にアクセスする
- `v[index]` で、配列内の指定した位置の要素 (`Type` 型) にアクセスします。
- インデックスは 0-indexed です。つまり、先頭の要素のインデックスが `0` で、その次が `1`, ..., `(.size() - 1)` です。
- 範囲外にアクセスしてはいけません。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50 };
	std::cout << numbers[0] << '\n'; // 0 番目の要素を出力する
	std::cout << numbers[1] << '\n'; // 1 番目の要素を出力する
	std::cout << numbers[2] << '\n'; // 2 番目の要素を出力する

	numbers[2] = -1; // 2 番目の要素を書き換える
	std::cout << numbers[2] << '\n';
}
```
```txt:出力
10
20
50
-1
```


## 7.2 先頭の要素にアクセスする
- `.front()` で、配列の先頭の要素にアクセスします。
- 空の配列で使うと範囲外アクセスになるため注意が必要です。
- `v[0]` と同等です。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50 };
	std::cout << numbers.front() << '\n'; // 先頭の要素を出力する

	numbers.front() = 1; // 先頭の要素を書き換える
	std::cout << numbers.front() << '\n';
}
```
```txt:出力
10
1
```

## 7.3 末尾の要素にアクセスする
- `.back()` で、配列の末尾の要素にアクセスします。
- 空の配列で使うと範囲外アクセスになるため注意が必要です。
- 配列が空でなければ `v[(v.size() - 1)]` と同等です。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50 };
	std::cout << numbers.back() << '\n'; // 末尾の要素を出力する

	numbers.back() = 500; // 末尾の要素を書き換える
	std::cout << numbers.back() << '\n';
}
```
```txt:出力
50
500
```

## 7.4 指定した位置の要素にアクセスする（範囲外アクセスを例外で検出）
- `.at(index)` で、指定した位置の要素 (`Type` 型) にアクセスします。
- インデックスは `v[index]` と同じく 0-indexed です。
- 関数の戻り値は `v[index]` と同じですが、範囲外アクセスがあった場合に `std::out_of_range` 例外を送出して知らせてくれます。
- 範囲外アクセスチェックのために内部処理で `if` を使うため、チェックの無い `v[index]` と比べると少し遅くなります。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 10, 20, 50 };
	std::cout << numbers.at(0) << '\n'; // 0 番目の要素を出力する
	std::cout << numbers.at(1) << '\n'; // 1 番目の要素を出力する
	std::cout << numbers.at(2) << '\n'; // 2 番目の要素を出力する

	numbers.at(2) = -1; // 2 番目の要素を書き換える
	std::cout << numbers.at(2) << '\n';
}
```
```txt:出力
10
20
50
-1
```


# 8. 基本的なループによる各要素へのアクセス

## 8.1 範囲ベース `for` 文を使い、各要素に `const` 参照でアクセスする
- `std::vector` に対する範囲ベース `for` 文は、配列の要素数分だけ繰り返すループです。
- ループ内で要素を書き換えない場合、`const` 参照を使います。
- `std::vector` に対する範囲ベース for ループは、**配列の要素数がループ中に変更されない**という前提で動作するため、絶対にループ内でその配列の要素数を増減させる操作をしてはいけません（8.3 参照）。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words = { "apple", "bird", "cat" };

	// 要素の個数だけ繰り返すループ、word は順に各要素への const 参照
	for (const auto& word : words)
	{
		std::cout << word << '\n';
	}
}
```
```txt:出力
apple
bird
cat
```

## 8.2 範囲ベース `for` 文を使い、各要素に参照でアクセスする
- ループ内で要素を書き換える場合、参照を使います
- `std::vector` に対する範囲ベース for ループは、**配列の要素数がループ中に変更されない**という前提で動作するため、絶対にループ内でその配列の要素数を増減させる操作をしてはいけません（8.3 参照）。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words = { "apple", "bird", "cat" };

	// 要素の個数だけ繰り返すループ、word は順に各要素への参照
	for (auto& word : words)
	{
		word.push_back('!');
	}

	for (const auto& word : words) // 変更しないので const 参照でよい
	{
		std::cout << word << '\n';
	}
}
```
```txt:出力
apple!
bird!
cat!
```

## 8.3 範囲ベース `for` 文を使うとき、要素数を変更してはいけない
- `std::vector` に対する範囲ベース for ループは、**配列の要素数がループ中に変更されない**という前提で動作するため、絶対にループ内でその配列の要素数を増減させる操作をしてはいけません。

```cpp:誤りのプログラム
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words = { "apple", "bird", "cat" };

	// 要素の個数だけ繰り返すループ、word は順に各要素への参照
	for (auto& word : words)
	{
		word.push_back('!');
		words.push_back("dog"); // これは絶対にダメ！
	}

	for (const auto& word : words)
	{
		std::cout << word << '\n';
	}
}
```


## 8.4 初期化式をともなう、範囲ベースの `for` 文を使う
- C++20 から、範囲ベースの `for` 文に初期化式を追加できるようになりました。
- 例えば、インデックスが偶数番目の要素を出力する際、変数 `i` を初期化式で定義することで、`i` のスコープをループ内に限定できます。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words = { "zero", "one", "two", "three", "four", "five" };

	for (int i = 0; const auto& word : words) // 変数 i のスコープがループ内に限定される
	{
		if ((i % 2) == 0)
		{
			std::cout << word << '\n';
		}

		++i;
	}
}
```
```txt:出力
zero
two
four
```

- C++17 までは、次のように書く必要がありました。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words = { "zero", "one", "two", "three", "four", "five" };

	int i = 0; // 変数 i のスコープが広くなってしまう

	for (const auto& word : words)
	{
		if ((i % 2) == 0)
		{
			std::cout << word << '\n';
		}

		++i;
	}
}
```
```txt:出力
zero
two
four
```


## 8.5 インデックスを使って各要素にアクセスする
- 通常の `for` 文で、インデックスを使って各要素にアクセスすることができます。
- この方法は、特にループが複雑になった時に、インデックスに使う変数の取り違えや、それにともなう範囲外アクセスのコードを書いてしまうミスの原因になります。必要がない限りは範囲ベースの `for` 文や、9. で説明するビューを使うことを推奨します。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words = { "zero", "one", "two", "three", "four", "five" };

	for (std::size_t i = 0; i < words.size(); ++i)
	{
		std::cout << words[i] << '\n';
	}
}
```
```txt:出力
zero
one
two
three
four
five
```


## 8.6 イテレータを使って各要素にアクセスする
- `std::vector` は、イテレータを使って各要素にアクセスすることができます。
- この方法は、特にループが複雑になった時に、イテレータに使う変数の取り違えや、それにともなう範囲外アクセスのコードを書いてしまうミスの原因になります。必要がない限りは範囲ベースの `for` 文や、9. で説明するビューを使うことを推奨します。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words = { "zero", "one", "two", "three", "four", "five" };

	for (auto it = words.begin(); it != words.end(); ++it)
	{
		// イテレータを使って各要素にアクセスする
		std::cout << *it << '\n';
	}
}
```
```txt:出力
zero
one
two
three
four
five
```


## 8.7 逆イテレータを使って各要素にアクセスする
- `std::vector` は、逆イテレータを使って各要素にアクセスすることができます。
- この方法は、特にループが複雑になった時に、イテレータに使う変数の取り違えや、それにともなう範囲外アクセスのコードを書いてしまうミスの原因になります。必要がない限りは 9. で説明するビューを使うことを推奨します。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words = { "zero", "one", "two", "three", "four", "five" };

	for (auto it = words.rbegin(); it != words.rend(); ++it)
	{
		// 逆イテレータを使って各要素にアクセスする
		std::cout << *it << '\n';
	}
}
```
```txt:出力
five
four
three
two
one
zero
```


# 9. ビューを用いたアクセス
- C++20 から、`std::vector` などレンジ全般に対する**ビュー**を作成し、複雑な設定に基づくレンジ内の一連の要素へのアクセスを簡潔に記述できるようになりました。
- ビューは、新しい `std::vector` を作成したり、既存の `std::vector` を変更したりするのではなく、既存の `std::vector` に対するアクセス方法を変更するだけであるため、通常、その操作に期待される最小のコストで動作します。
	- 例えば 9.1 の逆順ビューは、配列の要素を逆順にアクセスするビューを作成するだけで、配列の要素を実際に逆順に並べ替えるわけではありません。
- ビューに対しては、範囲ベースの `for` 文が使えるため、範囲外アクセスのミスを減らすことができます。

## 9.1 逆順にアクセスするビュー [🟢C++20]
- `std::views::reverse` を使うと、配列に対して逆順にアクセスするビューを作成できます。
- 配列の要素を `std::reverse()` / `std::ranges::reverse()` のように実際に逆順に並べ替えるわけではないため、効率的です。

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <ranges>

int main()
{
	std::vector<std::string> words = { "zero", "one", "two", "three", "four", "five" };

	// 逆順にアクセスするビュー
	for (const auto& word : words | std::views::reverse)
	{
		std::cout << word << '\n';
	}
}
```
```txt:出力
five
four
three
two
one
zero
```


## 9.2 最初の N 個の要素にアクセスするビュー [🟢C++20]
- `std::views::take(N)` を使うと、配列の最初の N 個の要素にアクセスするビューを作成できます。
- 要素数が N 未満の場合は、すべての要素にアクセスするビューを作成します。

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <ranges>

int main()
{
	std::vector<std::string> words = { "zero", "one", "two", "three", "four", "five" };

	// 最初の 3 個の要素にアクセスするビュー
	for (auto& word : words | std::views::take(3))
	{
		word.push_back('!');
	}

	for (const auto& word : words)
	{
		std::cout << word << '\n';
	}
}
```
```txt:出力
zero!
one!
two!
three
four
five
```


## 9.3 最初の N 個の要素を除いた要素にアクセスするビュー [🟢C++20]
- `std::views::drop(N)` を使うと、配列の最初の N 個の要素を除いた要素にアクセスするビューを作成できます。
- 要素数が N 未満の場合は、空のビューを作成します。

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <ranges>

int main()
{
	std::vector<std::string> words = { "zero", "one", "two", "three", "four", "five" };

	// 最初の 3 個の要素を除いた要素にアクセスするビュー
	for (auto& word : words | std::views::drop(3))
	{
		word.push_back('!');
	}

	for (const auto& word : words)
	{
		std::cout << word << '\n';
	}
}
```
```txt:出力
zero
one
two
three!
four!
five!
```


## 9.4 最初の N 個の要素を除いたあとの最初の M 個の要素にアクセスするビュー [🟢C++20]
- ビューを作るためのアダプタは、パイプ `|` でつなげることができます。
- `std::views::drop(N) | std::views::take(M)` を使うと、配列の最初の N 個の要素を除いたあとの最初の M 個の要素にアクセスするビューを作成できます。

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <ranges>

int main()
{
	std::vector<std::string> words = { "zero", "one", "two", "three", "four", "five" };

	// 最初の 3 個の要素を除いたあとの最初の 2 個の要素にアクセスするビュー
	for (auto& word : words | std::views::drop(3) | std::views::take(2))
	{
		word.push_back('!');
	}

	for (const auto& word : words)
	{
		std::cout << word << '\n';
	}
}
```
```txt:出力
zero
one
two
three!
four!
five
```

- `std::views::counted(it, count)` を使って書くこともできます。イテレータ `it` の指す位置が範囲外にならないように注意してください。

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <ranges>

int main()
{
	std::vector<std::string> words = { "zero", "one", "two", "three", "four", "five" };

	// インデックス 3 番目から 2 個の要素にアクセスするビューを作成する
	for (auto& word : std::views::counted((words.begin() + 3), 2))
	{
		word.push_back('!');
	}

	for (const auto& word : words)
	{
		std::cout << word << '\n';
	}
}
```
```txt:出力
zero
one
two
three!
four!
five
```


## 9.5 ビューを `std::vector` に変換する [🟢C++20]
- `std::vector<Type>` に対するビューから新しい `std::vector<Type>` を構築するには、ビューのイテレータペアを使います。

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <ranges>

int main()
{
	std::vector<std::string> words = { "zero", "one", "two", "three", "four", "five" };

	auto view = (words | std::views::take(3));

	std::vector<std::string> words2(view.begin(), view.end());

	for (const auto& word : words2)
	{
		std::cout << word << '\n';
	}
}
```
```txt:出力
zero
one
two
```

- C++23 になると、`std::ranges::to<std::vector>()` を使うことで、ビューを `std::vector` に変換できるようになります。
- AtCoder の gcc 12.2 では実装されていません。

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <ranges>

int main()
{
	std::vector<std::string> words = { "zero", "one", "two", "three", "four", "five" };

	std::vector<std::string> words2 = (words | std::views::take(3) | std::ranges::to<std::vector>());

	for (const auto& word : words2)
	{
		std::cout << word << '\n';
	}
}
```
```txt:出力
zero
one
two
```


# 10. `std::vector` への追加

## 10.1 末尾に要素を追加する
- `.push_back(value)` で、配列の末尾に要素 `value` を追加します。
- 1.1 を見るとわかるように、配列の末尾への要素追加は非常に軽量な操作になります。
- 要素を追加するための配列のキャパシティが不足している場合には、より広い配列の再確保が行われます。その際には余裕を持ったサイズ（例えばその時点でのサイズの 2 倍など）の領域を確保することで、追加操作のたびに毎回配列を再確保するような非効率な事態が防がれています。
- そのため、`.push_back()` による要素追加は、償却計算量（ならし計算量） $O(1)$ です。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 1, 5, 10, 50, 100 };

	numbers.push_back(500); // 末尾に要素を追加する

	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
1 5 10 50 100 500
```

## 10.2 先頭に要素を追加する
- `.insert(it, value)` は、イテレータ `it` が指す位置の前に要素 `value` を追加します。
- 先頭位置のイテレータを返す `.begin()` と組み合わせることで、配列の先頭に要素を追加できます。
- 1.1 を見るとわかるように、配列の末尾以外への要素追加を行うと、それ以降の要素をすべて右へずらす必要が生じます。そのため、配列の先頭への要素追加の計算量は、配列の要素数を $N$ としたとき、$O(N)$ となることに注意してください。
	- 先頭への要素追加を $O(1)$ で行うことができる`std::deque` と比べると、`std::vector` での先頭への要素追加は重い操作であることがわかります。競技プログラミングにおいては避けるべきです。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 1, 5, 10, 50, 100 };

	numbers.insert(numbers.begin(), 500); // 先頭に要素を追加する

	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
500 1 5 10 50 100
```

## 10.3 末尾に配列を追加する
- `v.insert(v.end(), oter.begin(), other.end())` で、配列の末尾に別の配列 `other` を追加します。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 1, 5, 10, 50, 100 };

	std::vector<int> other = { 500, 1000, 2000, 5000, 10000 };

	numbers.insert(numbers.end(), other.begin(), other.end()); // 末尾に配列を追加する

	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
1 5 10 50 100 500 1000 2000 5000 10000
```

- C++23 になると、`v.append_range(other)` を使うことで同じ操作ができます。
- AtCoder の gcc 12.2 では実装されていません。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 1, 5, 10, 50, 100 };

	std::vector<int> other = { 500, 1000, 2000, 5000, 10000 };

	numbers.append_range(other); // 末尾に配列を追加する

	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
1 5 10 50 100 500 1000 2000 5000 10000
```


# 11. 要素の削除


## 11.1 末尾の要素を削除する
- `.pop_back()` は末尾の要素を 1 つ削除します。
- 計算量は $O(1)$ です。
- 空の配列で使うと範囲外アクセスになるため注意が必要です。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 1, 5, 10, 50, 100 };

	numbers.pop_back(); // 末尾の要素を削除する

	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
1 5 10 50
```

## 11.2 先頭の要素を削除する
- `.erase(it)` は、イテレータ `it` が指す位置の要素を削除します。
- 先頭位置のイテレータを返す `.begin()` と組み合わせることで先頭の文字を削除できます。
- 1.1 を見るとわかるように、配列の末尾以外への要素の削除を行うと、それ以降の要素をすべて左へずらす必要が生じます。そのため、配列の先頭要素を削除する計算量は、配列の長さを $N$ としたとき、$O(N)$ となることに注意してください。
	- 先頭要素の削除を $O(1)$ で行うことができる `std::deque` と比べると、`std::vector` での先頭要素の削除は重い操作であることがわかります。競技プログラミングにおいては避けるべきです。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 1, 5, 10, 50, 100 };

	numbers.erase(numbers.begin()); // 先頭の要素を削除する

	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
5 10 50 100
```


## 11.3 末尾の N 個の要素を削除する
- `.erase(itBegin, itEnd)` は、イテレータ [`itBegin`, `itEnd`) の範囲の要素を削除します。
- `itBegin` に `.end() - N` を、`itEnd` に `.end()` を指定することで、末尾の N 個の文字を削除できます。
- `.end() - N` が範囲外にならないように注意してください。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 1, 5, 10, 50, 100 };

	numbers.erase((numbers.end() - 3), numbers.end()); // 末尾の 3 個の要素を削除する

	for (const auto& number : numbers)
	{
		std::cout << number << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
1 5
```


## 11.4 配列を消去する
- `.clear()` を使うと、全要素を削除して空の配列になります。
- 一般的な `std::vector` の実装は、`.clear()` をしても、確保した配列領域はそのまま保持されます（`.capacity()` が変わりません）。そのオブジェクトを再利用して新しい要素を追加するときのメモリ確保のコストを抑制できますが、使用しない場合はメモリを無駄に占有することになります。
- 確保した配列のサイズを、現在の要素数に合わせてコンパクトにするには、追加で `.shrink_to_fit()` を使います。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 1, 5, 10, 50, 100 };

	for (int i = 0; i < numbers.size(); i++)
	{
		std::cout << numbers[i] << ' ';
	}

	std::cout << '\n';
	std::cout << numbers.size() << '\n';
	std::cout << numbers.capacity() << '\n';

	std::cout << "----\n";

	numbers.clear(); // 要素を全消去する
	std::cout << numbers.size() << '\n';
	std::cout << numbers.capacity() << '\n';

	std::cout << "----\n";

	numbers.shrink_to_fit(); // 確保している配列のサイズを現在の要素数に合わせてコンパクトにする
	std::cout << numbers.size() << '\n';
	std::cout << numbers.capacity() << '\n';
}
```
```txt:出力例
1 5 10 50 100
5
5
----
0
5
----
0
0
```


# 12. `std::vector` の交換

## 12.1 二つの `std::vector` を交換する
- 2 つの `std::vector<Type>` 型の変数 `a`, `b` の中身を入れ替えたい場合は `std::swap(a, b)` を使います。
- 双方とも要素の型 `Type` が一致している必要があります。
- 実際に配列の要素をひとつずつ入れ替えるのではなく、内部のポインタなど、数個の変数を交換するだけなので、格納している配列の要素数によらず、計算量は $O(1)$ です。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> v1 = { 1, 5, 10, 50, 100 };

	std::vector<int> v2 = { 11, 22, 33 };

	std::swap(v1, v2);

	for (const auto& e : v1)
	{
		std::cout << e << " ";
	}

	std::cout << '\n';

	for (const auto& e : v2)
	{
		std::cout << e << " ";
	}

	std::cout << '\n';
}
```
```txt:出力
11 22 33
1 5 10 50 100
```

- C++20 以降では、同じ操作を行う `std::ranges::swap(a, b)` を使うことが推奨されます。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> v1 = { 1, 5, 10, 50, 100 };

	std::vector<int> v2 = { 11, 22, 33 };

	std::ranges::swap(v1, v2);

	for (const auto& e : v1)
	{
		std::cout << e << " ";
	}

	std::cout << '\n';

	for (const auto& e : v2)
	{
		std::cout << e << " ";
	}

	std::cout << '\n';
}
```
```txt:出力
11 22 33
1 5 10 50 100
```


# 13. `std::vector` のイテレータ

削除や挿入の位置指定や、アルゴリズム関数で使うためのイテレータを、以下の関数で取得できます。イテレータを取得した時点から配列のサイズが変更されると、それ以前のイテレータは無効になることがあるため、イテレータを変数に保存して長い間保持することは避けるべきです。

## 13.1 先頭位置のイテレータを取得する
- `.begin()` は配列の先頭位置を指すイテレータを返します。
- 空の配列の場合 `.begin() == .end()` です。

## 13.2 終端位置のイテレータを取得する
- `.end()` は配列の終端位置を指すイテレータを返します。
- 空の配列の場合 `.begin() == .end()` です。

## 13.3 末尾を指す逆イテレータを取得する
- `.rbegin()` は配列の末尾を指す逆イテレータを返します。
- 空の配列の場合 `.rbegin() == .rend()` です。

## 13.4 先頭の前を指す逆イテレータを取得する
- `.rend()` は配列の先頭の前を指す逆イテレータを返します。
- 空の配列の場合 `.rbegin() == .rend()` です。


# 14. `std::vector<bool>` の注意
C++ 標準の `std::vector<bool>` は、1 バイトに `bool` 型の値を 8 個分記録することで、単純な `bool` の配列よりもメモリ消費量を小さくするという特殊な仕様になっています。そのため、通常の `std::vector` とは一部の機能の挙動が異なります。

## 14.1 `std::vector<int>` と `std::vector<bool>` の挙動の違い
- `std::vector<bool>` では、個別の要素への参照やポインタを取得できない制約があります。
- `std::vector<bool>` における `v[index]`, `.front()`, `.back()`, `.at(index)` は、`bool` 型の参照ではなく、「プロキシ」と呼ばれる特殊なオブジェクトを返します。
- このプロキシは、次のように `auto` と組み合わせたときに、通常の値とは異なる挙動をします。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> numbers = { 0, 0, 0 };
	auto n = numbers.front(); // int 型
	n = 1; // numbers[0] は変更されない
	std::cout << numbers.front() << '\n';

	std::vector<bool> booleans = { false, false, false };
	auto b = booleans.front(); // プロキシ型
	b = true; // booleans[0] を false から true に変更できてしまう
	std::cout << std::boolalpha << booleans.front() << '\n';
}
```
```txt:出力
0
true
```

## 14.2 `std::vector<bool>` 固有の機能
- 一方で、`std::vector<bool>` にのみ提供される機能があります。
- `.flip()` は配列内の `false` / `true` をすべて反転します。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<bool> booleans = { false, false, true, true };
	
	booleans.flip(); // 全要素の bool 値を反転する

	std::cout << std::boolalpha;

	for (const auto& boolean : booleans)
	{
		std::cout << boolean << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
true true false false
```


## 14.3 `std::vector<bool>` の特殊な挙動を回避する方法
`std::vector<bool>` の特殊性を理解していれば、実用上大きく困ることはありません。しかし、通常の `std::vector` の挙動に近い `bool` 型の動的配列を使いたい場合には、次のような方法があります。

- 方式 A: `std::vector<char>` で代替する
- 方式 B: `std::basic_string<bool>` で代替する
- 方式 C: `std::deque<bool>` で代替する

### 方式 A: `std::vector<char>` で代替する
- 利点: `std::vector` と同じ操作方法が使えます。
- `char(0)` を `false` として、`char(1)` を `true` として扱います。
- 欠点: `char` → `bool` への明示的な変換が必要です。
- 欠点: 配列の要素に `'A'` や `'z'` など、`bool` 型以外の値を代入できてしまいます。

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<char> booleans(3, false);
	
	booleans.push_back(true);
	
	booleans.front() = true;

	if (booleans.front())
	{
		std::cout << "booleans.front() is true\n";
	}

	std::cout << std::boolalpha;

	for (const auto& boolean : booleans)
	{
		std::cout << static_cast<bool>(boolean) << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
booleans.front() is true
true false false true
```


### 方式 B: `std::basic_string<bool>` で代替する
- 利点: `std::vector` とほぼ同じ操作方法が使えます。
- 利点: 要素は常に `bool` 型の値しか格納しません。
- 欠点: `std::basic_string<bool> booleans(100);` のような、要素の個数のみでの構築ができません。代わりに `std::basic_string<bool> booleans(100, false);` を使います。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::basic_string<bool> booleans(3, false);
	
	booleans.push_back(true);
	
	booleans.front() = true;

	if (booleans.front())
	{
		std::cout << "booleans.front() is true\n";
	}

	std::cout << std::boolalpha;

	for (const auto& boolean : booleans)
	{
		std::cout << boolean << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
booleans.front() is true
true false false true
```


### 方式 C: `std::deque<bool>` で代替する
- 利点: `std::vector` とほぼ同じ操作方法が使えます。
- 利点: 要素は常に `bool` 型の値しか格納しません。
- 欠点: `std::deque` の性質として、要素がメモリ連続で配置されないため、使い方によっては実行時性能が低下することがあります。

```cpp
#include <iostream>
#include <deque>

int main()
{
	std::deque<bool> booleans(3, false);
	
	booleans.push_back(true);
	
	booleans.front() = true;

	if (booleans.front())
	{
		std::cout << "booleans.front() is true\n";
	}

	std::cout << std::boolalpha;

	for (const auto& boolean : booleans)
	{
		std::cout << boolean << ' ';
	}

	std::cout << '\n';
}
```
```txt:出力
booleans.front() is true
true false false true
```
