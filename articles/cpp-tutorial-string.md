---
title: "【C++17】競技プログラミングのための <string>"
emoji: "💬"
type: "tech"
topics: ["cpp", "競プロ"]
published: false
---

C++ 標準ライブラリヘッダ `<string>` の主要な機能を紹介します。
- 1～11: 文字列クラス `std::string` の機能
- 12: 数値を文字列に変換する関数
- 13: 文字列を数値に変換する関数

# 1. `std::string` の構築

## 1.1 文字列リテラルから構築する
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
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::string t = s;
	std::cout << t << '\n';
	std::cout << t.size() << '\n';
}
```
```txt:出力
abc
3
```

## 1.4 空の文字列
- `std::string` 型のデフォルトコンストラクタは要素数が 0 の空の文字列を構築します  
- 空の文字列は、とくに何の問題もなくプログラムで使うことができます
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

# 2. `std::string` の入力

## 2.1 標準入力
- 1 回の `std::cin` で、改行もしくは空白文字が出現するまでの入力をすべて読み取ります 
- 改行文字や空白文字は除去されます
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s, t, u;
	std::cin >> s >> t >> u;

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

## 2.2 半角スペースによって入力が区切られる
- 1 行の入力の途中に空白文字が含まれていると、入力の区切りと見なされます
- 空白文字は除去されます
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s, t;
 
	std::cin >> s;
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	std::cin >> t;
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

## 2.3 丸ごと 1 行を入力
- 空白文字を含む 1 行を丸ごと読み込みたい場合は `std::getline` を使います
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s;
	std::getline(std::cin, s);
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


# 3. `std::string` の要素数

## 3.1 要素数を調べる
- `.size()` は要素数を符号無し整数型の値で返します
- 同じ効果を持つメンバ関数 `.length()` もあります
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::cout << s.size() << '\n';

	s = "apple";
	std::cout << s.size() << '\n';

	size_t n = s.size();
	std::cout << n << '\n';
}
```
```txt:出力
3
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
	std::string s = "abc";
	std::string t;

	if (s.empty())
	{
		std::cout << "s is empty.\n";
	}

	if (t.empty())
	{
		std::cout << "t is empty.\n";
	}
}
```
```txt:出力
t is empty.
```


# 4. `std::string` の代入

## 4.1 別の文字列を代入する
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s = "apple";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	std::string t = "bird";
	s = t;
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
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::string t = "dog";

	std::cout << std::boolalpha; // bool 型の値を true / false で表示させる
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
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::string t = "dog";

	std::cout << std::boolalpha;
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

	std::cout << std::boolalpha;
	std::cout << (s < t) << '\n';
	std::cout << (s < "dog") << '\n';
	std::cout << ("caa" < s) << '\n';
	std::cout << ("Dog" < t) << '\n';-
}
```
```txt:出力
true
true
true
true
```


# 6. `std::string` の要素にアクセス

## 6.1 指定した位置の要素にアクセス
- `s[index]` で指定した位置の要素 (`char` 型) にアクセスできます 
- インデックスは最初の文字が `0`, その次が `1`, ..., `(.size() - 1)` です
- 範囲外にアクセスしてはいけません
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s[0] << '\n';
	std::cout << s[1] << '\n';
	std::cout << s[2] << '\n';

	s[2] = 'n';
	std::cout << s << '\n';
}
```
```txt:出力
c
a
t
can
```

## 6.2 先頭の要素にアクセス
- `s[0]` と同等です
- 空の文字列で使うと範囲外アクセスになるため注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s.front() << '\n';

	s.front() = 'h';
	std::cout << s << '\n';
}
```
```txt:出力
c
hat
```

## 6.3 末尾の要素にアクセス
- `s[(s.size() - 1)]` と同等です（ただし `0 < s.size()`）
- 空の文字列で使うと範囲外アクセスになるため注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s.back() << '\n';

	s.back() = 'r';
	std::cout << s << '\n';
}
```
```txt:出力
t
car
```

## 6.4 指定した位置の要素にアクセス（範囲外アクセスを例外で検出）
- `.at(index)` で指定した位置の要素 (`char` 型) にアクセスできます
- インデックスは最初の文字が `0`, その次が `1`, ..., `(.size() - 1)` です
- `s[index]` と異なり、範囲外アクセスがあった場合に `std::out_of_range` 例外を送出します。そのチェックのための追加のコストがあります
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "cat";
	std::cout << s.at(0) << '\n';
	std::cout << s.at(1) << '\n';
	std::cout << s.at(2) << '\n';

	s.at(2) = 'n';
	std::cout << s << '\n';
}
```
```txt:出力
c
a
t
can
```

## 6.5 range-based for を使い、各要素に `const` 参照でアクセス
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";

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

## 6.6 range-based for を使い、各要素に参照でアクセス
- 参照でアクセスすると、その要素を書き換えることができます
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";

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


# 7. `std::string` の追加

## 7.1 前後に別の文字列を足した、新しい `std::string` を作成する
- `+` 演算子を使うと、前後に別の文字列を追加した新しい文字列を作成できます
- 元の文字列は変更されません
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "school";

	std::string t = ("high " + s);
	std::cout << t << '\n';
	std::cout << t.size() << '\n';

	std::string u = (s + " bus");
	std::cout << u << '\n';
	std::cout << u.size() << '\n';

	std::cout << ("my " + s) << '\n';
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
- `.push_back(ch)` で、現在の文字列の末尾に文字 `ch` を追加します
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "t";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.push_back('o');
	std::cout << s << '\n';
	std::cout << s.size() << '\n';	

	s.push_back('p');
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

## 7.3 末尾に文字列を追加する
- `+= other;` で、現在の文字列の末尾に文字列 `other` を追加します
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "t";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s += "ea";
	std::cout << s << '\n';
	std::cout << s.size() << '\n'; 

	s += "cher";
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


# 8. `std::string` の削除

## 8.1 末尾の文字を削除
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

	s.pop_back();
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.pop_back();
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

## 8.2 先頭の文字を削除
- `.erase(it)` は、イテレータ `it` により指定した位置の要素を削除します
- 先頭位置のイテレータを返す `.begin()` と組み合わせることで先頭の文字を削除できます
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.erase(s.begin());
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.erase(s.begin());
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

## 8.3 文字列を消去
- `.clear()` を使うと、全要素を削除して空の文字列になります
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::cout << s << '\n';
	std::cout << s.size() << '\n';

	s.clear();
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

## 9.1 指定した位置以降の文字列を取得
- `.substr(pos)` は、`pos` 番目以降の文字列を新しい `std::string` として返します
- 範囲外アクセスに注意が必要です
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "computer";
	std::cout << s.substr(1) << '\n';
	std::cout << s.substr(2) << '\n';
	std::cout << s.substr(5) << '\n';
}
```
```txt:出力
omputer
mputer
ter
```

## 9.2 指定した位置以降、指定した文字数分の文字列を取得
- `.substr(pos, count)` は、`pos` 番目以降 `count` 文字の文字列を新しい `std::string` として返します
- 第 1 引数 `pos` は範囲外アクセスに注意が必要です
- 第 2 引数 `count` は実際の文字数を超えた分は無視されます
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "computer";
	std::cout << s.substr(1, 3) << '\n';
	std::cout << s.substr(2, 2) << '\n';
	std::cout << s.substr(5, 100) << '\n';
}
```
```txt:出力
omp
mp
ter
```


# 10. `std::string` の入れ替え

## 10.1 二つの `std::string` を交換
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "apple";
	std::string t = "bird";

	std::swap(s, t);
	std::cout << s << '\n';
	std::cout << t << '\n';
}
```
```txt:出力
bird
apple
```


# 11. `std::string` のイテレータ

削除や挿入、アルゴリズム関数で使うためのイテレータを以下の関数で取得できます。

## 11.1 先頭位置のイテレータを取得
- `.begin()` は文字列の先頭位置のイテレータを返します 
- 空の文字列の場合 `.begin() == .end()` です

## 11.2 終端位置のイテレータを取得
- `.end()` は文字列の終端位置のイテレータを返します
- 空の文字列の場合 `.begin() == .end()` です


# 12. 数値から `std::string` への変換

## 12.1 数値を `std::string` に変換
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

## 13.1 数が書かれた文字列をパースして整数に変換
- `std::stoi(s, idx, base)` は、文字列 `s` をパースし `int` 型に変換します
- `std::stoull(s, idx, base)` は、文字列 `s` をパースし `unsigned long long` 型に変換します
- 書かれている数が、変換後の型で表現できる範囲外の場合、`std::out_of_range` 例外が送出されます
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

## 13.2 二進数が書かれた文字列パースして整数に変換
- `std::stoi(s, idx, base)` は、文字列 `s` を `base` 進数とみなしてパースし `int` 型に変換します。`idx` は今回は使いません
- `std::stoull(s, idx, base)` は、文字列 `s` を `base` 進数とみなしてパースし `unsigned long long` 型に変換します。`idx` は今回は使いません
- `base` を `2` に、`idx` を `nullptr` にすることで、二進数が記述された文字列 `s` から数値を得ることができます
- 文字列で表現されている数が、変換後の型で表現できる範囲外の場合、`std::out_of_range` 例外が送出されます
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
