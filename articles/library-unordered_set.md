---
title: "<unordered_set>"
emoji: "🤏"
type: "tech"
topics: ["cpp"]
published: false
---

---
title: "<unordered_set>"
free: true
---

# 1. `std::unordered_set` の構築

## 1.1 リストから構築する
- `= { ... }` の中に値を用意して初期化します
- リスト内の値に重複がある場合、そのうちの 1 つだけが追加されます
- データ構造の性質上、要素の順序は保存されず、実装依存の並び順になります
- リスト内の値は `std::unordered_set<Type>` の要素の型 `Type` に合わせます
```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	std::unordered_set<int> coins = { 1, 5, 10, 50, 100, 500 };
	std::cout << coins.size() << '\n'; // 要素数を出力
	// 保持している要素を出力
	for (const auto& coin : coins)
	{
		std::cout << coin << '\n';
	}

	std::cout << "---\n"; // 実行結果を見やすくするための区切り線

	// 重複する値を含んでいる ("red")
	std::unordered_set<std::string> colors = { "red", "red", "green", "blue", "yellow", "red" };
	std::cout << colors.size() << '\n'; // 要素数を出力
	// 保持している要素を出力
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力（例）
6
100
500
10
5
50
1
---
4
yellow
blue
green
red
```

## 1.2 別の `std::vector` から構築する
- `std::unordered_set<Type> s(itFirst, itLast)` は、範囲 `[itFirst, itLast)` の要素でハッシュテーブルを初期化します
- ある `std::vector` の `.begin()`, `.end()` を渡すことで、その配列の要素から構築できます
- 要素に重複がある場合、そのうちの 1 つだけが追加されます
- データ構造の性質上、要素の順序は保存されず、実装依存の並び順になります
- イテレータの指す値は `std::unordered_set<Type>` の要素の型 `Type` に合わせます
```cpp
#include <iostream>
#include <string>
#include <vector>
#include <unordered_set>

int main()
{
	// 重複する要素を含んでいる
	std::vector<int> vi = { 1, 5, 10, 50, 100, 500, 1, 5, 5, 5, 5 };
	std::unordered_set<int> coins(vi.begin(), vi.end());
	std::cout << coins.size() << '\n';
	for (const auto& coin : coins)
	{
		std::cout << coin << '\n';
	}

	std::cout << "---\n";

	// 重複する要素を含んでいる
	std::vector<std::string> vs = { "red", "red", "red", "green", "blue", "red", "yellow" };
	std::unordered_set<std::string> colors(vs.begin(), vs.end());
	std::cout << colors.size() << '\n';
	for (const auto& color : colors)
	{
		std::cout << color << '\n';
	}
}
```
```txt:出力（例）
6
50
10
500
5
100
1
---
4
yellow
blue
green
red
```

## 1.3 別の `std::unordered_set` から構築する
- 別の `std::unordered_set<Type>` 型の中身をコピーしてハッシュテーブルを初期化します
- コピー元とコピー先の要素の型 `Type` は一致している必要があります
- データ構造の性質上、要素の順序は保存されず、実装依存の並び順になります
- コピー元とコピー先で要素の順序が一致する保証はありません
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

    std::cout << vs.size() << '\n'; // 要素数を出力（コピーしたほうは変化しない）
	for (const auto& s : vs) // 保持している要素を出力（コピーしたほうは変化しない）
	{
		std::cout << s << '\n';
	}
}
```
```txt:出力（例）
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

## 1.4 空のハッシュテーブル
- `std::unordered_set` 型のデフォルトコンストラクタは、要素数が 0 の空のハッシュテーブルを構築します  
- 空のハッシュテーブルも、何の問題もなくプログラムで使うことができます
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


# 2. `std::unordered_set` への入力
`std::unordered_set<Type>` 型の変数を直接 `std::cin` で使うことはできないため、以下のいずれかの方法を使います。

- 方式 A: `Type` 型の変数に `std::cin` を使って入力を読み込み、それらのリストでハッシュテーブルを構築する
- 方式 B: `std::vector<Type>` に全要素の入力を保存し、それをもとにハッシュテーブルを構築する
- 方式 C: `Type` 型の変数に `std::cin` を使って入力を読み込み、`std::unordered_set<Type>` の `.emplace(value)` を使ってハッシュテーブルを構築する

## 2.1 入力された値を `std::unordered_set` に追加する (方式 A)
- `Type` 型の変数に `std::cin` を使って要素 1 個分の入力を読み込み、それをハッシュテーブルに追加します
- 入力された値の順序を保存する必要が無く、重複する入力を捨ててもよい場合にこの方法が使えます
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
```txt:出力 1（例）
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
```txt:出力 2（例）
2
blue
red
```

## 2.2 入力された値を `std::unordered_set` に追加する (方式 B)
-  `std::vector<Type>` に全要素の入力を保存し、それをもとに `std::unordered_set<Type>` を構築します
-  `std::vector` に元の入力を保存できるため、入力された値の順序を保持しておきたい場合や、重複する入力を捨てたくない場合にこの方法を使います
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
```txt:入力 1（例）
6
red
red
green
blue
yellow
red
```
```txt:出力 1（例）
4
yellow
blue
green
red
```
```txt:入力 2（例）
4
red
red
blue
blue
```
```txt:出力 2（例）
2
blue
red
```

## 2.3 入力された値を `std::unordered_set` に追加する (方式 C)
- 空のハッシュテーブルに `.emplace(value)` を使って、値を 1 個ずつ追加します
- ハッシュテーブルにすでに存在する値を `.emplace(value)` で追加しようとすると、値の追加はされず、戻り値 (`std::pair<iterator, bool>` 型) の要素 `.second` が `false` になります（重複が無く、追加されたときは `true`）
- 重複する値の入力を検知したい場合に、この方法を使います
```cpp
#include <iostream>
#include <string>
#include <unordered_set>

int main()
{
	int N;
	std::cin >> N; // 単語の個数を読み込む

	std::unordered_set<std::string> words;

	for (int i = 0; i < N; ++i)
	{
		std::string s;
		std::cin >> s;

		if (words.emplace(s).second)
		{
			std::cout << s << " (first time)\n";
		}
		else
		{
			std::cout << s << '\n';
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
```txt:入力 1（例）
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
```txt:出力 1（例）
red (first time)
red
green (first time)
blue (first time)
yellow (first time)
red
green
yellow
---
4
yellow
red
blue
green
```
```txt:入力 2（例）
4
red
blue
red
red
```
```txt:出力 2（例）
red (first time)
blue (first time)
red
red
---
2
blue
red
```
