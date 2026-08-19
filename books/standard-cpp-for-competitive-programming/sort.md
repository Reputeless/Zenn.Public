---
title: "ソート"
free: true
---

## 1. std::sort の使い方

### 1.1 昇順にソートする
- `std::sort(it1, it2)` は、イテレータ `it1` から `it2` の範囲を昇順（小さい順）に並べ替える
- `v.begin(), v.end()` を渡せば全体がソートされる
- `std::vector` や `std::array`、`std::string` に対して使うことができる
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
- `std::pair` や `std::tuple` の一部の要素だけ符号を反転させれば、「1 番目は降順、2 番目は昇順」のような混在した基準を、デフォルトの昇順ソートだけで表現できる（ラムダ式を書かなくて済む）

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

	for (auto&& [number, word] : v)
	{
		number = -number; // 整数の符号を反転させる
	}

	std::sort(v.begin(), v.end()); // 昇順にソートする

	for (auto&& [number, word] : v)
	{
		std::cout << -number << ' ' << word << '\n'; // 符号を元に戻して出力
	}
	std::cout << '\n';
}
```
```txt:出力
10 ten
5 five
2 second
2 two
1 first
1 one
```


## 4. ソートで解ける問題のパターン
- ソートは、順番に並べて出力する以外にも、問題を解きやすい形に整える目的で使うことができる

### 4.1 並び順を正規化して比較する
- 並び順の違いを無視して、中身が同じかどうかを判定したいときは、両方をソートしてから比較する
- ソート後の並びは中身だけで決まるため、同じ要素の集まりであれば必ず一致する

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

- `std::vector<int>` でも同様に、要素の集まりが同じかどうかを判定できる

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

### 4.2 同じ値を隣り合わせにする
- ソートすると同じ値どうしが必ず連続して並ぶ
- そのため、「重複があるか」「同じ値が何個あるか」を、隣り合う 2 つの要素の比較だけで調べられる

---

- 5 枚のカードの数字を std::vector<int> に格納する
- フルハウスは「同じ数字が 3 枚 + 別の同じ数字が 2 枚」の組み合わせ
- `std::sort` で昇順に並べると、同じ数字のカードが隣り合う
- ソート後の並びは、フルハウスなら `x x x y y` または `x x y y y` のどちらかになる
- その 2 パターンのどちらかに当てはまるかを調べれば、フルハウスかどうかを判定できる

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<int> cards = { 7, 3, 7, 3, 7 };

	std::sort(cards.begin(), cards.end());

	if (((cards[0] == cards[2]) && (cards[3] == cards[4])) || // x x x y y のパターン
		((cards[0] == cards[1]) && (cards[2] == cards[4]))))  // x x y y y のパターン 
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


### 4.3 複数の条件で並べ替える
- 「スコアの高い順、同点なら名前の辞書順」のようなランキングは、複数の基準を組み合わせたソートで作れる

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

## 5. 練習問題

:::details ABC042 B - 文字列大好きいろはちゃんイージー
### [ABC042 B - Iroha Loves Strings](https://atcoder.jp/contests/abc042/tasks/abc042_b)
```cpp

```

:::details ABC082 B - Two Anagrams
### [ABC082 B - Two Anagrams](https://atcoder.jp/contests/abc082/tasks/abc082_b)
```cpp

```

:::details ABC088 B - Card Game for Two
### [ABC088 B - Card Game for Two](https://atcoder.jp/contests/abc088/tasks/abc088_b)
```cpp

```


:::details ABC386 A - Full House 2
### [ABC386 A - Full House 2](https://atcoder.jp/contests/abc386/tasks/abc386_a)
```cpp

```


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

:::details ABC432 A - Permute to Maximize
### [ABC432 A - Permute to Maximize](https://atcoder.jp/contests/abc432/tasks/abc432_a)
```cpp
#include <iostream>
#include <string>
#include <algorithm>

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



:::details ABC102 C - Linear Approximation
### [ABC102 C - Linear Approximation](https://atcoder.jp/contests/abc102/tasks/abc102_c)
```cpp

```


:::details ABC113 C - ID
### [ABC113 C - ID](https://atcoder.jp/contests/abc113/tasks/abc113_c)
```cpp

```


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

	// 高さの降順でソートする
	std::sort(mountains.begin(), mountains.end(), [](const auto& a, const auto& b)
		{
			return a.second > b.second;
		});

	// 2 番目に高い山の名前を出力する
	std::cout << mountains[1].first << '\n';
}
:::
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
	for (int i = 0; i < N; ++i)
	{
		int t;
		std::cin >> t;
		T[i] = { (i + 1), t };
	}

	// 時間の昇順でソートする
	std::sort(T.begin(), T.end(), [](const auto& a, const auto& b)
		{
			return a.second < b.second;
		});

	// 上位 3 頭の番号を出力する
	for (int i = 0; i < 3; ++i)
	{
		std::cout << T[i].first << ' ';
	}
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

			return a.score > b.score; // 同じ市なら点数の降順でソートする
		});

	for (const auto& restaurant : restaurants)
	{
		std::cout << restaurant.number << '\n';
	}
}
```
:::


:::details ABC142 C - Go to School
### [ABC142 C - Go to School](https://atcoder.jp/contests/abc142/tasks/abc142_c)
```cpp

```


:::details ABC219 C - Neo-lexicographic Ordering
### [ABC219 C - Neo-lexicographic Ordering](https://atcoder.jp/contests/abc219/tasks/abc219_c)
```cpp

```


:::details ABC308 C - Standings
### [ABC308 C - Standings](https://atcoder.jp/contests/abc308/tasks/abc308_c)
```cpp

```


:::details ABC268 F - Best Concatenation
### [ABC268 F - Best Concatenation](https://atcoder.jp/contests/abc268/tasks/abc268_f)
```cpp

```
