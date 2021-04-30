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
- a
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



# 4. ソート済みの範囲に対する操作

## 4.1 指定した値が存在するか二分探索する
- a
```cpp

```
```txt:出力

```

## 4.2 ある値を範囲に挿入するとして、ソートされた状態を維持できる最も左の位置を二分探索で取得する
- a
- 計算量: イテレータがランダムアクセスイテレータの場合 $O(\log N)$, そうでなければ $O(N)$

::: message
`std::lower_bound()` に渡すイテレータがランダムアクセスイテレータでない場合（具体的には `std::set` や `std::multiset` のイテレータ）、関数内部でのイテレータの移動の処理に線形時間を要するため、計算量は $O(N)$ に悪化します。代わりにそれぞれのコンテナのメンバ関数 `std::set::lower_bound()`, `std::multiset::lower_bound()` を使えば、データ構造の特性に合わせた実装になっているため、計算量は $O(\log N)$ になります。
:::

```cpp

```
```txt:出力

```

## 4.3 ある値を範囲に挿入するとして、ソートされた状態を維持できる最も右の位置を二分探索で取得する
- `std::upper_bound(itFirst, itLast, value)` は、ソート済みの範囲 `[itFirst, itLast)` に対して値 `value` を挿入するとして、ソートされた状態を壊さない最も右の位置を二分探索し、その位置を指すイテレータを返します
- 何番目かを整数値で得たい場合は、`std::distance(itFirst, itLast)` に、範囲の先頭のイテレータと `std::upper_bound()` が返したイテレータを渡して、その間の距離を求めます
- `std::upper_bound()` の計算量: イテレータがランダムアクセスイテレータの場合 $O(\log N)$, そうでなければ $O(N)$
- `std::distance()` の計算量: イテレータがランダムアクセスイテレータの場合 $O(1)$, そうでなければ $O(N)$

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
---
alarm
apple
banana
bird
cat
dog
```


# 5. 順列

## 5.1 順列を作成する
- `std::next_permutaion(itFirst, itLast)` は、範囲 `[itFirst, itLast)` について、辞書順で次にくる順列になるよう要素を並び替えます
- 次の順列が存在する場合は `true`, それ以外の場合は `false` を返します
- ソート済みの範囲から始め、`do while()` と組み合わせることで、全ての順列を列挙できます
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

