---
title: "クラスに大小関係を定義する"
free: true
---

## 1. 概要
- ユーザー定義のクラスには、デフォルトでは大小関係が定義されない
- 競技プログラミングでは、次のような場面でクラスの大小関係の定義が必要になる:
	- `std::sor()` による要素の並び替え
	- `std::set` や `std::map` のキーとしての使用
	- `std::priority_queue` での優先順位の決定
- C++20 では、三方比較演算子 `<=>` をオーバーロードすることで、6 種類の比較演算子（`<`, `>`, `<=`, `>=`, `==`, `!=`）を自動的に定義できる
	- 大小比較と同値比較を分けて定義する場合は、`<=>` に加えて `==` 演算子もオーバーロードする

## 2. 先頭要素から辞書順比較する
- `std::pair` や `std::tuple` と同じように、先頭要素から辞書順比較する場合、`= default` を使って実装を省略できる

```cpp
struct PageLine
{
	// ページ番号
	int page;

	// 行番号
	int line;

    // デフォルトの三方比較演算子により、
    // メンバ変数を宣言順（page, line）に比較
	friend auto operator <=>(const PageLine&, const PageLine&) = default;
};
```

- このクラスでは、次のような比較演算が定義される:
	- すべてのメンバ変数が等しい場合は等価
	- `page` が異なる場合は `page` の値で比較
	- `page` が等しい場合は `line` の値で比較


## 3. 要素ごとに大小関係の基準を変える
- 複数のメンバ変数を持つクラスで、大小関係の基準を要素ごとに変えたい場合は、`<=>` 演算子をカスタムする

```cpp
struct Player
{
	// プレイヤー名
	std::string name;

	// スコア
	int score;

    // カスタム比較：
    // 1. name で昇順にソート
    // 2. name が同じ場合は、score で降順にソート
	friend std::strong_ordering operator <=>(const Player& a, const Player& b)
	{
        // まず name で比較
		if (auto c = (a.name <=> b.name); c != 0)
		{
			return c;
		}

        // name が等しい場合は score で降順比較（b <=> a の順番に注意）
		return (b.score <=> a.score);
	}
};
```

- このクラスでは、次のような比較演算が定義される:
	- すべてのメンバ変数が等しい場合は等価
	- `name` が異なる場合は `name` の値で比較
	- `name` が等しい場合は `score` の値で比較
		- `score` はソートしたとき降順になるよう比較される


## 4. 特定のメンバ変数だけに着目して大小比較する
- 特定のメンバ変数の大小のみで比較する場合は、`==` 演算子と、カスタムした `<=>` 演算子をオーバーロードする

```cpp
struct Edge
{
	// 頂点番号
	int u, v;

	// 辺の重み
	int weight;

    // weight だけで大小比較する場合でも、
    // 等価比較は全メンバで行いたいため、
    // == 演算子は default で定義
	friend bool operator ==(const Edge&, const Edge&) = default;

    // 重みのみで大小を比較
    // （u, v の値は無視される）
	friend auto operator <=>(const Edge& a, const Edge& b)
	{
		return (a.weight <=> b.weight);
	}
};
```

- このクラスでは、次のような比較演算が定義される:
	- すべてのメンバ変数が等しい場合は等価
	- 大小は `weight` の値で比較
		- `u`, `v` の値は比較に影響しない

