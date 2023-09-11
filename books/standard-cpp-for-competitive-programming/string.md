---
title: "std::string（C++20 版）"
free: true
---

`std::string`（ストリング）は C++ で文字列を便利に扱うためのクラスです。標準ライブラリの `<string>` ヘッダに定義されています。

C++ 標準ライブラリの多くの機能が `std::string` と連係するよう設計されているため、特別な理由がない限り、C++ で文字列を操作する際には `std::string` を使うことが推奨されます。


# 1. `std::string` の構造
`std::string` の内部では `char` 型の動的配列が実装されています。ただし、文字列操作のための最適化や、C 言語との互換を保つための処理が行われているため、`std::vecotr<char>` とは少し異なる実装になっています。

## 1.1 `std::string` の内部構造 (1)
C++ 標準ライブラリは、任意の文字型 `Char` に対して、便利な文字列処理を提供するためのクラステンプレート `std::basic_string<Char>` を定義しています。それを `char` 型に対して特殊化（`std::basic_string<char>`）したものが `std::string` です。

```cpp
namespace std
{
	// std::string は std::basic_string<char> の型エイリアス（別名）
	using string = basic_string<char>;
}
```

`std::basic_string` の内部構造を簡略化して説明すると、次のようになります。

```cpp
template <class Char>
struct basic_string
{
	Char* m_ptr; // 確保したメモリの先頭を指すポインタ
	size_t m_size; // 格納している文字列の長さ
	size_t m_allocated_capacity; // 動的確保した配列に格納できる文字列の最大の長さ
};
```

競技プログラミングでは `char` 以外の文字型を使うことはほとんどないため、`Char` を `char` に固定した簡易版で説明を進めます。

```cpp
struct string
{
	char* m_ptr; // 確保したメモリの先頭を指すポインタ
	size_t m_size; // 格納している文字列の長さ
	size_t m_allocated_capacity; // 動的確保した配列に格納できる文字列の最大の長さ
};
```

`m_ptr` は文字列を格納するためにメモリのどこかに動的確保した `char` 型の配列の先頭を指すポインタです。

`m_size` は文字列の長さを示します。後述する NULL 終端文字はこの数には含まれません。`"hello"` という文字列を格納している場合は 5 です。

`m_allocated_capacity` は、現在動的確保している配列に格納できる、最大の文字列の長さを示します。こちらも NULL 終端文字の分は含みません。`m_size <= m_allocated_capacity` が常に成り立ちます。

### 例 1
`"hello"` という文字列を格納する `std::string` の中身の例を図示すると次のようになります。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/zenn/competitive-programming/string/1.png)

確保された一定のサイズの `char` 型の配列の**先頭**から、文字列の長さ（`"hello"` の場合は 5）分を消費して、文字列の各要素を、**連続して**格納します。文字列を格納し終わった直後（ここでは `o` の直後）の要素には、必ず NULL 終端文字 `'\0'` が格納されます。「文字列の長さ」というときには、NULL 終端文字 `'\0'` の分は含みません。配列で、NULL 終端文字 `'\0'` 以降の要素の状態は不定です。

### 例 2
何も文字列を格納していない、空の状態の `std::string` の中身の例を図示します。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/zenn/competitive-programming/string/2.png)

格納する文字列の長さが 0 だとしても、最後の文字の直後（ここでは配列の先頭）に NULL 終端文字 `'\0'` を必ず格納するため、一定のサイズの配列は確保されています。


### 例 3
`m_allocated_capacity` と同じ長さの文字列を格納している `std::string` の中身を図示します。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/zenn/competitive-programming/string/3.png)

この状態からさらに新しい要素を追加しようとすると、配列の再確保が行われます。例えば `.push_back()` や `+=` が呼ばれると、より広い `m_allocated_capacity` の値で配列を再確保し、既存の文字列を新しい配列にコピーしたのち、新しい追加の文字列が格納されます。


## 1.2 `std::string` の内部構造 (2)
実際の `std::string` は、現実世界の文字列処理プログラムに合わせて、さらに工夫された実装になっています。ここでは、その一部を紹介します。

現実の文字列処理のプログラムでは、`"hello"` や `"dog"`, `"atcoder"` のように、数文字程度の短い文字列をたくさん扱うことが多いです。そのような場合、`std::string` が毎回配列の動的確保（内部での `new` による確保）を行っていると、プログラムの実行速度が低下してしまいます。そこで、一般的な `std::string` では、文字列の長さが短い場合にメモリの動的確保を行わなくて済むよう、`std::string` 内に小さな配列を用意し、そこに文字列を格納する工夫を行っています。これを **SSO (Small String Optimization)** と呼びます。

```cpp
struct string
{
	char* m_ptr; // 確保したメモリの先頭を指すポインタ
	size_t m_size; // 格納している文字列の長さ
	size_t m_allocated_capacity; // 動的確保した配列に格納できる文字列の最大の長さ
	
	static constexpr size_t LocalCapacity = 15; // 動的確保不要で格納できる文字列の最大の長さ
	char m_local_buffer[LocalCapacity + 1]; // 文字列の長さが LocalCapacity 以下の場合、動的確保の代わりに使う配列
};
```

`m_local_buffer` は、文字列の長さが `LocalCapacity` 以下の場合に使うことのできる配列です。

`LocalCapacity` の値は、`std::string` の実装によって異なりますが、一般には 15 程度になっています。この値が大きいほど、より長い文字列をメモリの動的確保を行わずに格納できますが、`std::string` 自体のサイズが大きくなり、空の文字列や 1 文字の文字列を格納する際にも大きなメモリを消費してしまいます。やみくもに大きくすれば高速化できるというわけではありません。

文字列の格納に `m_local_buffer` を使っている場合、`m_ptr` は `m_local_buffer` の先頭を指します。そのときの文字列の `.capacity()` は `LocalCapacity` です。

```cpp
struct string
{
	char* m_ptr; // 確保したメモリの先頭を指すポインタ
	size_t m_size; // 格納している文字列の長さ
	size_t m_allocated_capacity; // 動的確保した配列に格納できる文字列の最大の長さ
	
	static constexpr size_t LocalCapacity = 15; // 動的確保不要で格納できる文字列の最大の長さ
	char m_local_buffer[LocalCapacity + 1]; // 文字列の長さが LocalCapacity 以下の場合、動的確保の代わりに使う配列

	size_t capacity() const noexcept
	{
		if (is_local())
		{
			return LocalCapacity;
		}
		else
		{
			return m_allocated_capacity;
		}
	}

	// 内部関数: 文字列が m_local_buffer に格納されているかどうかを返す
	bool _is_local() const noexcept
	{
		return (m_ptr == m_local_buffer);
	}
};
```

手元で使用している標準ライブラリの実装が採用している `LocalCapacity` の値を調べるには、デフォルト構築した `std::string` の `.capacity()` の値を確認してください。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s; // 空の文字列の std::string を構築する
	std::cout << s.capacity() << '\n';
}
```

## 1.3 `std::string` の内部構造 (3)
1.2 から少しだけ消費メモリを節約することができます。次のように `union` を用いて `m_local_buffer` と `m_allocated_capacity` を同じメモリ領域に配置します。実際、`m_local_buffer` を使用しているときは `m_allocated_capacity` は使わないため、合理的です。1.2 の `std::string` の実装と比べると、`sizeof(std::string)` は 40 バイトから 32 バイトに減ります。gcc の標準ライブラリではこの実装が採用されています。

```cpp
struct string
{
	char* m_ptr; // 確保したメモリの先頭を指すポインタ
	size_t m_size; // 格納している文字列の長さ

	static constexpr size_t LocalCapacity = 15; // 動的確保不要で格納できる文字列の最大の長さ

	union
	{
		char m_local_buffer[LocalCapacity + 1]; // 文字列の長さが LocalCapacity 以下の場合、動的確保の代わりに使う配列
		size_t m_allocated_capacity; // 動的確保した配列に格納できる文字列の最大の長さ
	} m_storage;
};
```

# 2. C 言語の文字列との相互運用性
C++ の `std::string` クラスは、C 言語の文字列と簡単に相互運用できるように設計されています。これは主に C 言語のライブラリや API と連携するときに便利です。`std::string` が NULL 終端文字 `'\0'` を持っている主な理由も、この互換性を保つためです。

## 2.1 `.c_str()` を使用する

`std::string` オブジェクトのメンバ関数 `.c_str()` は、C 言語の文字列（`char` 型の配列に NULL 終端文字 `'\0'` が付いたもの。具体的には 1.1 の `m_ptr`）を返します。このメンバ関数を使用することで、`std::string` を、NULL 終端された文字列を要求する C 言語の関数で使用できます。

```cpp
#include <string>
#include <cstdio>

int main()
{
	std::string s = "hello";
	printf("%s\n", s.c_str()); // NULL 終端されているおかげで、C 言語の関数で使うことができる
}
```
```txt:出力
hello
```


## 2.2 文字列リテラルは `std::string` 型ではない
C++ において、文字列リテラル（例：`"hello"`）は `std::string` 型のオブジェクトではありません。もしそうであれば、`"hello".size()` のようにメンバ関数を呼び出せますが、実際にはコンパイルエラーになります。

C++ の文字列リテラルは `const char[]` 型、つまり読み取り専用の `char` 型の配列です。その配列は NULL 終端されています。これは C 言語の文字列リテラルの仕様をおおよそ引き継いだものです。

```cpp
const char* s = "hello";  // 's' は `const char[6]` 型へのポインタ
```

上記の `"hello"` は 6 個の要素（`'h'`, `'e'`, `'l'`, `'l'`, `'o'`, `'\0'`）を持つ配列（`const char[]` 型）です。このように NULL 終端された `char` 文字列を、`std::string` によって表現する文字列と区別するため、「C 言語の文字列」といいます。


## 2.3 `std::string` へ変換する
C 言語の文字列から `std::string` に変換するのは簡単です。

```cpp
std::string word = "hello";
```

```cpp
const char* s = "hello";
std::string word = s;
```


# 3. `std::string` の構築
ここでは、`std::string` を構築する方法を紹介します。

## 3.1 文字列リテラルから構築する
- 文字列リテラルを使って `std::string` を構築します。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::cout << s << '\n'; // 保持している文字列を出力する
	std::cout << s.size() << '\n'; // 要素数を出力する
}
```
```txt:出力
abc
3
```

## 3.2 (個数) × (文字) で構築する
- `std::string s(n, ch);` は、`n` 回繰り返す文字 `ch` からなる文字列を構築します。
- `n` と `ch` の順番を思い出せない時は、「5 個の a」を英語にすると `5 a's` になることから、`n` が先にくることを思い出せます。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s(5, 'a'); // 5 個の 'a'
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:出力
aaaaa
5
```

## 3.3 別の `std::string` から構築する
- 別の `std::string` オブジェクトをコピーして新しい `std::string` を構築します。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::string t = s; // s の値をコピーする
	std::cout << t << '\n';
	std::cout << t.size() << '\n';
	std::cout << s << '\n'; // コピー元は変化していない
	std::cout << s.size() << '\n'; // コピー元は変化していない
}
```
```txt:出力
abc
3
abc
3
```

## 3.4 複数の文字から構築する
- `std::string s = { 'a', 'b', 'c' };` のようにして、複数の文字から `std::string` を構築します。

```cpp
#include <iostream>
#include <string>

int main()
{
	char c1 = 'a';
	char c2 = 'b';
	char c3 = 'c';
	std::string s = { c1, c2, c3 };
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:出力
abc
3
```


## 3.5 空の文字列
- `std::string` 型のデフォルトコンストラクタは、要素数が 0 である空の `std::string` を構築します。
- 空の文字列であっても、何の問題もなくプログラムで扱うことができます。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s; // 空の文字列を構築する
	std::cout << s << '\n'; // 出力する（何も表示されない）
	std::cout << s.size() << '\n'; // 長さは 0
}
```
```txt:出力

0
```


# 4. `std::string` への入力

## 4.1 標準入力の基本
- `std::string` 型の変数に対する `std::cin` は、標準入力から読み取った文字列を変数に格納します。
- 区切り（改行や空白文字、終端）が出現するまでの連続する文字を 1 つの文字列として読み込みます。
- 区切りと見なされた改行文字や空白文字は除去されます。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s;
	std::cin >> s; // 標準入力から単語を読み込む
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:入力
apple
```
```txt:出力
apple
5
```
## 4.2 複数個の入力を受け取る
- 複数の文字列の入力には次のように対応します。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s, t, u;

	 // 標準入力から 3 つの単語を読み込む
	std::cin >> s;
	std::cin >> t;
	std::cin >> u;

	std::cout << s << '\n';
	std::cout << s.size() << '\n';
	std::cout << t << '\n';
	std::cout << t.size() << '\n';
	std::cout << u << '\n';
	std::cout << u.size() << '\n';
}
```
```txt:入力
apple
bird
cat
```
```txt:出力
apple
5
bird
4
cat
3
```


- `>>` を連続させることもできます。次のコードは、上記のコードと同じ動作をします。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s, t, u;
	std::cin >> s >> t >> u; // 標準入力から 3 つの単語を読み込む

	std::cout << s << '\n';
	std::cout << s.size() << '\n';
	std::cout << t << '\n';
	std::cout << t.size() << '\n';
	std::cout << u << '\n';
	std::cout << u.size() << '\n';
}
```
```txt:入力
apple
bird
cat
```
```txt:出力
apple
5
bird
4
cat
3
```

## 4.3 半角空白で区切られた入力を受け取る
- 1 行の入力の途中にある空白文字も、改行と同様に入力の区切りと見なされます。
- 区切りと見なされた空白文字は除去されます。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s, t;
	std::cin >> s >> t; // 標準入力から 2 つの単語を読み込む

	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	std::cout << t << '\n';
	std::cout << t.size() << '\n';
}
```
```txt:入力
blue ocean
```
```txt:出力
blue
4
ocean
5
```

## 4.4 丸ごと 1 行の入力を受け取る
- 空白文字を含む 1 行を丸ごと読み込みたい場合は `std::getline()` を使います。
- 末尾の改行文字は除去されます。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s;
	std::getline(std::cin, s); // 標準入力から 1 行を読み込む
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:入力
blue ocean
```
```txt:出力
blue ocean
10
```

## 4.5 【サンプル】たくさんの単語を読み込む
- 最初に単語の個数 $N$, 続いて $N$ 個の単語が入力される問題において、入力されるすべての単語を `std::vector<std::string>` に読み込んで格納するには次のようにします。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	int N;
	std::cin >> N; // 単語の個数

	std::vector<std::string> words(N);
	for (auto& word : words) // N 回繰り返す
	{
		std::cin >> word;
	}

	for (const auto& word : words)
	{
		std::cout << word << "!\n";
	}
}
```
```txt:入力
4
red
green
blue
yellow
```
```txt:出力
red!
green!
blue!
yellow!
```


## 4.6 【サンプル】たくさんの単語を読み込む（入力個数が与えられない場合）
- 一部のコンテストでは、4.5 のような問題で、入力される単語数が最初に与えられない場合があります。その場合は `std::cin >> s` を `while( )` でループします。
- 入力がこれ以上存在しなくなったとき、`(std::cin >> s)` の条件部分が `false` になり、それに伴ってループが終了します。

```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> words;
	std::string in;
	while (std::cin >> in) // 入力が存在しなくなるまで繰り返す
	{
		words.push_back(in);
	}

	for (const auto& word : words)
	{
		std::cout << word << "!\n";
	}
}
```
```txt:入力
red
green
blue
yellow
```
```txt:出力
red!
green!
blue!
yellow!
```


# 5. 文字列の長さ

## 5.1 要素数を調べる
- `.size()` は、文字列の長さ（要素数）を符号無し整数型の値で返します。
- 同じ効果を持つメンバ関数として `.length()` もあります。
- 例えば `std::vector` には `.length()` が無く `.size()` があるため、`std::vector` とコードを共通化するために、`std::string` について常に `.size()` を使うという方針が考えられます。そうすれば、`std::string` のために書いた関数を `std::vector` 用に書き換える際の負担が減ります。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::cout << s.size() << '\n'; // 要素数を出力する

	s = "apple";
	std::cout << s.size() << '\n';
	std::cout << s.length() << '\n'; // .size() と同じ

	std::size_t n = s.size();
	std::cout << n << '\n';
}
```
```txt:出力
3
5
5
5
```

## 5.2 空の文字列であるかを調べる
- `.empty()` は、長さが 0 の文字列（空の文字列）であるかを `bool` 型の値で返します
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s1;

	if (s1.empty()) // 空の文字列なので true
	{
		std::cout << "s1 is empty.\n";
	}

	std::string s2 = "abc";

	if (s2.empty()) // 空の文字列ではないので false
	{
		std::cout << "s2 is empty.\n";
	}
}
```
```txt:出力
s1 is empty.
```


## 5.3 内部で確保している配列のサイズを調べる
- 1.1 で説明したように、`std::string` は内部で文字列を格納するために、実際の文字列よりも長い配列を確保しています。`.capacity()` は、現在確保している配列に格納可能な文字列の最大の長さを返します。
- `.size() <= .capacity()` が常に成り立ちます。
- `.capacity()` が返す長さ以下の文字列を格納するかぎりにおいては、新しい配列の再確保は行われません。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::cout << s.size() << '\n'; // 要素数を出力する
	std::cout << s.capacity() << '\n'; // 確保している配列のサイズを出力する

	s = "abcdefghijklmnopqrstuvwxyz";
	std::cout << s.size() << '\n'; 
	std::cout << s.capacity() << '\n';
}
```
```txt:出力例
3
15
26
30
```


# 6. `std::string` への代入

## 6.1 別の文字列を代入する
- `=` を使って別の文字列リテラルや `std::string` オブジェクトの文字列を代入します。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s = "apple"; // "apple" を代入する
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	std::string t = "bird";
	s = t; // t の値を代入する
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:出力
abc
3
apple
5
bird
4
```


# 7. `std::string` の比較

## 7.1 同じ文字列であるかを調べる
- `a`, `b` の少なくとも一方が `std::string` 型である場合、`a == b` は 2 つの文字列 `a`, `b` が一致するかを `bool` 型の値で返します。

> - `std::string` どうしの `==` の計算量: 最初に双方の要素数が同じかを調べるため、双方のサイズが異なる場合は $O(1)$, それ以外の場合は $O(N)$

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s1 = "cat";
	std::string s2 = "dog";

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (s1 == "cat") << '\n';
	std::cout << (s2 == "cat") << '\n';
	std::cout << (s1 == s1) << '\n';
	std::cout << (s1 == s2) << '\n';
}
```
```txt:出力
true
false
true
false
```

## 7.2 違う文字列であるかを調べる
- `a`, `b` の少なくとも一方が `std::string` 型である場合、`a != b` は 2 つの文字列 `a`, `b` が異なるかを `bool` 型の値で返します。

> - `std::string` どうしの `!=` の計算量: 最初に双方の要素数が違うかを調べるため、双方のサイズが異なる場合は $O(1)$, それ以外の場合は $O(N)$

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s1 = "cat";
	std::string s2 = "dog";

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (s1 != "cat") << '\n';
	std::cout << (s2 != "cat") << '\n';
	std::cout << (s1 != s1) << '\n';
	std::cout << (s1 != s2) << '\n';
}
```
```txt:出力
false
true
false
true
```

## 7.3 文字列の順序比較
- `a`, `b` の少なくとも一方が `std::string` 型である場合、`a < b`, `a <= b`, `a > b`, `a >= b` は 2 つの文字列の順序関係を `bool` 型の値で返します。
- 基本の順序: `0 < 9 < A < Z < a < z`  
  - 参考: [ASCII コード表](https://www.k-cube.co.jp/wakaba/server/ascii_code.html)  
- 順序の例: `A < AA < AB < ABC < Aa < Ab < B < BA < Z < ZZ < a < aA < aa < b < zzz`  
- 大文字と小文字が混在する場合の順序には注意が必要です。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s1 = "cat";
	std::string s2 = "dog";

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (s1 < s2) << '\n';
	std::cout << (s1 < "dog") << '\n';
	std::cout << ("caa" < s1) << '\n';
	std::cout << ("Dog" < s2) << '\n';
}
```
```txt:出力
true
true
true
true
```


# 8. 要素へのアクセス

## 8.1 指定した位置の要素にアクセスする
- `s[index]` で、文字列内の指定した位置の要素 (`char` 型) にアクセスします。
- インデックスは 0-indexed です。つまり、先頭の文字が `0` で、その次が `1`, ..., `(.size() - 1)` です。
- 範囲外にアクセスしてはいけません（例外: 8.5）。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s[0] << '\n'; // 0 番目の要素を出力する
	std::cout << s[1] << '\n'; // 1 番目の要素を出力する
	std::cout << s[2] << '\n'; // 2 番目の要素を出力する

	s[2] = 'n'; // 2 番目の要素を書き換える
	std::cout << s << '\n';
}
```
```txt:出力
c
a
t
can
```

## 8.2 先頭の要素にアクセスする
- `.front()` で、文字列の先頭の要素にアクセスします。
- 空の文字列で使うと範囲外アクセスになるため注意が必要です。
- 文字列が空でなければ `s[0]` とほぼ同等です。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s.front() << '\n'; // 先頭の要素を出力する

	s.front() = 'h'; // 先頭の要素を書き換える
	std::cout << s << '\n';
}
```
```txt:出力
c
hat
```

## 8.3 末尾の要素にアクセスする
- `.back()` で、文字列の末尾の要素にアクセスします。
- 空の文字列で使うと範囲外アクセスになるため注意が必要です。
- 文字列が空でなければ `s[(s.size() - 1)]` と同等です。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s.back() << '\n'; // 末尾の要素を出力する

	s.back() = 'r'; // 末尾の要素を書き換える
	std::cout << s << '\n';
}
```
```txt:出力
t
car
```

## 8.4 指定した位置の要素にアクセスする（範囲外アクセスを例外で検出）
- `.at(index)` で、指定した位置の要素 (`char` 型) にアクセスします。
- インデックスは `s[index]` と同じく 0-indexed です。
- 関数の戻り値は `s[index]` と同じですが、範囲外アクセスがあった場合に `std::out_of_range` 例外を送出して知らせてくれます。
- 範囲外アクセスチェックのために内部処理で `if` を使うため、チェックの無い `s[index]` と比べると少し遅くなります。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s.at(0) << '\n'; // 0 番目の要素を出力する
	std::cout << s.at(1) << '\n'; // 1 番目の要素を出力する
	std::cout << s.at(2) << '\n'; // 2 番目の要素を出力する

	s.at(2) = 'n'; // 2 番目の要素を書き換える
	std::cout << s << '\n';
}
```
```txt:出力
c
a
t
can
```


## 8.5 NULL 終端文字にアクセスする
- `std::string` の長さが `N` の場合、`s[N]` は NULL 終端文字 `'\0'` にアクセスします。`s[N]` への書き込み操作は禁止されていますが、読み込み操作は可能です。
- これは `std::string` が末尾に NULL 終端文字を常に持つことに由来する仕様で、長さが `N` の配列や `std::vector` では `v[N]` にアクセスすることができないのとは対照的です。
- `.at(N)` や `.front()`, `.back()` では NULL 終端文字にアクセスすることはできません。
- 通常のプログラムで `s[N]` にアクセスするような状況はほとんどないため、この仕様を意識する必要はありません。


```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s;
	char ch = s[0]; // NULL 終端の位置の要素を読み込むことは可能

	if (ch == '\0')
	{
		std::cout << "s[0] is NULL.\n";
	}

	// NG: NULL 終端の位置の要素を書き換えてはいけない
	//s[0] = 'a';
}
```
```txt:出力
s[0] is NULL.
```


# 9. 基本的なループによる各要素へのアクセス

## 9.1 範囲ベース `for` 文を使い、各要素に `const` 参照でアクセスする
- `std::string` に対する範囲ベース `for` 文は、文字列の長さだけ繰り返すループです。
- ループ内で要素を書き換えない場合、`const` 参照を使います。
- `std::string` に対する範囲ベース for ループは、**文字列の要素数がループ中に変更されない**という前提で動作するため、絶対にループ内でその文字列の要素数を増減させる操作をしてはいけません（9.3 参照）。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";

	// 要素の個数だけ繰り返すループ、ch は順に各要素への const 参照
	for (const auto& ch : s)
	{
		std::cout << ch << '\n';
	}
}
```
```txt:出力
a
p
p
l
e
```

## 9.2 範囲ベース `for` 文を使い、各要素に参照でアクセスする
- ループ内で要素を書き換える場合、参照を使います
- `std::string` に対する範囲ベース for ループは、**文字列の要素数がループ中に変更されない**という前提で動作するため、絶対にループ内でその文字列の要素数を増減させる操作をしてはいけません（9.3 参照）。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";

	// 要素の個数だけ繰り返すループ、ch は順に各要素への参照
	for (auto& ch : s)
	{
		ch += 1;
	}

	std::cout << s << '\n';
}
```
```txt:出力
bqqmf
```

## 9.3 範囲ベース `for` 文を使うとき、要素数を変更してはいけない
- `std::string` に対する範囲ベース for ループは、**文字列の要素数がループ中に変更されない**という前提で動作するため、絶対にループ内でその文字列の要素数を増減させる操作をしてはいけません。

```cpp:誤りのプログラム
#include <iostream>
#include <string>

int main()
{
	std::string s = "straightforward";

	for (const auto& ch : s)
	{
		std::cout << ch << '\n';

		// NG: 範囲ベース for 文の中でループ対象の文字列の長さを変更してはいけない
		s.push_back('-');
	}
}
```

## 9.4 初期化式をともなう、範囲ベースの `for` 文を使う
- C++20 から、範囲ベースの `for` 文に初期化式を追加できるようになりました。
- 例えば、インデックスが偶数番目の文字を出力する際、変数 `i` を初期化式で定義することで、`i` のスコープをループ内に限定できます。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abcdefg";

	for (int i = 0; const auto& ch : s) // 変数 i のスコープがループ内に限定される
	{
		if ((i % 2) == 0)
		{
			std::cout << ch;
		}

		++i;
	}

	std::cout << '\n';
}
```
```txt:出力
aceg
```

- C++17 までは、次のように書く必要がありました。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abcdefg";

	int i = 0; // 変数 i のスコープが広くなってしまう

	for (const auto& ch : s)
	{
		if ((i % 2) == 0)
		{
			std::cout << ch;
		}

		++i;
	}

	std::cout << '\n';
}
```
```txt:出力
aceg
```


## 9.5 インデックスを使って各要素にアクセスする
- 通常の `for` 文で、インデックスを使って各要素にアクセスすることができます。
- この方法は、特にループが複雑になった時に、インデックスに使う変数の取り違えや、それにともなう範囲外アクセスのコードを書いてしまうミスの原因になります。必要がない限りは範囲ベースの `for` 文や、10. で説明するビューを使うことを推奨します。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";

	for (std::size_t i = 0; i < s.size(); ++i)
	{
		// インデックスを使って各要素にアクセスする
		std::cout << s[i] << '\n';
	}
}
```
```txt:出力
a
p
p
l
e
```


## 9.6 イテレータを使って各要素にアクセスする
- `std::string` は、イテレータを使って各要素にアクセスすることができます。
- この方法は、特にループが複雑になった時に、イテレータに使う変数の取り違えや、それにともなう範囲外アクセスのコードを書いてしまうミスの原因になります。必要がない限りは範囲ベースの `for` 文や、10. で説明するビューを使うことを推奨します。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";

	for (auto it = s.begin(); it != s.end(); ++it)
	{
		// イテレータを使って各要素にアクセスする
		std::cout << *it << '\n';
	}
}
```
```txt:出力
a
p
p
l
e
```


## 9.7 逆イテレータを使って各要素にアクセスする
- `std::string` は、逆イテレータを使って各要素にアクセスすることができます。
- この方法は、特にループが複雑になった時に、イテレータに使う変数の取り違えや、それにともなう範囲外アクセスのコードを書いてしまうミスの原因になります。必要がない限りは 10. で説明するビューを使うことを推奨します。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";

	for (auto it = s.rbegin(); it != s.rend(); ++it)
	{
		// 逆イテレータを使って各要素にアクセスする
		std::cout << *it << '\n';
	}
}
```
```txt:出力
e
l
p
p
a
```


# 10. ビューを用いたアクセス
- C++20 から、`std::string` を含むレンジ全般に対する**ビュー**を作成し、複雑な設定に基づくレンジ内の一連の要素へのアクセスを簡潔に記述できるようになりました。
	- ここでのビューは、文字列ビュー（`std::string_view`）とは異なることに注意してください。
- ビューは、新しい `std::string` を作成したり、既存の `std::string` を変更したりするのではなく、既存の `std::string` に対するアクセス方法を変更するだけであるため、通常、その操作に期待される最小のコストで動作します。
	- 例えば 10.1 の逆順ビューは、文字列の要素を逆順にアクセスするビューを作成するだけであり、文字列の要素を実際に逆順に並べ替えるわけではありません。
- ビューに対しては、範囲ベースの `for` 文が使えるため、範囲外アクセスのミスを防ぐことができます。

## 10.1 逆順にアクセスするビュー [C++20]
- `std::views::reverse` を使うと、文字列に対して逆順にアクセスするビューを作成できます。
- 文字列の要素を `std::reverse()` / `std::ranges::reverse()` のように実際に逆順に並べ替えるわけではないため、効率的です。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/zenn/competitive-programming/string/v1.png =400x)

```cpp
#include <iostream>
#include <string>
#include <ranges>

int main()
{
	std::string s = "atcoder";

	// 逆順にアクセスするビュー
	for (const auto& ch : s | std::views::reverse)
	{
		std::cout << ch << '\n';
	}
}
```
```txt:出力
r
e
d
o
c
t
a
```


## 10.2 最初の N 個の要素にアクセスするビュー [C++20]
- `std::views::take(N)` を使うと、文字列の最初の N 個の要素にアクセスするビューを作成できます。
- 要素数が N 未満の場合は、すべての要素にアクセスするビューを作成します。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/zenn/competitive-programming/string/v2.png =400x)

```cpp
#include <iostream>
#include <string>
#include <ranges>

int main()
{
	std::string s = "atcoder";

	// 最初の 3 個の要素にアクセスするビュー
	for (auto& ch : s | std::views::take(3))
	{
		ch -= 32;
	}

	std::cout << s << '\n';
}
```
```txt:出力
ATCoder
```


## 10.3 最初の N 個の要素を除いた要素にアクセスするビュー [C++20]
- `std::views::drop(N)` を使うと、文字列の最初の N 個の要素を除いた要素にアクセスするビューを作成できます。
- 要素数が N 未満の場合は、空のビューを作成します。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/zenn/competitive-programming/string/v3.png =400x)

```cpp
#include <iostream>
#include <string>
#include <ranges>

int main()
{
	std::string s = "atcoder";

	// 最初の 3 個の要素を除いた要素にアクセスするビュー
	for (auto& ch : s | std::views::drop(3))
	{
		ch -= 32;
	}

	std::cout << s << '\n';
}
```
```txt:出力
atcODER
```


## 10.4 最初の N 個の要素を除いたあとの最初の M 個の要素にアクセスするビュー [C++20]
- ビューを作るためのアダプタは、パイプ `|` でつなげることができます。
- `std::views::drop(N) | std::views::take(M)` を使うと、文字列の最初の N 個の要素を除いたあとの最初の M 個の要素にアクセスするビューを作成できます。

```cpp
#include <iostream>
#include <string>
#include <ranges>

int main()
{
	std::string s = "atcoder";

	// 最初の 3 個の要素を除いたあとの最初の 2 個の要素にアクセスするビュー
	for (auto& ch : s | std::views::drop(3) | std::views::take(2))
	{
		ch -= 32;
	}

	std::cout << s << '\n';
}
```
```txt:出力
atcODer
```

- `std::views::counted(it, count)` を使って書くこともできます。イテレータ `it` の指す位置が範囲外にならないように注意してください。

```cpp
#include <iostream>
#include <string>
#include <ranges>

int main()
{
	std::string s = "atcoder";

	// インデックス 3 番目から 2 個の要素にアクセスするビューを作成する
	for (auto& ch : std::views::counted((s.begin() + 3), 2))
	{
		ch -= 32;
	}

	std::cout << s << '\n';
}
```
```txt:出力
atcODer
```


## 10.5 ビューを `std::string` に変換する [C++20]
- `std::string` に対するビューから新しい `std::string` を構築するには、ビューのイテレータペアを使います。

```cpp
#include <iostream>
#include <string>
#include <ranges>

int main()
{
	std::string s1 = "abcdefg";

	auto view = (s1 | std::views::take(3));
	std::string s2(view.begin(), view.end());

	std::cout << s2 << '\n';
}
```
```txt:出力
abc
```

- C++23 になると、`std::ranges::to<std::string>()` を使うことで、ビューを `std::string` に変換できるようになります。

```cpp:C++23 のコード
#include <iostream>
#include <string>
#include <ranges>

int main()
{
	std::string s1 = "abcdefg";
	
	std::string s2 = s1 | std::views::take(3) | std::ranges::to<std::string>();

	std::cout << s2 << '\n';
}
```
```txt:出力
abc
```


# 11. `std::string` への追加

## 11.1 前後に別の文字列を足した、新しい `std::string` を作成する
- `+` 演算子を使うと、前後に別の文字列を追加した新しい `std::string` オブジェクトを作成できます。
- 元の文字列は変更されません。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "school";

	std::string t = ("high" + s); // 前に足した文字列を作成する
	std::cout << t << '\n';

	std::string u = (s + "bus"); // うしろに足した文字列を作成する
	std::cout << u << '\n';

	std::cout << ("my" + s) << '\n'; // 前に足した文字列を作成する
}
```
```txt:出力
highschool
schoolbus
myschool
```

## 11.2 末尾に 1 文字追加する
- `.push_back(ch)` で、文字列の末尾に文字 `ch` を追加します。
- 1.1 を見るとわかるように、文字列の末尾への要素追加は、その時点での NULL 終端文字を新しい文字に置き換え、その直後に新しい NULL 終端文字を格納するだけの非常に軽量な操作になります。
- 要素の追加を重ね、配列のサイズが不足した場合には、より広い配列の再確保が行われます。その際には余裕を持ったサイズ（例えばその時点でのサイズの 2 倍など）の領域を確保することで、追加操作のたびに毎回配列を再確保するような非効率な事態が防がれています。
- そのため、`.push_back()` による要素追加は、償却計算量（ならし計算量） $O(1)$ です。


```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "t";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.push_back('o'); // 末尾に `o` を追加する
	std::cout << s << '\n';
	std::cout << s.size() << '\n';	

	s.push_back('p'); // 末尾に `p` を追加する
	std::cout << s << '\n';
	std::cout << s.size() << '\n';	
}
```
```txt:出力
t
1
to
2
top
3
```

## 11.3 先頭に 1 文字追加する
- `.insert(it, ch)` は、イテレータ `it` が指す位置の前に要素 `ch` を追加します。
- 先頭位置のイテレータを返す `.begin()` と組み合わせることで、文字列の先頭に文字を追加できます。
- 1.1 を見るとわかるように、文字列の末尾以外への要素追加を行うと、それ以降の要素をすべて右へずらす必要が生じます。そのため、文字列の先頭への要素追加の計算量は、文字列の長さを $N$ としたとき、$O(N)$ となることに注意してください。これは `std::vector` でも同様です。
	- 先頭への要素追加を $O(1)$ で行うことができる`std::deque` と比べると、`std::string` や `std::vector` での先頭への要素追加は重い操作であることがわかります。競技プログラミングにおいては避けるべきです。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "p";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.insert(s.begin(), 'o'); // 先頭に `o` を追加する
	std::cout << s << '\n';
	std::cout << s.size() << '\n';	

	s.insert(s.begin(), 't'); // 先頭に `t` を追加する
	std::cout << s << '\n';
	std::cout << s.size() << '\n';	
}
```
```txt:出力
p
1
op
2
top
3
```

## 11.4 末尾に文字列を追加する
- `+= other;` で、文字列の末尾に文字列 `other` を追加します。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "t";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s += "ea"; // 末尾に "ea" を追加する
	std::cout << s << '\n';
	std::cout << s.size() << '\n'; 

	std::string t = "cher";
	s += t; // 末尾に "cher" を追加する
	std::cout << s << '\n';
	std::cout << s.size() << '\n'; 
}
```
```txt:出力
t
1
tea
3
teacher
7
```


# 12. 要素の削除

## 12.1 末尾の要素を削除する
- `.pop_back()` は末尾の要素を 1 つ削除します。
- 計算量は $O(1)$ です。
- 空の文字列で使うと範囲外アクセスになるため注意が必要です。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.pop_back(); // 末尾の要素を削除する
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.pop_back(); // 末尾の要素を削除する
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:出力
apple
5
appl
4
app
3
```

## 12.2 先頭の要素を削除する
- `.erase(it)` は、イテレータ `it` が指す位置の要素を削除します。
- 先頭位置のイテレータを返す `.begin()` と組み合わせることで先頭の文字を削除できます。
- 1.1 を見るとわかるように、文字列の末尾以外への要素の削除を行うと、それ以降の要素をすべて左へずらす必要が生じます。そのため、文字列の先頭要素を削除する計算量は、文字列の長さを $N$ としたとき、$O(N)$ となることに注意してください。これは `std::vector` でも同様です。
	- 先頭要素の削除を $O(1)$ で行うことができる `std::deque` と比べると、`std::string` や `std::vector` での先頭要素の削除は重い操作であることがわかります。競技プログラミングにおいては避けるべきです。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.erase(s.begin()); // 先頭の要素を削除する
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.erase(s.begin()); // 先頭の要素を削除する
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:出力
apple
5
pple
4
ple
3
```

## 12.3 文字列を消去する
- `.clear()` を使うと、全要素を削除して空の文字列になります。
	- 一般的な `std::string` の実装は、配列の先頭を NULL 終端文字 `'\0'` にして、1.1 で説明した `m_size` を 0 にすることで、外見上消去したように見せかける省コストなものになっています。その場合の計算量は $O(1)$ です。
- 一般的な `std::string` の実装は、`.clear()` をしても、確保した配列領域はそのまま保持されます（`.capacity()` が変わりません）。そのオブジェクトを再利用して新しい要素を追加するときのメモリ確保のコストを抑制できますが、使用しない場合はメモリを無駄に占有することになります。
- 確保した配列のサイズを、現在の要素数に合わせてコンパクトにするには、追加で `.shrink_to_fit()` を使います。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s(100, 'a');

	std::cout << s << '\n';
	std::cout << s.size() << '\n';
	std::cout << s.capacity() << '\n';

	std::cout << "----\n";

	s.clear(); // 要素を全消去する
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
	std::cout << s.capacity() << '\n';

	std::cout << "----\n";

	s.shrink_to_fit(); // 確保している配列のサイズを現在の文字列の長さに合わせてコンパクトにする
	std::cout << s.size() << '\n';
	std::cout << s.capacity() << '\n';
}
```
```txt:出力例
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
100
100
----

0
100
----
0
15
```

# 13. `std::string` の交換

## 13.1 二つの `std::string` を交換する
- 2 つの `std::string` 型の変数 `a`, `b` の中身を入れ替えたい場合は `std::swap(a, b)` を使います。
- 実際に配列の要素をひとつずつ入れ替えるのではなく、1.1 で見た内部のポインタなど、数個の変数を交換するだけなので、格納している文字列の長さによらず、計算量は $O(1)$ です。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s1 = "abcdefghijklmnopqrstuvwxyz";
	std::string s2 = "111111111111111111111111111111";

	std::swap(s1, s2); // 中身を交換する

	std::cout << s1 << '\n';
	std::cout << s2 << '\n';
}
```
```txt:出力
111111111111111111111111111111
abcdefghijklmnopqrstuvwxyz
```

- C++20 以降では、同じ操作をより汎用的に実現する `std::ranges::swap(a, b)` を使うことが推奨されます。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s1 = "abcdefghijklmnopqrstuvwxyz";
	std::string s2 = "111111111111111111111111111111";

	std::ranges::swap(s1, s2); // 中身を交換する

	std::cout << s1 << '\n';
	std::cout << s2 << '\n';
}
```
```txt:出力
111111111111111111111111111111
abcdefghijklmnopqrstuvwxyz
```


# 14. 便利なメンバ関数

## 14.1 ある文字列から始まるかを調べる [C++20]
- `.starts_with(str)` は、自身が文字列 `str` から始まるかを `bool` 値で返します。
- `.starts_with(ch)` は、自身が文字 `ch` から始まるかを `bool` 値で返します。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "atcoder";

	std::cout << std::boolalpha;
	std::cout << s.starts_with("at") << '\n'; // true
	std::cout << s.starts_with("coder") << '\n'; // false
	std::cout << s.starts_with('a') << '\n'; // true
	std::cout << s.starts_with('c') << '\n'; // false
}
```
```txt:出力
true
false
true
false
```


## 14.2 ある文字列で終わるかを調べる [C++20]
- `.ends_with(str)` は、自身が文字列 `str` で終わるかを `bool` 値で返します。
- `.ends_with(ch)` は、自身が文字 `ch` で終わるかを `bool` 値で返します。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "atcoder";

	std::cout << std::boolalpha;
	std::cout << s.ends_with("at") << '\n'; // false
	std::cout << s.ends_with("coder") << '\n'; // true
	std::cout << s.ends_with('r') << '\n'; // true
	std::cout << s.ends_with('a') << '\n'; // false
}
```
```txt:出力
false
true
true
false
```


## 14.3 ある文字列を含むかを調べる [C++23]
- `.contains(str)` は、自身が文字列 `str` を含むかを `bool` 値で返します。
- `.contains(ch)` は、自身が文字 `ch` を含むかを `bool` 値で返します。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "atcoder";

	std::cout << std::boolalpha;
	std::cout << s.contains("at") << '\n'; // true
	std::cout << s.contains("coder") << '\n'; // true
	std::cout << s.contains("atcoder") << '\n'; // true
	std::cout << s.contains("atcoderr") << '\n'; // false
	std::cout << s.contains('a') << '\n'; // true
	std::cout << s.contains('z') << '\n'; // false
}
```
```txt:出力
true
true
true
false
true
false
```


# 15. 部分文字列

## 15.1 新しい部分文字列の `std::string` を作成する
- `.substr(pos, count)` は、文字列の `pos` 番目の要素から `count` 個までの要素からなる部分文字列を、新しい `std::string` として返します。
- `count` を省略した場合は、`pos` 番目の要素から末尾までの要素からなる部分文字列を返します。
- `pos` が文字列の長さ以上の場合は、空の文字列を返します。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "atcoder";

	std::cout << s.substr(0, 2) << '\n'; // "at"
	std::cout << s.substr(3, 3) << '\n'; // "ode"
	std::cout << s.substr(3) << '\n'; // "oder"
	std::cout << s.substr(7) << '\n'; // ""
}
```
```txt:出力
at
ode
oder

```


## 15.2 新しい部分文字列の `std::string` を、イテレータのペアから作成する
- イテレータのペアを使って、部分文字列から新しい `std::string` を作成することができます。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "atcoder";

	std::cout << std::string(s.begin(), (s.begin() + 2)) << '\n'; // "at"
	std::cout << std::string((s.begin() + 1), s.end()) << '\n'; // "tcoder"
	std::cout << std::string(s.begin(), (s.end() - 1)) << '\n'; // "atcode"
	std::cout << std::string((s.end() - 2), s.end()) << '\n'; // "er"
}
```
```txt:出力
at
ode
oder

```


## 15.3 部分文字列を指す `std::string_view` を、イテレータのペアから作成する [C++20]
- 15.1, 15.2 の方法では、新しい `std::string` を作成するため、その分、新しいメモリの消費と文字列のコピーが発生します。このコストを回避するには、代わりに既存の `std::string` 内の部分文字列を指す文字列ビュー `std::string_view` を作成します。
- `std::string_view` は `std::string` と異なり、既存の文字列の一部の範囲を指す情報（範囲の先頭のポインタと、そこからの長さ）だけを保持するため、非常に軽量です。
- ただし、指していた `std::string` が破棄されたり、サイズ変更によってメモリが再確保されたりすると、`std::string_view` は不正な位置を指すことになり、それを使用すると未定義動作になります。そのため、`std::string_view` を変数に保存して長い間保持することは避けるべきです。

```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "atcoder";

	std::cout << std::string_view{ s.begin(), (s.begin() + 2) } << '\n'; // "at"
	std::cout << std::string_view{ (s.begin() + 1), s.end() } << '\n'; // "tcoder"
	std::cout << std::string_view{ s.begin(), (s.end() - 1) } << '\n'; // "atcode"
	std::cout << std::string_view{ (s.end() - 2), s.end() } << '\n'; // "er"
}
```
```txt:出力
at
tcoder
atcode
er
```


# 16. `std::string` のイテレータ
削除や挿入の位置指定や、アルゴリズム関数で使うためのイテレータを、以下の関数で取得できます。イテレータを取得した時点から文字列のサイズが変更されると、それ以前のイテレータは無効になることがあるため、イテレータを変数に保存して長い間保持することは避けるべきです。

## 16.1 先頭位置のイテレータを取得する
- `.begin()` は文字列の先頭位置を指すイテレータを返します。
- 空の文字列の場合 `.begin() == .end()` です。

## 16.2 終端位置のイテレータを取得する
- `.end()` は文字列の終端位置を指すイテレータを返します。
- 空の文字列の場合 `.begin() == .end()` です。

## 16.3 末尾を指す逆イテレータを取得する
- `.rbegin()` は文字列の末尾を指す逆イテレータを返します。
- 空の文字列の場合 `.rbegin() == .rend()` です。

## 16.4 先頭の前を指す逆イテレータを取得する
- `.rend()` は文字列の先頭の前を指す逆イテレータを返します。
- 空の文字列の場合 `.rbegin() == .rend()` です。


# 参考資料
- [Inside STL: The string - The Old New Thing](https://devblogs.microsoft.com/oldnewthing/20230803-00/?p=108532)

