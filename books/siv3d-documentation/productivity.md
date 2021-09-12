---
title: "テクニック | 開発生産性の向上"
free: true
---

Siv3D の開発を便利にするツールの活用法などを紹介します。

## Visual Studio の活用法


### プログラムの実行中にコードの変更を適用する



### 例外の発生箇所を表示する

サブスレッドで `Main()` を実行する Siv3D Windows 版の設計の制約上、デフォルトの設定では、例外が発生した行がコードエディタ上に表示されません。

```cpp
# include <Siv3D.hpp>

void Main()
{
	if (false)
	{
		throw Error{ U"A1" };
	}

	int32 a = 10;

	if (true)
	{
		throw Error{ U"A2" };
	}

	int32 b = 20;

	while (System::Update())
	{

	}
}
```

例外が発生した箇所をエディタ上で確認できるようにするには、Visual Studio メニューの「デバッグ」→「ウィンドウ」→「例外設定」を開き、「スローされたときに中断」のリストに `s3d::Error` を加えます。すると、その種類の例外が発生した次の行でプログラムが中断するようになり、例外発生個所の特定がしやすくなります。

![](/images/doc_v6/technique/productivity/exception.png)


### 出力メッセージを色付けする

Visual Studio の拡張機能「[VSColorOutput](https://marketplace.visualstudio.com/items?itemName=MikeWard-AnnArbor.VSColorOutput)」を使うと、出力ウィンドウ内で "warning" や "error" が含まれているメッセージが色付けされ、警告やエラーなどの重要な情報を見つけやすくなります。

![](/images/doc_v6/technique/productivity/vscoloroutput.png)
