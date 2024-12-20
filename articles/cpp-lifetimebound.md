---
title: "C++ で [[lifetimebound]] 属性を用いてダングリング参照の発生リスクを軽減する"
emoji: "⌛"
type: "tech"
topics: ["cpp"]
published: true
---

> [C++ Advent Calendar 2024](https://qiita.com/advent-calendar/2024/cxx), 8 日目の記事です。

- [English](https://zenn.dev/reputeless/articles/cpp-lifetimebound-en)

## ポイント
- 近年の C++ コンパイラでは、ダングリング参照（ライフタイムが終了したオブジェクトへの参照）の検出が強化されつつある。
- 一部のコンパイラでは、コンパイラ拡張 `[[lifetimebound]]` 属性を用いることで、特定のケースにおけるダングリング参照をコンパイル時に検出できる。
- この機能によってすべてのダングリング参照を防げるわけではないが、ライブラリ作者が `[[lifetimebound]]` を適切な関数やコンストラクタに付与することで、ユーザコードにおけるダングリング参照の発生リスクを軽減できる。

## 1. 概要
GCC 13 以降では、新たに導入された `-Wdangling-reference` 警告によって、一時オブジェクトのライフタイムに関連するダングリング参照の問題を検出できるようになりました。

- [GCC Add warnings for common dangling problems](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=106393)

また、Visual Studio 2022（17.7 以降）や Clang 7 以降ではコンパイラ拡張として **[[lifetimebound]]** 属性が利用可能になりました。これを使うと、関数の呼び出し後もオブジェクトの有効性が期待されるようなケースでの一時オブジェクトの使用が検査され、警告を出せます。

- [Warning C26815 | Microsoft Learn](https://learn.microsoft.com/en-us/cpp/code-quality/c26815?view=msvc-170)
- [LLVM: [P0936R0] add [[clang::lifetimebound]] attribute](https://reviews.llvm.org/D49922)

次のコードでは、そうした GCC の新しい警告や `[[lifetimebound]]` 属性の使用によって、一時オブジェクト由来のダングリング参照の問題がコンパイラによって警告されます。

```cpp
#include <iostream>
#include <string>

#if defined(_MSC_VER) // MSVC
	#define LIFETIMEBOUND [[msvc::lifetimebound]]
	#include <CppCoreCheck/Warnings.h>
	#pragma warning(default: CPPCORECHECK_LIFETIME_WARNINGS) // lifetimebound 関連の警告の有効化
#elif defined(__clang__) // Clang
	#define LIFETIMEBOUND [[clang::lifetimebound]]
#else
	#define LIFETIMEBOUND
#endif

const std::string& GetOption(const std::string& userOption LIFETIMEBOUND, const std::string& defaultOption LIFETIMEBOUND) // ℹ️ 引数に lifetimebound 属性を付与
{
	if (userOption.empty())
	{
		return defaultOption;
	}

	return userOption;
}

std::string MakeDefaultOption()
{
	return "The quick brown fox jumps over the lazy dog.";
}

int main()
{
	const std::string emptyOption = "";
	const std::string userOption = "The quick brown fox jumps over the lazy dog.";
	const std::string defaultOption = MakeDefaultOption();

	const std::string& s1 = GetOption(userOption, defaultOption);
	// ✅ OK
	
	const std::string& s2 = GetOption("The quick brown fox jumps over the lazy dog.", defaultOption);
	// ⚠️ 警告
	
	const std::string& s3 = GetOption(emptyOption, MakeDefaultOption());
	// ⚠️ 警告

	std::cout << s1 << '\n';
	std::cout << s2 << '\n';
	std::cout << s3 << '\n';
}
```

GCC での警告例:
```sh
prog.cc: In function 'int main()':
prog.cc:38:28: warning: possibly dangling reference to a temporary [-Wdangling-reference]
   38 |         const std::string& s2 = GetOption("The quick brown fox jumps over the lazy dog.", defaultOption);
      |                            ^~
prog.cc:38:43: note: 'const std::string' {aka 'const std::__cxx11::basic_string<char>'} temporary created here
   38 |         const std::string& s2 = GetOption("The quick brown fox jumps over the lazy dog.", defaultOption);
      |                                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
prog.cc:41:28: warning: possibly dangling reference to a temporary [-Wdangling-reference]
   41 |         const std::string& s3 = GetOption(emptyOption, MakeDefaultOption());
      |                            ^~
prog.cc:41:73: note: 'std::string' {aka 'std::__cxx11::basic_string<char>'} temporary created here
   41 |         const std::string& s3 = GetOption(emptyOption, MakeDefaultOption());
      |                                                        ~~~~~~~~~~~~~~~~~^~
```

Clang での警告例:
```sh
prog.cc:38:36: warning: temporary bound to local reference 's2' will be destroyed at the end of the full-expression [-Wdangling]
   38 |         const std::string& s2 = GetOption("The quick brown fox jumps over the lazy dog.", defaultOption);
      |                                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
prog.cc:41:49: warning: temporary bound to local reference 's3' will be destroyed at the end of the full-expression [-Wdangling]
   41 |         const std::string& s3 = GetOption(emptyOption, MakeDefaultOption());
      |                                                        ^~~~~~~~~~~~~~~~~~~
```

Visual Studio での Code Analysis による警告例:
```
1>Main.cpp
C:\***\Main.cpp(38): warning C26815: このポインターは、破棄された一時インスタンスを指しているため、ダングリング状態です。
C:\***\Main.cpp(41): warning C26815: このポインターは、破棄された一時インスタンスを指しているため、ダングリング状態です。
```


## 2. 背景
C++ では、コピーを回避する効率的なコードを書くために参照やポインタが使われます。しかし、ローカル変数や一時オブジェクトへの参照を取得することは、オブジェクトが破棄されたあともそれらを参照し続けるダングリング参照の問題につながります。

### 2.1 ローカル変数の参照を返してしまう事例
次の例では、関数 `Concat` が、関数終了とともに破棄されるローカル変数 `result` への参照を返しているため、戻り値はダングリング参照となります。

```cpp
#include <iostream>
#include <string>

const std::string& Concat(const std::string& a, const std::string& b)
{
	const std::string result = (a + b);
	return result; // ローカル変数への参照を返す → ダングリング
}

int main()
{
	const std::string& result = Concat("Cat", "Dog");
	std::cout << result << '\n'; // 未定義動作
}
```

このようなケースは検出が容易で、従来からほとんどのコンパイラが警告を発します。


### 2.2 `std::minmax` での事例
もう少し複雑な例が、標準ライブラリ関数 `std::minmax` の誤用です。`std::minmax` の戻り値は `std::pair<const T&, const T&>` 型で、最小値と最大値への参照を保持します。この挙動が、特定の状況でダングリング参照を引き起こします。次のコードがその例です。

```cpp
#include <iostream>
#include <algorithm>
#include <string>

std::string GetCat()
{
	return "cat cat cat cat cat cat cat cat";
}

std::string GetDog()
{
	return "dog dog dog dog dog dog dog dog";
}

int main()
{
	// 一時オブジェクトへの参照を返すため NG
	auto result = std::minmax(GetCat(), GetDog());
	std::cout << "Min: " << result.first << ", Max: " << result.second << '\n'; // 未定義動作
}
```

`GetCat()` と `GetDog()` が返す一時オブジェクトは、`std::minmax` の呼び出し後すぐにライフタイムが終わります。すると、以降の `result.first` や `result.second` はダングリング参照となり、アクセスすると未定義動作を引き起こします。

以前のコンパイラは、このような `std::minmax` の誤用を検出できませんでしたが、新しい警告や `std::minmax` への `[[lifetimebound]]` 属性の適用によって、こうしたケースで警告を発生させられるようになりました。

- [Clang 16 での結果](https://wandbox.org/permlink/5ntl9vYpObzTByOj) → [Clang 17 での結果](https://wandbox.org/permlink/TDgPdXtwxQUlZDVO)
- [libc++ における std::minmax 関数の引数への lifetimebound 属性の使用](https://github.com/llvm/llvm-project/blob/6b1c357acc312961743bef05f99120e7c68b2e25/libcxx/include/__cxx03/__algorithm/minmax.h#L28)
- [MSVC STL における std::minmax 関数の引数への lifetimebound 属性の使用](https://github.com/microsoft/STL/commit/7c7cc0c13dd75957b2d23952cb9b99a17193004b)

なお、この問題を避けるには、一時オブジェクトではなく、ライフタイムが十分に長い変数を用います。

```cpp
#include <iostream>
#include <algorithm>
#include <string>

std::string GetCat()
{
	return "cat cat cat cat cat cat cat cat";
}

std::string GetDog()
{
	return "dog dog dog dog dog dog dog dog";
}

int main()
{
	// OK: 戻り値を変数で受け取り、ライフタイムを延ばす
	std::string a = GetCat(), b = GetDog();
	auto result = std::minmax(a, b);
	std::cout << "Min: " << result.first << ", Max: " << result.second << '\n'; // OK
}
```


## 3. `[[lifetimebound]]` 属性の使い方と効果
Visual Studio 2022（17.7 以降）や Clang 7 以降では、`[[lifetimebound]]` 属性を、関数の引数や戻り値、メンバ関数、コンストラクタ引数に付与することで、次のような注意をコンパイラに伝えることができます。

- ① メンバ関数に付けた場合:
	- このメンバ関数の戻り値は、このオブジェクトを参照する。
- ② 関数の引数に付けた場合:
	- この関数の戻り値は、この引数のオブジェクトを参照する。
- ③ コンストラクタ引数に付けた場合:
	- ここで構築するオブジェクトは、この引数のオブジェクトを参照する。

そして、参照されたオブジェクトが一時変数で、参照する側の変数よりも先に破棄される場合、コンパイラは警告を発生させます。

### 3.1 メンバ関数に付ける

```cpp
#include <iostream>
#include <string>

#if defined(_MSC_VER) // MSVC
	#define LIFETIMEBOUND [[msvc::lifetimebound]]
	#include <CppCoreCheck/Warnings.h>
	#pragma warning(default: CPPCORECHECK_LIFETIME_WARNINGS) // lifetimebound 関連の警告の有効化
#elif defined(__clang__) // Clang
	#define LIFETIMEBOUND [[clang::lifetimebound]]
#else
	#define LIFETIMEBOUND
#endif

struct Holder
{
	std::string value;

	const std::string& getValue() const LIFETIMEBOUND
	{
		return value;
	}
};

int main()
{
	{
		Holder holder;
		holder.value = "The quick brown fox jumps over the lazy dog.";
		const std::string& value = holder.getValue();
		// ✅ OK
		std::cout << value << '\n';
	}

	{
		const std::string& value = Holder{ "The quick brown fox jumps over the lazy dog." }.getValue();
		// ⚠️ 警告
		std::cout << value << '\n';
	}
}
```


### 3.2 関数の引数に付ける

```cpp
#include <iostream>
#include <string>

#if defined(_MSC_VER) // MSVC
	#define LIFETIMEBOUND [[msvc::lifetimebound]]
	#include <CppCoreCheck/Warnings.h>
	#pragma warning(default: CPPCORECHECK_LIFETIME_WARNINGS) // lifetimebound 関連の警告の有効化
#elif defined(__clang__) // Clang
	#define LIFETIMEBOUND [[clang::lifetimebound]]
#else
	#define LIFETIMEBOUND
#endif

const std::string& GetOption(const std::string& userOption LIFETIMEBOUND, const std::string& defaultOption LIFETIMEBOUND)
{
	if (userOption.empty())
	{
		return defaultOption;
	}

	return userOption;
}

std::string MakeDefaultOption()
{
	return "The quick brown fox jumps over the lazy dog.";
}

int main()
{
	const std::string emptyOption = "";
	const std::string userOption = "The quick brown fox jumps over the lazy dog.";
	const std::string defaultOption = MakeDefaultOption();

	const std::string& s1 = GetOption(userOption, defaultOption);
	// ✅ OK
	
	const std::string& s2 = GetOption("The quick brown fox jumps over the lazy dog.", defaultOption);
	// ⚠️ 警告
	
	const std::string& s3 = GetOption(emptyOption, MakeDefaultOption());
	// ⚠️ 警告

	std::cout << s1 << '\n';
	std::cout << s2 << '\n';
	std::cout << s3 << '\n';
}
```


### 3.3 コンストラクタ引数に付ける
GCC の `-Wdangling-reference` はこのケースをカバーしませんが、Visual Studio や Clang の `[[lifetimebound]]` では警告が発生します。

```cpp
#include <iostream>
#include <string>

#if defined(_MSC_VER) // MSVC
	#define LIFETIMEBOUND [[msvc::lifetimebound]]
	#include <CppCoreCheck/Warnings.h>
	#pragma warning(default: CPPCORECHECK_LIFETIME_WARNINGS) // lifetimebound 関連の警告の有効化
#elif defined(__clang__) // Clang
	#define LIFETIMEBOUND [[clang::lifetimebound]]
#else
	#define LIFETIMEBOUND
#endif

struct StringPiece
{
public:

	StringPiece() = default;
	StringPiece(const std::string& s LIFETIMEBOUND)
		: data{ s.data() }
		, size{ s.size() } {}

	const char* data = nullptr;
	size_t size = 0;
};

std::string MakeString()
{
	return "The quick brown fox jumps over the lazy dog.";
}

int main()
{
	{
		const std::string s = MakeString();
		const StringPiece sp{ s };
		// ✅ OK
		std::cout << std::string_view{ sp.data, sp.size } << '\n';
	}

	{
		const StringPiece sp{ MakeString() };
		// ⚠️ 警告
		std::cout << std::string_view{ sp.data, sp.size } << '\n';
	}
}
```

### 3.4 `[[lifetimebound]]` 属性が役に立たないケース
`[[lifetimebound]]` 属性は、一時オブジェクトへの参照など、とくに検出が容易なケースのみに有効です。次のコードのように、複雑なケースやコンテナ操作による間接的な参照無効化などでは役に立たないという点に注意が必要です。

```cpp
int main()
{
	{
		std::vector<int> v = { 200, 100 };
		auto result = std::minmax(v[0], v[1]);
		v.resize(1000); // ここで参照先が無効化される
		std::cout << result.first << ' ' << result.second << '\n';
	}

	{
		StringPiece sp;
		{
			std::string s = MakeString();
			sp = StringPiece{ s };
		} // ここで s が破棄される

		std::cout << std::string_view{ sp.data, sp.size } << '\n';
	}
}
```

### 3.5 実際の使用例
`[[lifetimebound]]` 属性は、次のような有名ライブラリのソースコードで既に導入されています。

- [MSVC STL](https://github.com/microsoft/STL): `std::min`, `std::max`, `std::minmax`, `std::clamp` など
- [libc++](https://github.com/llvm/llvm-project): `std::min`, `std::max`, `std::minmax`, `std::clamp`, `std::move`, `std::forward`, `std::forward_like` など
- [Abseil](https://github.com/abseil/abseil-cpp): `absl::optional`, `absl::FixedArray`, `absl::Span` の各種メンバ関数など


## 4. まとめ
GCC の新しい警告や、Visual Studio, Clang の `[[lifetimebound]]` 属性を用いることで、一時オブジェクトに由来するダングリング参照のうち、いくつかのケースをコンパイル時に検出できるようになりました。

これだけですべてのダングリングを防げるわけではなく、問題の洗い出しのためには、より強力な静的解析ツールやサニタイザが必要になります。それでも、コンパイラが少しでも多くの情報を提供できるようになるのは、開発者にとって大きな助けとなります。

ライブラリ作者にとっても、`[[lifetimebound]]` を適切な関数やコンストラクタに付与することで、ユーザコードにおけるダングリングの発生リスクを軽減できる利点があります。


## 5. 周辺の話題
現時点で `[[lifetimebound]]` を C++ 標準に追加するアクティブな提案はありませんが、次のような動きがあります。

- 引数の依存関係を記述して、コンパイラやツールによるダングリングの検出を支援する `[[parameter_dependency]]` 属性の追加が提案されましたが、委員会では否決されました。
	- [P2742 indirect dangling identification](https://github.com/cplusplus/papers/issues/1435)
- Clang では、より幅広いケースでダングリング参照を検出するための拡張が研究されています。
    - [LLVM: [RFC] Lifetime annotations for C++](https://discourse.llvm.org/t/rfc-lifetime-annotations-for-c/61377)
- 借用チェック（borrow checking）を用いて、より包括的なライフタイム安全性の提供を目指す提言も発表されています。
    - [D3390: Safe C++](https://safecpp.org/draft.html)

実現や実用化には相当な時間がかかると思われますが、C++ のライフタイム安全性の向上に向けた取り組みは、今後も進展していくことが期待されます。
