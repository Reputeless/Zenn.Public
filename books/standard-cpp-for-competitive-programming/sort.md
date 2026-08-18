---
title: "ソート"
free: true
---

## 1. std::sort の使い方

### 1.1 昇順にソートする
- `std::sort(it1, it2)` は、イテレータ `it1` から `it2` の範囲を昇順（小さい順）に並べ替える
- `v.begin(), v.end()` を渡せば全体がソートされる
- `std::vector` や `std::array`、`std::string` で使うことができる
- 計算量は $O(N \log N)$

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
- 文字コード（ASCII）の順のため、`'0'`～`'9'` < `'A'`～`'Z'` < `'a'`～`'z'` の順になる

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
- ここにラムダ式を書くと、あらかじめ用意された基準に頼らず、自由なルールでソートできる
- 渡すラムダ式は「2 つの要素を受け取って `bool` を返す」形で書く

```cpp
std::sort(v.begin(), v.end(), [](const auto& a, const auto& b)
	{
		return /* a を b より前に置くべきなら true */;
	});
```

- この `bool` は、`std::sort` からの「`a` を `b` より前に置くべきですか？」という質問に対する返事だと考えるとよい。`true` なら `a` が前、`false` なら `b` が前に置かれる
- `a` と `b` が同順位（引き分け）のときは必ず `false` を返す必要がある。「前に置くべき」とは言えないため
- したがって、比較式は `!=` と `<` または `>` を組み合わせて書き、`<=` と `>=` は絶対に使わない
- 複数の基準で並べたいときは、「注目する要素が異なるときだけその要素で決める（`!=` で判定）→ 同じなら次の要素を見る」という形で書く
- **2.1～2.4** では、通常の昇順・降順ソートや、`std::pair`・`std::tuple` のソートを、あえてラムダ式を使って再現する例を示す（ラムダ式を使わなくても実現できる内容）
- **2.5～2.6** では、ラムダ式を使って独自のルールでソートする例を示す（ラムダ式を使わないと実現できない内容）


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
- 「`a.first != b.first` のとき `a.first > b.first`、 そうでないとき `a.second > b.second`」というルールにすれば、カスタムソートで `std::pair` のデフォルトの降順ソートを再現できる

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

### 2.5 pair のソートのカスタマイズ
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
2 second
2 two
5 five
10 ten
```

### 2.6 自作クラスのソート
- 自作クラスにはデフォルトで大小関係が定義されていないため、そのままでは `std::sort` でソートできない
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
		{ "Alice", 30 },
		{ "Bob", 25 },
		{ "Charlie", 35 },
		{ "David", 30 },
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

### 2.7 特定の要素だけでソートする
- 例えば、`std::pair<int, std::string>` の整数の部分だけでソートしたい場合は、文字列の部分を無視して比較するルールにする
- 整数が同じ場合の文字列の順序は保証されない。次の例では `1 one` と `1 first` のどちらが先に来るかは環境によって変わる
- 元の並び順を保ちたい場合は `std::sort` の代わりに `std::stable_sort` を使う

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


## 3. 符号反転でソート条件を表現する



## 4. ソートで解ける問題のパターン


### 4.1 並び順を正規化して比較する


### 4.2 同じ値を隣り合わせにする


### 4.3 複数の条件で並べ替える


## 5. 練習問題


