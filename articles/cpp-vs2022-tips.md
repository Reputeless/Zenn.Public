---
title: "良い C++ を書く・教えるのに役立つ Visual Studio の機能"
emoji: "⌨️"
type: "tech"
topics: ["cpp"]
published: false
---

> [C++ Advent Calendar 2023](https://qiita.com/advent-calendar/2023/cxx), 18 日目の記事です。

本記事では、Visual Studio での C++ プログラミングの生産性・品質向上に役立つ機能を、昨今のバージョンで追加されたものを中心に紹介します。

## コーディング用の合字フォント
日本語環境の Visual Studio のデフォルトのフォントは **MS ゴシック** なので、フォントにこだわりのない人は MS ゴシックを使い続けているかもしれません。英語環境では Visual Studio 2022 から **Cascadia** がデフォルトのフォントになっています。

Cascadia は、Microsoft が 2019 年にリリースしたコーディング用のオープンソースフォントです。合字（リガチャ）の有無に応じて **Cascadia Code** と **Cascadia Mono** があります。合字によって特定の 2 つ以上の文字が、より洗練された 1 つのグリフで表現されます。人によって好みは分かれますが、一般にコードの可読性を向上させると考えられています。C++ のコードにおいては、`!=` や `>=`, `<=`, `++`, `<<`, `>>`, `^=`, `/=`, `::`, `&&`, `||`, `//`, `///` などの見た目が影響を受けます。

![](https://raw.githubusercontent.com/microsoft/cascadia-code/main/images/ligatures.png)
*Cascadia Code に収録されているおもな合字*

![](https://storage.googleapis.com/zenn-user-upload/b1655934400b-20231218.gif)
*Cascadia Code（合字あり）と Cascadia Mono（合字なし）の比較。<br>合字を有効にしても本来の字幅は維持される*

同じコードを、MS ゴシック、以前の英語版 Visual Studio のデフォルトフォント Consolas, そして Cascadia Code で比較してみましょう。

| MS ゴシック | Consolas | Cascadia Code |
|:--:|:--:|:--:|
| ![](https://storage.googleapis.com/zenn-user-upload/a46eaa30efa0-20231218.png) | ![](https://storage.googleapis.com/zenn-user-upload/d7ce83191a5c-20231218.png) | ![](https://storage.googleapis.com/zenn-user-upload/e47b6127db5f-20231218.png) |

#### 設定の手順
メニュー > ツール > オプション > 環境 > フォントおよび色 からフォントを変更できます。

![](https://storage.googleapis.com/zenn-user-upload/1bf510e2d084-20231218.png)

## Doxygen スタイルのコメントの補助



## コンパイラのメッセージの色分け



## スペルチェック



## クラスのサイズとアライメントの表示



## クラスのメンバ変数のメモリレイアウトの可視化



## まとめ
本記事では、

