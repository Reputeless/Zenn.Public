---
title: "std::set"
free: true
---

- 読み方: セット
- `<set>` ヘッダに含まれる
- **重複しない要素**を**常にソートされた状態**で管理する連想コンテナ
- `std::set<T>` は、型 `T` の要素を持つ集合を表す
- 競技プログラミングでは、**値の集合管理**、**存在判定**、**ソート済みデータに対する二分探索**などでよく使われる

## 1. std::set の特徴

#### 重複する要素は持たない
- 同じ値を複数持つことはない
- すでに入っている値をもう一度追加しようとしても、追加されない

#### 要素は自動でソートされる
- すべての要素は常にソートされた状態で保持される
- デフォルトでは要素の型の `<` 演算子による昇順でソートされる

#### 各種操作の計算量は $O(\log N)$
- 挿入・検索・削除などの主要な操作は安定して $O(\log N)$ で行われる
- 内部実装に自己平衡二分探索木（赤黒木など）を用いる

#### イテレータによる走査が可能
- イテレータを使って各要素にアクセスしたり、範囲を指定して削除したりできる
- イテレータの種類は**双方向イテレータ**

#### 格納した要素は変更できない
- 要素を書き換えると要素の順序が壊れるため、格納済みの要素は変更できない（`const` 扱い）
- 値を変更したい場合は、いったん削除して新しい値を挿入する

#### 要素への添え字アクセス `s[i]` はできない
- `std::vector` と異なり、インデックスによるランダムアクセスには対応しない

## 2. よく使う操作

| 操作 | 説明 | 計算量 | 重要度 |
|---|---|---|---|
| `.insert(value)` | 要素を追加する（重複する場合は何もしない） | $O(\log N)$ | ★★★ |
| `.emplace(args...)` | 引数から要素を構築して追加する（重複する場合は何もしない） | $O(\log N)$ | ★ |
| `.erase(value)` | 値を指定して削除する（存在しない場合は何もしない） | $O(\log N)$ | ★★★ |
| `.erase(it)` | イテレータが指す要素を削除する | 償却定数時間 | ★ |
| `.erase(it1, it2)` | イテレータで指定した範囲の要素を削除する | $O(N)$ |  |
| `.clear()` | 全要素を削除する | $O(N)$ | ★ |
| `.contains(value)` | 要素が存在するかを返す | $O(\log N)$ | ★★★ |
| `.count(value)` | 要素の個数（`0` または `1`）を返す | $O(\log N)$ |  |
| `.find(value)` | 要素を指すイテレータを返す（存在しなければ `.end()`） | $O(\log N)$ |  |
| `.lower_bound(value)` | ソート順を壊さず `value` を挿入できる最も左の位置のイテレータを返す | $O(\log N)$ | ★ |
| `.upper_bound(value)` | ソート順を壊さず `value` を挿入できる最も右の位置のイテレータを返す | $O(\log N)$ | ★ |
| `.size()` | 要素数を返す | $O(1)$ | ★★ |
| `.empty()` | 空であるかを返す | $O(1)$ | ★★★ |
| `.begin()`, `.end()` | 先頭・終端位置のイテレータを返す | $O(1)$ | ★★ |
| `==`, `!=` | すべての要素が等しいかを比較する | $O(N)$ |  |
| `<`, `<=`, `>`, `>=` | 辞書順で比較する | $O(N)$ |  |

### 構築方法

:::details 空の set を構築する

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s; // 空の set を構築する
}
```
:::

:::details 初期化リストから構築する

```cpp
#include <iostream>
#include <set>

int main()
{
	// 重複は除かれ、ソートされた状態で構築される
	std::set<int> s = { 3, 1, 4, 1, 5 }; // { 1, 3, 4, 5 }
}
```
:::

:::details 別の set をコピーして構築する

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> t = { 3, 1, 4, 1, 5 };
	std::set<int> s = t; // t をコピーして構築する
}
```
:::

:::details 別のコンテナから構築する
- イテレータで指定した範囲の要素から構築できる

```cpp
#include <iostream>
#include <vector>
#include <set>

int main()
{
	std::vector<int> v = { 3, 1, 4, 1, 5 };

	// vector の要素から構築する（重複は除かれ、ソートされる）
	std::set<int> s(v.begin(), v.end()); // { 1, 3, 4, 5 }
}
```
:::

:::details 空の set を構築してから要素を追加する

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s;
	s.insert(3); // { 3 }
	s.insert(1); // { 1, 3 }
	s.insert(4); // { 1, 3, 4 }
	s.insert(1); // { 1, 3, 4 }（重複は無視される）
}
```
:::

### メンバ関数

:::details std::pair<iterator, bool> insert(const T& value)
- 要素を追加する。すでに同じ値が存在する場合は追加しない
- 戻り値は `std::pair<iterator, bool>`
	- `.first`: 挿入された（あるいは元々存在した）要素を指すイテレータ
	- `.second`: 追加に成功したかを表す `bool` 値（成功なら `true`、重複なら `false`）

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s;

	if (s.insert(10).second) // 10 を追加する
	{
		std::cout << "10 を追加しました\n";
	}
	else
	{
		std::cout << "10 はすでに存在しています\n";
	}

	if (s.insert(10).second) // 10 をもう一度追加する
	{
		std::cout << "10 を追加しました\n";
	}
	else
	{
		std::cout << "10 はすでに存在しています\n";
	}
}
```
```txt:出力
10 を追加しました
10 はすでに存在しています
```
:::

:::details emplace(Args&&... args)
- 引数を元に要素を直接構築して追加する。すでに同じ値が存在する場合は追加しない
- 要素が `std::pair` や `std::tuple` などの場合に `insert` よりも若干効率が良い
- 戻り値は `insert` と同じく `std::pair<iterator, bool>`
	- `.first`: 挿入された（あるいは元々存在した）要素を指すイテレータ
	- `.second`: 追加に成功したかを表す `bool` 値（成功なら `true`、重複なら `false`）

```cpp
#include <iostream>
#include <set>
#include <utility>

int main()
{
	std::set<std::pair<int, int>> s;
	s.emplace(1, 2); // set は { (1, 2) }
	s.emplace(3, 4); // set は { (1, 2), (3, 4) }
}
```
:::

:::details size_t erase(const T& value)
- 値を指定して要素を削除する。存在しない場合は何もしない
- 戻り値は削除した要素の個数（`0` または `1`）

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s = { 1, 2, 3 };

	if (s.erase(2)) // 2 を削除する。set は { 1, 3 } になる
	{
		std::cout << "2 を削除しました\n";
	}
	else
	{
		std::cout << "2 は存在しません\n";
	}

	if (s.erase(9)) // 9 を削除する。set は { 1, 3 } のまま
	{
		std::cout << "9 を削除しました\n";
	}
	else
	{
		std::cout << "9 は存在しません\n";
	}
}
```
```txt:出力
2 を削除しました
9 は存在しません
```
:::

:::details iterator erase(iterator pos)
- イテレータが指す要素を削除する
- 戻り値は削除された要素の次を指すイテレータ

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s = { 1, 2, 3 };

	const auto it = s.find(2);
	s.erase(it); // set は { 1, 3 }
}
```
:::

:::details iterator erase(iterator first, iterator last)
- イテレータで指定した範囲 `[first, last)` の要素を削除する
- 戻り値は削除された要素の次を指すイテレータ

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s = { 1, 2, 3, 4, 5 };

	const auto first = s.find(3);
	s.erase(first, s.end()); // [3, 5) を削除する。set は { 1, 2 }
}
```
:::

:::details void clear()
- すべての要素を削除して空にする

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s = { 1, 2, 3 };
	s.clear(); // set は空

	std::cout << s.size() << '\n'; // 0
}
```
:::

:::details bool contains(const T& value)
- 指定した要素が存在するかを返す

```cpp
#include <iostream>
#include <set>

int main()
{
	std::cout << std::boolalpha;

	std::set<int> s = { 1, 2, 3 };

	std::cout << s.contains(2) << '\n'; // true
	std::cout << s.contains(9) << '\n'; // false
}
```
:::

:::details size_t count(const T& value)
- 指定した要素の個数（`0` または `1`）を返す
- C++20 以降では `.contains(value)` が使えるようになり、不要になった

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s = { 1, 2, 3 };

	std::cout << s.count(2) << '\n'; // 1
	std::cout << s.count(9) << '\n'; // 0
}
```
:::

:::details iterator find(const T& value)
- 指定した値の要素を指すイテレータを返す。存在しない場合は `.end()` を返す
- 「存在するか」だけでなく「どこにあるか」が必要なときに使う

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s = { 1, 2, 3 };

	if (const auto it = s.find(2); it != s.end())
	{
		std::cout << *it << '\n'; // 2
	}
}
```
:::

:::details iterator lower_bound(const T& value)
- ソート順を壊さず `value` を挿入できる最も左の位置のイテレータを返す

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s = { 10, 20, 30, 40 };

	std::cout << *s.lower_bound(20) << '\n'; // 20
	std::cout << *s.lower_bound(25) << '\n'; // 30
}
```
:::

:::details iterator upper_bound(const T& value)
- ソート順を壊さずに `value` を挿入できる最も右の位置のイテレータを返す

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s = { 10, 20, 30, 40 };

	std::cout << *s.upper_bound(20) << '\n'; // 30
	std::cout << *s.upper_bound(25) << '\n'; // 30
}
```
:::

:::details size_t size()
- 要素数を返す

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s;
	std::cout << s.size() << '\n'; // 0

	s.insert(1); // { 1 }
	s.insert(2); // { 1, 2 }

	std::cout << s.size() << '\n'; // 2
}
```
:::

:::details bool empty()
- 空であるかを返す
- 空の場合は `true`、そうでない場合は `false` を返す

```cpp
#include <iostream>
#include <set>

int main()
{
	std::cout << std::boolalpha;

	std::set<int> s;
	std::cout << s.empty() << '\n'; // true

	s.insert(1); // { 1 }
	std::cout << s.empty() << '\n'; // false
}
```
:::

:::details iterator begin(), iterator end()
- `.begin()` は先頭位置のイテレータを返す
- `.end()` は終端位置のイテレータを返す

```cpp
#include <iostream>
#include <iterator>
#include <set>

int main()
{
	std::set<int> s = { 3, 1, 2 };

	std::cout << *s.begin() << '\n';          // 1（最小の要素）
	std::cout << *std::prev(s.end()) << '\n'; // 3（最大の要素）
}
```
:::

### 比較演算

:::details ==, !=
- すべての要素が等しいかどうかを比較した結果を `bool` 型で返す

```cpp
#include <iostream>
#include <set>

int main()
{
	std::cout << std::boolalpha;

	std::set<int> s1 = { 1, 2, 3 };
	std::set<int> s2 = { 1, 2, 3 };
	std::set<int> s3 = { 1, 2 };

	std::cout << (s1 == s2) << '\n'; // true
	std::cout << (s1 == s3) << '\n'; // false
}
```
:::

:::details <, <=, >, >=
- 先頭の要素から順に辞書順で比較した結果を `bool` 型で返す

```cpp
#include <iostream>
#include <set>

int main()
{
	std::cout << std::boolalpha;

	std::set<int> s1 = { 1, 2, 3 };
	std::set<int> s2 = { 1, 2, 4 };
	std::set<int> s3 = { 1, 2 };

	std::cout << (s1 < s2) << '\n'; // true
	std::cout << (s1 > s3) << '\n'; // true
}
```
:::

### 範囲 for 文

:::details 範囲 for 文で要素を走査する
- 範囲 `for` 文を使い、ソートされた順番で要素を走査できる
- 要素への参照は必ず `const` 参照になるため、要素の変更はできない

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s = { 3, 1, 4, 1, 5 };

	// 範囲 for 文で全要素を走査する
	for (const auto& elem : s)
	{
		std::cout << elem << ' ';　// 1 3 4 5
	}

	std::cout << '\n';
}
```
:::


## 3. 使い方のポイント

### 3.1 二分探索はメンバ関数版を使う
- `std::set` に対して二分探索を行う場合は、必ずメンバ関数版の `.lower_bound()`、`.upper_bound()` を使う
- メンバ関数版は `std::set` の内部実装を考慮した最適な探索を行うため、計算量が $O(\log N)$ で済む
- 一方、汎用版の `std::lower_bound()`、`std::upper_bound()` は、`std::set` の双方向イテレータと相性が悪く、計算量が $O(N)$ に悪化する

```cpp
const auto it = s.lower_bound(x);                           // ✅ O(log N)
// const auto it = std::lower_bound(s.begin(), s.end(), x); // ❌ O(N) に悪化
```

### 3.2 i 番目の要素へのアクセスは苦手
- `i` 番目の要素を取得したい場合、`std::advance(it, i)` でイテレータを進める手段があるが、計算量が $O(i)$ かかるため避けるべき

### 3.3 要素数が小さいときは vector + sort + unique も検討する
- 要素数が小さければ、`std::set` の代わりに `std::vector` + `std::sort` + `std::unique` を使っても十分速い場合がある（キャッシュ効率が良い）
- 構築後に集合を変更しないケースでは特に有効

## 4. よく使うパターン

### 4.1 最小・最大の要素にアクセスする
- **最小の要素**にアクセスするには、`.begin()` で取得したイテレータが指す要素を参照する（`*s.begin()`）
- **最大の要素**にアクセスするには、`.end()` の 1 つ前のイテレータが指す要素を参照する（`*std::prev(s.end())`）

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

### 4.2 範囲 for 文で要素を走査する
- 範囲 `for` 文を使い、ソートされた順番で要素を走査できる
- 要素への参照は必ず `const` 参照になるため、要素の変更はできない

```cpp
#include <iostream>
#include <set>

int main()
{
	std::set<int> s = { 3, 1, 4, 1, 5 };

	// 範囲 for 文で全要素を走査する
	for (const auto& elem : s)
	{
		std::cout << elem << ' ';　// 1 3 4 5
	}

	std::cout << '\n';
}
```

### 4.3 重複を確認しながら入力を追加する
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


## 5. 練習問題

:::details ABC415 A - Unsupported Type
### [ABC415 A - Unsupported Type](https://atcoder.jp/contests/abc415/tasks/abc415_a)
```cpp
#include <iostream>
#include <set>

int main()
{
	// 長さ N の整数列
	int N;
	std::cin >> N;

	// 整数列に含まれる整数を格納するセット
	std::set<int> set;
	for (int i = 0; i < N; ++i)
	{
		int a;
		std::cin >> a;
		set.insert(a);
	}

	// 含まれているか判定したい整数
	int X;
	std::cin >> X;

	// セットが X を含んでいるか判定
	if (set.contains(X))
	{
		std::cout << "Yes\n";
	}
	else
	{
		std::cout << "No\n";
	}
}
```
:::

:::details ABC454 B - Mapping
### [ABC454 B - Mapping](https://atcoder.jp/contests/abc454/tasks/abc454_b)
```cpp
#include <iostream>
#include <set>

int main()
{
	// N 人, M 種類の服
	int N, M;
	std::cin >> N >> M;

	// 服の種類を格納する set
	std::set<int> set;
	for (int i = 0; i < N; ++i)
	{
		int F;
		std::cin >> F;
		set.insert(F);
	}

	// set のサイズが N と同じならば、全員が異なる服を着ていることになる
	std::cout << ((set.size() == N) ? "Yes\n" : "No\n");

	// set のサイズが M と同じならば、全ての種類の服が使用されていることになる
	std::cout << ((set.size() == M) ? "Yes\n" : "No\n");
}
```
:::

:::details ABC236C - Route Map
### [ABC236C - Route Map](https://atcoder.jp/contests/abc236/tasks/abc236_c)
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
:::

:::details ABC294D - Bank
### [ABC294D - Bank](https://atcoder.jp/contests/abc294/tasks/abc294_d)
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

:::details ABC217D - Cutting Woods
### [ABC217D - Cutting Woods](https://atcoder.jp/contests/abc217/tasks/abc217_d)
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
