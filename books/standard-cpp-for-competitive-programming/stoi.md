---
title: "std::stoi(), std::stoll(), std::stoull()"
free: true
---

## 1. 概要
- 読み方: エストゥーアイ, エストゥーエルエル, エストゥーユーエルエル
- `<string>` ヘッダに含まれる
- （十進数をはじめとする）文字列を整数に変換する
- 「文字列を整数に変換したい」「N 進数の文字列を整数に変換したい」という場面で活躍

#### (1) `std::stoi(s)`
- 十進数文字列 `s` を `int` に変換する
- 戻り値: `int`

#### (2) `std::stoll(s)`
- 十進数文字列 `s` を `long long` に変換する
- 戻り値: `long long`

#### (3) `std::stoull(s)`
- 十進数文字列 `s` を `unsigned long long` に変換する
- 戻り値: `unsigned long long`

#### (4) `std::stoi(s, nullptr, base)`
- 基数 `base` の文字列 `s` を `int` に変換する
- 戻り値: `int`
- `base` は 2 から 36 までの整数

#### (5) `std::stoll(s, nullptr, base)`
- 基数 `base` の文字列 `s` を `long long` に変換する
- 戻り値: `long long`
- `base` は 2 から 36 までの整数

#### (6) `std::stoull(s, nullptr, base)`
- 基数 `base` の文字列 `s` を `unsigned long long` に変換する
- 戻り値: `unsigned long long`
- `base` は 2 から 36 までの整数

#### 備考
- (1)～(6) は、いずれも先頭に続く空白文字、先頭に続く `0` を無視する
- (4)～(6) で基数が 11 以上のとき（アルファベットが登場するとき）、アルファベットの小文字・大文字は問わない


## 2. 使い方

### 2.1 文字列を整数に変換する
- 十進数の文字列を整数に変換する
	- "123" -> 123

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "123";

	// 文字列を整数に変換する
	int n = std::stoi(s);

	std::cout << n << '\n'; // 123

	// 先頭に続く 0 は無視される
	std::cout << std::stoi("000456") << '\n'; // 456
}
```

### 2.2 二進数を整数に変換する
- 二進数の文字列を整数に変換する
	- "1110" -> 14

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "1110";

	// 二進数を整数に変換する
	int n = std::stoi(s, nullptr, 2);

	std::cout << n << '\n'; // 14
}
```

### 2.3 十六進数を整数に変換する
- 十六進数の文字列を整数に変換する
	- "1A" -> 26

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "1A";

	// 十六進数を整数に変換する
	int n = std::stoi(s, nullptr, 16);

	std::cout << n << '\n'; // 26
}
```

### 2.4 三十六進数を整数に変換する
- 三十六進数の文字列を整数に変換する
	- "Z0" -> 1260

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "Z0";

	// 三十六進数を整数に変換する
	int n = std::stoi(s, nullptr, 36);

	std::cout << n << '\n'; // 1260
}
```


## 3. 使用上の注意
- 入力が空の文字列や、数値として解釈できない文字列の場合、例外 `std::invalid_argument` を投げる
- 変換後の整数が、その型（`int` や `long long` など）の表現範囲を超える場合、例外 `std::out_of_range` を投げる


## 4. 関連する関数
- 整数を文字列に変換したい場合は `std::to_string()`
- 基数を指定して整数を文字列に変換したい場合は `std::to_chars()`


## 5. 練習問題

> [✅ ABC350A - Past ABCs](https://atcoder.jp/contests/abc350/tasks/abc350_a)

:::details コード
```cpp
#include <iostream>
#include <string>

int main()
{
	// 先頭 3 文字が "ABC", 末尾 3 文字が数字である文字列
	std::string S;
	std::cin >> S;

	// S の 最初の 3 文字を除いた文字列
	// "ABC350" なら "350"
	const std::string number = S.substr(3);

	// 文字列 number を整数に変換する
	const int n = std::stoi(number);

	// n が 1 以上 349 以下であり, かつ 316 でないなら YES
	if ((1 <= n) && (n <= 349) && (n != 316))
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


> [✅ ABC220B - Base K](https://atcoder.jp/contests/abc220/tasks/abc220_b)

:::details コード
```cpp
#include <iostream>
#include <string>

int main()
{
	// K 進法
	int K;
	std::cin >> K;

	// K 進法で表された整数 A, B
	std::string A, B;
	std::cin >> A >> B;

	// (a * b) がオーバーフローしないよう, 型は long long を使用
	const long long a = std::stoi(A, nullptr, K);
	const long long b = std::stoi(B, nullptr, K);

	std::cout << (a * b) << '\n';
}
```
:::


> [✅ 競プロ典型 90 問 067 - Base 8 to 9（★2）](https://atcoder.jp/contests/typical90/tasks/typical90_bo)

:::details コード
```cpp
#include <iostream>
#include <string>
#include <algorithm> 

// 整数 n を 9 進法に変換する
std::string ToBase9(long long n)
{
	if (n == 0)
	{
		return "0";
	}

	std::string s;

	while (n)
	{
		const char ch = ('0' + (n % 9));
		s.push_back(ch);
		n /= 9;
	}

	// 下位桁から .push_back() していたので反転する
	std::ranges::reverse(s);

	return s;
}

int main()
{
	// 8 進法の整数 N（文字列）
	std::string N;
	std::cin >> N;

	// K 回の操作
	int K;
	std::cin >> K;

	for (int i = 0; i < K; ++i)
	{
		// 8 進法の文字列 N を 10 進法に変換し, 9 進法に変換する
		N = ToBase9(std::stoll(N, nullptr, 8));

		// 文字列中の '8' を '5' に置き換える
		std::ranges::replace(N, '8', '5');
	}

	std::cout << N << '\n';
}
```
:::
