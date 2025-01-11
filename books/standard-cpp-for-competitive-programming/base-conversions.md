---
title: "基数変換"
free: true
---

## 1. 実装

### 1.1 整数を N 進数の文字列に変換する

```cpp
#include <string>
#include <algorithm>
#include <stdexcept>

/// @brief 数値を指定した基数の文字列表現に変換します。
/// @param n 変換する数値
/// @param base 基数（2～36）
/// @return 変換後の文字列。0 は "0" を返します。
/// @throw std::invalid_argument 基数が範囲外の場合
/// @note 変換後の最大桁数は 64 桁（base が 2 の場合）
/// @note 出典:『競プロのための標準 C++』
[[nodiscard]]
constexpr std::string ToBaseN(unsigned long long n, const unsigned int base)
{
	if ((base < 2) || (36 < base))
	{
		throw std::invalid_argument{ "base must be in the range [2, 36]." };
	}

	constexpr char Digits[] = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
	std::string s;

	do
	{
		s.push_back(Digits[n % base]);
		n /= base;
	} while (n);

	std::ranges::reverse(s);
	return s;
}
```


### 1.2 N 進数文字列を整数に変換する
- [`std::stoi()`](./stoi) などの関数を使う
	- 2 から 36 までの基数に対応する

```cpp
#include <iostream>
#include <string>

int main()
{
	// 16 進数文字列
	std::string s = "FF00";

	// 文字列を整数に変換する
	int n = std::stoi(s, nullptr, 16);

	std::cout << n << '\n'; // 65280
}
```


### 1.3 N 進数文字列を M 進数文字列に変換する
- N 進数文字列を 10 進数に変換してから、10 進数の値を M 進数文字列に変換する


## 2. 練習問題

### [✅ 第五回 アルゴリズム実技検定 過去問 C - 三十六進法](https://atcoder.jp/contests/past202012-open/tasks/past202012_c)

:::details コード
```cpp
#include <iostream>
#include <string>
#include <algorithm>
#include <stdexcept>

/// @brief 数値を指定した基数の文字列表現に変換します。
/// @param n 変換する数値
/// @param base 基数（2～36）
/// @return 変換後の文字列。0 は "0" を返します。
/// @throw std::invalid_argument 基数が範囲外の場合
/// @note 変換後の最大桁数は 64 桁（base が 2 の場合）
/// @note 出典:『競プロのための標準 C++』
[[nodiscard]]
constexpr std::string ToBaseN(unsigned long long n, const unsigned int base)
{
	if ((base < 2) || (36 < base))
	{
		throw std::invalid_argument{ "base must be in the range [2, 36]." };
	}

	constexpr char Digits[] = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
	std::string s;

	do
	{
		s.push_back(Digits[n % base]);
		n /= base;
	} while (n);

	std::ranges::reverse(s);
	return s;
}

int main()
{
	int N;
	std::cin >> N;

	// 36 進数に変換して出力
	std::cout << ToBaseN(N, 36) << '\n';
}
```
:::


### [✅ ABC220B - Base K](https://atcoder.jp/contests/abc220/tasks/abc220_b)

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


### [✅ 競プロ典型 90 問 067 - Base 8 to 9（★2）](https://atcoder.jp/contests/typical90/tasks/typical90_bo)

:::details コード
```cpp
#include <iostream>
#include <string>
#include <algorithm>
#include <stdexcept>

/// @brief 数値を指定した基数の文字列表現に変換します。
/// @param n 変換する数値
/// @param base 基数（2～36）
/// @return 変換後の文字列。0 は "0" を返します。
/// @throw std::invalid_argument 基数が範囲外の場合
/// @note 変換後の最大桁数は 64 桁（base が 2 の場合）
/// @note 出典『競プロのための標準 C++』
[[nodiscard]]
constexpr std::string ToBaseN(unsigned long long n, const unsigned int base)
{
	if ((base < 2) || (36 < base))
	{
		throw std::invalid_argument{ "base must be in the range [2, 36]." };
	}

	constexpr char Digits[] = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
	std::string s;

	do
	{
		s.push_back(Digits[n % base]);
		n /= base;
	} while (n);

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
		N = ToBaseN(std::stoll(N, nullptr, 8), 9);

		// 文字列中の '8' を '5' に置き換える
		std::ranges::replace(N, '8', '5');
	}

	std::cout << N << '\n';
}
```
:::

