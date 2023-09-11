---
title: "<string> の関数（C++20 版）"
free: true
---

標準ライブラリの `<string>` ヘッダは、`std::string` 以外にも、文字列を操作するためのいくつかの関数を提供しています。


# 1. 数値から `std::string` への変換

## 1.1 数値を `std::string` に変換する
- `std::to_string(n)` は、数 `n` を文字列に変換し、`std::string` 型で返します。
- `float` や `double` など浮動小数点数型については精度の指定はできません。

```cpp
#include <iostream>
#include <string>

int main()
{
	int n = 123;
	double x = 1.23456789;

	std::string s = std::to_string(n);
	std::string t = std::to_string(x);

	std::cout << s << '\n';
	std::cout << t << '\n';
}
```
```txt:出力
123
1.234568
```


# 2. 文字列から数値への変換

## 2.1 数が書かれた文字列をパースして整数に変換する
- `std::stoi(s)` は、文字列 `s` をパースし `int` 型で返します。
- `std::stoull(s)` は、文字列 `s` をパースし `unsigned long long` 型で返します。
- `s` に書かれている数が、変換後の型で表現できる範囲外の場合、`std::out_of_range` 例外が送出されます。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s1 = "1200";
	std::string s2 = "9876543210";

	int n1 = std::stoi(s1); // 文字列をパースして int 型に変換する
	unsigned long long n2 = std::stoull(s2); // 文字列をパースして unsigned long long 型に変換する

	std::cout << n1 << '\n';
	std::cout << n2 << '\n';
}
```
```txt:出力
1200
9876543210
```


## 2.2 数（2 進数）が書かれた文字列をパースして整数に変換する
- `std::stoi(s, idx, base)` は、文字列 `s` を `base` 進数とみなしてパースし `int` 型で返します。`idx` は今回は使わず、`nullptr` にします。
- `std::stoull(s, idx, base)` は、文字列 `s` を `base` 進数とみなしてパースし `unsigned long long` 型で返します。`idx` は今回は使わず、`nullptr` にします。
- 2 進数が書かれた文字列 `s` をパースして整数に変換するには、`base` に `2` を指定します。
- `s` に書かれている数が、変換後の型で表現できる範囲外の場合、`std::out_of_range` 例外が送出されます。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s1 = "0111";
	std::string s2 = "1000";
	// 1 が 64 個
	std::string s3 = "1111111111111111111111111111111111111111111111111111111111111111";

	int n1 = std::stoi(s1, nullptr, 2); // 2 進数としてパースして int 型に変換する
	int n2 = std::stoi(s2, nullptr, 2); // 2 進数としてパースして int 型に変換する
	unsigned long long n3 = std::stoull(s3, nullptr, 2); // 2 進数としてパースして unsigned long long 型に変換する

	std::cout << n1 << '\n';
	std::cout << n2 << '\n';
	std::cout << n3 << '\n';
}
```
```txt:出力
7
8
18446744073709551615
```


## 2.3 数（16 進数）が書かれた文字列をパースして整数に変換する
- 16 進数が書かれた文字列 `s` をパースして整数に変換するには、`base` に `16` を指定します。
- `s` に書かれている数が、変換後の型で表現できる範囲外の場合、`std::out_of_range` 例外が送出されます。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s1 = "7ff";
	std::string s2 = "800";
	// f が 16 個
	std::string s3 = "ffffffffffffffff";

	int n1 = std::stoi(s1, nullptr, 16); // 16 進数としてパースして int 型に変換する
	int n2 = std::stoi(s2, nullptr, 16); // 16 進数としてパースして int 型に変換する
	unsigned long long n3 = std::stoull(s3, nullptr, 16); // 16 進数としてパースして int 型に変換する

	std::cout << n1 << '\n';
	std::cout << n2 << '\n';
	std::cout << n3 << '\n';
}
```
```txt:出力
2047
2048
18446744073709551615
```


## 2.4 数（36 進数）が書かれた文字列をパースして整数に変換する
- 36 進数が書かれた文字列 `s` をパースして整数に変換するには、`base` に `36` を指定します。
- `s` に書かれている数が、変換後の型で表現できる範囲外の場合、`std::out_of_range` 例外が送出されます。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s1 = "zzz";
	std::string s2 = "1000000000";

	int n1 = std::stoi(s1, nullptr, 36); // 36 進数としてパースして int 型に変換する
	unsigned long long n2 = std::stoull(s2, nullptr, 36); // 36 進数としてパースして unsigned long long 型に変換する

	std::cout << n1 << '\n';
	std::cout << n2 << '\n';
}
```
```txt:出力
46655
101559956668416
```