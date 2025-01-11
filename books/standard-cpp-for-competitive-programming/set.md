---
title: "std::set"
free: true
---

- 読み方: セット
- `<set>` ヘッダに含まれる

## 1. std::set の特徴
- 要素が重複しない集合を扱えるコンテナ。次のような特徴を持つ

- **要素の一意性**
	- すべての要素は重複することなく管理される
	- 同じ値を挿入しようとした場合は無視される
- **要素は自動でソートされる**
	- すべての要素は常にソートされた状態で保持される
	- デフォルトでは要素の型の `<` 演算子によってソートされる
- **各種操作の計算量は $O(\log N)$**
	- 挿入・検索・削除などの主要な操作は安定して $O(\log N)$ で行われる
	- 内部実装に自己平衡二分探索木（赤黒木など）を用いる
- **イテレータによる走査が可能**
	- イテレータを使って各要素にアクセスしたり、範囲を指定して削除したりできる
	- イテレータの種類は**双方向イテレータ**
- **要素への添え字アクセス `s[i]` はできない**
	- `std::vector` と異なり、インデックスによるランダムアクセスには対応しない

## 2. よく使う操作

| 操作 | 説明 | 計算量 |
|---|---|---|
| `.insert(value)` | 要素を追加（重複する場合は何もしない） | $O(\log N)$ |
| `.erase(value)` | 要素を削除（存在しない場合は何もしない） | $O(\log N)$ |
| `.erase(it)` | イテレータが指す要素を削除 | 償却定数時間 |
| `.find(value)` | 要素を指すイテレータを返す（存在しなければ `.end()`） | $O(\log N)$ |
| `.contains(value)` | 要素が存在するかどうかを返す | $O(\log N)$ |
| `.lower_bound(value)` | ソート順を壊さず `value` を挿入できる最も左のイテレータを返す | $O(\log N)$ |
| `.upper_bound(value)` | ソート順を壊さず `value` を挿入できる最も右のイテレータを返す | $O(\log N)$ |
| `.size()` | 要素数を返す | $O(1)$ |
| `.empty()` | 空であるかを返す | $O(1)$ |
| `.clear()` | 全要素を削除 | $O(N)$ |
| `.begin()`, `.end()` | 先頭・終端位置のイテレータを返す | $O(1)$ |

## 3. よく使うパターン

### 3.1 最小・最大の要素にアクセスする
- 最小の要素にアクセスするには、`.begin()` で取得したイテレータを参照する
- 最大の要素にアクセスするには、`.end()` の 1 つ前のイテレータを参照する

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s = { 3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5 };

	// 最小の要素にアクセスする
	std::cout << *s.begin() << '\n'; // 1

	// 最大の要素にアクセスする
	std::cout << *std::prev(s.end()) << '\n'; // 9
}
```

### 3.2 範囲 for 文で要素を走査する
- 範囲 for 文を使い、ソートされた順番で要素を走査できる
- 要素への参照は必ず const 参照になるため、要素の変更はできない

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s = { 3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5 };

	// 範囲 for 文で全要素を走査する
	for (const auto& elem : s)
	{
		std::cout << elem << ' '; // 1 2 3 4 5 6 9
	}

	std::cout << '\n';
}
```

### 3.3 別のコンテナから構築する
- イテレータで指定した範囲の要素から構築できる

```cpp
#include <iostream>
#include <vector>
#include <set>

int main()
{
	std::vector<int> v = { 3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5 };

	// 別のコンテナから構築する
	std::set<int> s(v.begin(), v.end());

	for (const auto& elem : s)
	{
		std::cout << elem << ' '; // 1 2 3 4 5 6 9
	}

	std::cout << '\n';
}
```

### 3.4 重複を確認しながら入力を追加する
- `.insert(x)` の戻り値は `std::pair<iterator, bool>`
- このペアの `.second` は、追加に成功したかを表す `bool` 値
	- 追加に成功した場合は `true`, 重複があった場合は `false`

```cpp
#include <iostream>
#include <set>

int main()
{
	int n;
	std::cin >> n;

	std::set<int> s;

	for (int i = 0; i < n; ++i)
	{
		int x;
		std::cin >> x;

		// .insert(x).second は挿入に成功したかを表す bool 値
		// 追加に成功したら true, 重複があった場合は false
		if (!s.insert(x).second) // 重複していたら
		{
			std::cout << "重複あり\n";
		}
	}
}
```


## 4. 使用上の注意
- `std::set` の要素の二分探索を行う場合、必ずメンバ関数版の `.lower_bound()`, `.upper_bound()` を使うこと
	- メンバ関数版の `.lower_bound()`, `.upper_bound()` は、`std::set` の内部実装にとって最適な探索を行うため、計算量が $O(\log N)$ で済む
	- 一方、`std::lower_bound()`, `std::upper_bound()` は `std::set` の双方向イテレータと相性が悪く、計算量が $O(N)$ に悪化する
- `k` 番目の要素を取得したい場合、`std::advance(it, k)` でイテレータを進めるという手段があるが、計算量が $O(k)$ かかるため避けるべき
- 要素数が小さければ、`std::set` の代わりに `std::vector` + `std::sort` + `std::unique` を使っても十分速い場合がある（キャッシュ効率が良い）


## 5. 構築方法

### 5.1 空の set を作成する

:::details コード
```cpp
#include <iostream>
#include <set>

int main()
{
	// 空の set を作成
	std::set<int> s;

	std::cout << "size: " << s.size() << '\n'; // size: 0

	for (const auto& elem : s)
	{
		std::cout << elem << ' ';
	}

	std::cout << '\n';
}
```
:::

### 5.2 初期化リストから作成する

:::details コード
```cpp
#include <iostream>
#include <set>

int main()
{
	// 初期化リストから set を作成
	std::set<int> s = { 3, 1, 4, 1, 5 };

	std::cout << "size: " << s.size() << '\n'; // size: 4

	for (const auto& elem : s)
	{
		std::cout << elem << ' '; // 1 3 4 5
	}

	std::cout << '\n';
}
```
:::

### 5.3 別の set をコピーして作成する

:::details コード
```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> t = { 3, 1, 4, 1, 5 };

	// 別の set をコピーして作成
	std::set<int> s = t;

	std::cout << "size: " << s.size() << '\n'; // size: 4

	for (const auto& elem : s)
	{
		std::cout << elem << ' '; // 1 3 4 5
	}

	std::cout << '\n';
}
```
:::

### 5.4 別のコンテナから要素をコピーして作成する

:::details コード
```cpp
#include <iostream>
#include <vector>
#include <set>

int main()
{
	std::vector<int> v = { 3, 1, 4, 1, 5 };

	// 別のコンテナから要素をコピーして作成
	std::set<int> s(v.begin(), v.end());

	std::cout << "size: " << s.size() << '\n'; // size: 4

	for (const auto& elem : s)
	{
		std::cout << elem << ' '; // 1 3 4 5
	}

	std::cout << '\n';
}
```
:::

### 5.5 空の set を作成し、要素を追加する

:::details コード
```cpp
#include <iostream>
#include <set>

int main()
{
	// 空の set に要素を追加する
	std::set<int> s;
	s.insert(3);
	s.insert(1);
	s.insert(4);
	s.insert(1);
	s.insert(5);

	std::cout << "size: " << s.size() << '\n'; // size: 4

	for (const auto& elem : s)
	{
		std::cout << elem << ' '; // 1 3 4 5
	}

	std::cout << '\n';
}
```
:::

## 6. おもなメンバ関数

### 🌟 要素数を返す | `.size()`
- 戻り値: `size_t`
- 計算量: $O(1)$

### 🌟 空であるかを返す | `.empty()`
- 戻り値: `bool`
- 計算量: $O(1)$

### 🌟 要素を追加する ① | `.insert(value)`
- 要素を追加する。すでに同じ値が存在する場合は追加しない
- 戻り値: `std::pair<iterator, bool>`
	- `iterator`: 挿入された（あるいは元々存在した）要素を指すイテレータ
	- `bool`: 追加に成功したかを表す `bool` 値
- 計算量: $O(\log N)$

### 要素を追加する ② | `.emplace(args...)`
- 引数を元に要素を構築して追加する。すでに同じ値が存在する場合は追加しない
- 要素が `std::pair` や `std::tuple` などの場合に ① よりも若干効率が良い
- 戻り値: `std::pair<iterator, bool>`
	- `iterator`: 挿入された（あるいは元々存在した）要素を指すイテレータ
	- `bool`: 追加に成功したかを表す `bool` 値
- 計算量: $O(\log N)$

:::details コード
```cpp
#include <iostream>
#include <set>
#include <utility>

int main()
{
	std::set<std::pair<int, int>> s;

	for (int i = 0; i < 10; ++i)
	{
		// s.insert({ i, (i * i) });

		s.emplace(i, (i * i));
	}
}
```
:::

### 🌟 要素を削除する ① | `.erase(value)`
- 指定した要素を削除する。存在しない場合は何もしない
- 戻り値: `size_t`: 削除した要素の個数（0 または 1）
- 計算量: $O(\log N)$

### 要素を削除する ② | `.erase(it)`
- イテレータが指す要素を削除する
- 戻り値: 削除された要素の次を指すイテレータ
- 計算量: 償却定数時間

### 要素を削除する ③ | `.erase(it1, it2)`
- イテレータで指定した範囲 `it1`～`it2` の要素を削除する
- 戻り値: 削除された要素の次を指すイテレータ
- 計算量: $O(N)$

### 全要素を削除する | `.clear()`
- すべての要素を削除して空にする
- 計算量: $O(N)$

### 指定した要素の個数を返す | `.count(value)`
- 指定した要素の個数（0 または 1）を返す
	- `.contains(value)` とほぼ同じ
- 戻り値: `size_t`: 要素があれば 1, なければ 0
- 計算量: $O(\log N)$

### 🌟 指定した要素が存在するかを返す | `.contains(value)`
- 指定した要素が存在するかを返す
- 戻り値: `bool`
- 計算量: $O(\log N)$
- C++20～

### 指定した要素へのイテレータを返す | `.find(value)`
- 指定した値の要素を指すイテレータを返す。存在しない場合は `.end()` を返す
- 戻り値: イテレータ
- 計算量: $O(\log N)$

### 🌟 二分探索で lower_bound のイテレータを返す | `.lower_bound(value)`
- ソート順を壊さず、`value` を挿入できる最も左のイテレータを返す
- 戻り値: イテレータ
- 計算量: $O(\log N)$

### 🌟 二分探索で upper_bound のイテレータを返す | `.upper_bound(value)`
- ソート順を壊さず、`value` を挿入できる最も右のイテレータを返す
- 戻り値: イテレータ
- 計算量: $O(\log N)$

### 別の set を代入する | `=`
- 計算量: 代入する set の要素数に対して $O(N)$

### 別の set と等しいかを比較する | `==`, `!=`
- すべての要素が等しいかどうかを比較する
- 戻り値の型: `bool`
- 計算量: 要素数が互いに異なれば $O(1)$, そうでない場合 $O(N)$ 

### 別の set と大小関係を比較する | `<`, `<=`, `>`, `>=`
- 先頭の要素から順に辞書順で比較した結果を返す
- 戻り値の型: `bool`
- 計算量: $O(N)$

### 🌟 先頭・終端位置のイテレータを返す | `.begin()`, `.end()`
- `.begin()` は先頭位置のイテレータを返す
- `.end()` は終端位置のイテレータを返す
- 戻り値: イテレータ
- 計算量: $O(1)$


## 7. 練習問題

> [✅ ABC236C - Route Map](https://atcoder.jp/contests/abc236/tasks/abc236_c)

:::details コード
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <set>

int main()
{
	// N 個の駅, M 個の急行停車駅
	int N, M;
	std::cin >> N >> M;

	// すべての駅名
	std::vector<std::string> S(N);
	for (auto& s : S)
	{
		std::cin >> s;
	}

	// 急行停車駅
	std::set<std::string> T;
	while (M--)
	{
		std::string s;
		std::cin >> s;
		T.insert(s);
	}

	// 各駅について
	for (const auto& s : S)
	{
		if (T.contains(s)) // 急行停車駅に含まれている場合
		{
			std::cout << "Yes\n";
		}
		else // 含まれていない場合
		{
			std::cout << "No\n";
		}
	}
}
```


> [✅ ABC294D - Bank](https://atcoder.jp/contests/abc294/tasks/abc294_d)

:::details コード
```cpp
#include <iostream>
#include <set>

int main()
{
	// N 人, Q 個のイベント
	int N, Q;
	std::cin >> N >> Q;

	// 呼ばれ中の人の集合
	std::set<int> called;

	// 次に呼ぶ人の番号
	int current = 1;

	while (Q--)
	{
		// イベントの種類
		int e;
		std::cin >> e;

		if (e == 1) // 呼ぶ
		{
			// 呼ばれ中の人の集合に追加する
			called.insert(current);

			// 番号を進める
			++current;
		}
		else if (e == 2) // 呼ばれている人が受付に行く
		{
			// 受付に行く人の番号
			int x;
			std::cin >> x;

			// 呼ばれ中の人の集合から削除する
			called.erase(x);
		}
		else
		{
			// 呼ばれ中の人のうち、最も番号が小さい人を出力する
			std::cout << *called.begin() << '\n';
		}
	}
}
```
:::


> [✅ ABC217D - Cutting Woods](https://atcoder.jp/contests/abc217/tasks/abc217_d)

:::details コード
```cpp
#include <iostream>
#include <iterator>
#include <set>

int main()
{
	// L メートルの木材、Q 個のクエリ
	int L, Q;
	std::cin >> L >> Q;

	// 切断位置を格納する set
	std::set<int> cutPoints;

	// 初期の切断位置として、木材の両端を追加する
	cutPoints.insert(0);
	cutPoints.insert(L);

	while (Q--)
	{
		int c, x;
		std::cin >> c >> x;

		if (c == 1) // クエリ「木材を x で切断」
		{
			// 切断位置を追加する
			cutPoints.insert(x);
		}
		else // クエリ「x を含む木材の長さを出力」
		{
			// x より大きい最小の切断位置を指すイテレータを取得する
			const auto itRight = cutPoints.lower_bound(x);
			
			// x 未満の最大の切断位置を指すイテレータ（itRight の 1 つ前）を取得する
			const auto itLeft = std::prev(itRight);

			// x を含む木材の長さを出力する
			std::cout << (*itRight - *itLeft) << '\n';
		}
	}
}
```
:::
