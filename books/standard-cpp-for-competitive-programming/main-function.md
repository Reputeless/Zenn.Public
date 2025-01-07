---
title: "main 関数"
free: true
---

- 読み方: メイン関数

## 1. main 関数の役割
- C++ プログラムのエントリーポイントとなる関数
- C++ のプログラムは、必ず 1 個だけの main 関数を持つ
- 競技プログラミングでは、実装の大部分は main 関数またはそこから呼び出す関数の中に書くことになる

:::details エントリーポイント
- プログラムを実行したときに最初に呼び出される関数のこと
- エントリーポイントである関数が終了すると、プログラムも終了する
:::

## 2. main 関数の最小構成
- main 関数の最小構成は次のとおり

```cpp
int main()
{
	return 0;
}
```

- ここでの `int` は main 関数の戻り値の型を示す
- プログラムが成功して終了した場合に、`return 0;` と記述してプログラムが正常終了したことを示すことができる
	- `return 1;` のように 0 以外の値を記述すると、コンテストのジャッジシステムによってはエラーとして扱われ、不正解になることがある
- C++ の仕様では、main 関数で `return 0;` を省略した場合、自動的に末尾に `return 0;` が挿入されることになっているため、`return 0;` は書かなくてよい
- 次のように `return 0;` を省略した main 関数も正しい

```cpp
int main()
{

}
```

:::details これ以外の main 関数
- 次のような形式の main 関数もある
- `argc`, `argv` はコマンドライン引数を扱う際に使わるが、競技プログラミングでは標準入力からデータを受け取ることが多く、基本的には使わない
```cpp
int main(int argc, char *argv[])
{

}
```
:::


## 3. 実際の main 関数のコード例
- 次のコードは、実際の競技プログラミングの問題を解く main 関数の例

> [✅ AtCoder practice contest A - Welcome to AtCoder](https://atcoder.jp/contests/practice/tasks/practice_1)
```cpp
// 必要な標準ライブラリのヘッダファイルをインクルードする
#include <iostream>
#include <string>

int main()
{
	// 3 つの整数 a, b, c を受け取る
	int a, b, c;
	std::cin >> a >> b >> c;

	// 文字列 s を受け取る
	std::string s;
	std::cin >> s;
	
	// a + b + c と s を出力する
	std::cout << (a + b + c) << ' ' << s << '\n';
}
```

## 4. 明示的に `return 0;` を書く場合
- main 関数の途中でプログラムを早期に終了する目的で、main 関数に明示的に `return 0;` を書くことがある
- 次のコードは、`No` を出力した直後に `return 0;` でプログラムを終了する

> [✅ ABC205B - Permutation Check](https://atcoder.jp/contests/abc205/tasks/abc205_b)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	// 数列の長さ N を受け取る
	int N;
	std::cin >> N;

	// 数列 A を受け取る
	std::vector<int> A(N);
	for (auto& a : A)
	{
		std::cin >> a;
	}

	// 数列 A をソートする
	std::sort(A.begin(), A.end());

	// 数列が 1, 2, ... N となっているか判定する
	for (int i = 0; i < N; ++i)
	{
		// なっていない場合
		if (A[i] != (i + 1))
		{
			// No と出力してプログラムを終了する
			std::cout << "No\n";
			return 0;
		}
	}

	// Yes と出力する
	std::cout << "Yes\n";
}
```
