---
title: "<algorithm>"
emoji: "🌡"
type: "tech"
topics: ["cpp"]
published: false
---


title: "<algorithm>"
free: true

# 1. 最小値と最大値

## 1.1 二つの値のうち小さいほうの値を得る
- `std::min(a, b)` は `a` と `b` のうち小さいほうを返します
- `a` と `b` が同じ型でない場合、`std::min<Type>(a, b)` のように明示的に型 `Type` を指定します
```cpp

```
```txt:出力

```

## 1.2 二つの値のうち大きいほうの値を得る
- a
```cpp

```
```txt:出力

```

## 1.3 三つ以上の値から最小値を得る
- a
```cpp

```
```txt:出力

```

## 1.4 三つ以上の値から最大値を得る
- a
```cpp

```
```txt:出力

```

## 1.5 配列の中から最小の要素とその位置を得る
- a
```cpp

```
```txt:出力

```

## 1.6 配列の中から最大の要素とその位置を得る
- a
```cpp

```
```txt:出力

```

## 1.7 二つの値から小さいほうの値と大きいほうの値を一度に得る
- a
```cpp

```
```txt:出力

```

## 1.8 三つ以上の値から最小値と最大値を一度に得る
- a
```cpp

```
```txt:出力

```

## 1.9 配列の中から最小の要素と最大の要素、およびそれらの位置を一度に得る
- a
```cpp

```
```txt:出力

```

## 1.10 ある値を、指定した最小値と最大値の範囲に収める
- a
```cpp

```
```txt:出力

```


# 2. 範囲に対する検索操作

## 2.1 
- a
```cpp

```
```txt:出力

```

## 2.2 
- a
```cpp

```
```txt:出力

```

## 2.3 
- a
```cpp

```
```txt:出力

```

## 2.4
- a
```cpp

```
```txt:出力

```

## 2.5
- a
```cpp

```
```txt:出力

```

## 2.6 
- a
```cpp

```
```txt:出力

```


# 3. 範囲に対する一般的な操作

## 3.1 
- a
```cpp

```
```txt:出力

```

## 3.2 
- a
```cpp

```
```txt:出力

```

## 3.3 
- a
```cpp

```
```txt:出力

```

## 3.4 
- a
```cpp

```
```txt:出力

```

## 3.5 
- a
```cpp

```
```txt:出力

```

## 3.6 
- a
```cpp

```
```txt:出力

```

## 3.7 
- a
```cpp

```
```txt:出力

```

## 3.8 
- a
```cpp

```
```txt:出力

```

## 3.9 
- a
```cpp

```
```txt:出力

```



# 4. ソート済みの範囲に対する二分探索

## 4.1 ある値を範囲に挿入するとして、ソートされた状態を維持できる最も左の位置 (lower_bound) を二分探索で取得する
- `std::lower_bound(itFirst, itLast, value)` は、ソート済みの範囲 `[itFirst, itLast)` に対して値 `value` を挿入するとして、ソートされた状態を壊さない最も左の位置を二分探索し、その位置を指すイテレータを返します
- 何番目かを整数値で得たい場合は、`std::distance(itFirst, itLast)` に、範囲の先頭のイテレータと `std::lower_bound()` が返したイテレータを渡して、その間の距離を求めます
> - `std::lower_bound(itFirst, itLast, value)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(\log N)$, そうでなければ $O(N)$
> - `std::distance(itFirst, itLast)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$

::: message
`std::lower_bound()` に渡すイテレータがランダムアクセスイテレータでない場合（具体的には `std::set` や `std::multiset` のイテレータ）、関数内部でのイテレータの移動の処理に線形時間を要するため、計算量は $O(N)$ に悪化します。代わりにそれぞれのコンテナのメンバ関数 `std::set::lower_bound()`, `std::multiset::lower_bound()` を使えば、データ構造の特性に合わせた実装になっているため、計算量は $O(\log N)$ になります。
:::

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	// ソート済みの状態
	std::vector<int> coins = { 1, 5, 10, 50, 100, 500 };

	auto it = std::lower_bound(coins.begin(), coins.end(), 0);
	std::cout << "(0): " << std::distance(coins.begin(), it) << '\n';

	it = std::lower_bound(coins.begin(), coins.end(), 1);
	std::cout << "(1): " << std::distance(coins.begin(), it) << '\n';

	it = std::lower_bound(coins.begin(), coins.end(), 2);
	std::cout << "(2): " << std::distance(coins.begin(), it) << '\n';

	it = std::lower_bound(coins.begin(), coins.end(), 5);
	std::cout << "(5): " << std::distance(coins.begin(), it) << '\n';

	it = std::lower_bound(coins.begin(), coins.end(), 499);
	std::cout << "(499): " << std::distance(coins.begin(), it) << '\n';

	it = std::lower_bound(coins.begin(), coins.end(), 500);
	std::cout << "(500): " << std::distance(coins.begin(), it) << '\n';

	it = std::lower_bound(coins.begin(), coins.end(), 501);
	std::cout << "(501): " << std::distance(coins.begin(), it) << '\n';

	std::cout << "---\n";

	// ソート済みの状態
	std::vector<std::string> words = { "apple", "bird", "cat" };

	auto it2 = std::lower_bound(words.begin(), words.end(), "alarm");
	words.insert(it2, "alarm");

	it2 = std::lower_bound(words.begin(), words.end(), "banana");
	words.insert(it2, "banana");

	it2 = std::lower_bound(words.begin(), words.end(), "dog");
	words.insert(it2, "dog");

	// ソート済みの状態が保たれていることを確認
	for (const auto& word : words)
	{
		std::cout << word << '\n';
	}
}
```
```txt:出力
(0): 0
(1): 0
(2): 1
(5): 1
(499): 5
(500): 5
(501): 6
---
alarm
apple
banana
bird
cat
dog
```

## 4.2 ある値を範囲に挿入するとして、ソートされた状態を維持できる最も右の位置 (upper_bound) を二分探索で取得する
- `std::upper_bound(itFirst, itLast, value)` は、ソート済みの範囲 `[itFirst, itLast)` に対して値 `value` を挿入するとして、ソートされた状態を壊さない最も右の位置を二分探索し、その位置を指すイテレータを返します
- 何番目かを整数値で得たい場合は、`std::distance(itFirst, itLast)` に、範囲の先頭のイテレータと `std::upper_bound()` が返したイテレータを渡して、その間の距離を求めます
> - `std::upper_bound(itFirst, itLast, value)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(\log N)$, そうでなければ $O(N)$
> - `std::distance(itFirst, itLast)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$

::: message
`std::upper_bound()` に渡すイテレータがランダムアクセスイテレータでない場合（具体的には `std::set` や `std::multiset` のイテレータ）、関数内部でのイテレータの移動の処理に線形時間を要するため、計算量は $O(N)$ に悪化します。代わりにそれぞれのコンテナのメンバ関数 `std::set::upper_bound()`, `std::multiset::upper_bound()` を使えば、データ構造の特性に合わせた実装になっているため、計算量は $O(\log N)$ になります。
:::

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	// ソート済みの状態
	std::vector<int> coins = { 1, 5, 10, 50, 100, 500 };
	
	auto it = std::upper_bound(coins.begin(), coins.end(), 0);
	std::cout << "(0): " << std::distance(coins.begin(), it) << '\n';

	it = std::upper_bound(coins.begin(), coins.end(), 1);
	std::cout << "(1): " << std::distance(coins.begin(), it) << '\n';

	it = std::upper_bound(coins.begin(), coins.end(), 2);
	std::cout << "(2): " << std::distance(coins.begin(), it) << '\n';

	it = std::upper_bound(coins.begin(), coins.end(), 5);
	std::cout << "(5): " << std::distance(coins.begin(), it) << '\n';

	it = std::upper_bound(coins.begin(), coins.end(), 499);
	std::cout << "(499): " << std::distance(coins.begin(), it) << '\n';

	it = std::upper_bound(coins.begin(), coins.end(), 500);
	std::cout << "(500): " << std::distance(coins.begin(), it) << '\n';

	it = std::upper_bound(coins.begin(), coins.end(), 501);
	std::cout << "(501): " << std::distance(coins.begin(), it) << '\n';

	std::cout << "---\n";

	// ソート済みの状態
	std::vector<std::string> words = { "apple", "bird", "cat" };

	auto it2 = std::upper_bound(words.begin(), words.end(), "alarm");
	words.insert(it2, "alarm");

	it2 = std::upper_bound(words.begin(), words.end(), "banana");
	words.insert(it2, "banana");

	it2 = std::upper_bound(words.begin(), words.end(), "dog");
	words.insert(it2, "dog");

	// ソート済みの状態が保たれていることを確認
	for (const auto& word : words)
	{
		std::cout << word << '\n';
	}
}
```
```txt:出力
(0): 0
(1): 1
(2): 1
(5): 2
(499): 5
(500): 6
(501): 6
---
alarm
apple
banana
bird
cat
dog
```

## 4.3 lower_bound と upper_bound の結果を同時に取得する
- `std::equal_range(itFirst, itLast, value)` は、`std::lower_bound(itFirst, itLast, value)` と `std::upper_bound(itFirst, itLast, value)` の結果を `std::pair` で返します
- 探索時に情報を共有できるため、`std::lower_bound()`, `std::upper_bound()` を個別に呼ぶよりも定数倍高速化されます
> - `std::equal_range(itFirst, itLast, value)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(\log N)$, そうでなければ $O(N)$
> - `std::distance(itFirst, itLast)` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$

::: message
`std::equal_range()` に渡すイテレータがランダムアクセスイテレータでない場合（具体的には `std::set` や `std::multiset` のイテレータ）、関数内部でのイテレータの移動の処理に線形時間を要するため、計算量は $O(N)$ に悪化します。代わりにそれぞれのコンテナのメンバ関数 `std::set::equal_range()`, `std::multiset::equal_range()` を使えば、データ構造の特性に合わせた実装になっているため、計算量は $O(\log N)$ になります。
:::

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// ソート済みの状態
	std::vector<int> coins = { 1, 1, 5, 10, 10, 10, 500 };

	auto p = std::equal_range(coins.begin(), coins.end(), 0);
	std::cout << "(0): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';

	p = std::equal_range(coins.begin(), coins.end(), 1);
	std::cout << "(1): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';

	p = std::equal_range(coins.begin(), coins.end(), 2);
	std::cout << "(2): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';

	p = std::equal_range(coins.begin(), coins.end(), 5);
	std::cout << "(5): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';

	p = std::equal_range(coins.begin(), coins.end(), 10);
	std::cout << "(10): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';

	p = std::equal_range(coins.begin(), coins.end(), 500);
	std::cout << "(500): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';

	p = std::equal_range(coins.begin(), coins.end(), 501);
	std::cout << "(501): " << std::distance(coins.begin(), p.first)
		<< " -> " << std::distance(coins.begin(), p.second) << '\n';
}
```
```txt:出力
(0): 0 -> 0
(1): 0 -> 2
(2): 2 -> 2
(5): 2 -> 3
(10): 3 -> 6
(500): 6 -> 7
(501): 7 -> 7
```

# 5. ソート済みの二つの範囲に対する集合演算や操作

## 5.1 二つの集合の少なくとも片方に存在する要素からなる集合（和集合）を得る
- `std::set_union(itFirst1, itLast1, itFirst2, itLast2, itOut)` は、ソート済みの範囲 `[itFirst1, itLast1)` および `[itFirst2, itLast2)` の少なくとも片方に存在する要素からなる集合（和集合）の要素を `itOut` に出力していきます
- `{0, 1}` と `{1, 2}` の和集合は `{0, 1, 2}` です
- `{0, 0, 1, 1}` と `{1, 2}` の和集合は `{0, 0, 1, 1, 2}` です
- `std::back_inserter()` と組み合わせることで、和集合の結果を別の配列に格納することができます
- `itOut` に出力される要素の順序はソート済みです
> - `std::set_union(itFirst1, itLast1, itFirst2, itLast2, itOut)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// 両方ともソート済みの状態
	std::vector<int> a = { 0, 1, 2, 3 };
	std::vector<int> b = { 2, 3, 4, 5 };
	// 結果を格納する配列
	std::vector<int> c;

	// a と b の和集合を得る
	std::set_union(a.begin(), a.end(), b.begin(), b.end(), std::back_inserter(c));
	for (const auto& n : c)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	// 両方ともソート済みの状態
	std::vector<int> d = { 0, 0, 1, 1, 2, 2 };
	std::vector<int> e = { 1, 2, 2, 3, 3 };
	// 結果を格納する配列
	std::vector<int> f;

	// d と e の和集合を得る
	std::set_union(d.begin(), d.end(), e.begin(), e.end(), std::back_inserter(f));
	for (const auto& n : f)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
0 1 2 3 4 5
---
0 0 1 1 2 2 3 3
```

## 5.2 二つの集合の両方に存在する要素からなる集合（積集合）を得る
- `std::set_intersection(itFirst1, itLast1, itFirst2, itLast2, itOut)` は、ソート済みの範囲 `[itFirst1, itLast1)` および `[itFirst2, itLast2)` の両方に存在する要素からなる集合（積集合）の要素を `itOut` に出力していきます
- `{0, 1}` と `{1, 2}` の積集合は `{1}` です
- `{0, 1}` と `{2, 3}` の積集合は `{}` です
- `{0, 0, 1, 1, 2, 2}` と `{1, 1, 2}` の積集合は `{1, 1, 2}` です
- `std::back_inserter()` と組み合わせることで、積集合の結果を別の配列に格納することができます
- `itOut` に出力される要素の順序はソート済みです
> - `std::set_intersection(itFirst1, itLast1, itFirst2, itLast2, itOut)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// 両方ともソート済みの状態
	std::vector<int> a = { 0, 1, 2, 3 };
	std::vector<int> b = { 2, 3, 4, 5 };
	// 結果を格納する配列
	std::vector<int> c;

	// a と b の積集合を得る
	std::set_intersection(a.begin(), a.end(), b.begin(), b.end(), std::back_inserter(c));
	for (const auto& n : c)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	// 両方ともソート済みの状態
	std::vector<int> d = { 0, 0, 1, 1, 2, 2 };
	std::vector<int> e = { 1, 2, 2, 3, 3 };
	// 結果を格納する配列
	std::vector<int> f;

	// d と e の積集合を得る
	std::set_intersection(d.begin(), d.end(), e.begin(), e.end(), std::back_inserter(f));
	for (const auto& n : f)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
2 3
---
1 2 2
```

## 5.3 二つの集合について、前者の中から後者に属する要素を取り除いた集合（差集合）を得る
- `std::set_difference(itFirst1, itLast1, itFirst2, itLast2, itOut)` は、ソート済みの範囲 `[itFirst1, itLast1)` および `[itFirst2, itLast2)` について、前者の中から後者に属する要素を取り除いた集合（差集合）の要素を `itOut` に出力していきます
- `{0, 1, 2, 3}` と `{1, 2, 4, 5}` の差集合は `{0, 3}` です
- `{0, 1}` と `{0, 1}` の積集合は `{}` です
- `{0, 0, 1, 1, 2, 2}` と `{1, 1, 2}` の積集合は `{0, 0, 2}` です
- `std::back_inserter()` と組み合わせることで、差集合の結果を別の配列に格納することができます
- `itOut` に出力される要素の順序はソート済みです
> - `std::set_difference(itFirst1, itLast1, itFirst2, itLast2, itOut)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// 両方ともソート済みの状態
	std::vector<int> a = { 0, 1, 2, 3 };
	std::vector<int> b = { 2, 3, 4, 5 };
	// 結果を格納する配列
	std::vector<int> c;

	// a と b の差集合を得る
	std::set_difference(a.begin(), a.end(), b.begin(), b.end(), std::back_inserter(c));
	for (const auto& n : c)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	// 両方ともソート済みの状態
	std::vector<int> d = { 0, 0, 1, 1, 2, 2 };
	std::vector<int> e = { 1, 2, 2, 3, 3 };
	// 結果を格納する配列
	std::vector<int> f;

	// d と e の差集合を得る
	std::set_difference(d.begin(), d.end(), e.begin(), e.end(), std::back_inserter(f));
	for (const auto& n : f)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
0 1
---
0 0 1
```

## 5.4 二つの集合のどちらか片方にしか存在しない要素からなる集合（対称差集合）を得る
- `std::set_symmetric_difference(itFirst1, itLast1, itFirst2, itLast2, itOut)` は、ソート済みの範囲 `[itFirst1, itLast1)` および `[itFirst2, itLast2)` について、どちらか片方にしか存在しない要素からなる集合（対称差集合）の要素を `itOut` に出力していきます
- `{0, 1, 2, 3}` と `{1, 2, 4, 5}` の対称差集合は `{0, 4, 5}` です
- `{0, 1}` と `{0, 1}` の対称差集合は `{}` です
- `{0, 0, 1, 1, 2, 2}` と `{1, 1, 2}` の対称差集合は `{0, 0, 2}` です
- `std::back_inserter()` と組み合わせることで、対称差集合の結果を別の配列に格納することができます
- `itOut` に出力される要素の順序はソート済みです
> - `std::set_symmetric_difference(itFirst1, itLast1, itFirst2, itLast2, itOut)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// 両方ともソート済みの状態
	std::vector<int> a = { 0, 1, 2, 3 };
	std::vector<int> b = { 2, 3, 4, 5 };
	// 結果を格納する配列
	std::vector<int> c;

	// a と b の対称差集合を得る
	std::set_symmetric_difference(a.begin(), a.end(), b.begin(), b.end(), std::back_inserter(c));
	for (const auto& n : c)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	// 両方ともソート済みの状態
	std::vector<int> d = { 0, 0, 1, 1, 2, 2 };
	std::vector<int> e = { 1, 2, 2, 3, 3 };
	// 結果を格納する配列
	std::vector<int> f;

	// d と e の対称差集合を得る
	std::set_symmetric_difference(d.begin(), d.end(), e.begin(), e.end(), std::back_inserter(f));
	for (const auto& n : f)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
0 1 4 5
---
0 0 1 3 3
```

## 5.5 二つのソート済みの集合をマージした、ソート済みの集合を得る
- `std::merge(itFirst1, itLast1, itFirst2, itLast2, itOut)` は、ソート済みの範囲 `[itFirst1, itLast1)` および `[itFirst2, itLast2)` の要素をまとめた集合の要素を `itOut` に出力していきます
- `{0, 1, 2, 2}` と `{0, 1, 3, 4, 5}` をマージした集合は `{0, 0, 1, 1, 2, 2, 3, 4, 5}` です
- マージ後の要素数は 2 つの範囲の要素数の合計になります
- `std::back_inserter()` と組み合わせることで、マージした集合の結果を別の配列に格納することができます
- `itOut` に出力される要素の順序はソート済みです
> - `std::merge(itFirst1, itLast1, itFirst2, itLast2, itOut)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// 両方ともソート済みの状態
	std::vector<int> a = { 0, 1, 2, 3 };
	std::vector<int> b = { 2, 3, 4, 5 };
	// 結果を格納する配列
	std::vector<int> c;

	// a と b をマージした集合を得る
	std::merge(a.begin(), a.end(), b.begin(), b.end(), std::back_inserter(c));
	for (const auto& n : c)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';

	std::cout << "---\n";

	// 両方ともソート済みの状態
	std::vector<int> d = { 0, 0, 1, 1, 2, 2 };
	std::vector<int> e = { 1, 2, 2, 3, 3 };
	// 結果を格納する配列
	std::vector<int> f;

	// d と e をマージした集合を得る
	std::merge(d.begin(), d.end(), e.begin(), e.end(), std::back_inserter(f));
	for (const auto& n : f)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';
}
```
```txt:出力
0 1 2 2 3 3 4 5
---
0 0 1 1 1 2 2 2 2 3 3
```


# 6. 順列

## 6.1 順列を作成する
- `std::next_permutaion(itFirst, itLast)` は、範囲 `[itFirst, itLast)` について、辞書順で次にくる順列になるよう要素を並び替えます
- 次の順列が存在する場合は `true`, それ以外の場合は `false` を返します
- ソート済みの範囲から始め、`do while()` と組み合わせることで、全ての順列を列挙できます
> - `std::next_permutaion(itFirst, itLast)` の計算量: $O(N)$
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main()
{
	// すべての順列を列挙するためにはソート済みの状態から始める
	std::vector<int> v = { 1, 2, 3, 4 };
	do
	{
		for (const auto& n : v)
		{
			std::cout << n << ' ';
		}
		std::cout << '\n';
	} while (std::next_permutation(v.begin(), v.end())); // 辞書順で次の順列へ

	std::cout << "---\n";

	// すべての順列を列挙するためにはソート済みの状態から始める
	std::string s = "abc";
	do
	{
		std::cout << s << '\n';
	} while (std::next_permutation(s.begin(), s.end())); // 辞書順で次の順列へ
}
```
```txt:出力
1 2 3 4
1 2 4 3
1 3 2 4
1 3 4 2
1 4 2 3
1 4 3 2
2 1 3 4
2 1 4 3
2 3 1 4
2 3 4 1
2 4 1 3
2 4 3 1
3 1 2 4
3 1 4 2
3 2 1 4
3 2 4 1
3 4 1 2
3 4 2 1
4 1 2 3
4 1 3 2
4 2 1 3
4 2 3 1
4 3 1 2
4 3 2 1
---
abc
acb
bac
bca
cab
cba
```

