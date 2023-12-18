---
title: "C++ プログラミングの生産性を少し改善する Visual Studio の機能（2023）"
emoji: "⌨️"
type: "tech"
topics: ["cpp"]
published: false
---

> [C++ Advent Calendar 2023](https://qiita.com/advent-calendar/2023/cxx), 18 日目の記事です。

本記事では、Visual Studio での C++ プログラミングの生産性向上に役立つ機能を、最近のバージョンで追加された無料機能を中心に紹介します。

## 1. コーディング用の合字フォント
日本語環境の Visual Studio のデフォルトのフォントは **MS ゴシック** なので、フォントにこだわりのない人は MS ゴシックを使い続けているかもしれません。英語環境では Visual Studio 2022 から **Cascadia** がデフォルトのフォントになっています。

Cascadia は、Microsoft が 2019 年にリリースしたコーディング用のオープンソースフォントです。合字（リガチャ）の有無に応じて **Cascadia Code** と **Cascadia Mono** があります。合字を有効にすると特定の 2 つ以上の文字が、より洗練された 1 つのグリフで表現されます。人によって好みは分かれますが、一般にコードの可読性を向上させると考えられています。C++ のコードにおいては、`!=` や `>=`, `<=`, `++`, `<<`, `>>`, `^=`, `/=`, `::`, `&&`, `||`, `//`, `///` などの見た目が影響を受けます。

![](https://raw.githubusercontent.com/microsoft/cascadia-code/main/images/ligatures.png)
*Cascadia Code に収録されているおもな合字*

![](https://storage.googleapis.com/zenn-user-upload/b1655934400b-20231218.gif)
*Cascadia Code（合字あり）と Cascadia Mono（合字なし）の比較。<br>合字を有効にしても本来の字幅は維持される*

同じコードを、MS ゴシック、以前の英語版 Visual Studio のデフォルトフォント Consolas, そして Cascadia Code で比較してみましょう。

| MS ゴシック | Consolas | Cascadia Code |
|:--:|:--:|:--:|
| ![](https://storage.googleapis.com/zenn-user-upload/a46eaa30efa0-20231218.png) | ![](https://storage.googleapis.com/zenn-user-upload/d7ce83191a5c-20231218.png) | ![](https://storage.googleapis.com/zenn-user-upload/e47b6127db5f-20231218.png) |

#### 設定の手順
Cascadia は標準でインストールされています。*メニュー > ツール > オプション > 環境 > フォントおよび色* からフォントを変更できます。

![](https://storage.googleapis.com/zenn-user-upload/1bf510e2d084-20231218.png)


## 2. Doxygen スタイルのコメントの補助
Visual Studio 2019 16.6 から、XML コメントおよび Doxygen スタイルのコメントを補助する機能が追加されました。例えば C++ のコードにおいて、関数の前に `///` で始まるコメントを入力すると、関数の引数や戻り値の型、関数の説明を入力するためのテンプレートが自動的に挿入されます。

![](https://storage.googleapis.com/zenn-user-upload/05a8c7ab8292-20231218.gif)
*XML コメントの補助*

![](https://storage.googleapis.com/zenn-user-upload/d8031a172c69-20231218.gif)
*Doxygen スタイルのコメントの補助*

また、XML コメントと Doxygen スタイルのコメントどちらの内容も、ツールチップによるヒント表示に反映されるようになりました。

![](https://storage.googleapis.com/zenn-user-upload/3459b5d3e49a-20231218.png)
*ツールチップによるヒント表示*

#### 設定の手順

![](https://storage.googleapis.com/zenn-user-upload/c048311df87f-20231218.png)


## 3. コンパイラのメッセージの色分け
Visual Studio の拡張機能 [**VSColorOutput64**](https://marketplace.visualstudio.com/items?itemName=MikeWard-AnnArbor.VSColorOutput64) を使うと、コンパイラのメッセージを色分けして表示できます。例えば、エラーメッセージを赤、警告メッセージをオレンジ色で表示するように設定すると、大量のコンパイラのメッセージの中から重要な情報を見つけやすくなります。

#### 設定の手順
Visual Studio Marketplace または Visual Studio の拡張機能マネージャーから VSColorOutput64 をインストールします。インストール後、**メニュー > ツール > オプション > VSColorOutput64** から設定画面を開き、各項目の色を変更します。

この拡張を有効にすると、コンパイラのメッセージに作者への寄付を促すメッセージが表示されるようになりますが、`Yes I Donated!` オプションを `True` に変更すると、メッセージを非表示にできます。[私は GitHub Sponsors で作者に寄付をした](https://github.com/sponsors/mike-ward)ので、このオプションを `True` にしています。

![](https://storage.googleapis.com/zenn-user-upload/2fda1e98cfa4-20231218.png)


## 4. スペルチェック




#### 設定の手順
Visual Studio 2022 17.5 から、搭載されています。メニュー > 編集 > 詳細 > スペルチェッカーを切り替える から有効無効を切り替えられます。Visual Studio のメインツールバー上のボタンからも切り替えられます。

![](https://storage.googleapis.com/zenn-user-upload/a83aa667ac16-20231218.png)


## 5. クラスや union のメモリレイアウトの可視化




#### 設定の手順
2023 年 11 月にリリースされた Visual Studio 2022 17.8 から標準で有効になっています。


## まとめ
本記事では、Visual Studio での C++ プログラミングの生産性・品質向上に役立つ機能を 5 つ紹介しました。いずれも最近のバージョンで追加された無料機能です。ぜひ活用してみてください。

