---
title: "<tuple>"
free: true
---

- 1～6: `std::tuple`
- 7: `std::tie()`, `std::apply()`


# 1. `std::tuple` の基本

## 1.1 複数の型の値の組
- `std::tuple` は、複数の型の値の組を表すクラスです
- 関数から複数の戻り値を返したり、複数の値の組の配列を `std::vector` で管理したりするときに、自分で新しく構造体を作る代わりとして使えます
- ただし、2.3 で説明するように、構造体を使うほうが良いケースもあります

```cpp
#include <iostream>
#include <string>
#include <tuple>
#include <vector>

int main()
{
	// int, double, std::string 型の値の組
	std::tuple<int, double, std::string> t1{ 100, 1.1, "aaa" };

	// std::string, int, int 型の値の組
	std::tuple<std::string, int, int> t2{ "bbb", 200, 300 };

	// std::string, int, int 型の値の組の配列
	std::vector<std::tuple<std::string, int, int>> v =
	{
		{ "ccc", 400, 500 },
		{ "ddd", 600, 700 },
	};
}
```


## 1.2 型の個数
- `std::tuple` は 0 個以上の任意の個数の組に対応します
- なお、0, 1 個の場合はわざわざ `std::tuple` を使う必要はなく、2 個の場合は `std::pair` のほうが便利であるため、通常 `std::tuple` は 3 個以上の組を作るときに使います

```cpp
#include <iostream>
#include <string>
#include <tuple>
#include <utility> // std::pair

int main()
{
	// 0 個や 1 個の場合は、あえて std::tuple を使う意味はない
	std::tuple<> t1;	
	std::tuple<int> t2{ 100 };

	// 2 個の場合は std::pair を使ったほうが良い
	//std::tuple<std::string, int> p{ "abc", 100 };
	std::pair<std::string, int> p{ "abc", 100 };
}
```


## 1.3 タプルの組はコンパイル時に決まっていなければならない
- 型の種類や組み合わせはコンパイル時に決まっている必要があります
- 例えば `std::vector` の要素数は、プログラムの実行中に動的に増やしたり減らしたりできますが、`std::tuple` にはそのような仕組みはなく、最初に決めた型の組で固定です

```cpp
#include <iostream>
#include <string>
#include <tuple>
#include <vector>

int main()
{
	std::tuple<int, double, std::string> t{ 100, 1.1, "aaa" };

	// このあと、t に 4 つ目の値として char を追加して t の要素を増やすようなことはできない
}
```


## 1.4 型推論の利用（推奨しない）
- C++ では型推論により、`std::tuple<>` の `<>` の部分を、初期値から自動的に推論してくれます
- ただし、文字列リテラルは `std::string` 型ではなく `const char*` 型になることに注意します
- 型名が明示的に書かれていないと、コードが読みにくくなり、間違いにもつながりやすいため、`std::tuple` での型推論の使用は推奨しません

```cpp
#include <iostream>
#include <string>
#include <tuple>

int main()
{
	// t1 は std::tuple<int, bool, double>
	std::tuple t1{ 100, true, 2.5 };

	// t2 は std::tuple<long long, double, const char*>
	// 文字列リテラルは std::string 型ではないことに注意
	std::tuple t2{ 200LL, 2.2, "bbb" };

	// t3 は std::tuple<int, char, std::string>
	std::tuple t3{ 5, 'a', std::string("hello") };
}
```


# 2. `std::tuple` の要素へのアクセス

## 2.1 インデックスを指定して要素へアクセスする
- `std::tuple` の要素へは、コンパイル時に決まっている整数インデックス `I` を用いて `std::get<I>()` でアクセスできます
- このインデックスは 0 から始まり、`std::tuple<...>` の `<>` 内の型の順序に対応します

```cpp
#include <iostream>
#include <string>
#include <tuple>

int main()
{
	std::tuple<int, double, std::string> t{ 100, 1.1, "aaa" };

	std::cout << std::get<0>(t) << '\n'; // 100

	std::cout << std::get<1>(t) << '\n'; // 1.1

	std::cout << std::get<2>(t) << '\n'; // aaa

	std::get<1>(t) *= std::get<0>(t);

	std::cout << std::get<1>(t) << '\n'; // 110
}
```
```txt:出力
100
1.1
aaa
110
```

- `std::get<I>()` の引数 `I` は、コンパイル時定数である必要があります

```cpp
#include <iostream>
#include <string>
#include <tuple>

int main()
{
	std::tuple<int, double, std::string> t{ 100, 1.1, "aaa" };

	// 整数リテラル 0 はコンパイル時定数
	std::cout << std::get<0> (t) << '\n'; // ok

	constexpr int a = 1; // a はコンパイル時定数
	std::cout << std::get<a>(t) << '\n'; // ok

	int b = 2; // b はコンパイル時定数でない
	std::cout << std::get<b>(t) << '\n'; // コンパイルエラー
}
```

- `std::get<I>()` の引数 `I` が、範囲外のインデックスである場合はコンパイルエラーになります

```cpp
#include <iostream>
#include <string>
#include <tuple>

int main()
{
	std::tuple<int, double, std::string> t{ 100, 1.1, "aaa" };

	std::cout << std::get<5>(t) << '\n'; // コンパイルエラー
}
```

## 2.2 型を指定して要素へアクセスする
- `std::tuple` の要素へは、コンパイル時に決まっている型 `T` を用いて `std::get<T>()` でアクセスできます

```cpp
#include <iostream>
#include <string>
#include <tuple>

int main()
{
	std::tuple<int, double, std::string> t{ 100, 1.1, "aaa" };

	std::cout << std::get<int>(t) << '\n'; // 100

	std::cout << std::get<double>(t) << '\n'; // 1.1

	std::cout << std::get<std::string>(t) << '\n'; // aaa

	std::get<double>(t) *= std::get<int>(t);

	std::cout << std::get<double>(t) << '\n'; // 110
}
```
```txt:出力
100
1.1
aaa
110
```


- `std::get<T>()` の引数 `T` が、要素に存在しない型である場合はコンパイルエラーになります

```cpp
#include <iostream>
#include <string>
#include <tuple>

int main()
{
	std::tuple<int, double, std::string> t{ 100, 1.1, "aaa" };

	std::cout << std::get<long long>(t) << '\n'; // コンパイルエラー
}
```


- `std::tuple` が同じ型の要素を複数持っている場合、その型に対する `std::get<T>()` は 1 つに定まらないため、コンパイルエラーになります

```cpp
#include <iostream>
#include <string>
#include <tuple>

int main()
{
	std::tuple<int, int, std::string> t{ 100, 200, "aaa" };

	std::cout << std::get<int>(t) << '\n'; // コンパイルエラー: どの要素を指すか 1 つに定まらない

	std::cout << std::get<std::string>(t) << '\n'; // OK
}
```

## 2.3 構造体を使ったほうが良いケース
- ほとんどの場合、`std::tuple` を使うよりも、構造体を使ったほうが見通しが良いコードになります
  - (上級者向け) `std::tuple` は、すべての要素が Trivially Copyable であっても、`std::tuple` が Trivially Copyable になることが保証されていないため、Trivially Copyable にできる構造体と比較して最適化の機会が減ります（といっても、競プロの問題では実行時間に現れるほどの差は出ないでしょう）
- 構造体と比較した `std::tuple` の主なメリットは次の 4 つです
  - コードの行数が少ない (`struct { ... };` が不要なため)
  - 型の名前を考えなくてよい
  - 比較演算が自動的に定義される (5.1 参照)
    - (上級者向け) C++20 以降では、構造体に対する比較演算の定義が簡単になっているため、優位性は小さくなります
  - 7\. で紹介する `std::tie()` や `std::apply()` などのユーティリティ関数を使える

```cpp
#include <iostream>
#include <string>
#include <tuple>

struct Vec3
{
	double x, y, z;
};

int main()
{
	std::tuple<double, double, double> v1{ 1.1, 2.2, 3.3 };
	std::cout << std::get<0>(v1) << ", " << std::get<1>(v1) << ", " << std::get<2>(v1) << '\n';
	
	// 構造体のほうが見通しの良いコードになる
	Vec3 v2{ 1.1, 2.2, 3.3 };
	std::cout << v2.x << ", " << v2.y << ", " << v2.z << '\n';
}
```
```txt:出力
1.1, 2.2, 3.3
1.1, 2.2, 3.3
```


# 3. `std::tuple` の基本操作

## 3.1 タプルの初期化
- `std::tuple` に初期値を与えない場合、各要素は値初期化されます (`int` 型の場合は `0`, `double` 型の場合は `0.0`, `std::string` 型の場合は空の文字列)

```cpp
#include <iostream>
#include <string>
#include <tuple>

int main()
{
	// 要素はそれぞれ int(), double(), std::string() で初期化される
	std::tuple<int, double, std::string> t;

	std::cout << std::get<0>(t) << '\n'; // 0

	std::cout << std::get<1>(t) << '\n'; // 0.0

	std::cout << std::get<2>(t) << '\n'; // (空の文字列)
}
```
```txt:出力
0
0

```


## 3.2 タプルへの入力
- `std::tuple` 型の変数を直接 `std::cin` で使うことはできないため、要素ごとに入力を記述する必要があります

```cpp
#include <iostream>
#include <string>
#include <tuple>

int main()
{
	std::tuple<int, double, std::string> t;
	std::cin >> std::get<0>(t) >> std::get<1>(t) >> std::get<2>(t);

	std::cout << std::get<0>(t) << '\n';
	std::cout << std::get<1>(t) << '\n';
	std::cout << std::get<2>(t) << '\n';
}
```
```txt:入力
5 5.5 eee
```
```txt:出力
5
5.5
eee
```


## 3.3 別のタプルを代入する
- `=` を使って別のタプルの値を代入できます
- 代入するタプルは `{ ... }` で構築することもできます

```cpp
#include <iostream>
#include <string>
#include <tuple>

int main()
{
	std::tuple<int, double, std::string> t1{ 100, 1.1, "aaa" };
	std::tuple<int, double, std::string> t2{ 200, 2.2, "bbb" };

	t2 = t1;
	std::cout << std::get<0>(t2) << '\n'; // 100
	std::cout << std::get<1>(t2) << '\n'; // 1.1
	std::cout << std::get<2>(t2) << '\n'; // aaa

	t2 = { 300, 3.3, "ccc" };
	std::cout << std::get<0>(t2) << '\n'; // 300
	std::cout << std::get<1>(t2) << '\n'; // 3.3
	std::cout << std::get<2>(t2) << '\n'; // ccc
}
```
```txt:出力
100
1.1
aaa
300
3.3
ccc
```


- 双方のタプルの要素の個数が同じであり、各要素について `=` を使って代入ができれば、次のような異なる組であっても代入できます（各要素ごとに `=` による代入が行われます）

```cpp
#include <iostream>
#include <string>
#include <tuple>

int main()
{
	std::tuple<int, int, std::string> t1{ 123, 456, "abc" };
	std::tuple<double, double, std::string> t2{ 0.1, 0.2, "def" };
	t2 = t1; // ok

	std::cout << std::get<0>(t2) << '\n'; // 123
	std::cout << std::get<1>(t2) << '\n'; // 456
	std::cout << std::get<2>(t2) << '\n'; // abc
}
```
```txt:出力
123
456
abc
```


## 3.4 関数の戻り値としてタプルを返す
- 複数の値を返す関数を作るときに `std::tuple` が使えます
- 戻り値の型が `std::tuple` である関数は、`return{ ... }` によって `std::tuple` を構築して返すこともできます

```cpp
#include <iostream>
#include <string>
#include <tuple>

std::tuple<int, double, std::string> MakeTuple1()
{
	std::tuple<int, double, std::string> t;
	std::get<0>(t) = 100;
	std::get<1>(t) = 1.1;
	std::get<2>(t) = "aaa";
	return t;
}

std::tuple<int, double, std::string> MakeTuple2()
{
	// より簡潔な記述
	return{ 100, 1.1, "aaa" };
}

int main()
{
	std::tuple<int, double, std::string> t1 = MakeTuple1();
	std::cout << std::get<0>(t1) << '\n'; // 100
	std::cout << std::get<1>(t1) << '\n'; // 1.1
	std::cout << std::get<2>(t1) << '\n'; // aaa

	std::tuple<int, double, std::string> t2 = MakeTuple2();
	std::cout << std::get<0>(t2) << '\n'; // 100
	std::cout << std::get<1>(t2) << '\n'; // 1.1
	std::cout << std::get<2>(t2) << '\n'; // aaa
}
```
```txt:出力
100
1.1
aaa
100
1.1
aaa
```


# 4. 構造化束縛

## 4.1 タプルの要素を分解して別の変数に格納する
- 構造化束縛 (structured binding) という文法を用いると、`std::tuple` の要素を分解して別の変数に格納することができます
- `auto [...] = t;` とすると、`t` の要素を分解し、変数 `...` を作ると同時に、それぞれの値に格納します

```cpp
#include <iostream>
#include <string>
#include <tuple>
#include <vector>

std::tuple<int, double, std::string> MakeTuple()
{
	return{ 100, 1.1, "aaa" };
}

int main()
{
	{
		// 構造化束縛
		// a は int, b は double, c は std::string
		auto [a, b, c] = MakeTuple();
		std::cout << a << '\n'; // 100
		std::cout << b << '\n'; // 1.1
		std::cout << c << '\n'; // aaa
	}

	{
		std::vector<std::tuple<int, double, std::string>> v =
		{
			{ 100, 1.1, "aaa" },
			{ 200, 2.2, "bbb" },
			{ 300, 3.3, "ccc" },
		};

		// 構造化束縛
		// a は int, b は double, c は std::string
		for (const auto& [a, b, c] : v)
		{
			std::cout << a << ' ' << b << ' ' << c << '\n';
		}
	}
}
```
```txt:出力
100
1.1
aaa
100 1.1 aaa
200 2.2 bbb
300 3.3 ccc
```

- （上級者向け）構造化束縛で起こっていることを可視化すると、次のようになります

```cpp
#include <iostream>
#include <string>
#include <tuple>
#include <vector>

std::tuple<int, double, std::string> MakeTuple()
{
	return{ 100, 2.2, "aaa" };
}

int main()
{
	{
		auto [a, b, c] = MakeTuple();
		/*
		std::tuple<int, double, std::string> __T = MakeTuple(); // __T はプログラマには見えない変数
		int&& a = std::get<0>(static_cast<std::tuple<int, double, std::string>&&>(__T));
		double&& b = std::get<1>(static_cast<std::tuple<int, double, std::string>&&>(__T));
		std::string&& c = std::get<2>(static_cast<std::tuple<int, double, std::string>&&>(__T));
		*/
	}

	{
		const auto [a, b, c] = MakeTuple();
		/*
		const std::tuple<int, double, std::string> __T = MakeTuple(); // __T はプログラマには見えない変数
		const int&& a = std::get<0>(const static_cast<std::tuple<int, double, std::string>&&>(__T));
		const double&& b = std::get<1>(const static_cast<std::tuple<int, double, std::string>&&>(__T));
		const std::string&& c = std::get<2>(const static_cast<std::tuple<int, double, std::string>&&>(__T));
		*/
	}

	{
		const auto& [a, b, c] = MakeTuple();
		/*
		const std::tuple<int, double, std::string>& __T = MakeTuple(); // __T はプログラマには見えない変数
		const int& a = std::get<0>(__T);
		const double& b = std::get<1>(__T);
		const std::string& c = std::get<2>(__T);
		*/
	}

	std::vector<std::tuple<int, double, std::string>> v =
	{
		{ 100, 1.1, "aaa" },
		{ 200, 2.2, "bbb" },
		{ 300, 3.3, "ccc" },
	};

	{
		for (auto [a, b, c] : v); // std::string のコピーコストが発生するため好ましくない
		/*
		std::tuple<int, double, std::string> __T = *it; // __T はプログラマには見えない変数, it は v のイテレータ
		int&& a = std::get<0>(static_cast<std::tuple<int, double, std::string>&&>(__T));
		double&& b = std::get<1>(static_cast<std::tuple<int, double, std::string>&&>(__T));
		std::string&& c = std::get<2>(static_cast<std::tuple<int, double, std::string>&&>(__T));
		*/	
	}

	{
		for (const auto [a, b, c] : v); // std::string のコピーコストが発生するため好ましくない
		/*
		const std::tuple<int, double, std::string> __T = *it; // __T はプログラマには見えない変数, it は v のイテレータ
		const int&& a = std::get<0>(const static_cast<std::tuple<int, double, std::string>&&>(__T));
		const double&& b = std::get<1>(const static_cast<std::tuple<int, double, std::string>&&>(__T));
		const std::string&& c = std::get<2>(const static_cast<std::tuple<int, double, std::string>&&>(__T));
		*/	
	}

	{
		for (auto& [a, b, c] : v);
		/*
		std::tuple<int, double, std::string>& __T = *it; // __T はプログラマには見えない変数, it は v のイテレータ
		int& a = std::get<0>(__T);
		double& b = std::get<1>(__T);
		std::string& c = std::get<2>(__T);
		*/	
	}

	{
		for (const auto& [a, b, c] : v);
		/*
		const std::tuple<int, double, std::string>& __T = *it; // __T はプログラマには見えない変数, it は v のイテレータ
		const int& a = std::get<0>(__T);
		const double& b = std::get<1>(__T);
		const std::string& c = std::get<2>(__T);
		*/	
	}
}
```

## 4.2 構造化束縛の制約
- すでにある変数を使って構造化束縛を行うことはできません
  - すでにある変数に代入する場合は、7.1 で説明する `std::tie()` が使えます
- `std::tuple` の一部の要素に対してのみ構造化束縛を行うことはできません

```cpp
#include <iostream>
#include <string>
#include <tuple>

std::tuple<int, double, std::string> MakeTuple()
{
	return{ 100, 1.1, "aaa" };
}

int main()
{
	{
		int a;
		double b;
		std::string c;
		[a, b, c] = MakeTuple(); // コンパイルエラー: すでにある変数への構造化束縛はできない
	}

	{
		auto [a, b] = MakeTuple(); // コンパイルエラー: 一部の要素に対してのみ構造化束縛を行うことはできない
	}
}
```


# 5. `std::tuple` の比較演算

## 5.1 タプルの比較
- `std::tuple` を構成する要素すべてが「比較可能な型」(`int` や `double`, `std::string` など) であれば、その `std::tuple` は比較演算子を使って比較可能になります
- 比較のルールは「先頭の要素から比較され、等しい場合は次の値で比較される」です
  - ピンと来ない場合は、 `std::tuple<int, int, int>{ 100, 300, 500 }` と `std::tuple<int, int, int>{ 200, 1, 2 }` の大小を比較するとき、**「100 ページ目の 300 行目の 500 文字目」と「200 ページ目の 1 行目の 2 文字目」のどちらが本の最初のほうにあるか**と考えるとイメージしやすいです   　

![](/images/standard-cpp-for-competitive-programming/tuple/comparison.png)

```cpp
#include <iostream>
#include <string>
#include <tuple>

int main()
{
	std::tuple<int, double, std::string> t1{ 100, 5.5, "aaa" };
	std::tuple<int, double, std::string> t2{ 100, 5.5, "bbb" };
	std::tuple<int, double, std::string> t3{ 50, 999.9, "ccc" };

	std::cout << std::boolalpha;
	std::cout << (t1 < t2) << '\n'; // true
	std::cout << (t1 != t2) << '\n'; // true

	std::cout << (t2 < t3) << '\n'; // false
	std::cout << (t2 == t3) << '\n'; // false
}
```
```txt:出力
true
true
false
false
```


- 比較可能なのでソートできます

```cpp
#include <iostream>
#include <string>
#include <tuple>
#include <vector>
#include <algorithm> // std::sort()

int main()
{
	std::vector<std::tuple<std::string, int, int>> v =
	{
		{ "ccc", 400, 500 },
		{ "ddd", 600, 700 },
		{ "aaa", 700, 100 },
		{ "aaa", 500, 700 },
	};

	std::sort(v.begin(), v.end());

	for (const auto& [a, b, c] : v)
	{
		std::cout << a << ' ' << b << ' ' << c << '\n';
	}
}
```
```txt:出力
aaa 500 700
aaa 700 100
ccc 400 500
ddd 600 700
```



# 6. emplacement

## 6.1 要素がタプルであるコンテナへの追加
- (上級者向け) 要素が `std::tuple` 型であるコンテナについて、`std::tuple` の個別の構成要素から新しい `std::tuple` を構築してコンテナに追加する場合は、emplacement を使うと効率的です

```cpp
#include <iostream>
#include <string>
#include <tuple>
#include <vector>

int main()
{
	std::vector<std::tuple<std::string, int, int>> v;

	v.push_back({ "aaa", 100, 200 });

	// emplacement のほうがオーバーヘッドが小さい
	v.emplace_back("aaa", 100, 200);
}
```


# 7. その他の機能

## 7.1 すでにある変数にタプルをまとめて代入する
- `std::tie()` を使うと、すでにある変数への参照を持つ `std::tuple` を作ることができます
- これを利用することで、`std::tuple` の要素を、すでにあるそれぞれの変数に代入できます

```cpp
#include <iostream>
#include <string>
#include <tuple>

std::tuple<int, double, std::string> MakeTuple()
{
	return{ 100, 1.1, "aaa" };
}

int main()
{
	int a = 123;
	double b = 45.6;
	std::string c = "abc";

	// std::tie() によって std::tuple<int&, double&, std::string&> が作られるため,
	// 別のタプルを代入可能に
	std::tie(a, b, c) = MakeTuple();
	std::cout << a << ' ' << b << ' ' << c << '\n';
}
```
```txt:出力
100 1.1 aaa
```


## 7.2 `std::tie()` で一部の要素を無視する
- 7.1 の操作をする際、`std::tie()` に `std::ignore` を渡すことで、その要素をスキップできます

```cpp
#include <iostream>
#include <string>
#include <tuple>

std::tuple<int, double, std::string> MakeTuple()
{
	return{ 100, 1.1, "aaa" };
}

int main()
{
	double b = 45.6;
	std::string c = "abc";

	// b と c だけに代入する
	std::tie(std::ignore, b, c) = MakeTuple();
	std::cout << b << ' ' << c << '\n';
}
```
```txt:出力
1.1 aaa
```


## 7.3 `std::tie()` を使って、構造体を比較できるようにする
- 7.1 の応用として、`std::tie()` を使って `std::tuple` を作ることで、構造体の比較を簡単に記述できます
  - (上級者向け) C++20 以降では、デフォルト比較を用いて構造体に比較演算を定義するほうが良いです
- `std::tie()` で作られる `std::tuple` の要素は参照であるため、コピーのコストは発生しません

```cpp
#include <iostream>
#include <string>
#include <tuple>

struct Person
{
	std::string name;
	int age;

	bool operator <(const Person& other) const
	{
		// tuple の比較を利用する
		return (std::tie(name, age) < std::tie(other.name, other.age));
	}

	bool operator ==(const Person& other) const
	{
		// tuple の比較を利用する
		return (std::tie(name, age) == std::tie(other.name, other.age));
	}
};

int main()
{
	Person p1{ "aaa", 16 };
	Person p2{ "bbb", 22 };

	std::cout << std::boolalpha;
	std::cout << (p1 < p2) << '\n'; // true
	std::cout << (p1 == p2) << '\n'; // false
}
```
```txt:出力
true
false
```


## 7.4 タプルの要素を引数にあてはめて関数を呼び出す
- `std::apply(f, t)` を使うと、タプル `t` の要素をそれぞれの引数にあてはめて関数 `f()` を呼び出すことができます

```cpp
#include <iostream>
#include <string>
#include <tuple>

void Print(int a, double b, const std::string& c)
{
	std::cout << a << ' ' << b << ' ' << c << '\n';
}

int main()
{
	std::tuple<int, double, std::string> t{ 100, 1.1, "aaa" };

	// タプルの要素をそれぞれの引数にあてはめて Print() を呼び出す
	std::apply(Print, t);
}
```
```txt:出力
100 1.1 aaa
```

