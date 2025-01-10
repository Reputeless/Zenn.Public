---
title: "auto"
free: true
---

- 読み方: オート

## 1. 基本的な使い方

### 1.1 概要
- `auto` キーワードは、型推論を活用して冗長な型指定を省略できる機能
- 競技プログラミングでは、イテレータやラムダ式を扱う際に重宝する

### 1.2 基本的な使い方
- `auto` はコンパイル時に型推論を行う
- 初期化子（右辺の式）から型を推論し、その型に置き換わる
	- 動的な型付けではない

```cpp
#include <iostream>

bool IsEven(int x)
{
	return (x % 2 == 0);
}

int main()
{
	auto a = 123; // a は int 型
	auto b = 3.14; // b は double 型
	auto c = IsEven(12); // c は bool 型
	const auto d = 456; // d は const int 型

	std::cout << a << '\n'; // 123
	std::cout << b << '\n'; // 3.14
	std::cout << std::boolalpha << c << '\n'; // true
	std::cout << d << '\n'; // 456
}
```

:::message
- ただし、上記のような状況では、`auto` を使うよりも型名を明示的に書くほうが好ましい

```cpp
	int a = 123;
	double b = 3.14;
	bool c = IsEven(12);
	const int d = 456;
```

- 実際に `auto` を使うべきケースは、「4. 活用する場面」で説明する
:::


### 1.3 参照や const の除去
- `auto` では、参照や `const` は推論の型から除去される
	- トップレベルの `const` は外れるが、参照先の `const` は外れない

```cpp
#include <iostream>
#include <string>

void Show(const std::string& s)
{
	auto t = s; // t は std::string 型, コピーが発生
	t.push_back('!');
	std::cout << t << '\n';
}

int main()
{
	const int a = 123;
	auto b = a; // b は int 型
	b = 456;

	Show("Hello"); // Hello!
}
```

- 必要に応じて `const` や `&` を明示的に書く
	- 参照先が `const` であることは保持される

```cpp
#include <iostream>
#include <string>

void Show(const std::string& s)
{
	const auto& t = s; // t は const std::string& 型, コピーは発生しない
	auto& u = s; // 参照先が const であることは保持され, u も const std::string& 型, コピーは発生しない
	//t.push_back('!');
	//u.push_back('!');
	std::cout << t << ' ' << u << '\n';
}

int main()
{
	const int a = 123;
	const auto b = a; // b は const int 型
	//b = 456;

	Show("Hello"); // Hello Hello
}
```


## 2. `auto` が使えないケース

### 2.1 初期化子が無い場合
- あとで値を代入していたとしても `auto` は使えない

```cpp
int main()
{
	// コンパイルエラー
	auto a;
	a = 123;
}
```

### 2.2 異なる型の変数を同時に宣言する場合

```cpp
int main()
{
	// OK
	auto a = 1, b = 2;

	// コンパイルエラー
	auto c = 1, d = 0.5;
}
```

### 2.3 非静的メンバ変数の宣言

```cpp
struct Point
{
	 // コンパイルエラー
	auto x = 0;
	auto y = 0;
};
```

## 3. 注意が必要なケース

### 3.1 文字列リテラル
- 文字列リテラルは `std::string` ではなく `const char*` 型として推論される

```cpp
#include <iostream>
#include <string>

int main()
{
	auto s = "Hello"; // a は const char* 型

	//std::cout << s.size() << '\n'; // コンパイルエラー

	std::cout << s << '\n'; // Hello
}
```

- 次のようにすると `std::string` 型として推論される

```cpp
#include <iostream>
#include <string>

int main()
{
	auto s = std::string{ "Hello" }; // a は std::string 型

	std::cout << s.size() << '\n'; // 5

	std::cout << s << '\n'; // Hello
}
```


### 3.2 初期化リスト
- `{ ... }` は、配列や `std::vector` ではなく、初期化リスト `std::initializer_list<Type>` 型として推論される
	- `...` 内の要素がすべて同じ型である必要がある

```cpp
#include <iostream>

int main()
{
	auto a = { 100, 200, 300 }; // a は std::initializer_list<int> 型

	std::cout << a.size() << '\n'; // 3

	for (const auto& elem : a)
	{
		std::cout << elem << ' '; // 100 200 300
	}

	std::cout << '\n';
}
```


## 4. 活用する場面
- `auto` は次のような場面で活用する

### 4.1 イテレータの型名の省略
- イテレータの長い型名を省略できる

```cpp
#include <iostream>
#include <vector>
#include <map>
#include <string>
#include <algorithm>

int main()
{
	{
		std::vector<int> v = { 3, 1, 4, 1, 5 };
		//std:vector<int>::iterator it = std::max_element(v.begin(), v.end());
		auto it = std::max_element(v.begin(), v.end());
		std::cout << *it << '\n'; // 5
	}

	{
		std::map<std::string, int> m = { { "Alice", 100 }, { "Bob", 200 }, { "Charlie", 300 } };
		//std::map<std::string, int>::iterator it = m.find("Bob");
		auto it = m.find("Bob");
		std::cout << it->second << '\n'; // 200
	}
}
```

### 4.2 範囲 for 文でのイテレーション
- 要素の型が変わっても、`auto` を使っていればコードの修正が少なくて済む

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> v = { 3, 1, 4, 1, 5 };

	//std::vector<double> v = { 0.3, 0.1, 0.4, 0.1, 0.5 };

	for (auto& elem : v)
	{
		elem *= 2;
	}

	for (const auto& elem : v)
	{
		std::cout << elem << ' ';
	}

	std::cout << '\n';
}
```

### 4.3 ラムダ式の型名
- ラムダ式のオブジェクト型は複雑なので、`auto` を使う

```cpp
#include <iostream>

int main()
{
	auto f = [](int x) { return (x * x); };

	std::cout << f(3) << '\n'; // 9
}
```

## 5. `auto` と実行時性能
- `auto` はコンパイル時に型推論を行うため、実行時性能には影響しない


## 6. その他の文法
- 次のような構文でも `auto` が使われる

### 6.1 構造化束縛
- タプルなどの要素を分解して受け取る「構造束縛」でも `auto` が使われる

```cpp
#include <iostream>
#include <utility>

std::pair<int, int> GetPoint()
{
	return{ 10, 20 };
}

int main()
{
	auto [x, y] = GetPoint();

	std::cout << x << ' ' << y << '\n'; // 10 20
}
```

### 6.2 関数の戻り値の型推論
- 関数の戻り値の型を `auto` と書くだけで、関数内部の `return` 文から型を推論させることができる
	- `return` 文が複数ある場合、すべての型が一致している必要がある

```cpp
#include <iostream>

auto Square(int n)
{
	return (n * n);
}

int main()
{
	std::cout << Square(3) << '\n'; // 9
}
```

### 6.3 ラムダ式の引数の型推論
- 「ジェネリックラムダ」と呼ばれる機能で、ラムダ式の引数の型を実引数の型から推論できる
- 複数の引数でも推論できる

```cpp
#include <iostream>

int main()
{
	auto square = [](auto x) { return (x * x); };
	std::cout << square(3) << '\n'; // 9 (int)
	std::cout << square(0.5) << '\n'; // 0.25 (double)

	auto add = [](auto x, auto y) { return (x + y); };
	std::cout << add(3, 4) << '\n'; // 7 (int)
	std::cout << add(0.5, 1.5) << '\n'; // 2.0 (double)
	std::cout << add(3, 0.5) << '\n'; // 3.5 (double)
}
```

### 6.4 関数テンプレートの省略記法

```cpp
#include <iostream>
#include <string>

//template <class Type>
//void Show(const Type& x)
//{
//	std::cout << x << '\n';
//}

void Show(const auto& x)
{
	std::cout << x << '\n';
}

int main()
{
	int a = 123;
	double b = 3.14;
	std::string c = "Hello, World!";

	Show(a);
	Show(b);
	Show(c);
}
```

- C++20 の機能
