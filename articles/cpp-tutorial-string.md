---
title: "【C++17】競技プログラミングのための std::string"
emoji: "💬"
type: "tech"
topics: ["cpp"]
published: false
---

C++ 標準の文字列クラス `std::string` の基本機能をサンプルで説明します。  
`std::string` を使うには `#include <string>` が必要です。

# 1. 構築
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
	std::string t = a;
	std::cout << t << '\n';
	std::cout << t.size() << '\n';
}
```
```txt:出力
abc
3
```

## 1.4 空の文字列
デフォルトコンストラクタは要素数が 0 の空の文字列を構築します。  
空の文字列は、とくに何の問題もなくプログラムで使うことができます。
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

# 2. 入力

## 2.1 標準入力
1 回の `std::cin` で、改行もしくは空白文字が出現するまでの入力をすべて読み取ります。  
改行文字や空白文字は除去されます。
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
1 行の入力の途中に空白文字が含まれていると、入力の区切りと見なされます。  
空白文字は除去されます。
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
空白文字を含む 1 行を丸ごと読み込みたい場合は `std::getline` を使います。
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


# 3. 要素数

## 3.1  要素数を調べる
`.size()` は要素数を符号無し整数型の値で返します。
```cpp
#include <iostream>
#include <string>

int main()
{
	std::string s = "abc";
	std::cout << s.size() << '\n';

	s = "apple";
	std::cout << s.size() << '\n';

    size_t len = s.size();
    std::cout << len << '\n';
}
```
```txt:出力
3
5
5
```

## 3.2  空の文字列であるかを調べる
`.empty()` は、要素数が 0 の文字列（= 空の文字列）であるかを `bool` 型の値で返します。
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


# 4. 代入

## 別の文字列を代入する
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

# 5. 比較

## 同じ文字列であるかを調べる
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

## 違う文字列であるかを調べる
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

## 文字列の順序比較
参考: [ASCII コード表](https://www.k-cube.co.jp/wakaba/server/ascii_code.html)
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
	std::cout << ("Dog" < t) << '\n'; // A < Z < a < z であることに注意
}
```
```txt:出力
true
true
true
true
```


# 6. 要素にアクセス

## 指定した位置の要素にアクセス
インデックスは最初の文字が `0`, その次が `1`, ... `(.size() - 1)`。  
範囲外アクセスに注意が必要です。
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

## 先頭の要素にアクセス
空の文字列で使うと範囲外アクセスになるので注意が必要です。
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

## 末尾の要素にアクセス
空の文字列で使うと範囲外アクセスになるので注意が必要です。
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

## range-based for を使い、各要素に const 参照でアクセス
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

## range-based for を使い、各要素に参照でアクセス
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


# 7. 追加

## 文字列を追加した、新しい `std::string` を作成する
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

## 末尾に 1 文字追加する
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

## 末尾に文字列を追加する
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


# 8. 削除

## 末尾の文字を削除
空の文字列で使うと範囲外アクセスになるので注意が必要です。
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

## 	先頭の文字を削除
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

## 文字列を消去
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


# 9. 部分の取得

## 指定した位置以降の文字列を取得
範囲外アクセスに注意が必要です。
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

## 指定した位置以降、指定した文字数分の文字列を取得
第 1 引数は範囲外アクセスに注意が必要です。第 2 引数は実際の文字数を超えた分は無視されます。
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


# 10. イテレータ

削除や挿入、アルゴリズム関数で使うためのイテレータは以下の 2 つの関数で取得できます。

## 先頭位置のイテレータを取得
`.begin()` は文字列の先頭位置のイテレータを返します。  
空の文字列の場合 `.begin() == .end()` です。

## 終端位置のイテレータを取得
`.end()` は文字列の終端位置のイテレータを返します。  
空の文字列の場合 `.begin() == .end()` です。

