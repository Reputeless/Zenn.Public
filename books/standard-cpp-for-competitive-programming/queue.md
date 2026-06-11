---
title: "std::queue"
free: true
---

- 読み方: キュー
- `<queue>` ヘッダに含まれる
- **先入れ先出し方式**のデータ構造を提供するコンテナアダプタ
- **幅優先探索（BFS）**でよく使われる。先に見つけた頂点・状態から順に処理し、新しく到達した頂点・状態を末尾に追加する用途に向いている

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
	- 添字アクセスや全要素の走査が必要なら `std::deque` を使う
- **イテレータは提供されない**
	- `std::queue` はイテレータを提供しない
	- そのため、範囲 `for` 文や `.begin()`、`.end()` を使った走査はできない

## 2. よく使う操作

| 操作 | 説明 | 計算量* | 重要度 |
|---|---|---|---|
| `.push(value)` | 要素を末尾に追加する | $O(1)$ | ★★★ |
| `.emplace(args...)` | 要素を末尾に直接構築して追加する | $O(1)$ | ★ |
| `.pop()` | 先頭の要素を削除する | $O(1)$ | ★★★ |
| `.front()` | 先頭の要素にアクセスする | $O(1)$ | ★★★ |
| `.back()` | 末尾の要素にアクセスする | $O(1)$ | ★★ |
| `.size()` | 要素数を返す | $O(1)$ | ★★ |
| `.empty()` | 空であるかを返す | $O(1)$ | ★★★ |

\* 内部コンテナが `std::deque` の場合の計算量

:::details void push(const T& value)
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

:::details emplace(Args&&... args)
- 要素をキューの末尾に直接構築して追加する
- `T` の構築に必要なコンストラクタ引数を渡すと、キューの末尾に新しい要素が構築されて追加される
- `T` の構築にコストがかかる場合、`push` よりも `emplace` を使うと効率が良くなることがある

```cpp
#include <iostream>
#include <queue>
#include <utility>

int main()
{
	std::queue<std::pair<int, int>> q;
	q.emplace(1, 2); // キューは { (1, 2) }
	q.emplace(3, 4); // キューは { (1, 2), (3, 4) }
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


## 3. 使い方のポイント

### 3.1 なぜ std::deque ではなく std::queue を使うのか
- 


### 3.2 先頭要素の取得と削除をセットで書く
- `std::queue` では、「キューの先頭から要素を取り出す」という操作が、値の取得を行う `.front()` と、要素の削除を行う `.pop()` に分かれている
- `.pop()` は値を返さないため、先頭の値を使いたい場合は、先に `.front()` で取得してから `.pop()` で削除する

```cpp
// キューの先頭から要素を取り出す
const int x = q.front(); // キューの先頭の値を取得する
q.pop();                 // キューの先頭の要素を削除する
```

- 2 つに分かれているが、論理的には「要素を 1 つ取り出す」ための一連の操作である
- その対応関係を明確にするため、次のようにあえて 1 行にまとめて書くスタイルも見られる

```cpp
const int x = q.front(); q.pop(); // キューの先頭から要素を取り出す
```

- この書き方は、2 行に分かれている元のコードと同じ意味である。ただし、次のような意図を持たせることができる
	- 取得と削除を 1 セットの「取り出す」操作として示す
	- `.front()` と `.pop()` の間に無関係な処理が入るのを防ぐ
	- 他言語の `popleft()`、`poll()`、`Dequeue()` など（いずれも「先頭から要素を取り出す」操作）に近い感覚で読めるようにする


## 4. よく使うパターン

### 4.1 キューが空になるまで処理する

```cpp
#include <iostream>
#include <queue>

int main()
{
	std::queue<int> q;
	q.push(1);
	q.push(2);
	q.push(3);

	while (!q.empty())
	{
		const int x = q.front(); q.pop();
		std::cout << x << '\n';
	}
}
```

### 4.2 幅優先探索（BFS）の実装
- 頂点 `0` を始点として、各頂点までの最短距離を BFS で求める例
- 扱っているグラフは有向グラフで、辺は次のようにつながっている

```txt
0 -> 1 -> 3
 \
  -> 2 -> 4
```

```cpp
#include <iostream>
#include <queue>
#include <vector>

int main()
{
	// 頂点数
	const int n = 5;
	
	// 有向グラフの隣接リスト
	// graph[i] は頂点 i から出る辺の行き先を表す
	std::vector<std::vector<int>> graph(n);
	graph[0] = { 1, 2 };
	graph[1] = { 3 };
	graph[2] = { 4 };

	// BFS の始点
	const int start = 0;

	// distances[i] は頂点 start から頂点 i への最短距離を表す（-1 は未訪問）
	std::vector<int> distances(n, -1);
	std::queue<int> q;

	// 始点を訪問済みにして, BFS を開始する
	distances[start] = 0;
	q.push(start);

	while (!q.empty())
	{
		// キューから現在の頂点を取り出す
		const int current = q.front(); q.pop();

		// 頂点 current から移動できる頂点を調べる
		for (const auto& next : graph[current])
		{
			// すでに訪問済みの頂点は処理しない
			if (distances[next] != -1)
			{
				continue;
			}
			
			// BFS では, 最初に到達した時点で最短距離が確定する
			distances[next] = (distances[current] + 1);
			
			// あとで頂点 next から出る辺を調べるため, キューに追加する
			q.push(next);
		}
	}

	// 各頂点について, 頂点 start からの最短距離を出力する
	for (int i = 0; i < n; ++i)
	{
		std::cout << i << ": " << distances[i] << '\n';
	}
}
```
```txt:出力
0: 0
1: 1
2: 1
3: 2
4: 2
```
