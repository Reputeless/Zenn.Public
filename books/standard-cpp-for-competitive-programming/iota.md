---
title: "std::iota()"
free: true
---

## 1. 概要
- 読み方: イオタ
- `<numeric>` ヘッダに含まれる
- 指定した範囲に、1 ずつ単調増加する値を代入する
- 「配列の中身を 1, 2, 3, 4, ..., N にセットしたい」という場面で活躍

#### (1) `std::iota(it1, it2, value)`
- イテレータ `it1` から `it2` の範囲の要素に、`value` から `++` 演算子で単調増加していく値を代入する
- 計算量: `std::distance(it1, it2)` に対して $O(N)$

#### (2) `std::ranges::iota(it1, it2, value)`
- イテレータ `it1` から `it2` の範囲の要素に、`value` から `++` 演算子で単調増加していく値を代入する
- 戻り値: `iota_result`
	- `.out` には範囲の終端イテレータが格納される
	- `.value` には最後に代入した値が格納される
- 計算量: `std::distance(it1, it2)` に対して $O(N)$

#### (3) `std::ranges::iota(range, value)`
- 範囲 `range` の要素に、`value` から `++` 演算子で単調増加していく値を代入する
- 戻り値: `iota_result`
	- `.out` には範囲の終端イテレータが格納される
	- `.value` には最後に代入した値が格納される
- 計算量: `std::ranges::size(range)` に対して $O(N)$


## 2. 使い方

### 2.1 配列やコンテナの要素を、1 ずつ単調増加する値に変更する
- `std::vector` の要素を 0, 1, 2, 3, ..., N-1 に変更する

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> v(10);

	// 要素を 0, 1, 2, 3, ... に変更する
	std::ranges::iota(v, 0);

	for (const auto& elem : v)
	{
		std::cout << elem << ' '; // 0 1 2 3 4 5 6 7 8 9
	}

	std::cout << '\n';
}
```

- `std::vector` の要素を 1, 2, 3, 4, ..., N に変更する

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> v(10);

	// 要素を 1, 2, 3, 4, ... に変更する
	std::ranges::iota(v, 1);

	for (const auto& elem : v)
	{
		std::cout << elem << ' '; // 1 2 3 4 5 6 7 8 9 10
	}

	std::cout << '\n';
}
```

### 2.2 `a`～`z` までの文字からなる `std::string` を作る
- `std::string` の要素を `'a'`, `'b'`, `'c'`, ..., `'z'` に変更する

```cpp
#include <iostream>
#include <string>

int main()
{
	// 個数のみ指定のコンストラクタは無いので、26 個の 'a' で初期化
	std::string s(26, 'a');

	// 要素を 'a', 'b', 'c', ... に変更する
	std::ranges::iota(s, 'a');

	std::cout << s << '\n'; // abcdefghijklmnopqrstuvwxyz
}
```


## 3. 関連する関数
- 指定範囲に同じ値を代入したい場合は `std::fill()`
- ラムダ式などで生成した値を指定範囲に代入したい場合は `std::generate()`
