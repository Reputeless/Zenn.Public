---
title: "std::to_string()"
free: true
---

## 1. 概要
- 読み方: トゥーストリング
- `<string>` ヘッダに含まれる
- `int`, `long long` などの整数を `std::string` に変換する
- 「とにかく簡単に数値を文字列に変換したい」という場面で活躍

#### `std::to_string(n)`
- 整数 `n` を `std::string` に変換する
- 戻り値: `std::string`


## 2. 使い方

### 2.1 整数を文字列に変換する

```cpp
#include <iostream>
#include <string>
#include <algorithm>

int main()
{
	int n = 123456;

	// 整数を文字列に変換する
	std::string s = std::to_string(n);

	std::cout << s << '\n'; // 123456

	std::ranges::reverse(s);

	std::cout << s << '\n'; // 654321
}
```

## 3. 使用上の注意
- `std::string` の作成を含め、コストがやや大きいため、制約が厳しい問題では `std::to_string()` に頼らず、整数のまま操作するなど、代替手法を検討する


## 4. 関連する関数
- 文字列を整数に変換したい場合は `std::stoi()`, `std::stoll()`, `std::stoull()`
- 基数を指定して整数を文字列に変換したい場合は [基数変換](./base-conversions) または `std::to_chars()`


## 5. 練習問題

> [✅ ABC136B - Uneven Numbers](https://atcoder.jp/contests/abc136/tasks/abc136_b)

:::details コード
```cpp
#include <iostream>
#include <string>

int main()
{
	int N;
	std::cin >> N;

	// 桁数が奇数の整数の個数
	int count = 0;

	// 1 以上 N 以下の整数について
	for (int i = 1; i <= N; ++i)
	{
		// 桁数が奇数の場合
		if ((std::to_string(i).size() % 2) == 1)
		{
			++count;
		}
	}

	std::cout << count << '\n';
}
```
:::

> [✅ ABC192C - Kaprekar Number](https://atcoder.jp/contests/abc192/tasks/abc192_c)

:::details コード
```cpp
#include <iostream>
#include <string>
#include <algorithm>

// 各桁の数字を大きい順に並び替えてできる整数を返す
int G1(int x)
{
	// 整数を文字列に変換する
	std::string s = std::to_string(x);

	// 文字列を降順にソートする
	std::ranges::sort(s, std::greater<>{});

	// 文字列を整数に変換して返す
	return std::stoi(s);
}

// 各桁の数字を小さい順に並び替えてできる整数を返す
int G2(int x)
{
	// 整数を文字列に変換する
	std::string s = std::to_string(x);
	
	// 文字列を昇順にソートする
	std::ranges::sort(s);
	
	// 文字列を整数に変換して返す（先頭の 0 は無視される）
	return std::stoi(s);
}

int F(int x)
{
	return (G1(x) - G2(x));
}

int main()
{
	int N, K;
	std::cin >> N >> K;

	int a = N;

	// N に K 回 F を適用する
	for (int i = 0; i < K; i++)
	{
		a = F(a);
	}

	std::cout << a << '\n';
}
```
:::
