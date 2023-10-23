---
title: "<unordered_set> [執筆中 | 🟢C++20 対応]"
free: true
---

`std::unordered_set` は、同じ要素を 1 つまでしか持たない連想コンテナクラスで、ハッシュテーブルによって実装されます。要素の検索や追加、削除にかかる平均計算量は $O(1)$ です。

# 1. 他のコンテナとの違い

## 1.1 vs `std::set`
- `std::set` が要素をソートして保持するのに対し、`std::unordered_set` は、ハッシュ値に基づきコンピュータに都合の良い任意の順序で要素を保持します。
- `std::set` の要素の検索や追加、削除にかかる計算量は $O(\log N)$ ですが、`std::unordered_set` は平均 $O(1)$, 最悪 $O(N)$ です。

## 1.2 vs `std::unordered_map`
- `std::unordered_map` はキーと値のペアを要素として、キーを検索して値を取得するのに対し、`std::unordered_set` ではキーのみを要素とします。


# 2. `std::unordered_set` の構築

## 2.1 リストから構築する
- `= { ... }` の中に値を用意してハッシュテーブルを初期化します。
- リスト内の値に重複がある場合、そのうちの 1 つだけが追加されます。
- データ構造の性質上、リストに記述した要素の順序は保存されず、`std:unordered_set` 内では実装依存の並び順になります。
- リストの要素の型は `std::unordered_set<Type>` の `Type` に合わせます。

```cpp
#include <iostream>
#include <unordered_set>

int main()
{
	std::unordered_set<int> coins = { 1, 5, 10, 50, 100, 500 };
	
	std::cout << coins.size() << '\n'; // 要素数を出力する

	// 保持している要素を出力する
	for (const auto& coin : coins)
	{
		std::cout << coin << '\n';
	}
}
```
```txt:出力例
6
100
500
10
5
50
1
```


```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	// リスト内に重複する値 "red" が存在することに注意
	std::unordered_set<std::string> colors = { "red", "red", "green", "blue", "yellow", "red" };

	std::cout << colors.size() << '\n'; // 要素数を出力する

	// 保持している要素を出力する
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力例
4
yellow
blue
green
red
```


## 2.2 別の `std::vector` から構築する
- `std::unordered_set<Type> table(itFirst, itLast);` は、範囲 `[itFirst, itLast)` の要素でハッシュテーブルを初期化します。
- ある `std::vector` の `.begin()`, `.end()` を渡すことで、その配列のすべての要素をもとにハッシュテーブルを構築できます。
- 要素に重複がある場合、そのうちの 1 つだけが追加されます。
- データ構造の性質上、元の範囲での要素の順序は保存されず、`std:unordered_set` 内では実装依存の並び順になります。
- イテレータの指す値の型は `std::unordered_set<Type>` の要素の型 `Type` に変換できる必要があります。

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>

int main()
{
	std::vector<int> vi = { 1, 5, 10, 50, 100, 500, 1, 5, 5, 5, 5 };
	
	std::unordered_set<int> coins(vi.begin(), vi.end());
	
	std::cout << coins.size() << '\n'; // 要素数を出力する
	
	// 保持している要素を出力する
	for (const auto& coin : coins)
	{
		std::cout << coin << '\n';
	}
}
```
```txt:出力例
6
50
10
500
5
100
1
```

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <unordered_set>

int main()
{
	std::vector<std::string> vs = { "red", "red", "red", "green", "blue", "red", "yellow" };
	
	std::unordered_set<std::string> colors(vs.begin(), vs.end());
	
	std::cout << colors.size() << '\n'; // 要素数を出力する
	
	// 保持している要素を出力する
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力例
4
yellow
blue
green
red
```


## 2.3 別の `std::unordered_set` から構築する
- 別の `std::unordered_set<Type>` 型の中身をコピーしてハッシュテーブルを初期化できます。
- コピー元とコピー先の要素の型 `Type` は一致している必要があります。
- コピー元とコピー先で要素の順序が一致する保証はありません。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<std::string> vs = { "red", "green", "blue", "yellow" };
	
	std::unordered_set<std::string> colors = vs;
	
	std::cout << colors.size() << '\n';
	
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	std::cout << vs.size() << '\n'; // 要素数を出力する（コピー元は変化していないことを確認する）
	
	// 保持している要素を出力する（コピー元は変化していないことを確認する）
	for (const auto& s : vs)
	{
		std::cout << s << '\n';
	}
}
```
```txt:出力例
4
red
green
blue
yellow
---
4
yellow
blue
green
red
```


## 2.4 空のハッシュテーブル
- `std::unordered_set` のデフォルトコンストラクタは、要素数が 0 の空のハッシュテーブルを構築します。
- 空のハッシュテーブルであっても、何の問題もなくプログラムで扱うことができます。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<int> coins; // 空のハッシュテーブルを構築

	std::cout << coins.size() << '\n'; // 要素数は 0

	for (const auto& coin : coins)
	{
		// 1 回も実行されない
		std::cout << coin << '\n';
	}

	std::cout << "---\n";

	std::unordered_set<std::string> colors; // 空のハッシュテーブルを構築

	std::cout << colors.size() << '\n'; // 要素数は 0

	for (const auto& color : colors)
	{
		// 1 回も実行されない
		std::cout << color << '\n';
	}
}
```
```txt:出力
0
---
0
```


## 2.5 自分で定義した型を `std::unordered_set` の要素にする
- `std::unordered_set<Type>` の `Type` は、以下の 2 つを満たす必要があります。
  - `Type` 型の値同士の `==` が定義されている。
  - `std::hash<Type>` の特殊化が定義されている。
- `int` や `std::string` はこれを満たしますが、それ以外の型（自分で定義した型など）については、この 2 つを用意する必要があります。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

namespace detail // 便利ライブラリを置く名前空間（名前は任意）
{
	// 複数のハッシュ値を組み合わせて新しいハッシュ値を作る関数
	// 実装出典: Boost.ContainerHash
	inline std::size_t HashMix(std::size_t x) noexcept
	{
		constexpr std::uint64_t m = 0xe9846af9b1a615d;
		x ^= x >> 32;
		x *= m;
		x ^= x >> 32;
		x *= m;
		x ^= x >> 28;
		return x;
	}

	// 複数のハッシュ値を組み合わせて新しいハッシュ値を作る関数
	template <class Type>
	inline void HashCombine(std::size_t& h, const Type& value) noexcept
	{
		h = HashMix(h + 0x9e3779b9 + std::hash<Type>{}(value));
	}
}

struct Point
{
	int x, y;

	// Point どうしの ==
	bool operator ==(const Point& other) const = default;
};

// std::hash の特殊化
template <>
struct std::hash<Point>
{
	std::size_t operator()(const Point& p) const noexcept
	{
		std::size_t seed = 0;
		detail::HashCombine(seed, p.x);
		detail::HashCombine(seed, p.y);
		return seed;
	}
};

int main()
{
	std::unordered_set<Point> points = { { 0, 0 }, { 11, 22 }, { 33, 44 }, { 55, 66 }, { 0, 0 } };
	
	std::cout << points.size() << '\n';
	
	for (const auto& point : points)
	{
		std::cout << '(' << point.x << ", " << point.y << ")\n";
	}
}
```
```txt:出力例
4
(55, 66)
(33, 44)
(11, 22)
(0, 0)
```


# 3. `std::unordered_set` への入力
`std::unordered_set<Type>` 型の変数を直接 `std::cin` で使うことはできないため、いくつかの方法から選んでください。

## 3.1 入力された値を `std::unordered_set` に追加する
- `Type` 型の変数に `std::cin` を使って要素 1 個分の入力を読み込み、それをハッシュテーブルに追加します。
- 入力された値の順序を保存する必要が無く、重複する入力値を捨ててもよい場合にこの方法が使えます。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	// 3 個入力されることがわかっている場合
	std::string a, b, c;
	std::cin >> a >> b >> c;

	std::unordered_set<std::string> colors = { a, b, c };
	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}
}
```
```txt:入力 1
red
green
blue
```
```txt:出力例 1
3
blue
green
red
```
```txt:入力 2
red
red
blue
```
```txt:出力例 2
2
blue
red
```


## 2.2 入力された値を `std::unordered_set` に追加する
-  `std::vector<Type>` に全要素の入力を保存し、それをもとに `std::unordered_set<Type>` を構築します。
-  `std::vector` に元の入力を保存できるため、入力された値の順序を保存したり、重複する入力をあとで使ったりしたい場合にこの方法を使います。

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <unordered_set>

int main()
{
	int N;
	std::cin >> N; // 入力の個数 N を読み込む

	std::vector<std::string> ss(N);
	for (auto& s : ss)
	{
		std::cin >> s;
	}

	std::unordered_set<std::string> words(ss.begin(), ss.end());
	std::cout << words.size() << '\n';
	for (const auto& word : words)
	{
		std::cout << word << '\n';
	}
}
```
```txt:入力 1
6
red
red
green
blue
yellow
red
```
```txt:出力例 1
4
yellow
blue
green
red
```
```txt:入力 2
4
red
red
blue
blue
```
```txt:出力例 2
2
blue
red
```

## 3.3 入力された値を `std::unordered_set` に追加する
- 空のハッシュテーブルに `.insert(value)` を使って、値を 1 個ずつ追加します。
- ハッシュテーブルにすでに存在する値を `.insert(value)` で追加しようとすると、値の追加はされず、戻り値 (`std::pair<iterator, bool>` 型) の要素 `.second` が `false` になります。重複が無く、値が追加されたときは `true` を返します。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	int N;
	std::cin >> N; // 単語の個数を読み込む

	std::unordered_set<std::string> words; // 空のハッシュテーブルを構築

	for (int i = 0; i < N; ++i)
	{
		std::string s;
		std::cin >> s;

		if (words.insert(s).second) // ハッシュテーブルに同じ値が無かった場合 true
		{
			std::cout << s << '\n';
		}
		else // 重複である場合
		{
			std::cout << s << " (duplicated)\n";
		}
	}

	std::cout << "---\n";

	std::cout << words.size() << '\n';
	for (const auto& word : words)
	{
		std::cout << word << '\n';
	}
}
```
```txt:入力
8
red
red
green
blue
yellow
red
green
yellow
```
```txt:出力例
red
red (duplicated)
green
blue
yellow
red (duplicated)
green (duplicated)
yellow (duplicated)
---
4
yellow
blue
green
red
```


# 4. `std::unordered_set` の要素数

## 4.1 要素数を調べる
- `.size()` は、ハッシュテーブルが持つ要素数を符号無し整数型の値で返します。
- 計算量は $O(1)$ です。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<int> coins = { 1, 5, 10, 50, 100, 500 };
	std::cout << coins.size() << '\n'; // 要素数を出力

	std::unordered_set<std::string> colors = { "red", "green", "blue", "yellow" };
	std::cout << colors.size() << '\n'; // 要素数を出力

	std::size_t n = colors.size();
	std::cout << n << '\n';
}
```
```txt:出力
6
4
4
```


## 4.2 空のハッシュテーブルであるかを調べる
- `.empty()` は、ハッシュテーブルの要素数が 0（空）であるかを `bool` 型の値で返します。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<int> coins;
	std::unordered_set<std::string> colors = { "red", "green", "blue", "yellow" };

	if (coins.empty()) // 空のハッシュテーブルなので true
	{
		std::cout << "coins is empty.\n";
	}

	if (colors.empty()) // 空のハッシュテーブルではないので false
	{
		std::cout << "colors is empty.\n";
	}
}
```
```txt:出力
coins is empty.
```


# 5. `std::unordered_set` への代入

## 5.1 別のリストを代入する
- `=` を使って別のリスト `{ ... }` の値を代入します。
- リストの要素の型は、`std::unordered_set<Type>` の `Type` に変換できる型である必要があります。
- データ構造の性質上、リストに記述した要素の順序は保存されず、`std:unordered_set` 内では実装依存の並び順になります。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<std::string> colors = { "red", "green", "blue", "yellow" };
	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	colors = { "orange", "pink", "purple" }; // 代入する

	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力例
4
yellow
blue
green
red
---
3
purple
pink
orange
```


## 5.2 別の `std::unordered_set` を代入する
- `=` を使って別の `std::string<Type>` 型の値を代入します。
- 代入元と代入先の要素の型 `Type` は一致している必要があります。
- 代入元と代入先で要素の順序が一致する保証はありません。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<std::string> colors = { "red", "green", "blue", "yellow" };
	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	std::unordered_set<std::string> t = { "orange", "pink", "purple" };
	colors = t; // 代入
	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力例
4
yellow
blue
green
red
---
3
purple
pink
orange
```


# 6. `std::unordered_set` の比較

## 6.1 保持する要素が、2 つのハッシュテーブル間で一致するかを調べる
- ともに同じ型 `Type` の要素を持つ `std::unordered_set<Type>` 型の値 `a`, `b` について、`a == b` はハッシュテーブルに含まれる要素（順序は考慮しない）が一致するかを `bool` 型の値で返します。

> - `==` の計算量: 最初に双方の要素数が同じかを調べるため、双方のサイズが異なる場合は $O(1)$, それ以外の場合は $O(N)$

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<std::string> colors1 = { "red", "green", "blue", "yellow" };
	std::unordered_set<std::string> colors2 = { "orange", "pink", "purple" };
	std::unordered_set<std::string> colors3 = { "blue", "yellow", "red", "green" };

	for (const auto& color : colors1)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	for (const auto& color : colors2)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	for (const auto& color : colors3)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (colors1 == colors1) << '\n';
	std::cout << (colors1 == colors2) << '\n';
	std::cout << (colors1 == colors3) << '\n'; // 要素の順序が違っていても、含まれる要素が一致すれば true
}
```
```txt:出力例
yellow
blue
green
red
---
purple
pink
orange
---
red
yellow
green
blue
---
true
false
true
```


## 6.2 保持する要素が、2 つのハッシュテーブル間で違うかを調べる
- ともに同じ型 `Type` の要素を持つ `std::unordered_set<Type>` 型の値 `a`, `b` について、`a != b` はハッシュテーブルに含まれる要素（順序は考慮しない）が違うかを `bool` 型の値で返します。

> - `!=` の計算量: 最初に双方の要素数が違うかを調べるため、双方のサイズが異なる場合は $O(1)$, それ以外の場合は $O(N)$

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<std::string> colors1 = { "red", "green", "blue", "yellow" };
	std::unordered_set<std::string> colors2 = { "orange", "pink", "purple" };
	std::unordered_set<std::string> colors3 = { "blue", "yellow", "red", "green" };

	for (const auto& color : colors1)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	for (const auto& color : colors2)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	for (const auto& color : colors3)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (colors1 != colors1) << '\n';
	std::cout << (colors1 != colors2) << '\n';
	std::cout << (colors1 != colors3) << '\n';
}
```
```txt:出力例
yellow
blue
green
red
---
purple
pink
orange
---
red
yellow
green
blue
---
false
true
false
```


# 7. 要素の検索

## 7.1 ハッシュテーブルに指定したキーの要素があるか調べる [🟢C++20]
- `.contains(key)` は、ハッシュテーブルにキーが `key` の要素があるかを `bool` 型の値で返します。

> - `.contains(key)` の計算量: 平均 $O(1)$, 最悪 $O(N)$

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	const std::unordered_set<std::string> colors = { "red", "green", "blue", "yellow" };

	std::string s;
	std::cin >> s;

	if (colors.contains(s))
	{
		std::cout << "colors contains " << s << '\n';
	}
	else
	{
		std::cout << "colors does not contain " << s << '\n';
	}
}
```
```txt:入力 1
red
```
```txt:出力 1
colors contains red
```
```txt:入力 2
pink
```
```txt:出力 2
colors does not contain pink
```


## 7.2 ハッシュテーブルに指定したキーの要素があるか調べる（C++17 以前）
- `.count(key)` は、ハッシュテーブルに存在する、キーが `key` の要素の個数を返します
- ただし、`std::unordered_set` のハッシュテーブルには、同じキーを持つ要素は 1 つまでしか存在しないため、必ず `0` か `1` のどちらかを返します
- したがって、ハッシュテーブルに、指定したキー `key` の要素があるかを調べるには、`.count(key)` の戻り値が `1` であるかを調べます

> - `.count(key)` の計算量: 平均 $O(1)$, 最悪 $O(N)$


```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	const std::unordered_set<std::string> colors = { "red", "green", "blue", "yellow" };

	std::string s;
	std::cin >> s;

	if (colors.count(s)) // ハッシュテーブルに s が存在するなら 1, 存在しないなら 0
	{
		std::cout << "colors contains " << s << '\n';
	}
	else
	{
		std::cout << "colors does not contain " << s << '\n';
	}
}
```
```txt:入力 1
red
```
```txt:出力 1
colors contains red
```
```txt:入力 2
pink
```
```txt:出力 2
colors does not contain pink
```


# 8. 要素へのアクセス

## 8.1 範囲ベース `for` 文を使い、各要素にアクセスする
- `std::unordered_set` での要素（キー）へのアクセスは読み取り専用 (const) です
- `std::unordered_set` に対する範囲ベース for ループは、**配列の要素数がループ中に変更されない**という前提で動作するため、絶対にループ内でそのハッシュテーブルの要素数を増減させる操作をしてはいけません。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<std::string> colors = { "red", "green", "blue", "yellow" };

	for (const auto& color : colors) // 要素の個数だけ繰り返すループ、color は各要素への const 参照
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力例
yellow
blue
green
red
```


# 9. 要素の追加

## 9.1 要素を 1 個追加する
- `.insert(value)` で、ハッシュテーブルに値 `value` を追加します。
- ハッシュテーブルにすでに存在する値を `.insert(value)` で追加しようとすると、値の追加はされず、戻り値 (`std::pair<iterator, bool>` 型) の要素 `.second` が `false` になります。重複が無く、値が追加されたときは `true` を返します。
- `.insert(value)` の戻り値は、使わないのであれば無視しても問題ありません。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<std::string> colors = { "red", "green", "blue" };
	colors.insert("yellow"); // "yellow" が新しく追加される
	colors.insert("red"); // "red" はすでに存在するので追加しない

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ

	bool a = colors.insert("pink").second; // "pink" が新しく追加される。true
	std::cout << a << '\n';

	bool b = colors.insert("red").second; // "red" はすでに存在するので追加しない。false
	std::cout << b << '\n';

	std::cout << "---\n";

	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力例
true
false
---
5
pink
yellow
blue
green
red
```


# 10. 要素の削除

## 10.1 指定した要素を削除する
- `.erase(key)` は、ハッシュテーブルからキーが `key` の要素を削除します。
- 指定したキーが存在しなかった場合は何もしません。
- ハッシュテーブルにある、削除されなかった要素の順序は維持されます。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<std::string> colors = { "red", "green", "blue", "yellow", "pink", "black" };
	
	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	colors.erase("green"); // "green" を削除
	
	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	colors.erase("orange"); // "orange" を削除（存在しないので何も起こらない）

	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力例
6
pink
yellow
blue
green
black
red
---
5
pink
yellow
blue
black
red
---
5
pink
yellow
blue
black
red
```


## 10.2 条件を満たす要素をすべて削除する
- `.erase(it)` はイテレータ `it` が指す要素を削除し、その次の位置のイテレータを返します。
- イテレータを使ったループと組み合わせることで、ハッシュテーブルから条件を満たす要素をすべて削除できます。
- ハッシュテーブルにある、削除されなかった要素の順序は維持されます。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<std::string> colors = { "red", "green", "blue", "yellow", "pink", "black" };
	
	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	// ハッシュテーブルから、5 文字以上の要素を削除する
	for (auto it = colors.begin(); it != colors.end();)
	{
		if (it->size() >= 5) // 5 文字以上の要素なら
		{
			it = colors.erase(it); // その要素を削除し、イテレータを次の位置へ進める
		}
		else
		{
			++it; // イテレータを次の位置へ進める
		}
	}

	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力例
6
pink
yellow
blue
green
black
red
---
3
pink
blue
red
```


## 10.3 全要素を消去する
- `.clear()` を使うと、全要素を削除して空のハッシュテーブルになります。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<std::string> colors = { "red", "green", "blue", "yellow", "pink", "black" };
	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	colors.clear(); // 要素を全消去

	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力例
6
pink
yellow
blue
green
black
red
---
0
```


# 11. `std::unordered_set` の入れ替え

## 11.1 二つの `std::unordered_set` を交換する
- `std::unordered_set<Type>` 型の変数 `a`, `b` の中身を入れ替えるには `std::swap(a, b)` を使います。
- 双方とも要素の型 `Type` が一致している必要があります。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<std::string> colorsA = { "red", "green", "blue", "yellow" };
	std::unordered_set<std::string> colorsB = { "orange", "pink", "purple" };

	std::swap(colorsA, colorsB); // 中身を交換する

	std::cout << colorsA.size() << '\n';
	for (const auto& color : colorsA)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	std::cout << colorsB.size() << '\n';
	for (const auto& color : colorsB)
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力例
3
purple
pink
orange
---
4
yellow
blue
green
red
```

- C++20 以降では、同じ操作を行う `std::ranges::swap(a, b)` を使うことが推奨されます。

```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<std::string> colorsA = { "red", "green", "blue", "yellow" };
	std::unordered_set<std::string> colorsB = { "orange", "pink", "purple" };

	std::ranges::swap(colorsA, colorsB); // 中身を交換する

	std::cout << colorsA.size() << '\n';
	for (const auto& color : colorsA)
	{
		std::cout << color << '\n';
	}

	std::cout << "---\n";

	std::cout << colorsB.size() << '\n';
	for (const auto& color : colorsB)
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力例
3
purple
pink
orange
---
4
yellow
blue
green
red
```


# 12. `std::unordered_set` のイテレータ

アルゴリズム関数で使うためのイテレータを、以下の関数で取得できます。イテレータを取得した時点からハッシュテーブルの内容が変更されると、そのイテレータは無効になることがあるため、イテレータを変数に保存して長い期間保持するのは避けるべきです。

## 12.1 先頭位置のイテレータを取得する
- `.begin()` はハッシュテーブルの先頭位置を指すイテレータを返します 
- 空のハッシュテーブルの場合 `.begin() == .end()` です

## 12.2 終端位置のイテレータを取得する
- `.end()` はハッシュテーブルの終端位置を指すイテレータを返します
- 空のハッシュテーブルの場合 `.begin() == .end()` です


# 13. デフォルトのハッシュ関数についての注意

## 13.1 デフォルトのハッシュ関数への Hack
他人の提出に対する Hack / Challenge ができるプログラミングコンテストでは、ハッシュ関数の性質を逆手に取った特殊な入力を与えられ、`std::unordered_set` や `std::unordered_map` を使うプログラムの計算量を最悪計算量に近い状態にさせられてしまうことがあります。以下の記事では、その原理の説明と、時刻から生成した乱数をソルトとして使い、Hack に対抗できるハッシュ関数を作る方法が解説されています。
- [Blowing up unordered_map, and how to stop getting hacked on it](https://codeforces.com/blog/entry/62393), neal's blog

