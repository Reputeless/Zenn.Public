---
title: "ソート"
free: true
---

## 1. std::sort の使い方

### 1.1 昇順にソートする
- `std::sort(it1, it2)` は、イテレータ `it1` から `it2` の範囲の要素を昇順（小さい順）に並べ替える
- `v.begin(), v.end()` を渡せば全体がソートされる
- `std::sort` は対象の配列の要素を直接並べ替え、戻り値はない
- `std::vector` や `std::array`、`std::string` などに使える。一方、`std::list` のようにランダムアクセスできないコンテナには使えない
- 計算量は、要素の比較が $O(1)$ なら、全体として通常 $O(N \log N)$。`std::sort` 自体の比較回数は $O(N \log N)$

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<int> v = { 3, 1, 4, 1, 5, 9 };
	std::sort(v.begin(), v.end()); // 昇順にソートする

	for (const auto& x : v)
	{
		std::cout << x << ' ';
	}
	std::cout << '\n'; // 出力: 1 1 3 4 5 9
}
```

---

- `std::string` に `std::sort` を使うと、1 文字ずつが並べ替えの対象になる
- 文字コード（一般に ASCII）順のため、`'0'`～`'9'` < `'A'`～`'Z'` < `'a'`～`'z'` の順になる

```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	std::string s = "hello";
	std::sort(s.begin(), s.end()); // 昇順にソートする
	std::cout << s << '\n'; // 出力: ehllo
}
```

### 1.2 降順にソートする
- `std::sort` の第 3 引数に `std::greater{}`（`<functional>` が必要）を渡すと降順（大きい順）になる
- 「昇順にソートしてから `std::reverse` で反転」でも同じ結果になるが、`std::greater{}` なら 1 行で済む
- `std::sort` に `v.rbegin(), v.rend()` を渡す方法もあるが、1 文字違いで誤読しやすいため、`std::greater{}` を推奨する

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>

int main()
{
	std::vector<int> v = { 3, 1, 4, 1, 5, 9 };
	std::sort(v.begin(), v.end(), std::greater{}); // 降順にソートする

	for (const auto& x : v)
	{
		std::cout << x << ' ';
	}
	std::cout << '\n'; // 出力: 9 5 4 3 1 1
}
```

### 1.3 pair・tuple の昇順ソート
- `std::pair` や `std::tuple` は、デフォルトでは辞書順で比較・ソートされる
- 左の要素から順に比較し、最初に異なる要素の大小で全体の大小が決まる
- 例えば `std::pair<int, std::string>` の場合、まず整数を比較し、もし同じ整数なら文字列を比較する

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
#include <utility>

int main()
{
	std::vector<std::pair<int, std::string>> v =
	{
		{ 10, "ten" },
		{ 2, "second" },
		{ 1, "one" },
		{ 2, "two" },
		{ 1, "first" },
		{ 5, "five" },
	};

	std::sort(v.begin(), v.end()); // 昇順にソートする

	for (auto&& [number, word] : v)
	{
		std::cout << number << ' ' << word << '\n';
	}
}
```
```txt:出力
1 first
1 one
2 second
2 two
5 five
10 ten
```

### 1.4 pair・tuple の降順ソート
- `std::sort(it1, it2, std::greater{})` を使うと、`std::pair` や `std::tuple` も降順にソートできる
- `std::greater{}` は辞書順の比較をそのまま逆向きにするため、2 番目以降の要素も降順になる。「1 番目だけ降順、2 番目は昇順」のような混在は `std::greater{}` では書けず、**2.5** のようなラムダ式が必要

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
#include <utility>
#include <functional>

int main()
{
	std::vector<std::pair<int, std::string>> v =
	{
		{ 10, "ten" },
		{ 2, "second" },
		{ 1, "one" },
		{ 2, "two" },
		{ 1, "first" },
		{ 5, "five" },
	};

	std::sort(v.begin(), v.end(), std::greater{}); // 降順にソートする

	for (auto&& [number, word] : v)
	{
		std::cout << number << ' ' << word << '\n';
	}
}
```
```txt:出力
10 ten
5 five
2 two
2 second
1 one
1 first
```


## 2. ラムダ式によるカスタムソート
- **1.2** で使った `std::greater{}` のように、`std::sort` の第 3 引数には「どちらを前に置くか」を決めるルールを渡せる
- ここにラムダ式を書くと、あらかじめ用意された基準に頼らず、目的に応じたルールでソートできる
- 渡すラムダ式は「2 つの要素を受け取って `bool` を返す」形で書く

```cpp
std::sort(v.begin(), v.end(), [](const auto& a, const auto& b)
	{
		return /* a を b より前に置くべきなら true */;
	});
```

- この `bool` は、`std::sort` からの「`a` を `b` より前に置くべきですか？」という質問に対する返事だと考えるとよい。`true` なら `a` を `b` より前にし、`false` なら前にしない
- `a` と `b` が同順位のときは `false` を返す。どちらかを前にする必要がないため
- 複数の基準で並べる場合は、`!=` と `<` または `>` を組み合わせ、「注目する要素が異なるときだけその要素で決める → 同じなら次の要素を見る」という形で書く。**`<=` や `>=` は使わない**
- **2.1～2.4** では、通常の昇順・降順ソートや、`std::pair`・`std::tuple` のソートを、あえてラムダ式を使って再現する例を示す（ラムダ式を使わなくても実現できる内容）
- **2.5** では、ここまでの例に共通する比較関数の書き方を整理する
- **2.6～2.8** では、`std::greater{}` などのあらかじめ用意された基準では表現できない独自のルールを、ラムダ式で書く例を示す

### 2.1 通常の昇順ソートの再現
- 「`a < b` なら Yes」というルールにすれば、カスタムソートで通常の昇順ソートを再現できる（`std::sort(v.begin(), v.end())` と同じ結果）

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<int> v = { 3, 1, 4, 1, 5, 9 };
	std::sort(v.begin(), v.end(), [](const auto& a, const auto& b)
		{
			return a < b; // 昇順にソートする
		});

	for (const auto& x : v)
	{
		std::cout << x << ' ';
	}
	std::cout << '\n'; // 出力: 1 1 3 4 5 9
}
```

### 2.2 通常の降順ソートの再現
- 「`a > b` なら Yes」というルールにすれば、カスタムソートで通常の降順ソートを再現できる

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<int> v = { 3, 1, 4, 1, 5, 9 };
	std::sort(v.begin(), v.end(), [](const auto& a, const auto& b)
		{
			return a > b; // 降順にソートする
		});

	for (const auto& x : v)
	{
		std::cout << x << ' ';
	}
	std::cout << '\n'; // 出力: 9 5 4 3 1 1
}
```

### 2.3 pair の昇順ソートの再現
- 「`a.first != b.first` のとき `a.first < b.first`、 そうでないとき `a.second < b.second`」というルールにすれば、カスタムソートで `std::pair` のデフォルトの昇順ソートを再現できる

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
#include <utility>

int main()
{
	std::vector<std::pair<int, std::string>> v =
	{
		{ 10, "ten" },
		{ 2, "second" },
		{ 1, "one" },
		{ 2, "two" },
		{ 1, "first" },
		{ 5, "five" },
	};

	std::sort(v.begin(), v.end(), [](const auto& a, const auto& b)
		{
			if (a.first != b.first)
			{
				return a.first < b.first; // 整数の昇順でソートする
			}
			
			return a.second < b.second; // 文字列の昇順でソートする
		});

	for (auto&& [number, word] : v)
	{
		std::cout << number << ' ' << word << '\n';
	}
	std::cout << '\n';
}
```
```txt:出力
1 first
1 one
2 second
2 two
5 five
10 ten
```

### 2.4 pair の降順ソートの再現
- 「`a.first != b.first` のとき `a.first > b.first`、 そうでないとき `a.second > b.second`」というルールにすれば、カスタムソートで `std::pair` の降順ソートを再現できる

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
#include <utility>

int main()
{
	std::vector<std::pair<int, std::string>> v =
	{
		{ 10, "ten" },
		{ 2, "second" },
		{ 1, "one" },
		{ 2, "two" },
		{ 1, "first" },
		{ 5, "five" },
	};

	std::sort(v.begin(), v.end(), [](const auto& a, const auto& b)
		{
			if (a.first != b.first)
			{
				return a.first > b.first; // 整数の降順でソートする
			}
			
			return a.second > b.second; // 文字列の降順でソートする
		});

	for (auto&& [number, word] : v)
	{
		std::cout << number << ' ' << word << '\n';
	}
	std::cout << '\n';
}
```
```txt:出力
10 ten
5 five
2 two
2 second
1 one
1 first
```

### 2.5 比較関数を書くときの基本形
- **2.1～2.4** の例では、どれも「`a` を `b` より前に置く条件」を順番に調べている
- 1 つの基準だけで並べる場合は、`a < b` や `a > b` のようにそのまま比較すればよい
- 複数の基準で並べる場合は、「今見ている要素が異なればそこで順番を決め、同じなら次の要素を見る」という形で書く

```cpp
std::sort(v.begin(), v.end(), [](const auto& a, const auto& b)
	{
		if (a.first != b.first)
		{
			return a.first < b.first; // 第 1 の基準
		}

		return a.second > b.second; // 第 1 の基準が同じなら、第 2 の基準
	});
```

- この例では、`first` の昇順を第 1 の基準、`second` の降順を第 2 の基準にしている
- 比較する基準をさらに増やす場合も、同じ形で `if` を追加していけばよい
- すべての基準が同じ場合は、最後の比較も `false` になり、どちらも相手より前にはしない

### 2.6 pair のソートのカスタマイズ
- ラムダ式を使うと、要素ごとに昇順・降順を変えるなど、`std::greater{}` では実現できないルールでソートできる
- 整数の昇順でソートし、整数が同じ場合は文字列の降順でソートするルールにする例

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
#include <utility>

int main()
{
	std::vector<std::pair<int, std::string>> v =
	{
		{ 10, "ten" },
		{ 2, "second" },
		{ 1, "one" },
		{ 2, "two" },
		{ 1, "first" },
		{ 5, "five" },
	};

	std::sort(v.begin(), v.end(), [](const auto& a, const auto& b)
		{
			if (a.first != b.first)
			{
				return a.first < b.first; // 整数は昇順でソートする
			}
			
			return a.second > b.second; // 文字列は降順でソートする
		});

	for (auto&& [number, word] : v)
	{
		std::cout << number << ' ' << word << '\n';
	}
	std::cout << '\n';
}
```
```txt:出力
1 one
1 first
2 two
2 second
5 five
10 ten
```

### 2.7 自作クラスのソート
- 自作クラスで比較演算子を定義していない場合、`std::sort(v.begin(), v.end())` のようなデフォルトのソートは使えない
- ラムダ式によるカスタムソートを使えば、自作クラスのオブジェクトもソートできる

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>

struct Person
{
	std::string name;
	int age;
};

int main()
{
	std::vector<Person> people =
	{
		{ "David", 30 },
		{ "Bob", 25 },
		{ "Charlie", 35 },
		{ "Alice", 30 },
	};

	std::sort(people.begin(), people.end(), [](const auto& a, const auto& b)
		{
			if (a.age != b.age)
			{
				return a.age < b.age; // 年齢の昇順でソートする
			}
			
			return a.name < b.name; // 年齢が同じ場合は名前の昇順でソートする
		});

	for (const auto& person : people)
	{
		std::cout << person.name << ' ' << person.age << '\n';
	}
}
```
```txt:出力
Bob 25
Alice 30
David 30
Charlie 35
```

### 2.8 特定の要素だけでソートする
- 例えば、`std::pair<int, std::string>` の整数の部分だけでソートしたい場合は、文字列の部分を無視して比較するルールにする
- 整数が同じ場合の文字列の順序は保証されない。次の例では `1 one` と `1 first` のどちらが先に来るかは規格上保証されない
- 元の並び順を保ちたい場合は `std::sort` の代わりに **2.9** で説明する `std::stable_sort` を使う

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
#include <utility>

int main()
{
	std::vector<std::pair<int, std::string>> v =
	{
		{ 10, "ten" },
		{ 2, "second" },
		{ 1, "one" },
		{ 2, "two" },
		{ 1, "first" },
		{ 5, "five" },
	};

	std::sort(v.begin(), v.end(), [](const auto& a, const auto& b)
		{
			return a.first < b.first; // 整数の昇順でソートする
		});

	for (auto&& [number, word] : v)
	{
		std::cout << number << ' ' << word << '\n';
	}
	std::cout << '\n';
}
```
```txt:出力例
1 one
1 first
2 second
2 two
5 five
10 ten
```

### 2.9 同順位の要素の並び順を保つ
- `std::sort` では、比較上同じ順位になる要素どうしの並び順は保証されない
- `std::stable_sort` を使うと、比較上同じ順位になる要素どうしは、ソート前の並び順が保たれる
- 使い方は `std::sort` とほぼ同じで、第 3 引数にラムダ式を渡してカスタムソートすることもできる
- 例えば、**2.8** のように `std::pair<int, std::string>` の整数部分だけでソートし、整数が同じ要素については元の順番を保ちたい場合に使える

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
#include <utility>

int main()
{
	std::vector<std::pair<int, std::string>> v =
	{
		{ 2, "second" },
		{ 1, "one" },
		{ 2, "two" },
		{ 1, "first" },
		{ 2, "double" },
	};

	std::stable_sort(v.begin(), v.end(), [](const auto& a, const auto& b)
		{
			return a.first < b.first; // 整数の昇順でソートする
		});

	for (auto&& [number, word] : v)
	{
		std::cout << number << ' ' << word << '\n';
	}
}
```
```txt:出力
1 one
1 first
2 second
2 two
2 double
```

- `first` が `1` の要素は、ソート前から `"one"` → `"first"` の順なので、ソート後もその順番が保たれる
- 同様に、`first` が `2` の要素も `"second"` → `"two"` → `"double"` の順番が保たれる
- 同順位の要素の順番を保つ必要がない場合は、通常の `std::sort` を使えばよい


## 3. ソートで解ける問題のパターン
- ソートは、単に値を順番に並べて出力するためだけでなく、問題を解きやすい形に整えるためにも使える

### 3.1 並び順を正規化して比較する
- 並び順の違いを無視して、中身（各要素の種類と個数）が同じかどうかを判定したい場合は、両方をソートしてから比較する
- ソートすると、同じ要素構成のデータは同じ並びになるため、そのまま `==` で比較できる
- 文字列では、「文字を並べ替えると同じ文字列になるか」を調べる場合などに使える

```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	std::string s, t;
	std::cin >> s >> t;

	std::sort(s.begin(), s.end()); // 昇順にソートする
	std::sort(t.begin(), t.end()); // 昇順にソートする

	if (s == t) // 並べ替えて一致するなら、同じ文字の集まり
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
```txt:入力例
listen
silent
```
```txt:出力
Yes
```

---

- `std::vector<int>` などでも同様に使える

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<int> a = { 3, 1, 2 };
	std::vector<int> b = { 2, 3, 1 };

	std::sort(a.begin(), a.end());
	std::sort(b.begin(), b.end());

	if (a == b) // 並べ替えて一致するなら、同じ整数の集まり
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
```txt:出力
Yes
```

### 3.2 同じ値を隣り合わせにする
- ソートすると、同じ値どうしは必ず連続して並ぶ
- そのため、「重複があるか」「ある値が何個連続しているか」といったことを、隣り合う要素を見るだけで調べられる

---

- 例えば、5 枚のカードがフルハウスかどうかを調べる場合にも使える
	- フルハウスは「同じ数字が 3 枚 + 別の同じ数字が 2 枚」の組み合わせ
	- 昇順にソートすると、同じ数字のカードが隣り合う
	- ソート後の並びは `x x x y y` または `x x y y y` のどちらかになる

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<int> cards = { 7, 3, 7, 3, 7 };

	std::sort(cards.begin(), cards.end());

	const bool xxxyy = ((cards[0] == cards[2]) && (cards[3] == cards[4]) && (cards[2] != cards[3]));
	const bool xxyyy = ((cards[0] == cards[1]) && (cards[2] == cards[4]) && (cards[1] != cards[2]));

	if (xxxyy || xxyyy)
	{
		std::cout << "Full House\n";
	}
	else
	{
		std::cout << "Not Full House\n";
	}
}
```
```txt:出力
Full House
```

### 3.3 小さい順・大きい順に処理する
- ソートすると、「最小のもの」「最大のもの」だけでなく、「2 番目」「上位 K 個」のような要素も簡単に取り出せる
- 昇順にソートした配列では、先頭ほど小さく、末尾ほど大きい
- 降順にソートしておけば、大きいものから順番に処理できる

---

- 例えば、2 人のプレイヤーが大きいカードから交互にとる場合は、先に降順にソートしておくと、先頭から順番にカードを取るだけでよい

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>

int main()
{
	std::vector<int> cards = { 3, 1, 4, 1, 5, 9 };

	std::sort(cards.begin(), cards.end(), std::greater{}); // 降順にソートする

	int playerA = 0;
	int playerB = 0;

	for (size_t i = 0; i < cards.size(); ++i)
	{
		if ((i % 2) == 0)
		{
			playerA += cards[i]; // A のターン
		}
		else
		{
			playerB += cards[i]; // B のターン
		}
	}

	std::cout << "Player A: " << playerA << '\n';
	std::cout << "Player B: " << playerB << '\n';
}
```
```txt:出力
Player A: 14
Player B: 9
```

### 3.4 元の番号を保持してソートする
- 値だけをソートすると、その値が元の配列の何番目にあったかわからなくなる
- ソート後にも元の位置を使いたい場合は、元の番号もセットで持ってソートする
- `std::pair` や自作 `struct` を使うと書きやすい

---

- 例えば、入力された値を小さい順に並べながら、元の番号を出力する場合を考える

```cpp
#include <iostream>
#include <vector>
#include <utility>
#include <algorithm>

int main()
{
	int n;
	std::cin >> n;

	// { 元の番号, 値 }
	std::vector<std::pair<int, int>> values(n);
	for (int i = 0; auto& value : values)
	{
		value.first = ++i; // 元の番号（1-indexed）をセットする
		std::cin >> value.second;
	}

	std::sort(values.begin(), values.end(), [](const auto& a, const auto& b)
		{
			return a.second < b.second; // 値の昇順でソートする
		});

	for (const auto& value : values)
	{
		std::cout << value.first << '\n';
	}
}
```
```txt:入力例
6
50 30 40 20 60 10
```
```txt:出力
6
4
2
3
1
5
```

- ソート後の結果 `10 20 30 40 50 60` に対して、その値がもともと何番目にあったかを順に出力している


### 3.5 複数の条件で並べ替える
- 順位表などでは、1 つの値だけでは順番が決まらないことがある
- 例えば「得点の高い順、同点なら解答時間の短い順、それも同じなら名前の辞書順」のように、複数の条件を順番に使って並べ替える
- このような場合は、**2.5** で説明した形で比較関数を書く

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>

struct Result
{
	std::string name;
	int score; // 得点
	int time; // 解答時間
};

int main()
{
	std::vector<Result> results =
	{
		{ "Alice", 300, 75 },
		{ "Bob", 500, 90 },
		{ "Carol", 300, 60 },
		{ "Dave", 500, 90 },
	};

	std::sort(results.begin(), results.end(), [](const auto& a, const auto& b)
		{
			if (a.score != b.score)
			{
				return a.score > b.score; // 得点の降順でソートする
			}

			if (a.time != b.time)
			{
				return a.time < b.time; // 得点が同じ場合は解答時間の昇順でソートする
			}

			return a.name < b.name; // それも同じ場合は名前の昇順でソートする
		});

	for (const auto& result : results)
	{
		std::cout << result.name << ' ' << result.score << ' ' << result.time << '\n';
	}
}
```
```txt:出力
Bob 500 90
Dave 500 90
Carol 300 60
Alice 300 75
```

### 3.6 ソートして隣り合う要素を調べる
- ソートすると、値が小さい順または大きい順に並ぶ
- そのため、値が近い要素どうしも近い位置に並ぶ
- ソート前では配列上の隣に意味がなくても、ソート後では隣り合う要素を調べることに意味が生まれる

---

- 例えば、数直線上にいくつかの点があり、最も近い 2 点の距離を求めることを考える
- 次の状態では、どの 2 点が近いのかわかりにくい

```txt
8 1 12 5
```

- 昇順にソートすると、次のようになる

```txt
1 5 8 12
```

- 最も近い 2 点があるとき、その 2 点の間に別の点が入ることはないため、ソート後の隣り合う要素の差を調べるだけで最小距離を求められる

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <limits>

int main()
{
	std::vector<int> x = { 8, 1, 12, 5 };

	std::sort(x.begin(), x.end()); // 昇順にソートする

	int minDistance = std::numeric_limits<int>::max();

	for (size_t i = 1; i < x.size(); ++i)
	{
		int distance = (x[i] - x[i - 1]); // 隣り合う要素の差を計算する
		minDistance = std::min(minDistance, distance); // 最小距離を更新する
	}

	std::cout << minDistance << '\n'; // 出力: 3
}
```


## 4. 練習問題

| 問題 | 解法 | 難易度 |
| --- | --- | :---: |
| **[ABC432 A - Permute to Maximize](https://atcoder.jp/contests/abc432/tasks/abc432_a)** | ソート（数字を降順に並べて最大化） | 1 |
| **[ABC042 B - 文字列大好きいろはちゃんイージー](https://atcoder.jp/contests/abc042/tasks/abc042_b)** | ソート（文字列を辞書順に並べる） | 1 |
| **[ABC088 B - Card Game for Two](https://atcoder.jp/contests/abc088/tasks/abc088_b)** | ソート（降順 + 順番に要素を取る） | 1 |
| **[ABC082 B - Two Anagrams](https://atcoder.jp/contests/abc082/tasks/abc082_b)** | ソート（昇順・降順 + 辞書順比較） | 1 |
| **[ABC380 A - 123233](https://atcoder.jp/contests/abc380/tasks/abc380_a)** | ソート（並びを正規化して要素構成を判定） | 1 |
| **[ABC154 C - Distinct or Not](https://atcoder.jp/contests/abc154/tasks/abc154_c)** | ソート（同じ値を隣接させて重複判定） | 2 |
| **[ABC260 A - A Unique Letter](https://atcoder.jp/contests/abc260/tasks/abc260_a)** | ソート（同じ文字を隣接させて出現パターンを判定） | 2 |
| **[ABC263 A - Full House](https://atcoder.jp/contests/abc263/tasks/abc263_a)** | ソート（同じ値を隣接させて個数パターン判定） | 2 |
| **[ABC386 A - Full House 2](https://atcoder.jp/contests/abc386/tasks/abc386_a)** | ソート（同じ値を隣接させて複数パターン判定） | 2 |
| **[ABC409 B - Citation](https://atcoder.jp/contests/abc409/tasks/abc409_b)** | ソート（降順に並べて順位と値の条件を判定） | 3 |
| **[ABC201 B - Do you know the second highest mountain?](https://atcoder.jp/contests/abc201/tasks/abc201_b)** | カスタムソート（要素の一部分だけをキーにする） | 2 |
| **[ABC440 B - Trifecta](https://atcoder.jp/contests/abc440/tasks/abc440_b)** | カスタムソート（値をキーに並べて上位を取得） | 3 |
| **[ABC142 C - Go to School](https://atcoder.jp/contests/abc142/tasks/abc142_c)** | カスタムソート（元の番号を保持して値順に並べる） | 3 |
| **[ABC448 C - Except and Min](https://atcoder.jp/contests/abc448/tasks/abc448_c)** | カスタムソート（元の番号を保持 + 小さい順に除外対象を飛ばす） | 3 |
| **[ABC323 B - Round-Robin Tournament](https://atcoder.jp/contests/abc323/tasks/abc323_b)** | カスタムソート（第 1 キー降順・第 2 キー昇順） | 2 |
| **[ABC128 B - Guidebook](https://atcoder.jp/contests/abc128/tasks/abc128_b)** | カスタムソート（文字列昇順・数値降順の複数キー） | 2 |
| **[ABC113 C - ID](https://atcoder.jp/contests/abc113/tasks/abc113_c)** | ソート + 二分探索（グループごとに並べて順位を求める） | 4 |
| **[ABC308 C - Standings](https://atcoder.jp/contests/abc308/tasks/abc308_c)** | カスタムソート（分数を交差積で比較 + 同率処理） | 4 |
| **[ABC219 C - Neo-lexicographic Ordering](https://atcoder.jp/contests/abc219/tasks/abc219_c)** | カスタムソート（独自の辞書順を比較関数で実装） | 5 |
| **[ABC414 D - Transmission Mission](https://atcoder.jp/contests/abc414/tasks/abc414_d)** | ソート + 貪欲法（大きい隙間から切って合計を最小化） | 6 |
| **[ABC268 F - Best Concatenation](https://atcoder.jp/contests/abc268/tasks/abc268_f)** | カスタムソート + 貪欲法（2 要素の順序比較から最適な並びを決める） | 7 |


:::details ABC432 A - Permute to Maximize
### [ABC432 A - Permute to Maximize](https://atcoder.jp/contests/abc432/tasks/abc432_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>
#include <functional>

int main()
{
	// 1 以上 9 以下の数字 A, B, C
	char A, B, C;
	std::cin >> A >> B >> C;

	std::string result = { A, B, C };

	// 降順にソートする
	std::sort(result.begin(), result.end(), std::greater{});

	std::cout << result << '\n';
}
```
:::

:::details ABC042 B - 文字列大好きいろはちゃんイージー
### [ABC042 B - 文字列大好きいろはちゃんイージー](https://atcoder.jp/contests/abc042/tasks/abc042_b)
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	int N, L;
	std::cin >> N >> L;

	std::vector<std::string> S(N);
	for (auto& s : S)
	{
		std::cin >> s;
	}

	// 文字列を辞書順にソートする
	std::sort(S.begin(), S.end());

	for (const auto& s : S)
	{
		std::cout << s;
	}
	std::cout << '\n';
}
```
:::

:::details ABC088 B - Card Game for Two
### [ABC088 B - Card Game for Two](https://atcoder.jp/contests/abc088/tasks/abc088_b)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	// 大きいカードから順に並べる
	std::sort(A.begin(), A.end(), std::greater{});

	int alice = 0;
	int bob = 0;

	for (int i = 0; i < N; ++i)
	{
		if ((i % 2) == 0)
		{
			alice += A[i];
		}
		else
		{
			bob += A[i];
		}
	}

	std::cout << (alice - bob) << '\n';
}
```
:::

:::details ABC082 B - Two Anagrams
### [ABC082 B - Two Anagrams](https://atcoder.jp/contests/abc082/tasks/abc082_b)
```cpp
#include <iostream>
#include <string>
#include <algorithm>
#include <functional>

int main()
{
	std::string s, t;
	std::cin >> s >> t;

	// s は辞書順で最小になるようにする
	std::sort(s.begin(), s.end());

	// t は辞書順で最大になるようにする
	std::sort(t.begin(), t.end(), std::greater{});

	std::cout << ((s < t) ? "Yes\n" : "No\n");
}
```
:::


:::details ABC380 A - 123233
### [ABC380 A - 123233](https://atcoder.jp/contests/abc380/tasks/abc380_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	// 6 桁の正整数 N
	std::string N;
	std::cin >> N;

	// 条件を満たす場合, ソートすれば "122333" になる
	std::sort(N.begin(), N.end());

	std::cout << ((N == "122333") ? "Yes\n" : "No\n");
}
```
:::

:::details ABC154 C - Distinct or Not
### [ABC154 C - Distinct or Not](https://atcoder.jp/contests/abc154/tasks/abc154_c)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	// ソートすると、同じ値がある場合は必ず隣り合う
	std::sort(A.begin(), A.end());

	for (int i = 0; i < (N - 1); ++i)
	{
		if (A[i] == A[i + 1])
		{
			std::cout << "NO\n";
			return 0;
		}
	}

	std::cout << "YES\n";
}
```
:::

:::details ABC260 A - A Unique Letter
### [ABC260 A - A Unique Letter](https://atcoder.jp/contests/abc260/tasks/abc260_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	std::string S;
	std::cin >> S;

	std::sort(S.begin(), S.end());

	// ありうるパターン:
	// xxx
	// xxy
	// xyy
	// xyz
	
	if (S[0] != S[1]) // xyy, xyz の場合
	{
		std::cout << S[0] << '\n';
	}
	else if (S[1] != S[2]) // xxy の場合
	{
		std::cout << S[2] << '\n';
	}
	else // xxx の場合
	{
		std::cout << "-1\n";
	}
}
```
:::

:::details ABC263 A - Full House
### [ABC263 A - Full House](https://atcoder.jp/contests/abc263/tasks/abc263_a)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<int> A(5);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	std::sort(A.begin(), A.end());

	const bool xxxyy = ((A[0] == A[2]) && (A[3] == A[4]) && (A[2] != A[3]));
	const bool xxyyy = ((A[0] == A[1]) && (A[2] == A[4]) && (A[1] != A[2]));

	if (xxxyy || xxyyy)
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC386 A - Full House 2
### [ABC386 A - Full House 2](https://atcoder.jp/contests/abc386/tasks/abc386_a)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<int> A(4);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	std::sort(A.begin(), A.end());

	// 1 枚追加でフルハウスを作れるパターンは
	// xxyy
	// xxxy
	// xyyy

	const bool xxyy = ((A[0] == A[1]) && (A[2] == A[3]) && (A[1] != A[2]));
	const bool xxxy = ((A[0] == A[1]) && (A[1] == A[2]) && (A[2] != A[3]));
	const bool xyyy = ((A[0] != A[1]) && (A[1] == A[2]) && (A[2] == A[3]));

	if (xxyy || xxxy || xyyy)
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC409 B - Citation
### [ABC409 B - Citation](https://atcoder.jp/contests/abc409/tasks/abc409_b)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	// 降順にソートする
	std::sort(A.begin(), A.end(), std::greater{});

	int answer = 0;

	for (int i = 0; i < N; ++i)
	{
		// i + 1 以上の値が i + 1 個以上あるか
		if (A[i] >= (i + 1))
		{
			answer = (i + 1);
		}
	}

	std::cout << answer << '\n';
}
```
:::

:::details ABC201 B - Do you know the second highest mountain?
### [ABC201 B - Do you know the second highest mountain?](https://atcoder.jp/contests/abc201/tasks/abc201_b)
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <utility>
#include <algorithm>

int main()
{
	int N;
	std::cin >> N;

	// { 山の名前, 高さ } の配列
	std::vector<std::pair<std::string, int>> mountains(N);
	for (auto&& [name, height] : mountains)
	{
		std::cin >> name >> height;
	}

	std::sort(mountains.begin(), mountains.end(), [](const auto& a, const auto& b)
		{
			return a.second > b.second; // 高さの降順でソートする
		});

	// 2 番目に高い山の名前を出力する
	std::cout << mountains[1].first << '\n';
}
```
:::

:::details ABC440 B - Trifecta
### [ABC440 B - Trifecta](https://atcoder.jp/contests/abc440/tasks/abc440_b)
```cpp
#include <iostream>
#include <vector>
#include <utility>
#include <algorithm>

int main()
{
	// N 頭の馬
	int N;
	std::cin >> N;

	// { 番号, 時間 } の配列
	std::vector<std::pair<int, int>> T(N);
	for (int i = 0; auto& t : T)
	{
		t.first = ++i;
		std::cin >> t.second;
	}

	std::sort(T.begin(), T.end(), [](const auto& a, const auto& b)
		{
			return a.second < b.second; // 時間の昇順でソートする
		});

	// 上位 3 頭の番号を出力する
	for (int i = 0; i < 3; ++i)
	{
		std::cout << T[i].first << ' ';
	}
}
```
:::

:::details ABC142 C - Go to School
### [ABC142 C - Go to School](https://atcoder.jp/contests/abc142/tasks/abc142_c)
```cpp
#include <iostream>
#include <vector>
#include <utility>
#include <algorithm>

int main()
{
	int N;
	std::cin >> N;

	// { 出席番号, 登校時の人数 }
	std::vector<std::pair<int, int>> students(N);
	for (int i = 0; auto& student : students)
	{
		student.first = ++i;
		std::cin >> student.second;
	}

	std::sort(students.begin(), students.end(), [](const auto& a, const auto& b)
		{
			return a.second < b.second; // 登校時の人数の昇順でソートする
		});

	// 登校した順に出席番号を出力する
	for (const auto& student : students)
	{
		std::cout << student.first << ' ';
	}
	std::cout << '\n';
}
```
:::


:::details ABC448 C - Except and Min
### [ABC448 C - Except and Min](https://atcoder.jp/contests/abc448/tasks/abc448_c)
```cpp
#include <iostream>
#include <vector>
#include <set>
#include <utility>
#include <algorithm>

int main()
{
	int N, Q;
	std::cin >> N >> Q;

	// { ボール番号, 書かれている整数 } のペアを格納する配列
	std::vector<std::pair<int, int>> balls(N);
	for (int i = 0; auto& ball : balls)
	{
		ball.first = ++i;
		std::cin >> ball.second;
	}

	// 書かれている整数の昇順でボールをソートする
	std::sort(balls.begin(), balls.end(), [](const auto& a, const auto& b)
		{
			return a.second < b.second;
		});

	while (Q--)
	{
		int K;
		std::cin >> K;

		// 取り除き対象のボール番号を管理するセット
		std::set<int> B;
		for (int i = 0; i < K; ++i)
		{
			int b;
			std::cin >> b;
			B.insert(b);
		}

		// 取り除くボールは K 個なので, 先頭 K + 1 個の中には
		// 取り除かれないボールが必ず 1 個以上ある
		for (int i = 0; i <= K; ++i)
		{
			// 取り除かれないボールが見つかったら, その整数を出力する
			if (!B.contains(balls[i].first))
			{
				std::cout << balls[i].second << '\n';
				break;
			}
		}
	}
}
```
:::


:::details ABC323 B - Round-Robin Tournament
### [ABC323 B - Round-Robin Tournament](https://atcoder.jp/contests/abc323/tasks/abc323_b)
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

struct Player
{
	int number;
	int wins;
};

int main()
{
	int N;
	std::cin >> N;

	std::vector<Player> players(N);
	for (int i = 0; auto& player : players)
	{
		std::string S;
		std::cin >> S;
		player.wins = std::count(S.begin(), S.end(), 'o');
		player.number = ++i;
	}

	std::sort(players.begin(), players.end(), [](const auto& a, const auto& b)
		{
			if (a.wins != b.wins)
			{
				return a.wins > b.wins; // 勝ち数の降順でソートする
			}

			return a.number < b.number; // 勝ち数が同じなら, プレイヤー番号の昇順でソートする
		});

	for (const auto& player : players)
	{
		std::cout << player.number << ' ';
	}
	std::cout << '\n';
}
```
:::

:::details ABC128 B - Guidebook
### [ABC128 B - Guidebook](https://atcoder.jp/contests/abc128/tasks/abc128_b)
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

struct Restaurant
{
	std::string city;
	int score;
	int number;
};

int main()
{
	int N;
	std::cin >> N;

	std::vector<Restaurant> restaurants(N);
	for (int i = 0; auto& restaurant : restaurants)
	{
		std::cin >> restaurant.city >> restaurant.score;
		restaurant.number = ++i;
	}

	std::sort(restaurants.begin(), restaurants.end(), [](const auto& a, const auto& b)
		{
			if (a.city != b.city)
			{
				return a.city < b.city; // 市名の昇順でソートする
			}

			return a.score > b.score; // 同じ市なら, 点数の降順でソートする
		});

	for (const auto& restaurant : restaurants)
	{
		std::cout << restaurant.number << '\n';
	}
}
```
:::

:::details ABC113 C - ID
### [ABC113 C - ID](https://atcoder.jp/contests/abc113/tasks/abc113_c)
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <iterator>

struct City
{
	int prefecture;
	int year;
};

std::string Pad(int n)
{
	std::string s = std::to_string(n);
	return (std::string(6 - s.size(), '0') + s);
}

int main()
{
	// N 個の県, M 個の市
	int N, M;
	std::cin >> N >> M;

	std::vector<City> cities(M);
	std::vector<std::vector<int>> prefectures(N);

	for (auto& city : cities)
	{
		std::cin >> city.prefecture >> city.year;
		--city.prefecture;
		prefectures[city.prefecture].push_back(city.year);
	}

	// 県ごとに県内の市の誕生年をソートする
	for (auto& prefecture : prefectures)
	{
		std::sort(prefecture.begin(), prefecture.end());
	}

	for (const auto& city : cities)
	{
		// 県番号
		const int pID = (city.prefecture + 1);

		// その市が県内で何番目に誕生したかを求める
		const auto it = std::lower_bound(prefectures[city.prefecture].begin(), prefectures[city.prefecture].end(), city.year);
		const int index = std::distance(prefectures[city.prefecture].begin(), it);

		// 市番号
		const int cID = (index + 1);

		// 県番号と市番号をゼロ埋めして出力
		std::cout << Pad(pID) << Pad(cID) << '\n';
	}
}
```
:::


:::details ABC308 C - Standings
### [ABC308 C - Standings](https://atcoder.jp/contests/abc308/tasks/abc308_c)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

struct Person
{
	long long wins;
	long long losses;
	int number;
};

int main()
{
	int N;
	std::cin >> N;

	std::vector<Person> people(N);
	for (auto i = 0; auto& person : people)
	{
		std::cin >> person.wins >> person.losses;
		person.number = ++i;
	}

	std::sort(people.begin(), people.end(),
		[](const auto& a, const auto& b)
		{
			// a.wins / (a.wins + a.losses)
			// と
			// b.wins / (b.wins + b.losses)
			// を、浮動小数点数を使わずに比較する
			const long long left = a.wins * (b.wins + b.losses);
			const long long right = b.wins * (a.wins + a.losses);

			if (left != right)
			{
				return left > right; // 成功率の降順でソートする
			}

			return a.number < b.number; // 成功率が同じなら, 番号の昇順でソートする
		});

	for (const auto& person : people)
	{
		std::cout << person.number << ' ';
	}
	std::cout << '\n';
}
```
:::

:::details ABC219 C - Neo-lexicographic Ordering
### [ABC219 C - Neo-lexicographic Ordering](https://atcoder.jp/contests/abc219/tasks/abc219_c)
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	std::string X;
	std::cin >> X;

	// indices[c]: 文字 c が何番目に小さいか
	std::vector<int> indices(26);
	for (int i = 0; i < 26; ++i)
	{
		indices[X[i] - 'a'] = i;
	}

	int N;
	std::cin >> N;

	std::vector<std::string> names(N);
	for (auto& name : names)
	{
		std::cin >> name;
	}

	// 高橋君の定めたアルファベット順の昇順でソートする
	std::sort(names.begin(), names.end(),
		[&](const std::string& a, const std::string& b)
		{
			const size_t length = std::min(a.size(), b.size());

			for (size_t i = 0; i < length; ++i)
			{
				if (a[i] != b[i])
				{
					const int indexA = indices[a[i] - 'a'];
					const int indexB = indices[b[i] - 'a'];
					return indexA < indexB;
				}
			}

			// 途中まで同じなら, 短い文字列を前にする
			return a.size() < b.size();
		});

	for (const auto& name : names)
	{
		std::cout << name << '\n';
	}
}
```
:::


:::details ABC414 D - Transmission Mission
### [ABC414 D - Transmission Mission](https://atcoder.jp/contests/abc414/tasks/abc414_d)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>

int main()
{
	// N 棟の家, M 個の基地局
	int N, M;
	std::cin >> N >> M;

	std::vector<long long> X(N);
	for (auto& x : X)
	{
		std::cin >> x;
	}

	// 家を座標の昇順にソートする
	std::sort(X.begin(), X.end());

	// 隣り合う家どうしの距離を求める
	std::vector<long long> gaps;
	for (int i = 0; i < (N - 1); ++i)
	{
		gaps.push_back(X[i + 1] - X[i]);
	}

	// 隙間を大きい順にソートする
	std::sort(gaps.begin(), gaps.end(), std::greater{});

	// すべての家を 1 個の基地局でカバーする場合の必要な電波強度
	long long answer = (X.back() - X.front());

	// 大きい隙間から M - 1 個切り、そのぶんを合計から取り除く
	for (int i = 0; i < (M - 1); ++i)
	{
		answer -= gaps[i];
	}

	std::cout << answer << '\n';
}
```
:::

:::details ABC268 F - Best Concatenation
### [ABC268 F - Best Concatenation](https://atcoder.jp/contests/abc268/tasks/abc268_f)
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

struct Item
{
	std::string s;

	// X の個数
	long long xCount = 0;

	// 数字の合計
	long long digitSum = 0;
};

int main()
{
	// N 個の文字列
	int N;
	std::cin >> N;

	std::vector<Item> items(N);
	for (auto& item : items)
	{
		std::cin >> item.s;

		for (const auto& ch : item.s)
		{
			if (ch == 'X')
			{
				++item.xCount;
			}
			else
			{
				item.digitSum += (ch - '0');
			}
		}
	}

	std::sort(items.begin(), items.end(),
		[](const auto& a, const auto& b)
		{
			// a → b と並べた場合と
			// b → a と並べた場合のどちらが高得点になるかを比較する
			return (a.xCount * b.digitSum) > (b.xCount * a.digitSum);
		});

	// そこまで登場した X の個数
	long long xCount = 0;

	// 合計得点
	long long answer = 0;

	for (const auto& item : items)
	{
		for (const auto& ch : item.s)
		{
			if (ch == 'X')
			{
				++xCount;
			}
			else
			{
				answer += (xCount * (ch - '0'));
			}
		}
	}

	std::cout << answer << '\n';
}
```
:::
