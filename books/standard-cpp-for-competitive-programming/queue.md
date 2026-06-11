---
title: "std::queue"
free: true
---

- 読み方: キュー
- `<queue>` ヘッダに含まれる
- **先入れ先出し方式**のデータ構造を提供するコンテナアダプタ
- **幅優先探索**でよく使われる。先頭から順に処理し、新しく見つけた頂点や状態を末尾に追加する用途に向いている

## 1. std::queue の特徴

- **先入れ先出し（FIFO: First In, First Out）**
	- 最初に追加された要素が最初に取り出される
	- 追加は `.push()`、先頭要素の削除は `.pop()` で行う
	- `.pop()` は要素を返さないため、先頭の値を使いたい場合は先に `.front()` で取得する
- **内部実装はデフォルトで std::deque**
	- `std::queue` はコンテナアダプタであり、デフォルトでは内部コンテナとして `std::deque` を使う
- **要素へのアクセスは先頭と末尾のみ**
	- 先頭の要素には `.front()` でアクセスする
	- 末尾の要素には `.back()` でアクセスする
	- 要素への添え字アクセス `q[i]` はできない
- **イテレータは提供されない**
	- `std::queue` はイテレータを提供しない
	- そのため、範囲 `for` 文や `.begin()`、`.end()` を使った走査はできない

## 2. よく使う操作

| 操作 | 説明 | 計算量 | 重要度 |
|---|---|---|---|
| `.push(value)` | 要素をキューの末尾に追加する | $O(1)$ | ★★★ |
| `.emplace(args...)` | 要素をキューの末尾に直接構築して追加する | $O(1)$ | ★ |
| `.pop()` | キューの先頭の要素を削除する | $O(1)$ | ★★★ |
| `.front()` | キューの先頭の要素にアクセスする | $O(1)$ | ★★★ |
| `.back()` | キューの末尾の要素にアクセスする | $O(1)$ | ★★ |
| `.size()` | 要素数を返す | $O(1)$ | ★★ |
| `.empty()` | 空であるかを返す | $O(1)$ | ★★★ |

:::details void push(T&& value)
- 要素をキューの末尾に追加する

```cpp
#include <iostream>
#include <queue>

int main()
{
	std::queue<int> q;
	q.push(1); // キューは { 1 }
	q.push(2); // キューは { 1, 2 }
}
```
:::

:::details T& emplace(Args&&... args)
- 要素をキューの末尾に直接構築して追加する
- `T` の構築に必要なコンストラクタ引数を渡すと、キューの末尾に新しい要素が構築されて追加される
- `T` の構築にコストがかかる場合、`push` よりも `emplace` を使うと効率が良くなることがある
- 追加された要素への参照を返す（使う機会はあまりない）

```cpp
#include <iostream>
#include <queue>
#include <utility>

int main()
{
	std::queue<std::pair<int, int>> q;
	q.emplace(1, 2); // キューは { (1, 2) }
	q.emplace(3, 4); // キューは { (1, 2), (3, 4) }
	std::pair<int, int> p = q.emplace(5, 6); // キューは { (1, 2), (3, 4), (5, 6) }
	std::cout << p.first << ' ' << p.second << '\n'; // 5 6
}
```
---
```cpp
#include <iostream>
#include <queue>
#include <string>

int main()
{
	std::queue<std::string> q;
	q.emplace(3, 'a'); // キューは { "aaa" }
	q.emplace(2, 'b'); // キューは { "aaa", "bb" }
	std::string s = q.emplace(4, 'c'); // キューは { "aaa", "bb", "cccc" }
	std::cout << s << '\n'; // cccc
}
```
---
```cpp
#include <iostream>
#include <queue>

struct Point
{
	int x, y;
};

int main()
{
	std::queue<Point> q;
	q.emplace(1, 2); // キューは { (1, 2) }
	q.emplace(3, 4); // キューは { (1, 2), (3, 4) }
	Point p = q.emplace(5, 6); // キューは { (1, 2), (3, 4), (5, 6) }
	std::cout << p.x << ' ' << p.y << '\n'; // 5 6
}
```
:::

:::details void pop()
- キューの先頭の要素を削除する
- 空のキューに対して `.pop()` を呼び出してはならない

```cpp
#include <iostream>
#include <queue>

int main()
{
	std::queue<int> q;
	q.push(1); // キューは { 1 }
	q.push(2); // キューは { 1, 2 }
	q.pop(); // キューは { 2 }
	q.pop(); // キューは空
}
```
:::

:::details T& front()
- キューの先頭の要素にアクセスする
- 空のキューに対して `.front()` を呼び出してはならない

```cpp
#include <iostream>
#include <queue>

int main()
{
	std::queue<int> q;
	q.push(10); // キューは { 10 }
	q.push(20); // キューは { 10, 20 }
	q.push(30); // キューは { 10, 20, 30 }

	std::cout << q.front() << '\n'; // 10

	q.pop(); // キューは { 20, 30 }

	std::cout << q.front() << '\n'; // 20
}
```
:::

:::details T& back()
- キューの末尾の要素にアクセスする
- 空のキューに対して `.back()` を呼び出してはならない

```cpp
#include <iostream>
#include <queue>

int main()
{
	std::queue<int> q;
	q.push(10); // キューは { 10 }
	q.push(20); // キューは { 10, 20 }
	q.push(30); // キューは { 10, 20, 30 }

	std::cout << q.back() << '\n'; // 30

	q.pop(); // キューは { 20, 30 }

	std::cout << q.back() << '\n'; // 30
}
```
:::

:::details size_t size()
- 要素数を返す

```cpp
#include <iostream>
#include <queue>

int main()
{
	std::queue<int> q;
	std::cout << q.size() << '\n'; // size: 0

	q.push(1); // キューは { 1 }
	q.push(2); // キューは { 1, 2 }

	std::cout << q.size() << '\n'; // size: 2
}
```
:::

:::details bool empty()
- 空であるかを返す
- キューが空の場合は `true`、そうでない場合は `false` を返す

```cpp
#include <iostream>
#include <queue>

int main()
{
	std::cout << std::boolalpha;

	std::queue<int> q;
	std::cout << q.empty() << '\n'; // true

	q.push(1); // キューは { 1 }

	std::cout << q.empty() << '\n'; // false

	q.pop(); // キューは空

	std::cout << q.empty() << '\n'; // true
}
```
:::
