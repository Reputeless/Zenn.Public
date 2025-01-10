---
title: "std::fill()"
free: true
---

## 1. 概要
- 読み方: フィル
- `<algorithm>` ヘッダに含まれる
- 指定した範囲のすべての要素に、指定した値を代入する

#### (1) `std::fill(it1, it2, value)`
- イテレータ `it1` から `it2` の範囲の要素に値 `value` を代入する
- 計算量: `std::distance(it1, it2)` に対して $O(N)$

#### (2) `std::ranges::fill(it1, it2, value)`
- イテレータ `it1` から `it2` の範囲の要素に値 `value` を代入する
- 戻り値: 範囲の終端イテレータ
- 計算量: `std::distance(it1, it2)` に対して $O(N)$
- C++20～

#### (3) `std::ranges::fill(range, value)`
- 範囲 `range` の要素に値 `value` を代入する
- 戻り値: 範囲の終端イテレータ
- 計算量: `std::ranges::size(range)` に対して $O(N)$
- C++20～

## 2. 使い方

### 2.1 配列やコンテナのすべての要素に、指定した値を代入する
- `std::string` の要素をすべて `'a'` に変更する

```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	std::string s = "hello";

	std::ranges::fill(s, 'a');

	std::cout << s << '\n'; // aaaaa
}
```

- `std::vector` の要素をすべて `-1` に変更する

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<int> v = { 1, 2, 3, 4, 5 };

	std::ranges::fill(v, -1);

	for (const auto& elem : v)
	{
		std::cout << elem << ' '; // -1 -1 -1 -1 -1
	}

	std::cout << '\n';
}
```

- 配列の要素をすべて `-1` に変更する

```cpp
#include <iostream>
#include <algorithm>

int main()
{
	int a[] = { 1, 2, 3, 4, 5 };

	std::ranges::fill(a, -1);

	for (const auto& elem : a)
	{
		std::cout << elem << ' '; // -1 -1 -1 -1 -1
	}

	std::cout << '\n';
}
```

### 2.2 配列やコンテナの一部範囲の要素に、指定した値を代入する
- `std::vector` の `.begin()` から `(.begin() + 3)` の範囲の要素をすべて `-1` に変更する

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	std::vector<int> v = { 1, 2, 3, 4, 5 };

	std::ranges::fill(v.begin(), (v.begin() + 3), -1);

	for (const auto& elem : v)
	{
		std::cout << elem << ' '; // -1 -1 -1 4 5
	}

	std::cout << '\n';
}
```

- 配列の `begin()` から `(begin() + 3)` の範囲の要素をすべて `-1` に変更する

```cpp
#include <iostream>
#include <algorithm>

int main()
{
	int a[] = { 1, 2, 3, 4, 5 };

	std::ranges::fill(std::begin(a), (std::begin(a) + 3), -1);

	for (const auto& elem : a)
	{
		std::cout << elem << ' '; // -1 -1 -1 4 5
	}

	std::cout << '\n';
}
```


## 3. 使用上の注意
- すべての要素が同じ値である配列が必要な場合、すでに同じ大きさの配列があれば、それに対して `std::fill()` を使うと効率的
- 一方で、新しい配列を作成する場合は、`std::fill()` ではなくコンストラクタで解決したほうが効率的

### 3.1 すべての要素が同じ `std::string`
- すべての要素が同じ `std::string` を新しく作成する場合、`std::fill()` の代わりに `std::string` のコンストラクタを使う

```cpp
#include <iostream>
#include <string>

int main()
{
	// 5 個の 'a'
	std::string s(5, 'a');

	std::cout << s << '\n'; // aaaaa
}
```

### 3.2 すべての要素が同じ `std::vector`
- すべての要素が同じ値 `std::vector` を新しく作成する場合、`std::fill()` の代わりに `std::vector` のコンストラクタを使う

```cpp
#include <iostream>
#include <vector>

int main()
{
	// 5 個の -1
	std::vector<int> v(5, -1);

	for (const auto& elem : v)
	{
		std::cout << elem << ' '; // -1 -1 -1 -1 -1
	}

	std::cout << '\n';
}
```

### 4. 関連する関数
- ラムダ式などで生成した値を指定範囲に代入したい場合は `std::generate()`
- 「整数を 1 ずつ増やしながら代入する」など、単調増加列を作りたい場合は `std::iota()`
