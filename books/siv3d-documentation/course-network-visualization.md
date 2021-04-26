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

# 2. 中央にノードを描く

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// ノードの半径
	double nodeRadius = 40;

	// 画面の中心
	const Vec2 sceneCenter = Scene::Center();

	while (System::Update())
	{
		// 円を描く
		Circle{ sceneCenter, nodeRadius }.draw();
	}
}
```

# 3. リング状にノードを描く

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// ノードの個数
	int32 N = 5;

	double nodeRadius = 40;

	const Vec2 sceneCenter = Scene::Center();

	while (System::Update())
	{
		// 360° / N
		const double angleStep = (360_deg / N);

		// ノードを描く
		for (auto i : step(N)) // for (int32 i = 0; i < N; ++i) と同じ
		{
			// 中心から見たノードの方向 (12 時 = 0°, 3 時 = 90°)
			const double angle = (i * angleStep);

			// ノードの中心座標、sceneCenter からみて angle 方向に 200px
			const Vec2 nodeCenter = sceneCenter.getPointByAngleAndDistance(angle, 200);

			// 円を描く
			Circle{ nodeCenter, nodeRadius }.draw();
		}
	}
}
```

# 4. エッジを描く

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	int32 N = 5;

	double nodeRadius = 40;

	// エッジの太さ
	double edgeThickness = 3;

	const Vec2 sceneCenter = Scene::Center();

	while (System::Update())
	{
		const double angleStep = (360_deg / N);

		// エッジを描く
		for (auto i : step(N))
		{
			// 現在のノードの方向
			const double angle = (i * angleStep);

			// 次のノードの方向
			const double nextAngle = (angle + angleStep);

			// 現在のノードの中心座標
			const Vec2 nodeCenter = sceneCenter.getPointByAngleAndDistance(angle, 200);

			// 次のノードの中心座標
			const Vec2 nextNodeCenter = sceneCenter.getPointByAngleAndDistance(nextAngle, 200);

			// 線分を描く
			Line{ nodeCenter, nextNodeCenter }.draw(edgeThickness, ColorF{ 0.25 });
		}

		// ノードを描く
		for (auto i : step(N))
		{
			const double angle = (i * angleStep);

			const Vec2 nodeCenter = sceneCenter.getPointByAngleAndDistance(angle, 200);

			Circle{ nodeCenter, nodeRadius }.draw();
		}
	}
}
```

