---
title: "<string>"
free: true
---

- 1～11: 文字列クラス `std::string` の機能
- 12: 数値を文字列に変換する関数
- 13: 文字列を数値に変換する関数

# 1. `std::string` の構築

## 1.1 文字列リテラルから構築する
- 文字列リテラル (`""` で囲んでプログラム中に用意した文字列) を使って初期化します
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::cout << s << '\n'; // 保持している文字列を出力
	std::cout << s.size() << '\n'; // 要素数を出力
}
```
```txt:出力
abc
3
```

## 1.2 (個数) × (文字) で構築する
- `std::string s(n, ch);` は、`n` 回繰り返す文字 `ch` で初期化します
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

## 1.3 別の `std::string` から構築する
- 別の `std::string` 型の値をコピーして初期化します
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::string t = s; // s の値をコピー
	std::cout << t << '\n';
	std::cout << t.size() << '\n';
	std::cout << s << '\n'; // コピーしたほうは変化しない
	std::cout << s.size() << '\n'; // コピーしたほうは変化しない
}
```
```txt:出力
abc
3
abc
3
```

## 1.4 空の文字列
- `std::string` 型のデフォルトコンストラクタは要素数が 0 の空の文字列を構築します  
- 空の文字列も、何の問題もなくプログラムで使うことができます
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s; // 空の文字列を構築
	std::cout << s << '\n'; // 出力しても何も表示されない
	std::cout << s.size() << '\n'; // 要素数は 0
}
```
```txt:出力

0
```

# 2. `std::string` への入力

## 2.1 標準入力の基本
- 1 回の `std::cin` で、改行もしくは空白文字が出現するまでの入力（単語）を 1 つ読み取ります 
- 改行文字や空白文字は除去されます
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
## 2.2 複数個の入力
- 複数の入力は、連続する `>>` で読み込むこともできます
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

## 2.3 半角スペースによって入力が区切られる
- 1 行の入力の途中に空白文字が含まれていると、入力の区切りと見なされます
- 空白文字は除去されます
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

## 2.4 丸ごと 1 行を入力
- 空白文字を含む 1 行を丸ごと読み込みたい場合は `std::getline()` を使います
- 改行文字は除去されます
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

## 2.5 【サンプル】たくさんの単語を読み込む
- 最初に単語の個数 $N$, 続いて $N$ 個の単語が入力される問題において、入力されるすべての単語を `std::vector<std::string>` に読み込んで保存します
```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	int N;
	std::cin >> N; // 単語の個数を読み込む

	std::vector<std::string> ss(N);
	for (auto& s : ss) // N 回繰り返す
	{
		std::cin >> s;
	}

	for (const auto& s : ss) // 読み込めていることを確認
	{
		std::cout << '>' << s << '\n';
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
>red
>green
>blue
>yellow
```


## 2.6 【サンプル】たくさんの単語を読み込む（入力個数が与えられない場合）
- 入力される単語数が最初に与えられない場合、`std::cin >> s` をループします。入力がこれ以上存在しなくなると `if (std::cin >> s)` が `false` になるので、それを検知したらループを終了します
```cpp
#include <iostream>
#include <string>
#include <vector>

int main()
{
	std::vector<std::string> ss;
	std::string in;
	while (std::cin >> in) // 入力がこれ以上存在しなくなるまで繰り返す
	{
		ss.push_back(in);
	}

	for (const auto& s : ss) // 読み込めていることを確認
	{
		std::cout << '>' << s << '\n';
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
>red
>green
>blue
>yellow
```


# 3. `std::string` の要素数

## 3.1 要素数を調べる
- `.size()` は、文字列が持つ要素数を符号無し整数型の値で返します
- 同じ効果を持つメンバ関数 `.length()` もあります
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::cout << s.size() << '\n'; // 要素数を出力

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

## 3.2 空の文字列であるかを調べる
- `.empty()` は、要素数が 0 の文字列（空の文字列）であるかを `bool` 型の値で返します
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s;
	std::string t = "abc";

	if (s.empty()) // 空の文字列なので true
	{
		std::cout << "s is empty.\n";
	}

	if (t.empty()) // 空の文字列ではないので false
	{
		std::cout << "t is empty.\n";
	}
}
```
```txt:出力
s is empty.
```


# 4. `std::string` への代入

## 4.1 別の文字列を代入する
- `=` を使って別の文字列リテラルや `std::string` 型の値を代入します
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s = "apple"; // 代入
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	std::string t = "bird";
	s = t; // 代入
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

# 5. `std::string` の比較

## 5.1 同じ文字列であるかを調べる
- `a == b` の少なくとも一方が `std::string` 型の場合、2 つの文字列 `a`, `b` が一致するかを `bool` 型の値で返します
> - `==` の計算量: 最初に双方の要素数が同じかを調べるため、双方のサイズが異なる場合は $O(1)$, それ以外の場合は $O(N)$
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::string t = "dog";

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (s == "cat") << '\n';
	std::cout << (t == "cat") << '\n';
	std::cout << (s == s) << '\n';
	std::cout << (s == t) << '\n';
}
```
```txt:出力
true
false
true
false
```

## 5.2 違う文字列であるかを調べる
- `a != b` の少なくとも一方が `std::string` 型の場合、2 つの文字列 `a`, `b` が異なるかを `bool` 型の値で返します
> - `!=` の計算量: 最初に双方の要素数が違うかを調べるため、双方のサイズが異なる場合は $O(1)$, それ以外の場合は $O(N)$
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::string t = "dog";

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (s != "cat") << '\n';
	std::cout << (t != "cat") << '\n';
	std::cout << (s != s) << '\n';
	std::cout << (s != t) << '\n';
}
```
```txt:出力
false
true
false
true
```

## 5.3 文字列の順序比較
- `<`, `<=`, `>`, `>=` 演算子は 2 つの文字列の順序関係を `bool` 型の値で返します
- 基本の順序: `0 < 9 < A < Z < a < z`  
- 参考: [ASCII コード表](https://www.k-cube.co.jp/wakaba/server/ascii_code.html)  
- 順序の例: `A < AA < AB < ABC < Aa < Ab < B < BA < Z < ZZ < a < aA < aa < b < zzz`  
- 大文字と小文字が混在する場合の順序には注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::string t = "dog";

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させるためのマニピュレータ
	std::cout << (s < t) << '\n';
	std::cout << (s < "dog") << '\n';
	std::cout << ("caa" < s) << '\n';
	std::cout << ("Dog" < t) << '\n';
}
```
```txt:出力
true
true
true
true
```


# 6. 要素へのアクセス

## 6.1 指定した位置の要素にアクセスする
- `s[index]` で、指定した位置の要素 (`char` 型) にアクセスします 
- インデックスは先頭の文字が `0`, その次が `1`, ..., `(.size() - 1)` です
- 範囲外にアクセスしてはいけません
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s[0] << '\n'; // 0 番目の要素を出力
	std::cout << s[1] << '\n'; // 1 番目の要素を出力
	std::cout << s[2] << '\n'; // 2 番目の要素を出力

	s[2] = 'n'; // 2 番目の要素を書き換え
	std::cout << s << '\n';
}
```
```txt:出力
c
a
t
can
```

## 6.2 先頭の要素にアクセスする
- `.front()` で、文字列の先頭の要素にアクセスします
- `s[0]` と同等です
- 空の文字列で使うと範囲外アクセスになるため注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s.front() << '\n'; // 先頭の要素を出力

	s.front() = 'h'; // 先頭の要素を書き換え
	std::cout << s << '\n';
}
```
```txt:出力
c
hat
```

## 6.3 末尾の要素にアクセスする
- `.back()` で、文字列の末尾の要素にアクセスします
- `s[(s.size() - 1)]` と同等です（ただし `0 < s.size()`）
- 空の文字列で使うと範囲外アクセスになるため注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s.back() << '\n'; // 末尾の要素を出力

	s.back() = 'r'; // 末尾の要素を書き換え
	std::cout << s << '\n';
}
```
```txt:出力
t
car
```

## 6.4 指定した位置の要素にアクセスする（範囲外アクセスを例外で検出）
- `.at(index)` で、指定した位置の要素 (`char` 型) にアクセスします
- インデックスは先頭の文字が `0`, その次が `1`, ..., `(.size() - 1)` です
- `s[index]` と異なり、範囲外アクセスがあった場合に `std::out_of_range` 例外を送出します。そのチェックのための追加のコストがあります
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s.at(0) << '\n'; // 0 番目の要素を出力
	std::cout << s.at(1) << '\n'; // 1 番目の要素を出力
	std::cout << s.at(2) << '\n'; // 2 番目の要素を出力

	s.at(2) = 'n'; // 2 番目の要素を書き換え
	std::cout << s << '\n';
}
```
```txt:出力
c
a
t
can
```

## 6.5 range-based for を使い、各要素に `const` 参照でアクセスする
- ループ内で要素を書き換えない場合、`const` 参照を使います
- `std::string` に対する range-based for ループは、文字列の要素数がループ中に変更されないという前提で実行されるため、ループ内でその文字列の要素数を変更する操作をしてはいけません
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";

	for (const auto& ch : s) // 要素の個数だけ繰り返すループ、ch は順に各要素への const 参照
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

## 6.6 range-based for を使い、各要素に参照でアクセスする
- ループ内で要素を書き換える場合、参照を使います
- `std::string` に対する range-based for ループは、文字列の要素数がループ中に変更されないという前提で実行されるため、ループ内でその文字列の要素数を変更する操作をしてはいけません
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";

	for (auto& ch : s) // 要素の個数だけ繰り返すループ、ch は順に各要素への参照
	{
		ch += 1;
	}

	std::cout << s << '\n';
}
```
```txt:出力
bqqmf
```


# 7. `std::string` の追加

## 7.1 前後に別の文字列を足した、新しい `std::string` を作成する
- `+` 演算子を使うと、前後に別の文字列を追加した新しい `std::string` を作成できます
- 元の文字列は変更されません
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "school";

	std::string t = ("high " + s); // 前に足した
	std::cout << t << '\n';
	std::cout << t.size() << '\n';

	std::string u = (s + " bus"); // うしろに足した
	std::cout << u << '\n';
	std::cout << u.size() << '\n';

	std::cout << ("my " + s) << '\n'; // 前に足した
}
```
```txt:出力
high school
11
school bus
10
my school
```

## 7.2 末尾に 1 文字追加する
- `.push_back(ch)` で、文字列の末尾に文字 `ch` を追加します
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "t";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.push_back('o'); // 末尾に `o` を追加
	std::cout << s << '\n';
	std::cout << s.size() << '\n';	

	s.push_back('p'); // 末尾に `p` を追加
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

## 7.3 先頭に 1 文字追加する
- `.insert(it, ch)` は、イテレータ `it` が指す位置の前に要素 `ch` を追加します
- 先頭位置のイテレータを返す `.begin()` と組み合わせることで、文字列の先頭に文字を追加できます
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "p";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.insert(s.begin(), 'o'); // 先頭に `o` を追加
	std::cout << s << '\n';
	std::cout << s.size() << '\n';	

	s.insert(s.begin(), 't'); // 末尾に `t` を追加
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

## 7.4 末尾に文字列を追加する
- `+= other;` で、文字列の末尾に文字列 `other` を追加します
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "t";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s += "ea"; // 末尾に "ea" を追加
	std::cout << s << '\n';
	std::cout << s.size() << '\n'; 

	std::string t = "cher";
	s += t; // 末尾に "cher" を追加
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


# 8. 要素の削除

## 8.1 末尾の要素を削除する
- `.pop_back()` は末尾の要素を 1 つ削除します
- 空の文字列で使うと範囲外アクセスになるため注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.pop_back(); // 末尾の要素を削除
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.pop_back(); // 末尾の要素を削除
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

## 8.2 先頭の要素を削除する
- `.erase(it)` は、イテレータ `it` が指す位置の要素を削除します
- 先頭位置のイテレータを返す `.begin()` と組み合わせることで先頭の文字を削除できます
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.erase(s.begin()); // 先頭の要素を削除
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.erase(s.begin()); // 先頭の要素を削除
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

## 8.3 文字列を消去する
- `.clear()` を使うと、全要素を削除して空の文字列になります
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.clear(); // 要素を全消去
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:出力
apple
5

0
```


# 9. `std::string` の一部の取得

## 9.1 指定した位置以降の文字列を取得する
- `.substr(pos)` は、`pos` 番目以降の文字列を新しい `std::string` として返します
- インデックスは 0 から数えます
- 範囲外アクセスに注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "computer";
	std::cout << s.substr(1) << '\n'; // 1 番目の文字以降
	std::cout << s.substr(2) << '\n'; // 2 番目の文字以降
	std::cout << s.substr(5) << '\n'; // 5 番目の文字以降
}
```
```txt:出力
omputer
mputer
ter
```

## 9.2 指定した位置以降、指定した個数文の文字列を取得する
- `.substr(pos, count)` は、`pos` 番目以降最大 `count` 個の文字列を新しい `std::string` として返します
- 第 1 引数 `pos` は範囲外アクセスに注意が必要です
- 第 2 引数 `count` は実際の要素数を超えた分は無視されます
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "computer";
	std::cout << s.substr(1, 3) << '\n'; // 1 番目以降最大 3 個
	std::cout << s.substr(2, 2) << '\n'; // 2 番目以降最大 2 個
	std::cout << s.substr(5, 100) << '\n'; // 5 番目以降最大 100 個
}
```
```txt:出力
omp
mp
ter
```


# 10. `std::string` の入れ替え

## 10.1 二つの `std::string` を交換する
- `std::string` 型の変数 `a`, `b` の中身を入れ替えるには `std::swap(a, b)` を使います
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::string t = "bird";

	std::swap(s, t); // 中身を交換

	std::cout << s << '\n';
	std::cout << t << '\n';
}
```
```txt:出力
bird
apple
```


# 11. `std::string` のイテレータ

削除や挿入、アルゴリズム関数で使うためのイテレータを、以下の関数で取得できます。イテレータを取得した時点から文字列のサイズが変更されると、そのイテレータは無効になることがあるため、イテレータを変数に保存して長い期間保持するのは避けるべきです。

## 11.1 先頭位置のイテレータを取得する
- `.begin()` は文字列の先頭位置を指すイテレータを返します 
- 空の文字列の場合 `.begin() == .end()` です

## 11.2 終端位置のイテレータを取得する
- `.end()` は文字列の終端位置を指すイテレータを返します
- 空の文字列の場合 `.begin() == .end()` です


# 12. 数値から `std::string` への変換

## 12.1 数値を `std::string` に変換する
- `std::to_string(n)` は、数 `n` を文字列に変換し、`std::string` 型で返します
```cpp
#include <iostream>
#include <string>

int main()
{
	int a, b;
	std::cin >> a >> b;
	
	std::string s = std::to_string(a + b);
	std::cout << s << '\n';
	std::cout << s.size() << '\n';
}
```
```txt:入力
300 800
```
```txt:出力
1100
4
```


# 13. 文字列から数値への変換

## 13.1 数が書かれた文字列をパースして整数に変換する
- `std::stoi(s, idx, base)` は、文字列 `s` をパースし `int` 型で返します
- `std::stoull(s, idx, base)` は、文字列 `s` をパースし `unsigned long long` 型で返します
- `s` に書かれている数が、変換後の型で表現できる範囲外の場合、`std::out_of_range` 例外が送出されます
```cpp
#include <iostream>
#include <string>
#include <cstdint> // std::uint64_t のため

int main()
{
	std::string s = "1200";
	std::string t = "9876543210";

	int ss = std::stoi(s); // int 型に変換
	std::uint64_t tt = std::stoull(t); // unsigned long long 型に変換

	std::cout << ss << '\n';
	std::cout << tt << '\n';
}
```
```txt:出力
1200
9876543210
```

## 13.2 二進数が書かれた文字列をパースして整数に変換する
- `std::stoi(s, idx, base)` は、文字列 `s` を `base` 進数とみなしてパースし `int` 型で返します。`idx` は今回は使いません
- `std::stoull(s, idx, base)` は、文字列 `s` を `base` 進数とみなしてパースし `unsigned long long` 型で返します。`idx` は今回は使いません
- `base` を `2` に、`idx` を `nullptr` にすることで、二進数 01 で書かれた文字列 `s` を十進数の数値に変換できます
- `s` に書かれている数が、変換後の型で表現できる範囲外の場合、`std::out_of_range` 例外が送出されます
```cpp
#include <iostream>
#include <string>
#include <cstdint> // std::uint64_t のため

int main()
{
	std::string s = "0111";
	std::string t = "1000";
	// 1 が 64 個
	std::string u = "1111111111111111111111111111111111111111111111111111111111111111";

	int ss = std::stoi(s, nullptr, 2); // int 型に変換
	int tt = std::stoi(t, nullptr, 2); // int 型に変換
	std::uint64_t uu = std::stoull(u, nullptr, 2); // unsigned long long 型に変換

	std::cout << ss << '\n';
	std::cout << tt << '\n';
	std::cout << uu << '\n';
}
```
```txt:出力
7
8
18446744073709551615
```
