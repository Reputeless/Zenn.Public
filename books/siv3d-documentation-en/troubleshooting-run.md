---
title: "Troubleshooting Applications"
free: true
---

## 1. Windows でマウスカーソルの位置と `Cursor::Pos()` の位置がずれる

### 原因 1: OS の「カスタムスケーリング」の設定との相性の問題です。
Siv3D は Windows の「表示スケール」(DPI) の変更には対応していますが、「カスタムスケーリング」の変更には対応していません。Windows の設定から、ディスプレイ → 表示スケールの詳細設定 → カスタムスケーリング を調べ、カスタムスケーリングを設定しないようにすれば発生しません。

![](/images/doc_v6/troubleshooting/customscaling.png)

## 2. macOS で起動に失敗する

### 原因 1: 接続されているオーディオ機器との相性の問題です。
まれに AirPods を接続しているときに、オーディオの初期化に失敗し、アプリケーションが起動しないトラブルが報告されています。オーディオ機器を外すか再接続して、もう一度試してみてください。

## 3. Windows でグラフィックスの表示がおかしい

### 原因 1: グラフィックスのドライバとの相性の問題です。
使用しているグラフィックスのドライバを更新するか、開発者の場合はエンジン初期化設定で Direct3D の代わりに OpenGL を使用したり、Direct3D の [WARP ドライバ](https://docs.microsoft.com/en-us/windows/win32/direct3darticles/directx-warp) を使用してみてください。

```cpp
# include <Siv3D.hpp>

// (Windows のみ) Direct3D の代わりに OpenGL レンダリングエンジンを使用
SIV3D_SET(EngineOption::Renderer::OpenGL);

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		Circle{ Scene::Center(), 100 }.draw();
	}
}
```

```cpp
# include <Siv3D.hpp>

// (Windows のみ) WARP ドライバによる Direct3D レンダリングを使用
SIV3D_SET(EngineOption::D3D11Driver::WARP);

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		Circle{ Scene::Center(), 100 }.draw();
	}
}
```
