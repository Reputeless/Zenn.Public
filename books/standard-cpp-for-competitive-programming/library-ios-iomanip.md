---
title: "<ios>, <iosmanip>"
free: true
---

- 1～2: マニピュレータ
- 3: `ios::sync_with_stdio()`

`<ios>` ヘッダは `<iostream>` の中でインクルードされるため、明示的にインクルードする必要はありません

# 1. `bool` 型の値の処理形式

## 1.1 `bool` 型の値を `true` と `false` で表示する
- デフォルトの入出力ストリームにおいて、`bool` 型の値は、`false` が `0`, `true` が `1` として扱われます
- `std::boolalpha` マニピュレータは、ストリームに対して、`bool` 型の値を `true` と `false` という文字列で処理するよう設定します
- ストリームに対する設定は、そのストリームの以降のすべての処理に適用されます

```cpp
#include <iostream>

int main()
{
	std::cout << false << '\n'; // 0
	std::cout << true << '\n'; // 1

	std::cout << std::boolalpha; // 標準出力ストリームに boolalpha を設定
	std::cout << false << '\n'; // false
	std::cout << true << '\n'; // true

	std::cout << "---\n";

	bool a, b;
	std::cin >> a >> b;
	std::cout << a << '\n';
	std::cout << b << '\n';

	std::cin >> std::boolalpha; // 標準入力ストリームに boolalpha を設定
	std::cin >> a >> b;
	std::cout << a << '\n';
	std::cout << b << '\n';
}
```
```txt:入力
0 1
true false
```
```txt:出力
0
1
false
true
---
false
true
true
false
```


## 1.2 `bool` 型の値の処理形式をデフォルトに戻す
- `std::noboolalpha` マニピュレータは、ストリームに対して、`bool` 型の値を `0` と `1` という数値で処理するよう設定します（デフォルトの設定に戻します）
- ストリームに対する設定は、そのストリームの以降のすべての処理に適用されます

```cpp
#include <iostream>

int main()
{
	std::cout << std::boolalpha;
	std::cout << false << '\n'; // false
	std::cout << true << '\n'; // true

	std::cout << std::noboolalpha;
	std::cout << false << '\n'; // 0
	std::cout << true << '\n'; // 1
}

```
```txt:出力
false
true
0
1
```


# 2. 浮動小数点数の表示形式

## 2.1 浮動小数点数の表示形式を変更する
- C++ の出力ストリームにおける浮動小数点数の**表示形式**は、主に次の 3 種類が使われます
  - デフォルト表記
  - 固定小数点表記
  - 指数表記
- これらと、2.3 で説明する**表示精度**の組み合わせにより、浮動小数点数の表示方法をカスタマイズできます
- 一般的にコンテストのジャッジシステムは、浮動小数点数を出力する問題について、いずれの表示方法での出力も受け付けます
- 表示形式や表示精度を変更することで、デフォルトの出力では省略されてしまう、`float` 型や `double` 型が本来持っている精度までの計算結果を出力できるようになります

```cpp
#include <iostream>

int main()
{
	const double x = 100.0 / 3.0;
	std::cout << x << '\n'; // デフォルトでは 33.3333 までしか表示されない
}
```
```txt:出力
33.3333
```

- コンテストの問題によっては、浮動小数点数の出力に関して、一定の有効桁数や、小数点数以下の桁数で指定された出力精度を要求するものがあります。その場合、表示形式や表示精度のカスタマイズが役に立ちます
- 表示形式や表示精度の変更によって、`float` 型や `double` 型が本来表現できる以上の計算精度が実現されることはありません


## 2.2 デフォルト表記での表示
- C++ の出力ストリームに浮動小数点数を出力すると、デフォルトでは「デフォルト表記」のルールで表示されます
- デフォルト表記のルールは次の通りです
  - 値を指数表記した際に、指数部分が -5 以下であるか、「表示精度」（デフォルトは 6）以上である場合は指数表記、それ以外の場合は指数を使わない通常の表記
  - 小数部分の末尾の 0 (trailing zeros) は削除する
- `std::defaultfloat` マニピュレータは、ストリームに対して、浮動小数点数の表示形式をデフォルト表記に設定します（デフォルトではこれが設定されています）
- ストリームに対する設定は、そのストリームの以降のすべての処理に適用されます

```cpp
#include <iostream>

int main()
{
	std::cout << 0.0000007 << '\n'; // 指数表記
	std::cout << 0.000006 << '\n'; // 指数表記
	std::cout << 0.00005 << '\n'; // 指数部分が -5 以下なので指数表記
	std::cout << 0.0004 << '\n';
	std::cout << 0.003 << '\n';
	std::cout << 0.02 << '\n';
	std::cout << 0.1 << '\n';
	std::cout << 0.0 << '\n';
	std::cout << 10.0 << '\n';
	std::cout << 200.0 << '\n';
	std::cout << 3000.0 << '\n';
	std::cout << 40000.0 << '\n';
	std::cout << 500000.0 << '\n';
	std::cout << 6000000.0 << '\n'; // 指数部分が 6 以上なので指数表記
	std::cout << 70000000.0 << '\n'; // 指数表記
}
```
```txt:出力
7e-07
6e-06
5e-05
0.0004
0.003
0.02
0.1
0
10
200
3000
40000
500000
6e+06
7e+07
```


## 2.3 浮動小数点数の表示精度を変更する
- `std::setprecision(n)` マニピュレータは、ストリームに対して、浮動小数点数の「表示精度」を `n` に設定します
- 表示精度のデフォルト値は 6 です
- ストリームに対する設定は、そのストリームの以降のすべての処理に適用されます
- デフォルト表記において表示精度を変更するサンプルを次に示します
  - デフォルト表記のルールは 2.2 で説明しています

```cpp
#include <iostream>
#include <iomanip> // std::setprecision

int main()
{
	constexpr double x = 111222.333444555666777888999;
	constexpr double y = 0.000111222333444555666777888999;
	constexpr double z = 0.000000111222333444555666777888999;
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";

	std::cout << std::setprecision(0); // デフォルト表記の場合、表示精度 0 は 1 と見なされる
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(3);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(6);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(9);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(12);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(15);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(18);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
}
```
```txt:出力
111222
0.000111222
1.11222e-07
---
1e+05
0.0001
1e-07
---
1.11e+05
0.000111
1.11e-07
---
111222
0.000111222
1.11222e-07
---
111222.333
0.000111222333
1.11222333e-07
---
111222.333445
0.000111222333445
1.11222333445e-07
---
111222.333444556
0.000111222333444556
1.11222333444556e-07
---
111222.333444555668
0.000111222333444555672
1.11222333444555665e-07
```


## 2.4 固定小数点表記での表示
- `std::fixed` マニピュレータは、ストリームに対して、浮動小数点数の表示形式を固定小数点表記に設定します
- ストリームに対する設定は、そのストリームの以降のすべての処理に適用されます
- 固定小数点表記のルールは次の通りです
  - 整数部分はそのまま表示する
  - 小数点以下については「表示精度」（デフォルトは 6）桁まで表示する
  - 小数部分の末尾の 0 (trailing zeros) は削除しない

```cpp
#include <iostream>

int main()
{
	// 固定小数点表記に
	std::cout << std::fixed;

	std::cout << 0.0000007 << '\n';
	std::cout << 0.000006 << '\n';
	std::cout << 0.00005 << '\n';
	std::cout << 0.0004 << '\n';
	std::cout << 0.003 << '\n';
	std::cout << 0.02 << '\n';
	std::cout << 0.1 << '\n';
	std::cout << 0.0 << '\n';
	std::cout << 10.0 << '\n';
	std::cout << 200.0 << '\n';
	std::cout << 3000.0 << '\n';
	std::cout << 40000.0 << '\n';
	std::cout << 500000.0 << '\n';
	std::cout << 6000000.0 << '\n';
	std::cout << 70000000.0 << '\n';
}
```
```txt:出力
0.000001
0.000006
0.000050
0.000400
0.003000
0.020000
0.100000
0.000000
10.000000
200.000000
3000.000000
40000.000000
500000.000000
6000000.000000
70000000.000000
```

- 固定小数点表記において表示精度を変更するサンプルを次に示します

```cpp
#include <iostream>
#include <iomanip> // std::setprecision

int main()
{
	constexpr double x = 111222.333444555666777888999;
	constexpr double y = 0.000111222333444555666777888999;
	constexpr double z = 0.000000111222333444555666777888999;
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";

	// 固定小数点表記に
	std::cout << std::fixed;

	std::cout << std::setprecision(0);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(3);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(6);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(9);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(12);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(15);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(18);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
}
```
```txt:出力
111222
0.000111222
1.11222e-07
---
111222
0
0
---
111222.333
0.000
0.000
---
111222.333445
0.000111
0.000000
---
111222.333444556
0.000111222
0.000000111
---
111222.333444555668
0.000111222333
0.000000111222
---
111222.333444555668393
0.000111222333445
0.000000111222333
---
111222.333444555668393150
0.000111222333444556
0.000000111222333445
```


## 2.5 指数表記での表示
- `std::scientific` マニピュレータは、ストリームに対して、浮動小数点数の表示形式を指数表記に設定します
- ストリームに対する設定は、そのストリームの以降のすべての処理に適用されます
- 指数表記の場合のルールは次の通りです
  - 常に指数表記を使う
  - 仮数部分は、小数点以下について「表示精度」（デフォルトは 6）桁まで表示する
  - 小数部分の末尾の 0 (trailing zeros) は削除しない

```cpp
#include <iostream>

int main()
{
	// 指数表記に
	std::cout << std::scientific;

	std::cout << 0.0000007 << '\n';
	std::cout << 0.000006 << '\n';
	std::cout << 0.00005 << '\n';
	std::cout << 0.0004 << '\n';
	std::cout << 0.003 << '\n';
	std::cout << 0.02 << '\n';
	std::cout << 0.1 << '\n';
	std::cout << 0.0 << '\n';
	std::cout << 10.0 << '\n';
	std::cout << 200.0 << '\n';
	std::cout << 3000.0 << '\n';
	std::cout << 40000.0 << '\n';
	std::cout << 500000.0 << '\n';
	std::cout << 6000000.0 << '\n';
	std::cout << 70000000.0 << '\n';
}
```
```txt:出力
7.000000e-07
6.000000e-06
5.000000e-05
4.000000e-04
3.000000e-03
2.000000e-02
1.000000e-01
0.000000e+00
1.000000e+01
2.000000e+02
3.000000e+03
4.000000e+04
5.000000e+05
6.000000e+06
7.000000e+07
```

- 指数表記において表示精度を変更するサンプルを次に示します

```cpp
#include <iostream>
#include <iomanip> // std::setprecision

int main()
{
	constexpr double x = 111222.333444555666777888999;
	constexpr double y = 0.000111222333444555666777888999;
	constexpr double z = 0.000000111222333444555666777888999;
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";

	// 指数表記に
	std::cout << std::scientific;

	std::cout << std::setprecision(0);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(3);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(6);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(9);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(12);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(15);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
	std::cout << "---\n";
	std::cout << std::setprecision(18);
	std::cout << x << '\n';
	std::cout << y << '\n';
	std::cout << z << '\n';
}
```
```txt:出力
111222
0.000111222
1.11222e-07
---
1e+05
1e-04
1e-07
---
1.112e+05
1.112e-04
1.112e-07
---
1.112223e+05
1.112223e-04
1.112223e-07
---
1.112223334e+05
1.112223334e-04
1.112223334e-07
---
1.112223334446e+05
1.112223334446e-04
1.112223334446e-07
---
1.112223334445557e+05
1.112223334445557e-04
1.112223334445557e-07
---
1.112223334445556684e+05
1.112223334445556718e-04
1.112223334445556655e-07
```


# 3. C 言語の入出力ストリームとの同期設定

## 3.1 C 言語の入出力ストリームとの同期を無効にする

- `std::ios::sync_with_stdio(false)` は、C++ の入出力ストリームと C 言語の入出力ストリームの同期を無効にします
- デフォルトでは同期が有効です。同期が有効な間は次のような効果が得られます
  - C++ の入出力ストリームと C 言語の入出力ストリームを混在させた場合の順序や内容の正確性の保証
  - 複数スレッドから C++ の入出力ストリームを使用した場合のデータ競合の防止（ただし順序は不定）
- 同期を無効にすることで `std::cin`, `std::cout`, `std::cerr` 等の C++ 入出力ストリームがより高速に実行されるようになり、C++ 入出力ストリームを多用するプログラムの実行時間を短縮できます
- 同期を無効にする副作用は次の通りです
  - C++ 入出力ストリームと C 言語の入出力関数 (`std::snanf()`, `std::printf()`, `std::puts()`) が混在するプログラムにおいて、入出力を行った結果が不正になることがある
  - 複数のスレッドから C++ 入出力ストリームを使用するプログラムにおいて、データ競合が発生することがある
- したがって、C++ の入出力ストリームのみを使用し、複数のスレッドから C++ 入出力ストリームを使用しないプログラムに限って、安全に同期を無効にし、プログラムの実行時間を短縮できます
- プログラムの途中で同期を無効にした場合、その前に行った入出力に対して何が起こるかは実装依存であるため、同期の無効化はプログラムの最初に行うべきです

▼ 安全に同期を無効にできるプログラムの例
```cpp
#include <iostream>
#include <vector>

int main()
{
	// 同期を無効にする
	std::ios::sync_with_stdio(false);

	std::vector<int> v(10);

	for (auto& n : v)
	{
		std::cin >> n;
	}

	for (const auto& n : v)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';
}
```
```txt:入力
1 2 3 4 5 6 7 8 9 10
```
```txt:出力
1 2 3 4 5 6 7 8 9 10
```

▼ 同期の無効化の副作用で不正な結果が発生しうるプログラムの例 1
```cpp
#include <iostream>
#include <cstdio> // printf()

int main()
{
	// 同期を無効にする
	std::ios::sync_with_stdio(false);

	for (int i = 0; i < 10; ++i)
	{
		std::cout << "A";
		printf("B");
	}
	std::cout << '\n';
}
```
```txt:出力例（不正な結果）
AAAAAAAAAA
BBBBBBBBBB
```

▼ 同期の無効化の副作用で不正な結果が発生しうるプログラムの例 2
```cpp
#include <iostream>
#include <vector>
#include <cstdio> // scanf()

// C++ の入力ストリームと C 言語の入力ストリームが混在している
std::vector<int> Test()
{
	std::vector<int> v(10);

	for (size_t i = 0; i < v.size(); ++i)
	{
		if (i % 2 == 0)
		{
			std::cin >> v[i];
		}
		else
		{
			scanf("%d", &v[i]);
		}
	}

	return v;
}

int main()
{
	// 同期を無効にする
	std::ios::sync_with_stdio(false);

	const std::vector<int> v = Test();

	for (const auto& n : v)
	{
		std::cout << n << ' ';
	}
	std::cout << '\n';
}
```
```txt:入力
1 2 3 4 5 6 7 8 9 10
```
```txt:出力例（不正な結果）
0 1 0 2 0 3 0 4 0 5 
```
