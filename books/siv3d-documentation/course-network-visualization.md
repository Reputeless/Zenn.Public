---
title: "コース | グラフ（ネットワーク）の描画"
free: true
---

簡単なグラフ（ネットワーク）を描画するプログラムを作る学習コースです。

# 1. 背景を設定
基本画面を作ります。背景を白や黒以外の好きな色に設定しましょう。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

void Main()
{
	// 背景色を設定する
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{

	}
}
```


# 2. ノードクラス
ID と中心座標を持つ、ノード用のクラスを作ります。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

// ノード ID を表す型エイリアス
using NodeID = int32;

// ノードクラス
struct Node
{
	NodeID id; // ID

	Vec2 pos; // 中心座標
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// ノード
	Node node{ 0, Vec2{ 400, 300 } };

	while (System::Update())
	{

	}
}
```


# 3. ノード描画関数
`Node` クラスに、ノードの円を描画するメンバ関数 `.drawNode()` を追加します。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

using NodeID = int32;

struct Node
{
	NodeID id;

	Vec2 pos;

	// 円の描画用のメンバ関数
	void drawNode() const
	{
		// 円を描画
		Circle{ pos, 40 }.draw();
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	Node node{ 0, Vec2{ 400, 300 } };

	while (System::Update())
	{
		// ノードを描画
		node.drawNode();
	}
}
```


# 4. ノードのラベルを描画
`Node` クラスに、ノードのラベルを描画するメンバ関数 `.drawLabel()` を追加します。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

using NodeID = int32;

struct Node
{
	NodeID id;

	Vec2 pos;

	void drawNode() const
	{
		Circle{ pos, 40 }.draw();
	}

	// ラベル (id) の描画用メンバ関数
	void drawLabel(const Font& font) const
	{
		// font を使って id を描画
		font(id).drawAt(pos, ColorF{ 0.25 });
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// ラベル描画用のフォント
	const Font font{ 40, Typeface::Bold };

	Node node{ 0, Vec2{ 400, 300 } };

	while (System::Update())
	{
		node.drawNode();

		// ラベルを描画
		node.drawLabel(font);
	}
}
```


# 5. ノードに影を付ける
`Circle::drawShadow(offset, blur, spread, color)` を使って、ノードの影を描きます。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

using NodeID = int32;

struct Node
{
	NodeID id;

	Vec2 pos;

	void drawNode() const
	{
		Circle{ pos, 40 }
			.drawShadow(Vec2{ 1, 1 }, 8, 1) // オフセット (1, 1), ぼかし半径 8px, 広がり 1px で影を描く
			.draw();
	}

	void drawLabel(const Font& font) const
	{
		font(id).drawAt(pos, ColorF{ 0.25 });
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 40, Typeface::Bold };

	Node node{ 0, Vec2{ 400, 300 } };

	while (System::Update())
	{
		node.drawNode();

		node.drawLabel(font);
	}
}
```


# 6. 複数のノード
ハッシュテーブル `HashTable<NodeID, Node>` に複数のノードを用意し、描画します。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

using NodeID = int32;

struct Node
{
	NodeID id;

	Vec2 pos;

	void drawNode() const
	{
		Circle{ pos, 40 }
			.drawShadow(Vec2{ 1, 1 }, 8, 1)
			.draw();
	}

	void drawLabel(const Font& font) const
	{
		font(id).drawAt(pos, ColorF{ 0.25 });
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 40, Typeface::Bold };

	// ハッシュテーブルに複数のノードを格納（NodeID と Node のペアで 1 つのエントリ）
	HashTable<NodeID, Node> nodes =
	{
		{ 0, Node{ 0, Vec2{ 200, 500 } }},
		{ 1, Node{ 1, Vec2{ 400, 100 } }},
		{ 2, Node{ 2, Vec2{ 600, 300 } }},
	};

	while (System::Update())
	{
		// range-based for で各エントリにアクセス
		for (const auto [nodeID, node] : nodes)
		{
			node.drawNode();

			node.drawLabel(font);
		}
	}
}
```


# 7. ノードとのインタラクション（マウスオーバー）
ノードの上にマウスカーソルが重なっているとき、マウスカーソルを手のアイコンにします。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

using NodeID = int32;

struct Node
{
	NodeID id;

	Vec2 pos;

	// ノードの円を返すメンバ関数
	Circle getCircle() const
	{
		return Circle{ pos, 40 };
	}

	void drawNode() const
	{
		getCircle() // getCircle() に置き換え
			.drawShadow(Vec2{ 1, 1 }, 8, 1)
			.draw();
	}

	void drawLabel(const Font& font) const
	{
		font(id).drawAt(pos, ColorF{ 0.25 });
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 40, Typeface::Bold };

	HashTable<NodeID, Node> nodes =
	{
		{ 0, Node{ 0, Vec2{ 200, 500 } }},
		{ 1, Node{ 1, Vec2{ 400, 100 } }},
		{ 2, Node{ 2, Vec2{ 600, 300 } }},
	};

	while (System::Update())
	{
		// range-based for で各エントリにアクセス
		for (const auto [nodeID, node] : nodes)
		{
			// 円とマウスカーソルが重なっていたら
			if (node.getCircle().mouseOver())
			{
				// カーソルを手のアイコンに
				Cursor::RequestStyle(CursorStyle::Hand);
			}
		}

		for (const auto [nodeID, node] : nodes)
		{
			node.drawNode();

			node.drawLabel(font);
		}
	}
}
```


# 8. ノードとのインタラクション（選択）
ノードをクリックしたとき、そのノードの番号を取得します。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

using NodeID = int32;

struct Node
{
	NodeID id;

	Vec2 pos;

	Circle getCircle() const
	{
		return Circle{ pos, 40 };
	}

	void drawNode() const
	{
		getCircle()
			.drawShadow(Vec2{ 1, 1 }, 8, 1)
			.draw();
	}

	void drawLabel(const Font& font) const
	{
		font(id).drawAt(pos, ColorF{ 0.25 });
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 40, Typeface::Bold };

	HashTable<NodeID, Node> nodes =
	{
		{ 0, Node{ 0, Vec2{ 200, 500 } }},
		{ 1, Node{ 1, Vec2{ 400, 100 } }},
		{ 2, Node{ 2, Vec2{ 600, 300 } }},
	};

	// 選択中のノードの ID を保持する変数。初期値は none
	Optional<NodeID> activeNodeID;

	while (System::Update())
	{
		// デバッグ用の表示
		{
			ClearPrint();
			Print << activeNodeID;
		}

		// 左クリックされたら activeNodeID を none にリセット
		if (MouseL.down())
		{
			activeNodeID = none;
		}

		for (const auto [nodeID, node] : nodes)
		{
			if (node.getCircle().mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// 円が左クリックされていたら
			if (node.getCircle().leftClicked())
			{
				// activeNodeID にそのノードの ID をセット
				activeNodeID = nodeID;
			}
		}

		for (const auto [nodeID, node] : nodes)
		{
			node.drawNode();

			node.drawLabel(font);
		}
	}
}
```


# 9. アクティブなノードの可視化
アクティブなノードを違う色で表示するようにします。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

using NodeID = int32;

struct Node
{
	NodeID id;

	Vec2 pos;

	Circle getCircle() const
	{
		return Circle{ pos, 40 };
	}

	void drawNode() const
	{
		getCircle()
			.drawShadow(Vec2{ 1, 1 }, 8, 1)
			.draw();
	}

	// アクティブなノードの円の描画用メンバ関数
	void drawNodeActive() const
	{
		getCircle()
			.drawShadow(Vec2{ 1, 1 }, 8, 1)
			.draw(ColorF{ 1.0, 0.9, 0.8 });
	}

	void drawLabel(const Font& font) const
	{
		font(id).drawAt(pos, ColorF{ 0.25 });
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 40, Typeface::Bold };

	HashTable<NodeID, Node> nodes =
	{
		{ 0, Node{ 0, Vec2{ 200, 500 } }},
		{ 1, Node{ 1, Vec2{ 400, 100 } }},
		{ 2, Node{ 2, Vec2{ 600, 300 } }},
	};

	Optional<NodeID> activeNodeID;

	while (System::Update())
	{
		{
			ClearPrint();
			Print << activeNodeID;
		}

		if (MouseL.down())
		{
			activeNodeID = none;
		}

		for (const auto [nodeID, node] : nodes)
		{
			if (node.getCircle().mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			if (node.getCircle().leftClicked())
			{
				activeNodeID = nodeID;
			}
		}

		for (const auto [nodeID, node] : nodes)
		{
			// アクティブなノードと、そうでないノードで描画関数を呼び分ける
			if (nodeID == activeNodeID)
			{
				node.drawNodeActive();
			}
			else
			{
				node.drawNode();
			}

			node.drawLabel(font);
		}
	}
}
```


# 10. ノードとのインタラクション（移動）
アクティブなノードをマウスでドラッグして移動できるようにします。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

using NodeID = int32;

struct Node
{
	NodeID id;

	Vec2 pos;

	Circle getCircle() const
	{
		return Circle{ pos, 40 };
	}

	void drawNode() const
	{
		getCircle()
			.drawShadow(Vec2{ 1, 1 }, 8, 1)
			.draw();
	}

	void drawNodeActive() const
	{
		getCircle()
			.drawShadow(Vec2{ 1, 1 }, 8, 1)
			.draw(ColorF{ 1.0, 0.9, 0.8 });
	}

	void drawLabel(const Font& font) const
	{
		font(id).drawAt(pos, ColorF{ 0.25 });
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 40, Typeface::Bold };

	HashTable<NodeID, Node> nodes =
	{
		{ 0, Node{ 0, Vec2{ 200, 500 } }},
		{ 1, Node{ 1, Vec2{ 400, 100 } }},
		{ 2, Node{ 2, Vec2{ 600, 300 } }},
	};

	Optional<NodeID> activeNodeID;

	while (System::Update())
	{
		{
			ClearPrint();
			Print << activeNodeID;
		}

		if (MouseL.down())
		{
			activeNodeID = none;
		}

		for (const auto [nodeID, node] : nodes)
		{
			if (node.getCircle().mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			if (node.getCircle().leftClicked())
			{
				activeNodeID = nodeID;
			}
		}

		// アクティブなノードがある && マウスの左ボタンが押されている
		if (activeNodeID && MouseL.pressed())
		{
			// アクティブなノードの中心座標を、マウスカーソルの移動量分だけ移動
			nodes[activeNodeID.value()].pos += Cursor::DeltaF();
		}

		for (const auto [nodeID, node] : nodes)
		{
			if (nodeID == activeNodeID)
			{
				node.drawNodeActive();
			}
			else
			{
				node.drawNode();
			}

			node.drawLabel(font);
		}
	}
}
```


# 11. エッジクラス
始点と終点の `NodeID` を持つ `Edge` クラスを作ります。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

using NodeID = int32;

struct Node
{
	NodeID id;

	Vec2 pos;

	Circle getCircle() const
	{
		return Circle{ pos, 40 };
	}

	void drawNode() const
	{
		getCircle()
			.drawShadow(Vec2{ 1, 1 }, 8, 1)
			.draw();
	}

	void drawNodeActive() const
	{
		getCircle()
			.drawShadow(Vec2{ 1, 1 }, 8, 1)
			.draw(ColorF{ 1.0, 0.9, 0.8 });
	}

	void drawLabel(const Font& font) const
	{
		font(id).drawAt(pos, ColorF{ 0.25 });
	}
};

// エッジクラス
struct Edge
{
	NodeID from; // 始点の NodeID

	NodeID to; // 終点の NodeID
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 40, Typeface::Bold };

	HashTable<NodeID, Node> nodes =
	{
		{ 0, Node{ 0, Vec2{ 200, 500 } }},
		{ 1, Node{ 1, Vec2{ 400, 100 } }},
		{ 2, Node{ 2, Vec2{ 600, 300 } }},
	};

	// エッジの配列
	Array<Edge> edges =
	{
		Edge{ 0, 1 },
		Edge{ 1, 2 },
	};

	Optional<NodeID> activeNodeID;

	while (System::Update())
	{
		{
			ClearPrint();
			Print << activeNodeID;
		}

		if (MouseL.down())
		{
			activeNodeID = none;
		}

		for (const auto [nodeID, node] : nodes)
		{
			if (node.getCircle().mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			if (node.getCircle().leftClicked())
			{
				activeNodeID = nodeID;
			}
		}

		if (activeNodeID && MouseL.pressed())
		{
			nodes[activeNodeID.value()].pos += Cursor::DeltaF();
		}

		for (const auto [nodeID, node] : nodes)
		{
			if (nodeID == activeNodeID)
			{
				node.drawNodeActive();
			}
			else
			{
				node.drawNode();
			}

			node.drawLabel(font);
		}
	}
}
```


# 12. エッジの描画
2 つのノードの間に線を引く関数を実装します。
```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

using NodeID = int32;

struct Node
{
	NodeID id;

	Vec2 pos;

	Circle getCircle() const
	{
		return Circle{ pos, 40 };
	}

	void drawNode() const
	{
		getCircle()
			.drawShadow(Vec2{ 1, 1 }, 8, 1)
			.draw();
	}

	void drawNodeActive() const
	{
		getCircle()
			.drawShadow(Vec2{ 1, 1 }, 8, 1)
			.draw(ColorF{ 1.0, 0.9, 0.8 });
	}

	void drawLabel(const Font& font) const
	{
		font(id).drawAt(pos, ColorF{ 0.25 });
	}
};

struct Edge
{
	NodeID from;

	NodeID to;
};

// 2 つのノード間に線を引く関数
void DrawEdge(const Node& from, const Node& to)
{
	Line{ from.pos, to.pos }.draw(3, ColorF{ 0.25 });
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 40, Typeface::Bold };

	HashTable<NodeID, Node> nodes =
	{
		{ 0, Node{ 0, Vec2{ 200, 500 } }},
		{ 1, Node{ 1, Vec2{ 400, 100 } }},
		{ 2, Node{ 2, Vec2{ 600, 300 } }},
	};

	Array<Edge> edges =
	{
		Edge{ 0, 1 },
		Edge{ 1, 2 },
	};

	Optional<NodeID> activeNodeID;

	while (System::Update())
	{
		{
			ClearPrint();
			Print << activeNodeID;
		}

		if (MouseL.down())
		{
			activeNodeID = none;
		}

		for (const auto [nodeID, node] : nodes)
		{
			if (node.getCircle().mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			if (node.getCircle().leftClicked())
			{
				activeNodeID = nodeID;
			}
		}

		if (activeNodeID && MouseL.pressed())
		{
			nodes[activeNodeID.value()].pos += Cursor::DeltaF();
		}

		// range-based for で各エッジにアクセス
		for (const auto& edge : edges)
		{
			// 始点のノードと終点のノードを渡す
			DrawEdge(nodes[edge.from], nodes[edge.to]);
		}

		for (const auto [nodeID, node] : nodes)
		{
			if (nodeID == activeNodeID)
			{
				node.drawNodeActive();
			}
			else
			{
				node.drawNode();
			}

			node.drawLabel(font);
		}
	}
}
```






