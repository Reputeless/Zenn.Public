---
title: "標準入出力"
free: true
---

## 1. 標準出力 | std::cout
- 読み方: シーアウト
	- character output
- `<iostream>` ヘッダに含まれる

### 1.1. 概要
- `std::cout` は標準出力ストリームに関連付けられたグローバルオブジェクト
- `std::cout` に対して `<<` 演算子を使って「出力可能な型」の値を送ると、その値が標準出力に出力される
- 出力時に改行は自動では行われない。改行したい場合は改行文字 `'\n'` を出力する
- `<<` を連続して使用し、複数の値を出力することができる

```cpp
#include <iostream>
#include <string>

int main()
{
	int a = 222;
	std::string s = "hello";

	std::cout << 111 << ' ' << a << '\n';
	std::cout << s << '\n';
	std::cout << "world\n";
}
```

### 1.2 出力可能な型
- デフォルトで `std::cout` に対して `<<` 演算子を使える型は限られる
	- 基本型のうち、`bool`, `char`, `signed char`, `unsigned char`, `short`, `unsigned short`, `int`, `unsigned int`, `long`, `unsigned long`, `long long`, `unsigned long long`, `float`, `double`, `long double`, `std::nullptr_t`
	- ポインタ型
	- 標準ライブラリのうち、`std::string`, `std::bitset`, `std::string_view` など
- タプルやコンテナの多くは、標準ではそのまま出力できない
	- 出力可能な要素を個別に出力する必要がある

```cpp
#include <iostream>
#include <vector>

int main()
{
	std::vector<int> v = { 1, 2, 3, 4, 5 };

	//std::cout << v << '\n'; // コンパイルエラー

	for (const auto& elem : v)
	{
		std::cout << elem << ' ';
	}

	std::cout << '\n';
}
```

- クラスに対して `<<` 演算子をオーバーロードすると、`std::cout` で直接出力できるようになる

```cpp
#include <iostream>
#include <string>

struct Point
{
	int x;
	int y;

	friend std::ostream& operator <<(std::ostream& os, const Point& p)
	{
		return os << '(' << p.x << ", " << p.y << ')';
	}
};

int main()
{
	Point p{ 1, 2 };
	std::cout << p << '\n'; // (1, 2)
}
```

### 1.3 出力形式が特殊な型
- `bool` 型
	- `false` → `0`, `true` → `1` のように数字として出力される
	- `std::boolalpha` マニピュレータを使うことで `false` と `true` を出力できる（「4. マニピュレータ」参照）
- `char`, `signed char`, `unsigned char` 型
	- ASCII 表に基づいて、整数の代わりに文字が出力される
	- 印字できない範囲の文字（制御文字など）を出力すると、何も表示されないか、文字化けのような記号が表示される
	- 整数として出力した場合は、`static_cast<int>(ch)` で明示的に `int` 型にキャストする
- ポインタ型
	- ポインタの値（メモリアドレス）が出力される
- `char*`, `signed char*`, `unsigned char*` 型
	- 先頭の要素からヌル終端文字まで文字列として出力される
	- メモリアドレスを出力したい場合は、`static_cast<const void*>(ptr)` で明示的に `const void*` 型にキャストする
- 浮動小数点数型（`float`, `double`, `long double`）
	- デフォルトでは小数部と整数部合わせて有効桁が 6 桁程度に収まるよう調整される
		- `3.14159265358979323846` → `3.14159`
		- 省略された桁は四捨五入される `100.0005` → `100.001`
	- 小数点以下に 0 が続く場合、省略される `1.0` → `1`
	- 表示桁数によっては指数表記になる `1234567.8` → `1.23457e+06`
	- NaN（Not a Number）や Inf（Infinity）は、`nan`, `inf` として出力される
	- 問題設定によっては、小数点以下を指定桁数表示することが求められるケースがある
		- `std::fixed`, `std::setprecision(n)` を組み合わせて対処する（「4. マニピュレータ」参照）

### 1.4 標準出力のコスト
- 標準出力の処理は、一般的な計算処理よりもコストが大きい
- 競技プログラミングのように、短い実行時間や大量の入出力が求められる環境では、標準出力が、相対的に大きな実行時間を消費することがある
	- 「3. 標準入出力の高速化」で述べる方法で、ある程度は高速化できる

### 1.5 バッファリングとフラッシュ
- `std::cout` は、表示する内容を、まず内部で確保している配列に蓄積し、一定量たまったらその内容をまとめて出力する
	- それぞれ「バッファリング」、「フラッシュ」と呼ぶ
	- バッファリングのおかげで、頻繁に `std::cout` を使う場合の効率が向上する
- `std::cout << std::flush;` で明示的にフラッシュできる
- `std::cout << std::endl;` は「改行文字の出力 + フラッシュ」を行う
	- `std::cout << '\n' << std::flush;` と同じ
- C++ では、プログラムが正常終了するときに、必ず自動でフラッシュが行われる
- 競技プログラミングでは、フラッシュが行われないと出力がジャッジされないが、終了時の自動フラッシュがあるため、**通常の問題では明示的にフラッシュする必要はない**
	- **頻繁にフラッシュをすると実行時間が遅くなる**ため、改行は `std::endl`（改行 + フラッシュ）ではなく、改行文字 `'\n'` を使う

### 1.6 コミュニケーション（インタラクティブ）形式の問題におけるフラッシュ
- コンテストによっては、複数のプログラム間で通信する形式の問題（「コミュニケーション形式」や「インタラクティブ形式」と呼ばれる）が出題される
	- この場合、提出プログラムの途中で明示的にフラッシュしないと、他のプログラムに出力が送信されず、通信が進行不能になることがある
	- とくに、「3. 標準入出力の高速化」で述べる「`std::cin` 時のフラッシュの無効化」を使っている場合、この問題が発生する
	- コミュニケーション形式の問題では、出力完了時に `std::flush` または `std::endl` を使って、必ずフラッシュするよう心がける
- コミュニケーション形式の問題の例:
	- 次のコミュニケーション形式の問題の解答コードでは、通常の `\n` の代わりに `std::endl` を使っている
	- 「`std::cin` 時のフラッシュの無効化」を使っていなければ、厳密には `std::endl` は不要だが、すべてのジャッジ環境がそうであるとは限らないため、コミュニケーション形式の問題では、出力完了時に常に `std::endl` を使う

> [✅ ABC244C - Yamanote Line Game](https://atcoder.jp/contests/abc244/tasks/abc244_c)

:::details コード
```cpp
#include <iostream>
#include <set>

int main()
{
	int N;
	std::cin >> N;
	
	// まだ宣言されていない整数の集合
	std::set<int> remainings;

	// 1 から 2N+1 までを remainings に追加する
	for (int i = 1; i <= (2 * N + 1); ++i)
	{
		remainings.insert(i);
	}

	for (;;)
	{
		// 集合の中で最小の整数を高橋君が宣言する
		std::cout << *remainings.begin() << std::endl; // '\n' でなく std::endl を使う
		
		// 宣言した整数を削除する
		remainings.erase(remainings.begin());

		// すべての整数が宣言されたら終了する
		if (remainings.empty())
		{
			return 0;
		}

		// 青木君が宣言した整数を受け取る
		int aoki;
		std::cin >> aoki;

		// 青木君が宣言した整数を削除する
		remainings.erase(aoki);
	}
}
```
:::


## 2. 標準入力 | std::cin
- 読み方: シーイン
	- character input
- `<iostream>` ヘッダに含まれる

### 2.1. 概要
- `std::cin` は標準入力ストリームに関連付けられたグローバルオブジェクト
- `std::cin` から `>>` 演算子を「入力可能な型」の変数に向けると、その変数に標準入力からの値が格納される

### 2.2 入力可能な型
- デフォルトで `std::cin` からの `>>` 演算子を使える型は限られる
	- 基本型のうち、`bool`, `char`, `signed char`, `unsigned char`, `short`, `unsigned short`, `int`, `unsigned int`, `long`, `unsigned long`, `long long`, `unsigned long long`, `float`, `double`, `long double`
	- 標準ライブラリのうち、`std::string` など
- タプルやコンテナの多くは、標準ではそのまま直接入力はできない
	- ループなどを使って要素に対して個別に入力する必要がある

```cpp
#include <iostream>
#include <vector>

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> v(N);
	for (auto& elem : v)
	{
		std::cin >> elem;
	}
}
```

### 2.3 入力の区切り
- 標準入力で複数の整数を与える場合、整数どうしは空白文字（半角スペース、タブ、改行）で区切る
- 次のコードに対しては、以下のいずれの入力も正しい

```cpp
#include <iostream>

int main()
{
	int a, b, c;
	std::cin >> a >> b >> c;
}
```
```
1 2 3
```
```
1
2
3
```
```
1
2	3
```

### 2.4 標準入力のコスト
- 標準入力の処理は、一般的な計算処理よりもコストが大きい
- `std::cin` の使用が、デフォルトでは `std::cout` のフラッシュを実行することも一因
- 競技プログラミングのように、短い実行時間や大量の入出力が求められる環境では、標準入力が、相対的に大きな実行時間を消費することがある
	- 「3. 標準入出力の高速化」で述べる方法で、ある程度は高速化できる


## 3. 標準入出力の高速化

### 3.1 方法
- 次のような方法がある
	- ① `std::cin` が自動で `std::cout` のフラッシュを実行することを無効化する
		- `std::cin.tie(nullptr);`
	- ② `std::cout` や `std::cin` が、C 言語の入出力関数 `printf`, `scanf` とバッファを同期することを無効化する
		- `std::ios_base::sync_with_stdio(false);`
- ① と ② の設定を、プログラムで標準入出力を行う前に 1 回だけ行うことで、以降の標準入出力の処理時間を短縮できる

```cpp
#include <iostream>

int main()
{
	std::cin.tie(nullptr);
	std::ios_base::sync_with_stdio(false);
	
	// 以降に標準入出力
}
```

- 次のように 1 行に短くまとめて書くこともできる

```cpp
#include <iostream>

int main()
{
	std::cin.tie(0)->sync_with_stdio(0);
	
	// 以降に標準入出力
}
```

### 3.2 標準入出力の高速化の注意点
- ①, ② の方法で標準入出力を高速化すると、次のようなリスクが発生する
	- ① を適用すると、`std::cin` が `std::cout` のフラッシュを実行しなくなるため、コミュニケーション形式の問題で出力が送信されなくなる
		- 出力の完了時に明示的にフラッシュして対処する
	- ② を適用すると、C++ の標準入出力と C 言語の入出力関数が混在するプログラムで、入出力の順序に不整合が生じることがある
		- C 言語の入出力関数を使わないようにして対処する

## 4. マニピュレータ
- 出力ストリームに対して「マニピュレータ」と呼ばれるオブジェクトを送ることで、特定の設定を適用できる
- 設定は、それ以降のすべての出力に適用される

```cpp
std::cout << マニピュレータ;
```

- 改行を出力するマニピュレータ「`std::endl`」を除き、実際に何らかの文字を出力するわけではない


### 4.1 フラッシュする | `std::flush`
- 読み方: フラッシュ
- `<iostream>` ヘッダに含まれる

### 4.2 改行を出力してフラッシュする | `std::endl`
- 読み方: エンドエル
- `<iostream>` ヘッダに含まれる

### 4.3 bool 型を文字列で出力する | `std::boolalpha`
- 読み方: ブールアルファ
- `<iostream>` ヘッダに含まれる

```cpp
#include <iostream>

int main()
{
	std::cout << std::boolalpha;
	std::cout << false << ' ' << true << '\n'; // false true
}
```

### 4.4 浮動小数点数の出力精度を設定する | `std::setprecision(n)`
- 読み方: セットプレシジョン
- `<iomanip>` ヘッダに含まれる
- デフォルトでは 6
- 出力精度の変更によって、`float` 型や `double` 型が本来表現できる以上の計算精度が実現されるわけではない

### 4.5 浮動小数点数の出力形式を固定小数点にする | `std::fixed`
- 読み方: フィクスト
- `<iostream>` ヘッダに含まれる
- 小数点以下を、`std::setprecision(n)` で指定した出力精度 n 桁で表示するようになる

```cpp
#include <iostream>
#include <iomanip>
#include <numbers>

int main()
{
	std::cout << std::numbers::pi << '\n'; // 3.14159

	std::cout << std::fixed << std::setprecision(10);

	std::cout << std::numbers::pi << '\n'; // 3.1415926536

	std::cout << std::fixed << std::setprecision(3);

	std::cout << 123.4567 << '\n'; // 123.457
}
```


## 5. 標準エラー出力 | `std::clog`, `std::cerr`
- 読み方: シーログ, シーエラー
	- character log, character error
- `<iostream>` ヘッダに含まれる

### 5.1. 概要
- `std::clog` および `std::cerr` は標準エラー出力ストリームに関連付けられたグローバルオブジェクト
-  `<<` 演算子を使って「出力可能な型」の値を送ると、その値が標準エラー出力に出力される
- 通常、標準エラー出力はジャッジの対象外となるため、デバッグ情報を標準エラー出力することで、ジャッジ用の出力には影響を与えずに追加の情報を確認できる
- `std::cerr` はバッファリング無し、`std::clog` はバッファリングあり

### 5.2 標準エラー出力のコスト
- 標準エラー出力の処理は、標準出力と同様に、一般的な計算処理よりもコストが大きい
- **TLE を防ぐために、最終的な提出プログラムからは、標準エラー出力を行うコードは除去またはコメントアウトする**ことが望ましい
- コミュニケーション形式の問題の例:
	- 次のコミュニケーション形式の問題の解答コードでは、`std::clog` を使って、進行状況を標準エラー出力に出力している
	- 標準エラー出力はジャッジの対象外であるため、ジャッジ内容に影響を与えずにデバッグ情報を確認できる

> [✅ ABC244C - Yamanote Line Game](https://atcoder.jp/contests/abc244/tasks/abc244_c)

:::details コード
```cpp
#include <iostream>
#include <set>

int main()
{
	int N;
	std::cin >> N;

	// まだ宣言されていない整数の集合
	std::set<int> remainings;

	// 1 から 2N+1 までを remainings に追加する
	for (int i = 1; i <= (2 * N + 1); ++i)
	{
		remainings.insert(i);
	}

	for (;;)
	{
		// 集合の中で最小の整数を高橋君が宣言する
		std::cout << *remainings.begin() << std::endl;

		// 宣言した整数を削除する
		remainings.erase(remainings.begin());

		// 残りの整数の数を標準エラー出力で可視化する
		std::clog << "remainings.size(): " << remainings.size() << std::endl;

		// すべての整数が宣言されたら終了する
		if (remainings.empty())
		{
			return 0;
		}

		// 青木君が宣言した整数を受け取る
		int aoki;
		std::cin >> aoki;

		// 青木君が宣言した整数を削除する
		remainings.erase(aoki);
	}
}
```
:::


## 6. 入出力のリダイレクト

### 6.1 リダイレクトとは
- 通常、プログラムの標準入力はキーボード、標準出力は画面（コンソール）に接続される
- リダイレクトは、プログラムの標準入出力の接続先を変更する機能
  - 例：標準入力元をファイルに切り替え、ファイルからデータを読み込む
  - 例：標準出力先をファイルに切り替え、プログラムの出力をファイルに保存する
- 競技プログラミングでは、ローカルでテストをするときにリダイレクトを活用すると便利な場面がある

### 6.2 リダイレクトの例

#### 入力をファイルから読み込む
- コマンドライン上で、実行ファイルの後ろに `< 入力ファイル` と書くと、標準入力元がそのファイルになる 

```bash
./a.out < input.txt
```

- これによって、`std::cin` の入力はすべて `input.txt` の内容から読み込まれる

#### 出力をファイルに書き込む
- コマンドライン上で、`> 出力ファイル` と書くことで、標準出力先がそのファイルになる 

```bash
./a.out > output.txt
```

- これによって、`std::cout` の出力は画面ではなく `output.txt` に書き込まれる

#### 入力と出力を同時にリダイレクトする
- 入出力の両方をファイルに変更できる

```bash
./a.out < input.txt > output.txt
```

- これによって、`std::cin` は `input.txt`、`std::cout` は `output.txt` を使うようになる

### 6.3 リダイレクトを使ったローカルテストの例

#### 1. テスト用ファイルの作成
- テストしたい入力を `input.txt` にまとめておく

```
5
1 2 3 4 5
```

#### 2. プログラムをコンパイル

```cpp
#include <iostream>
#include <vector>

int main()
{
	int N;
	std::cin >> N;

	std::vector<int> v(N);
	for (auto& elem : v)
	{
		std::cin >> elem;
	}

	for (const auto& elem : v)
	{
		std::cout << (elem * 10) << ' ';
	}

	std::cout << '\n';
}
```

#### 3. リダイレクトで実行
```bash
./a.out < input.txt > output.txt
```

#### 4. 結果確認
- `output.txt` に正しい出力が書き込まれているかを確認する

### 6.4 まとめ
- リダイレクトを使うことで、コンソールへの手入力や画面への出力の代わりに、ファイルを入力元・出力先として使うことができる 
- 標準入出力を使っている C++ コードであれば、プログラム側に変更を加えずにリダイレクトを活用できる
