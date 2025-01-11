---
title: "基数変換"
free: true
---

## 1. 整数を N 進数の文字列に変換する

```cpp
#include <string>
#include <algorithm>
#include <stdexcept>

/// @brief	数値を指定した基数の文字列表現に変換します。
/// @param	n 変換する数値
/// @param	base 基数（2～36）
/// @return	変換後の文字列。0 は "0" を返します。
/// @throw	std::invalid_argument 基数が範囲外の場合
/// @note	変換後の最大桁数は 64 桁（base が 2 の場合）
/// @author	"競プロのための標準 C++"
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


## 2. N 進数文字列を整数に変換する
- [`std::stoi()`](stoi.md) などの関数を使う
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


## 3. N 進数文字列を M 進数文字列に変換する
- N 進数文字列を 10 進数に変換してから、10 進数の値を M 進数文字列に変換する



